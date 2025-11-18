# How to Write a New Data Parser for Dug

Parsers in Dug are responsible for bridging the gap between external data formats (XML, CSV, JSON, etc.) and Dug's internal data model.

## 1. The Core Concept

At its heart, a parser is simply a function (wrapped in a class) that accepts an input file and returns a list of Indexable objects.

These objects must inherit from the base DugElement class. Depending on the structure of your input data, your parser might return any combination of the following:

* `DugStudy`: Represents a dataset or clinical trial.

* `DugVariable`: Represents a specific data point or question.

* `DugSection`: Represents a grouping of variables (e.g., a form or questionnaire).

* `DugConcept`: Represents a pre-defined ontological term (less common in raw data parsers, but possible).

There is no strict rule that you must generate a hierarchy of Study -> Section -> Variable. A parser could simply return a flat list of Variables, or a set of Studies without variables, depending on your needs.

## 2. The Base Class

All file-based parsers must inherit from `FileParser` defined in `src/dug/core/parsers/_base.py`.

The core requirement is implementing the `__call__` method:

```python
from typing import List
from dug.core.parsers._base import FileParser, Indexable, InputFile

class MyCustomParser(FileParser):
    def __call__(self, input_file: InputFile) -> List[Indexable]:
        # Logic to parse file and return a list of DugElements
        # e.g., [DugStudy(...), DugVariable(...), DugVariable(...)]
        pass
```

## 3. Case Study: The HEALDPParser

To illustrate how to build a robust parser, we will look at the architecture used by the `HEALDPParser` (found in `src/dug/core/parsers/heal_dp_parser.py`).

This parser is designed for the HEAL Data Platform, which has a hierarchical structure where Variables belong to Sections, and Sections belong to a Study. While your data might be simpler, this example demonstrates how to handle relationships between different element types.

### A. Setup and Imports

Create a new file, for example `src/dug/core/parsers/my_custom_parser.py`.

```python
import logging
from typing import List
from xml.etree import ElementTree as ET

# Import Dug models
from dug.core.parsers._base import (
    DugVariable,
    DugStudy,
    DugSection,
    FileParser,
    Indexable,
    InputFile
)

logger = logging.getLogger('dug')

class MyCustomParser(FileParser):
    def __init__(self, study_type="My Study Program"):
        # You can pass configuration params here, similar to how HEALDPParser
        # accepts a 'study_type'
        self.study_type = study_type

    def __call__(self, input_file: InputFile) -> List[Indexable]:
        logger.debug(f"Parsing {input_file}")
        elements = []
        
        # 1. Load the raw data
        tree = ET.parse(input_file)
        root = tree.getroot()
        
        # ... parsing logic continues below ...
```

### B. Creating the Parent Element (Study)

In the HEAL example, the file represents a single Study. We extract the metadata and create a `DugStudy` object first.
```python
        # Extract study metadata from the root of the file
        study_id = root.attrib.get('study_id', 'UNKNOWN_ID')
        study_name = root.attrib.get('study_name', 'Unknown Study')
        description = root.attrib.get('description', 'No description provided')

        # Create the DugStudy object
        study = DugStudy(
            id=study_id,
            name=study_name,
            description=description,
            programs=[self.study_type],
            parents=[],                 # Studies usually have no parents
            action=f"[https://my-portal.org/study/](https://my-portal.org/study/){study_id}",
            metadata={
                "version": "1.0"
            }
        )
```

### C. Handling Hierarchies (Variables & Sections)

The `HEALDPParser` iterates through variables and dynamically creates `DugSection` objects when it encounters grouping attributes (like `module="Demographics"`). This allows it to build the hierarchy on the fly.

```python
        # Dictionary to keep track of sections we've already created
        # Key: section_id, Value: DugSection object
        sections_map = {} 
        
        # List to hold variables
        variable_elements = []

        for var_node in root.iter('variable'):
            # 1. Extract Variable Details
            var_id = var_node.attrib['id']
            var_name = var_node.find('name').text
            var_desc = var_node.find('description').text or var_name
            
            # 2. Create DugVariable
            # Note: We link it to the Study ID via 'parents'
            variable = DugVariable(
                id=var_id,
                name=var_name,
                description=var_desc,
                programs=[self.study_type],
                parents=[study_id], 
                data_type=var_node.find('type').text or "string"
            )
            
            # 3. Handle Grouping (Sections/Modules) - HEAL specific logic
            # Check if this variable belongs to a section
            if 'module' in var_node.attrib:
                section_name = var_node.attrib['module']
                
                # Create the section if we haven't seen it yet
                if section_name not in sections_map:
                    new_section = DugSection(
                        id=section_name,
                        name=section_name,
                        description=f"Section {section_name}",
                        programs=[self.study_type],
                        parents=[study_id], # Sections are also children of the Study
                        is_crf=True 
                    )
                    sections_map[section_name] = new_section

                # Link the variable to the section
                variable.parents.append(section_name)
                sections_map[section_name].variable_list.append(variable.id)

            variable_elements.append(variable)

        # 4. Finalize Study links
        # Add all variable IDs to the study's variable_list for easy lookup
        study.variable_list = [v.id for v in variable_elements]

        # 5. Aggregate all elements into a single list for return
        elements.append(study)
        elements.extend(list(sections_map.values()))
        elements.extend(variable_elements)

        return elements
```

### 4. Registering the Parser

Once the class is written, you must register it so Dug's CLI (`dug crawl`) can find it.

1. Open `src/dug/core/parsers/__init__.py`.

2. Import your new class.

3. Add it to the `define_parsers` hook.
```python

# src/dug/core/parsers/__init__.py

from .my_custom_parser import MyCustomParser

@hookimpl
def define_parsers(parser_dict: Dict[str, Parser]):
    # ... existing parsers ...
    
    # Registering your custom parser
    parser_dict["my-format"] = MyCustomParser(study_type="My Program")
    
    # Existing HEAL examples for reference:
    # parser_dict["heal-studies"] = HEALDPParser(study_type="HEAL Studies")
    # parser_dict["heal-research"] = HEALDPParser(study_type="HEAL Research Programs")
```

### 5. Usage

You can now use your parser with the dug crawl command:

`dug crawl input_data.xml --parser my-format`


## Checklist for a Good Parser

1. **Inheritance**: Ensure it inherits from `FileParser`.
2. **Polymorphism**: Return any valid `DugElement` subclass (`DugStudy`, `DugVariable`, etc.) that fits your data model. You are not forced to use all of them. 
3. **IDs**: Ensure `id` fields are unique strings. If IDs might collide across studies, prefix them (e.g., `f"{study_id}:{var_id}"`).
4. **Descriptions**: The `description` field is crucial. This is what the NLP annotator reads to find ontological concepts.
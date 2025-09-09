# Local install with Docker

To install Dug in your environment , run `make install`. Alternatively,

```shell
pip install -r requirements.txt
pip install -e .
```

To run the tests , run `make test`. Alternatively,

```shell
pytest .
```

(The makefile currently only executes unit tests,
whereas running pytest in the root directory will execute unit tests, integration tests, and doctests)

To bring up the backend services, run:

```shell
docker-compose up
```

If you're running dug-specific commands (i.e. `dug`) outside the docker container,
you have to make sure the env vars are set. Also, make sure all hostnames are correct
for the environment you're running in. For example, to be able to connect to dug backend
services from outside the container (but in a shell env), run:

```shell
source .env
export $(cut -d= -f1 .env)
export ELASTIC_API_HOST=localhost
export REDIS_HOST=localhost
```

Test the implementation of 
[GraphQL interface](https://eips.ethereum.org/EIPS/eip-1767) across clients
using Docker and Docker-compose.

The idea is to have a docker-compose file for each client that looks like this

```yml
services:
  init_data:
    image: <data-only image of client>
    volumes:
      - "data-dir:/tmp/client-data"

  client:
    image: <client image>
    command: ["the_client", "--graphql"]
    depends_on:
      - init_data
    volumes:
      - "data-dir:/tmp/client-data"
    expose:
      - 8547

  test:
    image: <generic tester>
    depends_on:
      client:
        - healthcheck
    volumes:
      - "test-results:/tmp/test-results"
```

The client images are the ones already deployed to Dockerhub. The dockerfile
for the data-only images are part of the repo. The generic tester is a script
that sends graphql queries to the running client at its standard http
endpoint and compares the returned json values with an existing json file.
The test report is written to a folder mounted on the local file system, so
it can be displayed locally or integrated into a CI.
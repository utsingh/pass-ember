# pass-ember
[![Build Status](https://travis-ci.org/OA-PASS/pass-ember.png?branch=master)](https://travis-ci.org/OA-PASS/pass-ember)
[![Coverage Status](https://coveralls.io/repos/github/OA-PASS/pass-ember/badge.svg)](https://coveralls.io/github/OA-PASS/pass-ember)

PASS is an ember application which provides a unified user interface that allow its users to deposit their manuscripts
into multiple repositories as required by applicable funding agency's public access policies

PASS communicates with a Fedora repository on the backend using JSON-LD. See the
[ember-fedora-adapter](https://github.com/OA-PASS/ember-fedora-adapter) for more information.
Objects persisted to Fedora are automatically indexed by Elasticsearch. See
[pass-indexer](https://github.com/OA-PASS/pass-indexer) for more information about how this works.
Note that the indexing process is asynchronous. An object persisted to Fedora will not immediately be available in Elasticsearch.

## Prerequisites

You will need the following things properly installed on your computer.

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com/) along with docker-compose.

The `hosts` (`C:\Windows\System32\Drivers\etc\hosts` for windows, `/etc/hosts` for *nix) file on your development computer needs to be updated to alias `pass.local` to your loopback address (`127.0.0.1`) or to your docker-machine address (e.g. `192.168.99.100`). For example, to alias `pass.local` to your loopback address, you would make sure your `hosts` file contains the line: `127.0.0.1  pass.local`

## Installation

* `git clone <repository-url>` this repository
* `cd pass-ember`

## Running / Development

A docker environement which relies on a Shibboleth proxy is in .docker/. All services should
be available at https://pass.local/.

* In .docker/ run `docker-compose pull`
  * To start PASS with JHU assets, run `docker-compose up -d`.
  * To run the Harvard development stack, do `docker-compose -f harvard.yml up -d`
  * To start PASS with only a few test assets, run `docker-compose -f docker-compose.yml -f docker-compose.public.yml up -d`
* Wait for the containers to finish coming up, this could take 5-10 minutes. There will be a long pause while the `ember` container builds. When you see the "Build successful" message from `ember` and a small table listing the "Slowest Nodes" that indicates the application is ready to use. It will look similar to:

```
ember            | Slowest Nodes (totalTime => 5% )              | Total (avg)
ember            | ----------------------------------------------+---------------------
ember            | Babel: pass-ember (4)                         | 15209ms (3802 ms)
ember            | broccoli-persistent-filter:EslintValid... (3) | 6654ms (2218 ms)
ember            | Babel: ember-lodash (1)                       | 5960ms
ember            | Babel: ember-data (2)                         | 5386ms (2693 ms)
```

* The local code runs in the `ember` container, and changes in the local code will be reflected there.
  * The app directory is bind mounted into the container.
* Visit your app at https://pass.local/. You will have to login as a test user.
* Run your tests at https://pass.local/app/tests
* Fedora repository is at https://pass.local/fcrepo/
* Elasticsearch index search endpoint is at https://pass.local/es/
* In order to remove persisted data, stop all the containers and `docker volume prune`
* Your web browser may complain about unsigned certificates, but the complaints can be ignored.

### Test users

See [https://github.com/OA-PASS/pass-docker/blob/master/README.md#shibboleth-users] for a list of test users. Each has a password of `moo`.

### Configuration

The configuration for the docker environment occurs in .env. See documentation on the individual
components for configuration options.

The pass-ember application configures the Fedora adapter uses these environment variables.
There are also defaults specified in config/environment.js. They tell the adapter where Fedora
and Elasticsearch are and generally will not need to be modified during development.

In order to prevent an Authorization header being sent to Fedora and Elasticsearch,
set FEDORA_ADAPTER_USER and FEDORA_ADAPTER_PASSWORD to empty strings.

### Using ember in the container.

Login to the ember container with `docker exec -it ember /bin/sh`.
Then run ember with `./node_modules/ember-cli/bin/ember`.
This can be useful for adding and updating dependencies.
You can run also run tests, but you may have to install other dependencies such as google-chrome.

### Running ember locally

If you wish, you may install ember locally. The application will not be able to run due to the need to access various web services,
but running tests on the command line can be useful. See documentation at [ember.js](https://emberjs.com/). There may be dependency issues requiring node-sass to be rebuilt for your environment.

https://github.com/utsingh/Data-Science-Books

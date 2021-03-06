# Load tests for consumer behaviour API

The project was writen using Scala programming language. 
For the load test I used the stress tool Gatling.

## Setup development environment

Install VirtualBox and Vagrant in your machine (preferable Unix machines)

Inside project folder run:

`vagrant up`

This will provide a Linux virtual machine with the dependencies already installed.
Also, the project's folder will be synchronized with the folder '/vagrant' of virtual machine. It means that you can edit files from both places: your machine and also the virtual machine.

To access the virtual machine run:

`vagrant ssh`
`cd /vagrant`

## Setup the configuration

Edit the file `/vagrant/src/main/resources/application.conf`
Set the values for service base URL, username, password, etc.

## Running the Tests

Inside the /vagrant directory of virtual machine run the command:

`sbt`

### Preparing the hashes for tests

The class ClientTranslator is prepared to grab a cvs file with a sequence of CPFs and apply the hash algorith on it.
Configure the translator section of the `application.conf` file:

 Config | Description 
 -------- | -------- 
 cpf_input_file | a cvs file with a CPF per line. The base path is the project folder 
 hash_output_file | a cvs file that will be generated with the resultant hashs for the given CPF 
 provider | the provider that will be used on the hash 

Then run the following command inside `sbt` terminal

`gatling:testOnly *ClientTranslator*`

Ps: this test will fail cause any http request were made =/

### Authentication tests

The class AuthSiulation has some tests for the authentication endpoint.
Configure the service and auth section of the `application.conf`file:

Config | Description 
 -------- | -------- 
 service.host | the service base URL 
 service.repeat | in combination with simultaneous_users defines the number of requests
 service.simultaneous_users | number of sitaneous users for the API requests
 auth.endpoint | the relative path related to the host to request a token
 auth.username | -
 auth.password | -

Then run the following command inside `sbt` terminal

`gatling:testOnly *AuthSimulation*`

### Consumer Behaviour tests

There are three classes for different types of tests considering this service:

Class | Description 
 -------- | -------- 
 BehaviourValidation | has the basic acceptance tests 
 BehaviourSimulation | grabs a list of CPFs and generates a file with the service response for each
 BehaviourLoadSimulation | tests with different size of bulk on request
 
 Configure the properties on `application.conf` file, then run the command inside `sbt` terminal replacing `CLASS_NAME` for one of the mentioned above:
 
 `gatling:testOnly *CLASS_NAME*`
 
## Test Reports
 
 After tests execution you can check a detailed report at `./target/gatling/{TEST_EXECUTION}/index.html`

# jbpm-marshalling-demo
jBPM marshalling strategies demo. Demo the use of custom complex objects as the jBPM business process parameter.
# Overview
The ticketing.ticket-process business process defines a simple "log parameter" task that uses System.out.println script to let us know the received parameters by the kie server API.
The expected object is a "ticketRequest" custom object with a "Title" and a "Description".
# Assumptions.
* To begin the usage of this POC, clone the git repository and follow the below instructions.
* We assume the git clone in the `~/gits/jbpm-marshalling-demo` for the purpose of the below instructions.
* We assume an unmanaged kie server
* We assume the `mvn install` uses the same local repository that the kie server is watching.
* We assume the kie server is deployed to localhost Jboss EAP instance.
* We assume the existence of a `kieserver` user with `kie-server` and `rest-all` roles and `kieserver1!` password.
Please consider the assumptions to customize the below instructions to your particular environment.
# Build and deploy the assets
1. Using terminal and maven, build the ticket-model components:
  1. Change working directory to `~/gits/jbpm-marshalling-demo/ticket-model`
  2. execute `mvn clean install` command
2. Using terminal and maven, build the ticketing process components:
  1. Change working directory to `~/gits/jbpm-marshalling-demo/ticketing`
  2. execute `mvn clean install` command
3. Ensure your kie server instance is up and running.
4. Deploy the kjar component to your kie server using the REST API:
  ```
  $ curl -X PUT -H "Accept:application/json" -H "Content-Type:application/json" --user kieserver:kieserver1! \
  -d '{"release-id":{"group-id":"org.acme.marshalling-demo","artifact-id":"ticketing","version":"3.0"}}' \
  "http://localhost:8080/kie-server/services/rest/server/containers/ticketing-container"
  ```
  _ticketing-container_ is the container name.
5. You should receive a reponse like the following:
  ```
  {
    "type" : "SUCCESS",
    "msg" : "Container ticketing-container successfully deployed with module org.acme.marshalling-demo:ticketing:3.0.",
    "result" : {
      "kie-container" : {
        "status" : "STARTED",
        "scanner" : {
          "status" : "DISPOSED",
          "poll-interval" : null
        },
        "messages" : [ ],
        "container-id" : "ticketing-container",
        "release-id" : {
          "version" : "3.0",
          "group-id" : "org.acme.marshalling-demo",
          "artifact-id" : "ticketing"
        },
        "resolved-release-id" : {
          "version" : "3.0",
          "group-id" : "org.acme.marshalling-demo",
          "artifact-id" : "ticketing"
        },
        "config-items" : [ ]
      }
    }
  }
  ```
# Execute the business process with custom object parameter
1. Change working directory to `~/gits/jbpm-marshalling-demo/extras`
2. Launch the business process execution by running:
  ```
  $ curl -X POST -H "Accept: application/json" -H "Content-Type: application/json" --user kieserver:kieserver1! -d @ticket.js \
  http://localhost:8080/kie-server/services/rest/server/containers/ticketing-container/processes/ticketing.ticket-process/instances
  ```
3. Check the EAP log to show lines like the following:
  ```
  06:04:15,292 INFO  [stdout] (default task-7) TICKET REQUEST RECEIVED:
  06:04:15,292 INFO  [stdout] (default task-7) TITLE: [a title]
  06:04:15,293 INFO  [stdout] (default task-7) DETAIL: [a detail]
  ```

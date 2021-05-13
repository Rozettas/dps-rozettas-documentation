# Rozettas' *Insert random name here* e-shop

##### Usage of this documentation:
1. This documentation contains all the features what are approved by the team
2. Use the Issue tab to make feature request, and make suggestions about the current features
   * Before opening a feature discussion, please check if the feature already has a thread
   * Please in the title of thread refer to the feature ID, and the feature name###Feature list

###### NOTE: Feature implementation roadmap should be controlled by the PM, base on the list of the features, and their noted importance.

|ID|Feature name|Feature importance|Done|Estimated implementation time(hours)| Dependencies|Description
| ---:| --- | --- | --- | --- | --- | --- |
#1|Infrastructure|Essential|[ ]| 16 hours| | The IT infrastructure of the webshop
#2|Database|Very important|[ ]|3 hours| [ ] Infrastructure| The database itself stores and manages every dataset we need
#3|Customer|Very important|[ ]|6 hours|[ ] Database| Service that handles sensitive customer data
#4|Seller|Very important|[ ]| 8 hours| [ ] Database| Service that handles sensitive seller data
#5|Inventory management|Important|[ ]|16 hours|[ ] Database</br>[ ] Seller| Service that can track the state of the sellers' inventory, possible third-party connections
#6|Shopping cart|Moderately important|[ ]|8 hours|[ ] Database|Service that handles the ordering lists
#7|Discount| Less important|[ ]|8 hours|[ ] Inventory management| Service that can handle temporary pricing rules
###### Footnote: The features can be divided to sub-features. The time estimation guesses are referring to the amount of time needed, to make a stable build with every dependency (what is not listed uniquely).

### Some descriptions:

#### Infrastructure and general principles:
* In our case we need a solution with High Availability. This denotes, that it should be scalable. Accordingly i propose to go from the beginning with containerized services and container orchestration tools.
* The concept of the solution is a service mesh, where every part plays it's on part independently. With this we can avoid the problems of Monolith softwares, while we can make it easily scalable and updatable.
* As most of the services' containers/pods are so called Statless(every request handled as a new), we gain the ability to easily replace every unhealthy container with an identical new one or a newly released one automatically.
* Living in a cluster has some more practical features, like:
   * Invisible for the outside world (We can of course open doors)
   * Based on our settings the load on the services is automatically split to maintain balance between the pod replicas.
* Beside this we have stateful services too, like database, but with enough care it can handled inside a cluster, and it can be scalable too.
* As we obviously have to store data, we have to utilize a data storage outside of the cluster.
   * The reason for this: As i mentioned above, the containerized solutions are basically have no state, so if a pod dies, everything in the pod will be lost. To avoid this, we can tell the service where we store the data. The same applies to 
     that scenario, when the whole cluster dies.
     
### Coding part:

#### Devops best-practices and tools to implement:
1. DevSecOps lifecycle - Used tools - Used methods:
   * Plan: Jira - Sprints/Gantt chart(Roadmap)
   * Code: Whatever IDE - Language based coding style conventions, 
   * Build: ??? Here we need to agree on a CI/CD pipeline for automating steps
   * Test: ??? As stated above
   * Release: On a successful pull request (after those automated test) push the new release to the artifactory
   * Deploy: ?Octopus deploy? - For implementing a public test phase we should implement a green/blue deployment method
   * Monitoring: Prometheus, Istio, Traefik mesh - Follow-up of currently used releases

###### Notes: Preferred to use an identical environment for development and for production</br>Green/Blue deployment in practice works like the currently used release is the blue, and after deploying a new release we won't redirect all the users instantly, just an amount of them, to utilize a beta testing while it is a production ready release. If everything works fine with time we redirect all the users, and the current green deployment will become the blue one.

###### Footnote: We need a developer meeting to discuss our CI/CD pipeline
2. Code Review and release management:
   * After every sprint I would like to have a code review session, where we sit down with each others code, and make comments with questions suggestions.
   * As a coding workflow I would ask the coder team to use gitflow
     https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow

#### Architecture:
   ?If event-driven:
      * Message broker needed, but we can make it more secure and stable, as the parts will become loosly coupled
   ?If RESTful:
      * Less pain to plan and implement

#### Connecting services through:

?If CRUD:</br>
   * GraphQL: https://graphql.org/ or
   * JSON: However JSON is plain-text, hereby carries some obvious vulnerabilities from the aspect of security

?If Protocol Buffer: (https://developers.google.com/protocol-buffers)
   * The communication between services are binaries, so it is partially encoded.
   * The implementation involves extra steps

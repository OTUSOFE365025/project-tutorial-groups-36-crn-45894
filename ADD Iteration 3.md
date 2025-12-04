# Iteration 3:

**Step 1: Re-review inputs**

Upon reviewing the inputs, we have discovered another quality attribute that would be partially satisfied by the end of this iteration.

| Category  | Details |
| :---- | :---- |
| Quality Attribute Scenarios | QA-4: “A system maintainer performs an update or rollback from backup, and the system continues operating normally, allowing changes to be applied and tested without affecting user sessions.” It will be included in this iteration as an add-on driving Quality Attribute. It can be easily partially addressed by the end of this iteration. |

**Step 2: Establish Iteration Goal By Selecting Drivers**

The goal of this iteration is to develop and complete the remaining driving Quality Attributes of the system. By developing these Quality Attributes, we can also address the remaining concerns and constraints. These Quality Attributes include: 

| Category  | Details |
| :---- | :---- |
| Quality Attribute Scenarios | QA-1: Performance \- A student queries academic information through the AI assistant. The system processes requests within 2 seconds on average under normal load. QA-2: Availability \- The system remains accessible and operational 99.5% of the time each month. Maintenance or deployment operations do not interrupt ongoing user sessions. QA-4: Maintainability \- A system maintainer performs an update or rollback from backup, and the system continues operating normally, allowing changes to be applied and tested without affecting user sessions. |
| Constraints | CON-5: The system must handle up to 5,000 concurrent users. CON-7: The system must maintain data integrity and consistency across all connected systems. CON-8: The system must handle failures in data source availability gracefully. CON-9: The system must allow maintainers to deploy updates with zero downtime. |
| Architectural Concerns | CRN-4: Ensure service reliability with clear backup/rollback procedures and fault detection to handle external data source outages. |

**Step 3: Choose One or More Elements of the System to Refine**

The elements of the system that need to be refined in this iteration are the ones most closely related to performance and system reliability. In previous iterations, we developed the overall system architecture of the AIDAPs system. When performance is a concern, we must develop the physical nodes of the application.   
More specifically, we must look at the physical implementation of the system in servers and how the users will interact with said servers. By targeting the integration of the system into physical environments, we can complete the remaining design objectives. This means we must expand on our original deployment diagram and take another look at the Web/App Server defined in Iteration 1\.   
With the inclusion of new modules that will be discussed in later steps, we will be expanding on some of the external systems outside of the main Web/App Server of AIDAP. This is done with the target of making the server side of AIDAP replicable in separate instances. With that being said, the main target of this iteration is to look beyond any specific layer within the AIDAP system, and to instead target the some of the systems that support the functionality of AIDAP.

**Step 4: Choose One or More Design Concepts That Satisfy the Selected Drivers**

The design concepts used in this iteration are the following:

| Design Decisions and Location | Rationale and Assumptions |
| :---- | :---- |
| Introduce the active redundancy tactic by replicating the Web/App server and other critical components like the external and database connections | Replicating the system critical elements of our system allows us to ensure the system stays operational during failures and maintenance. For example, if one instance of the server fails, the system will still remain online and available for student use. The same applies to maintenance periods. If a system maintainer performs an update or rollback (QA-4, QA-2, UC-6, CON-8, CON-9), other instances of the system will still be available for use. |
| Introduce Message Queues to aid in database and external communications | Messages from the AIDAP system will be placed in a message queue and will be used by external or database connections. Use of a queue will guarantee that cross-system messages are processed and delivered in order (CON-8, CON-7). |
| Implement direct synchronous API calls to check the health of various components of the system | Direct synchronous health-check API provides immediate fault detection, which enables automatic backup/rollback procedures and mitigates the impact of external data source outages (CRN-4). Health-check endpoints ensure the system can identify when a Web/App instance, database connection, or external connector becomes unhealthy. This allows the system to reroute requests and remove unhealthy nodes from service (QA-4). |

**Step 5: Instantiate Architectural Elements, Allocate Responsibilities, and Define Interfaces**

The instantiation design decisions are summarized in the following table:

| Design Decisions and Location | Rationale |
| :---- | :---- |
| Deploy message queue on a separate node | Asynchronous requests are buffered to improve availability and reduce blocking. Placing the Message Queue on its own node allows the system to handle failures in external academic data sources gracefully (CON-8) while preserving data consistency and ordering (CON-7). The queue absorbs spikes in load and prevents slow external systems from degrading performance (QA-1). |
| Instantiate redundant database connectivity so that the system remains operable even if one database connection path becomes unavailable | Without assuming replication inside the database itself, we can only instantiate redundant access paths to the data store. This supports QA-4 (maintainability) by ensuring that if a maintainer performs an update or rollback (UC-6), active users are still able to retrieve needed data through an alternative connection. |
| Use active redundancy and load balancing in the Web/App server. Deploy multiple servers and database instances instead of a single monolithic server. | Since the Web/App server is deployed in multiple replicas, using the Load-Balanced Cluster pattern ensures that requests are distributed across instances. This maintains responsive performance under high concurrency (QA-1, CON-5), improves availability by routing only to healthy replicas (QA-2), and allows updates or rollbacks on individual servers without disrupting users (QA-4, CON-9). |
| Instantiate redundant connections to external systems (LMS, SSO, academic data sources). | Step 4 specifically mentions replicating “external connections.” Therefore, the refined system must instantiate multiple communication pathways to the external systems used in UC-1, UC-2, UC-3, and UC-5. This prevents a single failed connection from interrupting academic queries or login, supporting QA-2 (availability) and CRN-4 (reliability). |
| Implement load balancing and redundancy using technology support  | There are many existing technological options for load balancing and redundancy that can be implemented, avoiding the need to develop a custom solution which would likely be less mature and more difficult to support. |

**Step 6: Sketch Views and Record Design Decisions**

Refined Deployment Diagram:![][image1]

| Element | Responsibility |
| :---- | :---- |
| Load Balancer | Distributes incoming client requests across the replicated Web/App server instances (QA-1, CON-5, CON-8). |
| Message Queue | Receives requests or events from the WebAppServer instances that must be processed asynchronously or retried. The queue persists these messages, delivers them reliably to consumers, and helps the system handle external failures and spikes in load without losing work (CON-7). |
| \<\<Replicated\>\> Database Tier, External Systems,  Authentication System | These replicated services represent repeated instances of connections to the same service. Their replicated nature highlights the redundancy and replaceability of these modified modules. |

The UML sequence diagram shown below illustrates how the Message Queue that was introduced in this iteration exchanges messages with other elements shown in the deployment diagram to support UC-2 (query academic information), which is associated with QA-1 (performance). It also shows how the Message Queue acts as a middle man between different system elements, containing temporary information until it is requested by the system. This diagram focuses on the communication between physical nodes as we are focusing on the deployment side of the system, hence, no actors will be listed.  
In this section, we will be taking another look at the sequence diagrams we designed last iteration. Because of the newly defined modules, we will be specifying their general placement in these updated sequence diagrams. Many of the core modules remain the same from the last iteration, so their functionality will not be reiterated.   
![][image2]  
UC-1: Publish announcements

| Message Name | Description |
| ----- | :---- |
| **Load Balancer** |  |
| announcementrequest | The load balancer forwards the incoming announcement request to an available instance of the AIDAP system. |
| publishconfirmation | The load balancer forwards the confirmation of the request back to the correct user. |
| **LMS Service Agent** |  |
| passAnnouncementRequest() | The service agent from the instantiated AIDAP system passes the request for an announcement to the external Message Queue. |
| **Message Queue** |  |
| LMSrequest | The message queue forwards the message/request to the external LMS system. |
| syncstatus | The message queue syncs the status of the instantiated AIDAP system with the newly established announcement time. |
| **LMS** |  |
| passAnswer() | Passes the response/answer from the LMS to the question or message back to the Message Queue. |

![][image3]  
UC-2: Query academic information

| Message Name | Description |
| ----- | :---- |
| **Load Balancer** |  |
| forwardedquestion | The load balancer forwards the incoming academic question/query request to an available instance of the AIDAP system. |
| AIresponse | The load balancer forwards the AI response to the user’s academic query back to the correct user. |
| **LMS Service Agent** |  |
| passAcademicRequest() | The service agent from the instantiated AIDAP system passes the request for academic information to the external Message Queue. |
| **Message Queue** |  |
| LMSrequest | The message queue forwards the message/request to the external LMS system. |
| academicinformation | The message queue passes the required academic information back to the instantiated AIDAP system. |
| **LMS** |  |
| passAnswer() | Passes the response/answer from the LMS to the question or message back to the Message Queue. |

![][image4]  
UC-5: Publish or modify course material

| Message Name | Description |
| ----- | :---- |
| **Load Balancer** |  |
| forwardedmaterial | The load balancer forwards the incoming course modification request to an available instance of the AIDAP system. |
| confirmation | The load balancer forwards the confirmation of the course being modified back to the correct user. |
| **LMS Service Agent** |  |
| passMaterialRequest() | The service agent from the instantiated AIDAP system passes the updated course material to the external Message Queue. |
| **Message Queue** |  |
| LMSrequest | The message queue forwards the message/request to the external LMS system. |
| syncstatus | The message queue syncs the status of the instantiated AIDAP system with the modified course. |
| **LMS** |  |
| passAnswer() | Passes the response/answer from the LMS to the question or message back to the Message Queue. |

**Step 7: Perform Analysis of Current Design and Review Iteration Goal and Achievement of Design Purpose**

| Not Addressed | Partially Addressed | Completely Addressed | Design Decisions Made During Iteration |
| :---- | :---- | :---- | :---- |
|  | QA-1 |  | Active redundancy, load balancing, and a dedicated message queue node improve expected performance, but actual performance cannot be guaranteed without selecting specific technologies. |
|  | QA-2 |  | Replicated servers and health checks support availability, but expected uptime cannot be guaranteed because the deployment environment and failover setup have not been finalized. |
|  | QA-4 |  | Redundant paths allow updates and rollbacks without disruption, but the specific update approach has not been finalized. |
|  | CON-5 |  | Load-balanced replicated servers will help support the required 5,000 concurrent users. |
|  | CON-7 |  | Message queues support ordered communication and consistency, but final behavior depends on the queue technology selected. |
|  |  | CON-8 | Message queues, redundant external connectors, and synchronous health checks fully define the system’s behavior during external data-source failures, enabling graceful degradation independent of specific technology choices. |
|  | CON-9 |  | Redundant server instances allow zero-downtime deployment by updating nodes individually, but the rollout method has not been specified. |
|  | CRN-4 |  | Redundancy and health checks improve reliability, but backup and rollback procedures have not been fully defined. |

# ATAM Review:

**ATAM Utility Tree**  
![][image5]

**ATAM Risk Assessment**  
Risks

* R1: Replicated Web/App server instances may behave inconsistently if their configurations or deployments become unsynchronized.  
* R2: The message queue may become overloaded and delay responses.  
* R3: The risk is introduced by the load balancer acting as a single point of failure.  
* R4: Misconfigured health-checks may incorrectly remove healthy nodes or keep unhealthy nodes active.

Non-Risks

* N1: Having multiple Web/App server replicas prevents a single server failure from taking the system offline.  
* N2: Redundant external connector paths allow the system to continue operating normally even when one external system becomes unavailable.  
* N3: The message queue handles incoming requests independently of external system speed, so brief slowdowns in external systems do not interrupt normal operation.  
* N4: Redundant Database paths allow the system to continue operating even if one DB connection becomes unavailable.

Sensitivity

* S1: System availability depends on how quickly the load balancer detects and removes unhealthy Web/App servers.  
* S2: System performance and availability depend on how evenly the load balancer distributes requests across Web/App server replicas.  
* S3: Zero-downtime operation depends on maintaining enough active Web/App replicas to continue serving users during updates or rollbacks.  
* S4: Availability relies on redundant external connector paths functioning properly when individual external systems experience outages.  
* S5: Concern that system consistency depends on the message queue preserving correct message ordering, since any deviation could result in out-of-order updates or outdated external data being returned.

Trade-off

* T1: Performance (+) vs. Modifiability (-) Asynchronous message handling through the message queue improves flexibility with external systems, but adds processing overhead that can slow response times.  
* T2: Availability (+) vs. Modifiability (-) Replicating external connector paths and using health checks increases availability during external system outages, but adds coordination and configuration complexity.  
* T3: Availability (+) vs. Performance (-) Using a load balancer and running server health checks improves uptime by removing unhealthy instances, but introduces routing and monitoring overhead that reduces performance.

**ATAM Risk Assessment Table**

| Analyzing Scenario | QA-2 |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
| **Scenario** | The system remains accessible and operational 99.5% of the time each month. Maintenance or deployment operations do not interrupt ongoing user sessions. |  |  |  |
| **Attributes** | Availability |  |  |  |
| **Stimulus** | A system maintainer initiates an update or rollback. |  |  |  |
| **Environment** | Normal load with two Web/App server replicas behind a load balancer, replicated external systems, and replicated database connections while multiple students are using the system. |  |  |  |
| **Response** | The system continues operating normally by routing requests to healthy replicas, with no downtime or disruption to ongoing sessions. |  |  |  |
| **Architecture Decision** | **Sensitivity** | **Tradeoff** | **Risk** | **Nonrisk** |
| AD1 Add Load Balancer and replicate Web/App servers | S1, S2, S3 | T3 | R3 | N1 |
| AD2 Message Queue between AIDAP and external systems | S5 | T1 | R2 | N3 |
| AD3 Redundant external connectors with health-checks | S3, S4 | T2 | R4 | N2 |
| Redundant Database connectivity for failover |  |  |  | N4 |
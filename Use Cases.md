| Use Case ID | Description | Requirement ID |
|--------------|--------------|----------------|
| **UC-1: Publish announcements** | A lecturer or admin uses the AI assistant to post course announcements. It interprets the command and publishes the announcement to the relevant course channel. An admin can broadcast to the entire campus. | R1, R3, R4, R5, RL2, RA3 |
| **UC-2: Query academic information** | Allows a student to ask various academic or administrative questions. The system will process the question using Natural Language Processing and provide an appropriate response. Data will be received from the school’s data source systems. | R1, R3, R4, R5, R8, RS1, RS4, RS5, RD1, RD4 |
| **UC-3: Send notifications** | When needed, the system will send out the appropriate notifications for the user. Students will receive academic reminders, lecturers will receive details about statistical anomalies. Lecturers can automate periodic notifications or reminders. | R3, RS2, RL4, RS9, RL7, RD1, RD4 |
| **UC-4: Log in** | Users will log into the system using a login/password screen. When they successfully log in, the user will be presented with different options and views according to their role. Users can bypass manual insertion by using SSO. | R8, RS7, RS8, RL8, RA5, RM7, RD1 |
| **UC-5: Store interaction data** | Previous user interactions with the AI assistant will be stored in the school database. Personal user histories can be browsed by said user. The data will be used to improve future responses. Recent interactions are also stored locally and can be seen offline. | R1, R2, R3, R4, R5, R6, R8, RS5, RS14, RA2, RD1, RD4 |
| **UC-6: Modify view and preferences** | The student dashboard can be personalized by the student. Language and notification preferences can also be changed to the student’s liking. | R4, R5, RS3, RS4, RS6, RS9, RS12 |
| **UC-7: View academic summaries and data** | A lecturer may request aggregated statistics about their courses. The system can collect and display various summaries of academic data and timelines from the school’s database for the user. | R1, R3, R8, RS3, RL3, RL6 |
| **UC-8: View system summaries** | An admin can see system usage and request system analytic reports. System maintainers can view and log system health, and various other system-related factors. | R1, R3, R8, RS10, RS11, RA4, RM2, RM4 |
| **UC-9: Publish or modify course material** | Lecturers can publish or update course materials accessible to students using the AI assistant. Updates will be made to the entire school’s LMS. | R1, R2, R3, R4, R5, R6, R8, RL1, RD1, RD4 |
| **UC-10: Export calendar** | A student can export their personal academic calendar to an external calendar by asking the AI assistant. Calendar information is taken from the school’s LMS. | R1, R2, R3, R4, R5, R6, RS3, RS13, RD1, RD4 |
| **UC-11: Manage TAs** | Lecturers can manage the access rights for any TAs that are in their course by prompting the AI assistant. The LMS will be updated accordingly. | R1, R2, R3, R4, R5, R6, RL5 |
| **UC-12: Manage institutional integrations** | The admin manages institutional integrations. Admins can edit, add or remove any integration from the system. | R3, R7, RA1, RD1 |
| **UC-13: Configure system** | An admin changes or defines new system policies (data retention, response, logging). Any changes made will be applied globally. | R3, RA2, RD1, RD4 |
| **UC-14: Manage AI model** | A system maintainer can integrate new AI services and insert or withdraw external data sources. They can also deploy updates to the system. | R3, R7, RM1, RM5, RD1 |
| **UC-15: Configure AI model** | System maintainers can configure AI model versions and API keys. | R5, R7, RM3, RD1 |
| **UC-16: Create and use backup** | A system maintainer can create backup states of the system for emergencies. They, alongside admins, can rollback the system state to an earlier version. Backups are stored in the school’s database. | R3, R7, RA6, RM6, RD1 |
| **UC-17: Detect Fault** | Periodically the system will ping the school’s data source systems to see if safe exchange of data can still occur. If there is no response, the system reverts to a safe state and users are updated on the situation. | R3, RS11, RA6, RD1, RD3, RD4 |


![Image of use case diagram](images/Use_Case_Diagrams_Project(f).png)

# Quality Attributes

| ID    | Quality Attribute | Scenario | Associated Use Cases |
|-------|-----------------|---------|--------------------|
| QA-1  | Performance     | A student queries academic information through the AI assistant. The system processes requests within 2 seconds on average under normal load. | UC-2 |
| QA-2  | Availability    | The system remains accessible and operational 99.5% of the time each month. Maintenance or deployment operations do not interrupt ongoing user sessions. | All |
| QA-3  | Security        | A user logs into the system through the institutional single sign-on (SSO). The system verifies authentication and ensures that user-specific information remains accessible only to the authenticated user. | UC-3 |
| QA-4  | Maintainability | A system maintainer performs an update or rollback from backup, and the system continues operating normally, allowing changes to be applied and tested without affecting user sessions. | UC-6 |
| QA-5  | Usability       | A user interacts with the assistant via text or voice, and the system interprets the input correctly, providing clear, contextually appropriate responses that allow the task to be completed efficiently. | UC-1, UC-2 |

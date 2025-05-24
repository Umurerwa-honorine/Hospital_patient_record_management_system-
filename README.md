# Hospital_patient_record_management_system-
A Hospital Patient Record Management System is a database used to efficiently store and manage patient information, including medical history and treatments. It enhances accuracy, data security, and operational efficiency while reducing paperwork.

#🏥 Problem Statement
Modern hospitals generate vast amounts of patient data daily — from admissions and diagnoses to treatments and discharges. Without a centralized system, managing this data can lead to inefficiencies, errors, data loss, and delays in patient care.
The Hospital Patient Record Management System aims to solve these challenges by providing a robust, secure, and efficient database-driven solution to:
Store and manage patient records, including personal details, medical history, and appointments.
Track doctor and department assignments.
Handle admissions, room allocations, and discharges.
Improve data accessibility, reduce paperwork, and enhance overall operational efficiency.
This project leverages PL/SQL and Oracle SQL Developer to implement stored procedures, triggers, sequences, and views, ensuring data integrity and automating hospital workflows.

#🛠️ Methodology
The development of the Hospital Patient Record Management System followed a structured and modular approach to ensure data integrity, scalability, and ease of use. The system was built using Oracle SQL and PL/SQL with the following methodology:

1. Requirement Analysis
Identified core hospital processes: patient registration, doctor assignment, room management, appointment scheduling, and discharge handling.
Outlined necessary entities: Patient, Doctor, Department, Room, Appointment, Admission, Discharge.

2. Database Design
Created an Entity-Relationship Diagram (ERD) to visualize relationships.
Defined tables with appropriate primary and foreign keys.
Ensured normalization to eliminate redundancy.

3. Implementation
Tables & Sequences: Designed and created all required tables with constraints and sequences for auto-incrementing IDs.

Procedures: Implemented modular PL/SQL procedures for:

Admitting patients
Discharging patients
Scheduling appointments

Views: Created views for simplified querying and reporting (e.g., PatientLatestAppointment).
Triggers (if used): Added logic to automate certain actions, like updating room occupancy.

4. Testing
Ran various scenarios using EXEC and SELECT queries to validate:
Correct data insertion
Sequence functionality
Logical accuracy of procedures
Referential integrity

5. Deployment
Connected through SQL Developer using a custom user schema.
Verified all components (procedures, views, data) function correctly.




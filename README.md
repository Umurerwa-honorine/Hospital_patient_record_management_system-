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


Connected to:
Oracle Database 21c Enterprise Edition Release 21.0.0.0.0 - Production
Version 21.3.0.0.0

SQL> CREATE TABLE patients (
  2    patient_id NUMBER PRIMARY KEY,
  3    name VARCHAR2(100),
  4    age NUMBER,
  5    gender VARCHAR2(10),
  6    contact VARCHAR2(20)
  7  );

Table created.

SQL> CREATE TABLE doctors (
  2    doctor_id NUMBER PRIMARY KEY,
  3    name VARCHAR2(100),
  4    specialty VARCHAR2(50),
  5    phone VARCHAR2(20)
  6  );

Table created.

SQL> CREATE TABLE appointments (
  2    appointment_id NUMBER PRIMARY KEY,
  3    patient_id NUMBER REFERENCES patients(patient_id),
  4    doctor_id NUMBER REFERENCES doctors(doctor_id),
  5    appointment_date DATE,
  6    reason VARCHAR2(200)
  7  );

Table created.

SQL> CREATE TABLE treatments (
  2    treatment_id NUMBER PRIMARY KEY,
  3    appointment_id NUMBER REFERENCES appointments(appointment_id),
  4    diagnosis VARCHAR2(200),
  5    prescription VARCHAR2(200),
  6    notes VARCHAR2(300)
  7  );

Table created.

SQL> CREATE TABLE bills (
  2    bill_id NUMBER PRIMARY KEY,
  3    patient_id NUMBER REFERENCES patients(patient_id),
  4    treatment_id NUMBER REFERENCES treatments(treatment_id),
  5    amount NUMBER(10, 2),
  6    payment_status VARCHAR2(20)
  7  );

Table created.

SQL> CREATE SEQUENCE patient_seq START WITH 1 INCREMENT BY 1;

Sequence created.

SQL> CREATE SEQUENCE doctor_seq START WITH 1 INCREMENT BY 1;

Sequence created.

SQL> CREATE SEQUENCE appointment_seq START WITH 1 INCREMENT BY 1;

Sequence created.

SQL> CREATE SEQUENCE treatment_seq START WITH 1 INCREMENT BY 1;

Sequence created.

SQL> CREATE SEQUENCE bill_seq START WITH 1 INCREMENT BY 1;

Sequence created.

SQL> ALTER TABLE patients ADD CONSTRAINT gender_check CHECK (gender IN ('Male', 'Female', 'Other'));

Table altered.

SQL> ALTER TABLE bills ADD CONSTRAINT payment_status_check CHECK (payment_status IN ('Paid', 'Unpaid', 'Pending'));

Table altered.

SQL> CREATE OR REPLACE PACKAGE patient_pkg AS
  2    PROCEDURE add_patient(p_name VARCHAR2, p_age NUMBER, p_gender VARCHAR2, p_contact VARCHAR2);
  3    PROCEDURE update_patient(p_id NUMBER, p_name VARCHAR2, p_age NUMBER, p_gender VARCHAR2, p_contact VARCHAR2);
  4    PROCEDURE delete_patient(p_id NUMBER);
  5    FUNCTION get_patient_name(p_id NUMBER) RETURN VARCHAR2;
  6  END patient_pkg;
  7  /

Package created.

SQL> CREATE OR REPLACE PACKAGE doctor_pkg AS
  2    PROCEDURE add_doctor(p_name VARCHAR2, p_specialty VARCHAR2, p_phone VARCHAR2);
  3    PROCEDURE update_doctor(p_id NUMBER, p_name VARCHAR2, p_specialty VARCHAR2, p_phone VARCHAR2);
  4    PROCEDURE delete_doctor(p_id NUMBER);
  5    FUNCTION get_doctor_specialty(p_id NUMBER) RETURN VARCHAR2;
  6  END doctor_pkg;
  7  /

Package created.

SQL> CREATE OR REPLACE PACKAGE appointment_pkg AS
  2    PROCEDURE schedule_appointment(p_patient_id NUMBER, p_doctor_id NUMBER, p_date DATE, p_reason VARCHAR2);
  3    PROCEDURE cancel_appointment(p_id NUMBER);
  4    FUNCTION get_appointment_reason(p_id NUMBER) RETURN VARCHAR2;
  5  END appointment_pkg;
  6  /

Package created.

SQL> CREATE OR REPLACE PACKAGE treatment_pkg AS
  2    PROCEDURE add_treatment(p_appointment_id NUMBER, p_diagnosis VARCHAR2, p_prescription VARCHAR2, p_notes VARCHAR2);
  3    FUNCTION get_treatment_notes(p_treatment_id NUMBER) RETURN VARCHAR2;
  4  END treatment_pkg;
  5  /

Package created.

SQL> CREATE OR REPLACE PACKAGE billing_pkg AS
  2    PROCEDURE create_bill(p_patient_id NUMBER, p_treatment_id NUMBER, p_amount NUMBER, p_status VARCHAR2);
  3    PROCEDURE update_payment_status(p_bill_id NUMBER, p_status VARCHAR2);
  4    FUNCTION get_bill_amount(p_bill_id NUMBER) RETURN NUMBER;
  5  END billing_pkg;
  6  /

Package created.

SQL> CREATE OR REPLACE PACKAGE BODY patient_pkg AS
  2
  3    PROCEDURE add_patient(p_name VARCHAR2, p_age NUMBER, p_gender VARCHAR2, p_contact VARCHAR2) IS
  4    BEGIN
  5      INSERT INTO patients(patient_id, name, age, gender, contact)
  6      VALUES (patient_seq.NEXTVAL, p_name, p_age, p_gender, p_contact);
  7    END add_patient;
  8
  9    PROCEDURE update_patient(p_id NUMBER, p_name VARCHAR2, p_age NUMBER, p_gender VARCHAR2, p_contact VARCHAR2) IS
 10    BEGIN
 11      UPDATE patients
 12      SET name = p_name,
 13          age = p_age,
 14          gender = p_gender,
 15          contact = p_contact
 16      WHERE patient_id = p_id;
 17    END update_patient;
 18
 19    PROCEDURE delete_patient(p_id NUMBER) IS
 20    BEGIN
 21      DELETE FROM patients WHERE patient_id = p_id;
 22    END delete_patient;
 23
 24    FUNCTION get_patient_name(p_id NUMBER) RETURN VARCHAR2 IS
 25      v_name VARCHAR2(100);
 26    BEGIN
 27      SELECT name INTO v_name FROM patients WHERE patient_id = p_id;
 28      RETURN v_name;
 29    EXCEPTION
 30      WHEN NO_DATA_FOUND THEN
 31        RETURN 'No such patient';
 32    END get_patient_name;
 33
 34  END patient_pkg;
 35  /

Package body created.

SQL> CREATE OR REPLACE PACKAGE BODY doctor_pkg AS
  2
  3    PROCEDURE add_doctor(p_name VARCHAR2, p_specialty VARCHAR2, p_phone VARCHAR2) IS
  4    BEGIN
  5      INSERT INTO doctors(doctor_id, name, specialty, phone)
  6      VALUES (doctor_seq.NEXTVAL, p_name, p_specialty, p_phone);
  7    END add_doctor;
  8
  9    PROCEDURE update_doctor(p_id NUMBER, p_name VARCHAR2, p_specialty VARCHAR2, p_phone VARCHAR2) IS
 10    BEGIN
 11      UPDATE doctors
 12      SET name = p_name,
 13          specialty = p_specialty,
 14          phone = p_phone
 15      WHERE doctor_id = p_id;
 16    END update_doctor;
 17
 18    PROCEDURE delete_doctor(p_id NUMBER) IS
 19    BEGIN
 20      DELETE FROM doctors WHERE doctor_id = p_id;
 21    END delete_doctor;
 22
 23    FUNCTION get_doctor_specialty(p_id NUMBER) RETURN VARCHAR2 IS
 24      v_specialty VARCHAR2(50);
 25    BEGIN
 26      SELECT specialty INTO v_specialty FROM doctors WHERE doctor_id = p_id;
 27      RETURN v_specialty;
 28    EXCEPTION
 29      WHEN NO_DATA_FOUND THEN
 30        RETURN 'No such doctor';
 31    END get_doctor_specialty;
 32
 33  END doctor_pkg;
 34  /

Package body created.

SQL> CREATE OR REPLACE PACKAGE BODY appointment_pkg AS
  2
  3    PROCEDURE schedule_appointment(p_patient_id NUMBER, p_doctor_id NUMBER, p_date DATE, p_reason VARCHAR2) IS
  4    BEGIN
  5      INSERT INTO appointments(appointment_id, patient_id, doctor_id, appointment_date, reason)
  6      VALUES (appointment_seq.NEXTVAL, p_patient_id, p_doctor_id, p_date, p_reason);
  7    END schedule_appointment;
  8
  9    PROCEDURE cancel_appointment(p_id NUMBER) IS
 10    BEGIN
 11      DELETE FROM appointments WHERE appointment_id = p_id;
 12    END cancel_appointment;
 13
 14    FUNCTION get_appointment_reason(p_id NUMBER) RETURN VARCHAR2 IS
 15      v_reason VARCHAR2(200);
 16    BEGIN
 17      SELECT reason INTO v_reason FROM appointments WHERE appointment_id = p_id;
 18      RETURN v_reason;
 19    EXCEPTION
 20      WHEN NO_DATA_FOUND THEN
 21        RETURN 'No such appointment';
 22    END get_appointment_reason;
 23
 24  END appointment_pkg;
 25  /

Package body created.

SQL> CREATE OR REPLACE PACKAGE BODY treatment_pkg AS
  2
  3    PROCEDURE add_treatment(p_appointment_id NUMBER, p_diagnosis VARCHAR2, p_prescription VARCHAR2, p_notes VARCHAR2) IS
  4    BEGIN
  5      INSERT INTO treatments(treatment_id, appointment_id, diagnosis, prescription, notes)
  6      VALUES (treatment_seq.NEXTVAL, p_appointment_id, p_diagnosis, p_prescription, p_notes);
  7    END add_treatment;
  8
  9    FUNCTION get_treatment_notes(p_treatment_id NUMBER) RETURN VARCHAR2 IS
 10      v_notes VARCHAR2(300);
 11    BEGIN
 12      SELECT notes INTO v_notes FROM treatments WHERE treatment_id = p_treatment_id;
 13      RETURN v_notes;
 14    EXCEPTION
 15      WHEN NO_DATA_FOUND THEN
 16        RETURN 'No such treatment';
 17    END get_treatment_notes;
 18
 19  END treatment_pkg;
 20  /

Package body created.

SQL> CREATE OR REPLACE PACKAGE BODY billing_pkg AS
  2
  3    PROCEDURE create_bill(p_patient_id NUMBER, p_treatment_id NUMBER, p_amount NUMBER, p_status VARCHAR2) IS
  4    BEGIN
  5      INSERT INTO bills(bill_id, patient_id, treatment_id, amount, payment_status)
  6      VALUES (bill_seq.NEXTVAL, p_patient_id, p_treatment_id, p_amount, p_status);
  7    END create_bill;
  8
  9    PROCEDURE update_payment_status(p_bill_id NUMBER, p_status VARCHAR2) IS
 10    BEGIN
 11      UPDATE bills SET payment_status = p_status WHERE bill_id = p_bill_id;
 12    END update_payment_status;
 13
 14    FUNCTION get_bill_amount(p_bill_id NUMBER) RETURN NUMBER IS
 15      v_amount NUMBER(10, 2);
 16    BEGIN
 17      SELECT amount INTO v_amount FROM bills WHERE bill_id = p_bill_id;
 18      RETURN v_amount;
 19    EXCEPTION
 20      WHEN NO_DATA_FOUND THEN
 21        RETURN 0;
 22    END get_bill_amount;
 23
 24  END billing_pkg;
 25  /

Package body created.

SQL> CREATE OR REPLACE TRIGGER before_insert_patient_trg
  2  BEFORE INSERT ON patients
  3  FOR EACH ROW
  4  BEGIN
  5    :NEW.created_at := SYSDATE;
  6  END;
  7  /

Warning: Trigger created with compilation errors.

SQL> SHOW ERRORS
Errors for TRIGGER BEFORE_INSERT_PATIENT_TRG:

LINE/COL ERROR
-------- -----------------------------------------------------------------
2/3      PLS-00049: bad bind variable 'NEW.CREATED_AT'
SQL> DESC patients;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 PATIENT_ID                                NOT NULL NUMBER
 NAME                                               VARCHAR2(100)
 AGE                                                NUMBER
 GENDER                                             VARCHAR2(10)
 CONTACT                                            VARCHAR2(20)

SQL> ALTER TABLE patients ADD created_at DATE;

Table altered.

SQL> DESC patients;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 PATIENT_ID                                NOT NULL NUMBER
 NAME                                               VARCHAR2(100)
 AGE                                                NUMBER
 GENDER                                             VARCHAR2(10)
 CONTACT                                            VARCHAR2(20)
 CREATED_AT                                         DATE

SQL> CREATE OR REPLACE TRIGGER before_insert_patient_trg
  2  BEFORE INSERT ON patients
  3  FOR EACH ROW
  4  BEGIN
  5    :NEW.created_at := SYSDATE;
  6  END;
  7  /

Trigger created.

SQL> CREATE TABLE audit_patients (
  2    audit_id     NUMBER GENERATED ALWAYS AS IDENTITY,
  3    patient_id   NUMBER,
  4    old_name     VARCHAR2(100),
  5    new_name     VARCHAR2(100),
  6    change_date  DATE
  7  );

Table created.

SQL> CREATE OR REPLACE TRIGGER audit_patient_changes_trg
  2  AFTER UPDATE ON patients
  3  FOR EACH ROW
  4  BEGIN
  5    INSERT INTO audit_patients(patient_id, old_name, new_name, change_date)
  6    VALUES (:OLD.patient_id, :OLD.name, :NEW.name, SYSDATE);
  7  END;
  8  /

Trigger created.

SQL> CREATE OR REPLACE TRIGGER validate_appointment_date_trg
  2  BEFORE INSERT OR UPDATE ON appointments
  3  FOR EACH ROW
  4  BEGIN
  5    IF :NEW.appointment_date < SYSDATE THEN
  6      RAISE_APPLICATION_ERROR(-20001, 'Appointment date must be in the future.');
  7    END IF;
  8  END;
  9  /

Trigger created.

SQL> CREATE OR REPLACE TRIGGER delete_doctor_cascade_trg
  2  BEFORE DELETE ON doctors
  3  FOR EACH ROW
  4  DECLARE
  5    v_count NUMBER;
  6  BEGIN
  7    SELECT COUNT(*) INTO v_count FROM appointments WHERE doctor_id = :OLD.doctor_id;
  8    IF v_count > 0 THEN
  9      RAISE_APPLICATION_ERROR(-20002, 'Doctor has scheduled appointments and cannot be deleted.');
 10    END IF;
 11  END;
 12  /

Trigger created.

SQL> CREATE OR REPLACE PROCEDURE list_appointments_by_doctor(p_doctor_id NUMBER) IS
  2    CURSOR appt_cursor IS
  3      SELECT a.appointment_id, p.name AS patient_name, a.appointment_date
  4      FROM appointments a
  5      JOIN patients p ON a.patient_id = p.patient_id
  6      WHERE a.doctor_id = p_doctor_id;
  7
  8    v_appt_id        appointments.appointment_id%TYPE;
  9    v_patient_name   patients.name%TYPE;
 10    v_appt_date      appointments.appointment_date%TYPE;
 11  BEGIN
 12    OPEN appt_cursor;
 13    LOOP
 14      FETCH appt_cursor INTO v_appt_id, v_patient_name, v_appt_date;
 15      EXIT WHEN appt_cursor%NOTFOUND;
 16
 17      DBMS_OUTPUT.PUT_LINE('Appointment ID: ' || v_appt_id || ', Patient: ' || v_patient_name || ', Date: ' || v_appt_date);
 18    END LOOP;
 19    CLOSE appt_cursor;
 20  EXCEPTION
 21    WHEN OTHERS THEN
 22      DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
 23  END;
 24  /

Procedure created.

SQL> CREATE OR REPLACE PROCEDURE get_patient_details(p_patient_id NUMBER) IS
  2    v_name    patients.name%TYPE;
  3    v_age     patients.age%TYPE;
  4    v_gender  patients.gender%TYPE;
  5  BEGIN
  6    SELECT name, age, gender INTO v_name, v_age, v_gender
  7    FROM patients
  8    WHERE patient_id = p_patient_id;
  9
 10    DBMS_OUTPUT.PUT_LINE('Name: ' || v_name || ', Age: ' || v_age || ', Gender: ' || v_gender);
 11
 12  EXCEPTION
 13    WHEN NO_DATA_FOUND THEN
 14      DBMS_OUTPUT.PUT_LINE('No patient found with ID ' || p_patient_id);
 15    WHEN TOO_MANY_ROWS THEN
 16      DBMS_OUTPUT.PUT_LINE('Multiple patients found with the same ID!');
 17    WHEN OTHERS THEN
 18      DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
 19  END;
 20  /

Procedure created.

SQL> CREATE OR REPLACE PROCEDURE summarize_bills IS
  2    CURSOR bill_cursor IS
  3      SELECT patient_id, SUM(amount) AS total_amount
  4      FROM bills
  5      GROUP BY patient_id;
  6
  7    v_patient_id   bills.patient_id%TYPE;
  8    v_total        NUMBER(10, 2);
  9  BEGIN
 10    OPEN bill_cursor;
 11    LOOP
 12      FETCH bill_cursor INTO v_patient_id, v_total;
 13      EXIT WHEN bill_cursor%NOTFOUND;
 14
 15      DBMS_OUTPUT.PUT_LINE('Patient ID: ' || v_patient_id || ' | Total Bill: ' || v_total);
 16    END LOOP;
 17    CLOSE bill_cursor;
 18  END;
 19  /

Procedure created.

SQL> CREATE OR REPLACE VIEW v_patient_summary AS
  2  SELECT
  3    p.patient_id,
  4    p.name AS patient_name,
  5    p.gender,
  6    p.age,
  7    MAX(a.appointment_date) AS last_appointment
  8  FROM patients p
  9  LEFT JOIN appointments a ON p.patient_id = a.patient_id
 10  GROUP BY p.patient_id, p.name, p.gender, p.age;

View created.

SQL> CREATE OR REPLACE VIEW v_doctor_schedule AS
  2  SELECT
  3    d.doctor_id,
  4    d.name AS doctor_name,
  5    d.specialty,
  6    COUNT(a.appointment_id) AS total_appointments
  7  FROM doctors d
  8  LEFT JOIN appointments a ON d.doctor_id = a.doctor_id
  9  GROUP BY d.doctor_id, d.name, d.specialty;

View created.

SQL> CREATE OR REPLACE VIEW v_pending_bills AS
  2  SELECT
  3    b.bill_id,
  4    p.name AS patient_name,
  5    t.diagnosis,
  6    b.amount,
  7    b.payment_status
  8  FROM bills b
  9  JOIN patients p ON b.patient_id = p.patient_id
 10  JOIN treatments t ON b.treatment_id = t.treatment_id
 11  WHERE b.payment_status = 'Unpaid';

View created.

SQL> CREATE OR REPLACE PROCEDURE report_busy_doctors IS
  2  BEGIN
  3    FOR rec IN (
  4      SELECT doctor_id, name, COUNT(*) AS appointment_count
  5      FROM doctors d
  6      JOIN appointments a ON d.doctor_id = a.doctor_id
  7      GROUP BY doctor_id, name
  8      HAVING COUNT(*) > 3
  9    )
 10    LOOP
 11      DBMS_OUTPUT.PUT_LINE('Doctor ID: ' || rec.doctor_id ||
 12                           ' | Name: ' || rec.name ||
 13                           ' | Appointments: ' || rec.appointment_count);
 14    END LOOP;
 15  END;
 16  /

Warning: Procedure created with compilation errors.

SQL> CREATE OR REPLACE PROCEDURE report_busy_doctors IS
  2  BEGIN
  3    FOR rec IN (
  4      SELECT d.doctor_id, d.name, COUNT(*) AS appointment_count
  5      FROM doctors d
  6      JOIN appointments a ON d.doctor_id = a.doctor_id
  7      GROUP BY d.doctor_id, d.name
  8      HAVING COUNT(*) > 3
  9    )
 10    LOOP
 11      DBMS_OUTPUT.PUT_LINE('Doctor ID: ' || rec.doctor_id ||
 12                           ' | Name: ' || rec.name ||
 13                           ' | Appointments: ' || rec.appointment_count);
 14    END LOOP;
 15  END;
 16  /

Procedure created.

SQL> CREATE OR REPLACE FUNCTION get_total_bill(p_patient_id NUMBER)
  2  RETURN NUMBER IS
  3    v_total NUMBER := 0;
  4  BEGIN
  5    SELECT SUM(amount)
  6    INTO v_total
  7    FROM bills
  8    WHERE patient_id = p_patient_id;
  9
 10    RETURN NVL(v_total, 0);
 11  EXCEPTION
 12    WHEN NO_DATA_FOUND THEN
 13      RETURN 0;
 14  END;
 15  /

Function created.

SQL> CREATE OR REPLACE FUNCTION get_doctor_info(p_doctor_id NUMBER)
  2  RETURN VARCHAR2 IS
  3    v_info VARCHAR2(200);
  4  BEGIN
  5    SELECT name || ' (' || specialty || ')'
  6    INTO v_info
  7    FROM doctors
  8    WHERE doctor_id = p_doctor_id;
  9
 10    RETURN v_info;
 11  EXCEPTION
 12    WHEN NO_DATA_FOUND THEN
 13      RETURN 'Doctor not found';
 14  END;
 15  /

Function created.

SQL> CREATE OR REPLACE FUNCTION get_age_category(p_age NUMBER)
  2  RETURN VARCHAR2 IS
  3  BEGIN
  4    IF p_age < 13 THEN
  5      RETURN 'Child';
  6    ELSIF p_age BETWEEN 13 AND 19 THEN
  7      RETURN 'Teenager';
  8    ELSIF p_age BETWEEN 20 AND 59 THEN
  9      RETURN 'Adult';
 10    ELSE
 11      RETURN 'Senior';
 12    END IF;
 13  END;
 14  /

Function created.

SQL> CREATE OR REPLACE FUNCTION patient_exists(p_id NUMBER)
  2  RETURN BOOLEAN IS
  3    v_count NUMBER;
  4  BEGIN
  5    SELECT COUNT(*) INTO v_count
  6    FROM patients
  7    WHERE patient_id = p_id;
  8
  9    RETURN v_count > 0;
 10  END;
 11  /

Function created.

SQL> CREATE OR REPLACE FUNCTION get_unpaid_bill(p_patient_id NUMBER)
  2  RETURN NUMBER IS
  3    v_amount NUMBER := 0;
  4  BEGIN
  5    SELECT SUM(amount)
  6    INTO v_amount
  7    FROM bills
  8    WHERE patient_id = p_patient_id AND payment_status = 'Unpaid';
  9
 10    RETURN NVL(v_amount, 0);
 11  EXCEPTION
 12    WHEN NO_DATA_FOUND THEN
 13      RETURN 0;
 14  END;
 15  /

Function created.

SQL> CREATE OR REPLACE FUNCTION days_since_last_appointment(p_patient_id NUMBER)
  2  RETURN NUMBER IS
  3    v_last_date DATE;
  4  BEGIN
  5    SELECT MAX(appointment_date)
  6    INTO v_last_date
  7    FROM appointments
  8    WHERE patient_id = p_patient_id;
  9
 10    RETURN TRUNC(SYSDATE - v_last_date);
 11  EXCEPTION
 12    WHEN NO_DATA_FOUND THEN
 13      RETURN NULL;
 14  END;
 15  /

Function created.

SQL> BEGIN
  2    report_busy_doctors;
  3  END;
  4  /

PL/SQL procedure successfully completed.

SQL> SELECT * FROM v_patient_summary;

no rows selected

SQL> SELECT view_name FROM user_views WHERE view_name = 'V_PATIENT_SUMMARY';

VIEW_NAME
--------------------------------------------------------------------------------
V_PATIENT_SUMMARY

SQL> SELECT * FROM v_patient_summary;

no rows selected

SQL> CREATE OR REPLACE VIEW v_patient_summary AS
  2  SELECT
  3    p.patient_id,
  4    p.name,
  5    p.gender,
  6    NVL(SUM(b.amount), 0) AS total_bill
  7  FROM
  8    patients p
  9  LEFT JOIN
 10    bills b ON p.patient_id = b.patient_id
 11  GROUP BY
 12    p.patient_id, p.name, p.gender;

View created.

SQL>  SELECT * FROM v_patient_summary;

no rows selected

SQL> SELECT * FROM patients;

no rows selected

SQL> -- Insert sample patients
SQL> INSERT INTO patients (patient_id, name, gender, age) VALUES (101, 'Jane Doe', 'F', 28);
INSERT INTO patients (patient_id, name, gender, age) VALUES (101, 'Jane Doe', 'F', 28)
*
ERROR at line 1:
ORA-02290: check constraint (SYSTEM.GENDER_CHECK) violated


SQL> INSERT INTO patients (patient_id, name, gender, age) VALUES (102, 'John Smith', 'M', 45);
INSERT INTO patients (patient_id, name, gender, age) VALUES (102, 'John Smith', 'M', 45)
*
ERROR at line 1:
ORA-02290: check constraint (SYSTEM.GENDER_CHECK) violated


SQL>
SQL> -- Insert sample bills for patients
SQL> INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1001, 101, 450, 'Paid');
INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1001, 101, 450, 'Paid')
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008322) violated - parent key not
found


SQL> INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1002, 101, 100, 'Unpaid');
INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1002, 101, 100, 'Unpaid')
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008322) violated - parent key not
found


SQL> INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1003, 102, 300, 'Paid');
INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1003, 102, 300, 'Paid')
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008322) violated - parent key not
found


SQL>
SQL> COMMIT;

Commit complete.

SQL> DESC patients;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 PATIENT_ID                                NOT NULL NUMBER
 NAME                                               VARCHAR2(100)
 AGE                                                NUMBER
 GENDER                                             VARCHAR2(10)
 CONTACT                                            VARCHAR2(20)
 CREATED_AT                                         DATE

SQL> DESC bills;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 BILL_ID                                   NOT NULL NUMBER
 PATIENT_ID                                         NUMBER
 TREATMENT_ID                                       NUMBER
 AMOUNT                                             NUMBER(10,2)
 PAYMENT_STATUS                                     VARCHAR2(20)

SQL> INSERT INTO patients (patient_id, name, gender, age) VALUES (101, 'Jane Doe', 'F', 28);
INSERT INTO patients (patient_id, name, gender, age) VALUES (101, 'Jane Doe', 'F', 28)
*
ERROR at line 1:
ORA-02290: check constraint (SYSTEM.GENDER_CHECK) violated


SQL> -- Add more columns if needed, e.g. address
SQL>
SQL> INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1001, 101, 450, 'Paid');
INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1001, 101, 450, 'Paid')
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008322) violated - parent key not
found


SQL> INSERT INTO patients (patient_id, name, gender, age) VALUES (101, 'Jane Doe', 'F', 28);
INSERT INTO patients (patient_id, name, gender, age) VALUES (101, 'Jane Doe', 'F', 28)
*
ERROR at line 1:
ORA-02290: check constraint (SYSTEM.GENDER_CHECK) violated


SQL> INSERT INTO patients (patient_id, name, gender, age) VALUES (102, 'John Smith', 'M', 45);
INSERT INTO patients (patient_id, name, gender, age) VALUES (102, 'John Smith', 'M', 45)
*
ERROR at line 1:
ORA-02290: check constraint (SYSTEM.GENDER_CHECK) violated


SQL> COMMIT;

Commit complete.

SQL> INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1001, 101, 450, 'Paid');
INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1001, 101, 450, 'Paid')
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008322) violated - parent key not
found


SQL> INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1002, 101, 100, 'Unpaid');
INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1002, 101, 100, 'Unpaid')
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008322) violated - parent key not
found


SQL> INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1003, 102, 300, 'Paid');
INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (1003, 102, 300, 'Paid')
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008322) violated - parent key not
found


SQL> COMMIT;

Commit complete.

SQL> DESC patients;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 PATIENT_ID                                NOT NULL NUMBER
 NAME                                               VARCHAR2(100)
 AGE                                                NUMBER
 GENDER                                             VARCHAR2(10)
 CONTACT                                            VARCHAR2(20)
 CREATED_AT                                         DATE

SQL> DESC doctors;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 DOCTOR_ID                                 NOT NULL NUMBER
 NAME                                               VARCHAR2(100)
 SPECIALTY                                          VARCHAR2(50)
 PHONE                                              VARCHAR2(20)

SQL> DESC appointments;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 APPOINTMENT_ID                            NOT NULL NUMBER
 PATIENT_ID                                         NUMBER
 DOCTOR_ID                                          NUMBER
 APPOINTMENT_DATE                                   DATE
 REASON                                             VARCHAR2(200)

SQL> DESC bills;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 BILL_ID                                   NOT NULL NUMBER
 PATIENT_ID                                         NUMBER
 TREATMENT_ID                                       NUMBER
 AMOUNT                                             NUMBER(10,2)
 PAYMENT_STATUS                                     VARCHAR2(20)

SQL> -- Sample data insert script
SQL>
SQL> INSERT INTO patients (patient_id, name, gender, age) VALUES (101, 'Jane Doe', 'F', 28);
INSERT INTO patients (patient_id, name, gender, age) VALUES (101, 'Jane Doe', 'F', 28)
*
ERROR at line 1:
ORA-02290: check constraint (SYSTEM.GENDER_CHECK) violated


SQL> INSERT INTO patients (patient_id, name, gender, age) VALUES (102, 'John Smith', 'M', 45);
INSERT INTO patients (patient_id, name, gender, age) VALUES (102, 'John Smith', 'M', 45)
*
ERROR at line 1:
ORA-02290: check constraint (SYSTEM.GENDER_CHECK) violated


SQL>
SQL> INSERT INTO doctors (doctor_id, name, specialty) VALUES (201, 'Dr. Alice', 'Cardiology');

1 row created.

SQL> INSERT INTO doctors (doctor_id, name, specialty) VALUES (202, 'Dr. Bob', 'Neurology');

1 row created.

SQL>
SQL> INSERT INTO appointments (appointment_id, patient_id, doctor_id, appointment_date)
  2  VALUES (3001, 101, 201, TO_DATE('2025-05-20', 'YYYY-MM-DD'));
INSERT INTO appointments (appointment_id, patient_id, doctor_id, appointment_date)
            *
ERROR at line 1:
ORA-20001: Appointment date must be in the future.
ORA-06512: at "SYSTEM.VALIDATE_APPOINTMENT_DATE_TRG", line 3
ORA-04088: error during execution of trigger
'SYSTEM.VALIDATE_APPOINTMENT_DATE_TRG'


SQL> INSERT INTO appointments (appointment_id, patient_id, doctor_id, appointment_date)
  2  VALUES (3002, 102, 202, TO_DATE('2025-05-21', 'YYYY-MM-DD'));
INSERT INTO appointments (appointment_id, patient_id, doctor_id, appointment_date)
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008317) violated - parent key not
found


SQL>
SQL> INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (4001, 101, 450, 'Paid');
INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (4001, 101, 450, 'Paid')
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008322) violated - parent key not
found


SQL> INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (4002, 102, 300, 'Unpaid');
INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (4002, 102, 300, 'Unpaid')
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008322) violated - parent key not
found


SQL>
SQL> COMMIT;

Commit complete.

SQL> -- Insert sample patients first
SQL> INSERT INTO patients (patient_id, name, gender, age) VALUES (101, 'Jane Doe', 'F', 28);
INSERT INTO patients (patient_id, name, gender, age) VALUES (101, 'Jane Doe', 'F', 28)
*
ERROR at line 1:
ORA-02290: check constraint (SYSTEM.GENDER_CHECK) violated


SQL> INSERT INTO patients (patient_id, name, gender, age) VALUES (102, 'John Smith', 'M', 45);
INSERT INTO patients (patient_id, name, gender, age) VALUES (102, 'John Smith', 'M', 45)
*
ERROR at line 1:
ORA-02290: check constraint (SYSTEM.GENDER_CHECK) violated


SQL>
SQL> -- Insert sample doctors
SQL> INSERT INTO doctors (doctor_id, name, specialty) VALUES (201, 'Dr. Alice', 'Cardiology');
INSERT INTO doctors (doctor_id, name, specialty) VALUES (201, 'Dr. Alice', 'Cardiology')
*
ERROR at line 1:
ORA-00001: unique constraint (SYSTEM.SYS_C008315) violated


SQL> INSERT INTO doctors (doctor_id, name, specialty) VALUES (202, 'Dr. Bob', 'Neurology');
INSERT INTO doctors (doctor_id, name, specialty) VALUES (202, 'Dr. Bob', 'Neurology')
*
ERROR at line 1:
ORA-00001: unique constraint (SYSTEM.SYS_C008315) violated


SQL>
SQL> -- Insert sample appointments (reference patients and doctors)
SQL> INSERT INTO appointments (appointment_id, patient_id, doctor_id, appointment_date)
  2  VALUES (3001, 101, 201, TO_DATE('2025-05-20', 'YYYY-MM-DD'));
INSERT INTO appointments (appointment_id, patient_id, doctor_id, appointment_date)
            *
ERROR at line 1:
ORA-20001: Appointment date must be in the future.
ORA-06512: at "SYSTEM.VALIDATE_APPOINTMENT_DATE_TRG", line 3
ORA-04088: error during execution of trigger
'SYSTEM.VALIDATE_APPOINTMENT_DATE_TRG'


SQL> INSERT INTO appointments (appointment_id, patient_id, doctor_id, appointment_date)
  2  VALUES (3002, 102, 202, TO_DATE('2025-05-21', 'YYYY-MM-DD'));
INSERT INTO appointments (appointment_id, patient_id, doctor_id, appointment_date)
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008317) violated - parent key not
found


SQL>
SQL> -- Insert sample bills (reference patients)
SQL> INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (4001, 101, 450, 'Paid');
INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (4001, 101, 450, 'Paid')
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008322) violated - parent key not
found


SQL> INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (4002, 102, 300, 'Unpaid');
INSERT INTO bills (bill_id, patient_id, amount, payment_status) VALUES (4002, 102, 300, 'Unpaid')
*
ERROR at line 1:
ORA-02291: integrity constraint (SYSTEM.SYS_C008322) violated - parent key not
found


SQL>
SQL> COMMIT;

Commit complete.

Microsoft Windows [Version 10.0.19045.5854]
(c) Microsoft Corporation. All rights reserved.

C:\Users\umure>sqlplus WED_27476_HONORINE/Aurorelouange123@localhost:1521/ORCLPDB

SQL*Plus: Release 21.0.0.0.0 - Production on Thu May 22 09:57:32 2025
Version 21.3.0.0.0

Copyright (c) 1982, 2021, Oracle.  All rights reserved.

Last Successful login time: Thu May 22 2025 09:51:17 +02:00

Connected to:
Oracle Database 21c Enterprise Edition Release 21.0.0.0.0 - Production
Version 21.3.0.0.0

SQL> CREATE SEQUENCE PatientID_seq START WITH 1 INCREMENT BY 1;

Sequence created.

SQL> CREATE OR REPLACE VIEW PatientLatestAppointment AS
  2  SELECT
  3      p.PatientID,
  4      p.FirstName || ' ' || p.LastName AS PatientName,
  5      MAX(a.AppointmentDate) AS LatestAppointmentDate,
  6      d.FirstName || ' ' || d.LastName AS DoctorName
  7  FROM Patient p
  8  JOIN Appointment a ON p.PatientID = a.PatientID
  9  JOIN Doctor d ON a.DoctorID = d.DoctorID
 10  GROUP BY p.PatientID, p.FirstName, p.LastName, d.FirstName, d.LastName;

View created.

SQL> CREATE OR REPLACE TRIGGER trg_check_appointment_date
  2  BEFORE INSERT ON Appointment
  3  FOR EACH ROW
  4  BEGIN
  5      IF :NEW.AppointmentDate < SYSDATE THEN
  6          RAISE_APPLICATION_ERROR(-20001, 'Appointment date cannot be in the past.');
  7      END IF;
  8  END;
  9  /

Trigger created.

SQL> CREATE OR REPLACE PROCEDURE AdmitPatient(
  2      p_PatientID IN NUMBER,
  3      p_RoomID IN NUMBER,
  4      p_AdmissionDate IN DATE
  5  ) AS
  6  BEGIN
  7      INSERT INTO Admission(PatientID, RoomID, AdmissionDate)
  8      VALUES (p_PatientID, p_RoomID, p_AdmissionDate);
  9
 10      UPDATE Room SET IsOccupied = 'Y' WHERE RoomID = p_RoomID;
 11
 12      COMMIT;
 13  END;
 14  /

Warning: Procedure created with compilation errors.

SQL> SHOW ERRORS PROCEDURE AdmitPatient;
Errors for PROCEDURE ADMITPATIENT:

LINE/COL ERROR
-------- -----------------------------------------------------------------
10/5     PL/SQL: SQL Statement ignored
10/21    PL/SQL: ORA-00904: "ISOCCUPIED": invalid identifier
SQL> DESC Room;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 ROOMID                                    NOT NULL NUMBER
 ROOMNUMBER                                         VARCHAR2(10)
 ROOMTYPE                                           VARCHAR2(20)
 DEPARTMENTID                                       NUMBER

SQL> UPDATE Room SET IS_OCCUPIED = 'Y' WHERE RoomID = p_RoomID;
UPDATE Room SET IS_OCCUPIED = 'Y' WHERE RoomID = p_RoomID
                                                 *
ERROR at line 1:
ORA-00904: "P_ROOMID": invalid identifier


SQL> UPDATE Room SET IS_OCCUPIED = 'Y' WHERE RoomID = 101;
UPDATE Room SET IS_OCCUPIED = 'Y' WHERE RoomID = 101
                *
ERROR at line 1:
ORA-00904: "IS_OCCUPIED": invalid identifier


SQL> DESC Room;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 ROOMID                                    NOT NULL NUMBER
 ROOMNUMBER                                         VARCHAR2(10)
 ROOMTYPE                                           VARCHAR2(20)
 DEPARTMENTID                                       NUMBER

SQL> SELECT column_name FROM user_tab_columns WHERE table_name = 'ROOM';

COLUMN_NAME
--------------------------------------------------------------------------------
ROOMID
ROOMNUMBER
ROOMTYPE
DEPARTMENTID

SQL> ALTER TABLE Room ADD IS_OCCUPIED CHAR(1) DEFAULT 'N' CHECK (IS_OCCUPIED IN ('Y','N'));

Table altered.

SQL> UPDATE Room SET IS_OCCUPIED = 'Y' WHERE RoomID = p_RoomID;
UPDATE Room SET IS_OCCUPIED = 'Y' WHERE RoomID = p_RoomID
                                                 *
ERROR at line 1:
ORA-00904: "P_ROOMID": invalid identifier


SQL> UPDATE Room SET IS_OCCUPIED = 'Y' WHERE RoomID = 101;

1 row updated.

SQL> ALTER TABLE Room ADD IS_OCCUPIED CHAR(1) DEFAULT 'N' CHECK (IS_OCCUPIED IN ('Y','N'));
ALTER TABLE Room ADD IS_OCCUPIED CHAR(1) DEFAULT 'N' CHECK (IS_OCCUPIED IN ('Y','N'))
                     *
ERROR at line 1:
ORA-01430: column being added already exists in table


SQL> CREATE OR REPLACE PROCEDURE AdmitPatient(
  2      p_PatientID IN NUMBER,
  3      p_RoomID IN NUMBER,
  4      p_AdmissionDate IN DATE
  5  ) AS
  6  BEGIN
  7      INSERT INTO Admission(PatientID, RoomID, AdmissionDate)
  8      VALUES (p_PatientID, p_RoomID, p_AdmissionDate);
  9
 10      UPDATE Room SET IS_OCCUPIED = 'Y' WHERE RoomID = p_RoomID;
 11
 12      COMMIT;
 13  END;
 14  /

Procedure created.

SQL> BEGIN
  2      AdmitPatient(1, 101, SYSDATE);
  3  END;
  4  /
BEGIN
*
ERROR at line 1:
ORA-01400: cannot insert NULL into
("WED_27476_HONORINE"."ADMISSION"."ADMISSIONID")
ORA-06512: at "WED_27476_HONORINE.ADMITPATIENT", line 7
ORA-06512: at line 2


SQL> CREATE OR REPLACE FUNCTION GetPatientAge(p_PatientID IN NUMBER) RETURN NUMBER IS
  2      v_BirthDate DATE;
  3      v_Age NUMBER;
  4  BEGIN
  5      SELECT BirthDate INTO v_BirthDate FROM Patient WHERE PatientID = p_PatientID;
  6      v_Age := TRUNC(MONTHS_BETWEEN(SYSDATE, v_BirthDate)/12);
  7      RETURN v_Age;
  8  EXCEPTION
  9      WHEN NO_DATA_FOUND THEN
 10          RETURN NULL;
 11  END;
 12  /

Warning: Function created with compilation errors.

SQL> SHOW ERRORS FUNCTION GetPatientAge;
Errors for FUNCTION GETPATIENTAGE:

LINE/COL ERROR
-------- -----------------------------------------------------------------
5/5      PL/SQL: SQL Statement ignored
5/12     PL/SQL: ORA-00904: "BIRTHDATE": invalid identifier
SQL> SELECT column_name FROM user_tab_columns WHERE table_name = 'PATIENT';

COLUMN_NAME
--------------------------------------------------------------------------------
PATIENTID
FIRSTNAME
LASTNAME
GENDER
DATEOFBIRTH
CONTACTNUMBER
ADDRESS

7 rows selected.

SQL> CREATE OR REPLACE FUNCTION GetPatientAge(p_PatientID IN NUMBER) RETURN NUMBER IS
  2      v_BirthDate DATE;
  3      v_Age NUMBER;
  4  BEGIN
  5      SELECT DATEOFBIRTH INTO v_BirthDate FROM Patient WHERE PatientID = p_PatientID;
  6      v_Age := TRUNC(MONTHS_BETWEEN(SYSDATE, v_BirthDate) / 12);
  7      RETURN v_Age;
  8  EXCEPTION
  9      WHEN NO_DATA_FOUND THEN
 10          RETURN NULL;
 11  END;
 12  /

Function created.

SQL> SELECT GetPatientAge(1) AS PatientAge FROM dual;

PATIENTAGE
----------
        25

SQL> CREATE OR REPLACE TRIGGER trg_update_room_occupancy
  2  AFTER INSERT ON Admission
  3  FOR EACH ROW
  4  BEGIN
  5    UPDATE Room
  6    SET DepartmentID = :NEW.RoomID,  -- Assuming RoomID = DepartmentID here, adjust if needed
  7        -- No occupancy column, so maybe add one if needed
  8        -- Otherwise, you can track occupancy elsewhere
  9        NULL
 10    WHERE RoomID = :NEW.RoomID;
 11  END;
 12  /

Warning: Trigger created with compilation errors.

SQL> ALTER TABLE Room ADD (IS_OCCUPIED CHAR(1) DEFAULT 'N');
ALTER TABLE Room ADD (IS_OCCUPIED CHAR(1) DEFAULT 'N')
                      *
ERROR at line 1:
ORA-01430: column being added already exists in table


SQL> CREATE OR REPLACE TRIGGER trg_update_room_occupancy
  2  AFTER INSERT ON Admission
  3  FOR EACH ROW
  4  BEGIN
  5    UPDATE Room
  6    SET IS_OCCUPIED = 'Y'
  7    WHERE RoomID = :NEW.RoomID;
  8  END;
  9  /

Trigger created.

SQL> SHOW ERRORS TRIGGER trg_update_room_occupancy;
No errors.
SQL> CREATE OR REPLACE VIEW DoctorAppointmentCount AS
  2  SELECT
  3      d.DoctorID,
  4      d.FirstName || ' ' || d.LastName AS DoctorName,
  5      COUNT(a.AppointmentID) AS TotalAppointments
  6  FROM Doctor d
  7  LEFT JOIN Appointment a ON d.DoctorID = a.DoctorID
  8  GROUP BY d.DoctorID, d.FirstName, d.LastName;

View created.

SQL> CREATE OR REPLACE PROCEDURE ScheduleAppointment(
  2      p_PatientID IN NUMBER,
  3      p_DoctorID IN NUMBER,
  4      p_AppointmentDate IN DATE
  5  ) AS
  6  BEGIN
  7      INSERT INTO Appointment (PatientID, DoctorID, AppointmentDate)
  8      VALUES (p_PatientID, p_DoctorID, p_AppointmentDate);
  9
 10      COMMIT;
 11  END;
 12  /

Procedure created.

SQL> SELECT p.PatientID, p.FirstName || ' ' || p.LastName AS PatientName
  2  FROM Patient p
  3  LEFT JOIN Appointment a ON p.PatientID = a.PatientID
  4  WHERE a.AppointmentID IS NULL;

 PATIENTID
----------
PATIENTNAME
--------------------------------------------------------------------------------
         1
Alice Mugisha

         2
Jean Dusabe


SQL> ALTER TABLE Admission ADD DischargeDate DATE;
ALTER TABLE Admission ADD DischargeDate DATE
                          *
ERROR at line 1:
ORA-01430: column being added already exists in table


SQL> CREATE OR REPLACE PROCEDURE DischargePatient(
  2      p_PatientID IN NUMBER,
  3      p_DischargeDate IN DATE
  4  ) AS
  5      v_RoomID Admission.RoomID%TYPE;
  6  BEGIN
  7      -- Get the room assigned to the patient
  8      SELECT RoomID INTO v_RoomID
  9      FROM Admission
 10      WHERE PatientID = p_PatientID AND DischargeDate IS NULL
 11      FETCH FIRST 1 ROWS ONLY;
 12
 13      -- Update admission with discharge date
 14      UPDATE Admission
 15      SET DischargeDate = p_DischargeDate
 16      WHERE PatientID = p_PatientID AND RoomID = v_RoomID AND DischargeDate IS NULL;
 17
 18      -- Simulate freeing up the room (if Is_Occupied column exists)
 19      BEGIN
 20          UPDATE Room
 21          SET Is_Occupied = 'N'
 22          WHERE RoomID = v_RoomID;
 23      EXCEPTION
 24          WHEN OTHERS THEN
 25              NULL; -- Skip if the column doesn't exist
 26      END;
 27
 28      COMMIT;
 29  EXCEPTION
 30      WHEN NO_DATA_FOUND THEN
 31          DBMS_OUTPUT.PUT_LINE('No active admission found for this patient.');
 32  END;
 33  /

Procedure created.

SQL> ALTER TABLE Room ADD Is_Occupied CHAR(1) DEFAULT 'N';
ALTER TABLE Room ADD Is_Occupied CHAR(1) DEFAULT 'N'
                     *
ERROR at line 1:
ORA-01430: column being added already exists in table


SQL> EXEC DischargePatient(1, SYSDATE);

PL/SQL procedure successfully completed.

SQL> CREATE TABLE Admission_Audit (
  2      AuditID NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  3      PatientID NUMBER,
  4      RoomID NUMBER,
  5      ActionType VARCHAR2(20), -- 'ADMIT' or 'DISCHARGE'
  6      ActionDate DATE DEFAULT SYSDATE
  7  );

Table created.

SQL> CREATE OR REPLACE TRIGGER trg_log_admission
  2  AFTER INSERT ON Admission
  3  FOR EACH ROW
  4  BEGIN
  5      INSERT INTO Admission_Audit (PatientID, RoomID, ActionType)
  6      VALUES (:NEW.PatientID, :NEW.RoomID, 'ADMIT');
  7  END;
  8  /

Trigger created.

SQL> CREATE OR REPLACE TRIGGER trg_log_discharge
  2  AFTER UPDATE OF DischargeDate ON Admission
  3  FOR EACH ROW
  4  WHEN (NEW.DischargeDate IS NOT NULL AND OLD.DischargeDate IS NULL)
  5  BEGIN
  6      INSERT INTO Admission_Audit (PatientID, RoomID, ActionType)
  7      VALUES (:NEW.PatientID, :NEW.RoomID, 'DISCHARGE');
  8  END;
  9  /

Trigger created.

SQL> EXEC AdmitPatient(1, 101, SYSDATE);
BEGIN AdmitPatient(1, 101, SYSDATE); END;

*
ERROR at line 1:
ORA-01400: cannot insert NULL into
("WED_27476_HONORINE"."ADMISSION"."ADMISSIONID")
ORA-06512: at "WED_27476_HONORINE.ADMITPATIENT", line 7
ORA-06512: at line 1


SQL> CREATE SEQUENCE seq_admission_id
  2  START WITH 1
  3  INCREMENT BY 1
  4  NOCACHE;

Sequence created.

SQL> CREATE OR REPLACE PROCEDURE AdmitPatient(
  2      p_PatientID IN NUMBER,
  3      p_RoomID IN NUMBER,
  4      p_AdmissionDate IN DATE
  5  ) AS
  6  BEGIN
  7      INSERT INTO Admission(AdmissionID, PatientID, RoomID, AdmissionDate)
  8      VALUES (seq_admission_id.NEXTVAL, p_PatientID, p_RoomID, p_AdmissionDate);
  9
 10      COMMIT;
 11  END;
 12  /

Procedure created.

SQL> CREATE OR REPLACE VIEW PatientLatestAppointment ASSELECT    p.PatientID,    p.FirstName || ' ' || p.LastName AS PatientName,    MAX(a.AppointmentDate) AS LatestAppointmentDate,    d.FirstName || ' ' || d.LastName AS DoctorNameFROM Patient pJOIN Appointment a ON p.PatientID = a.PatientIDJOIN Doctor d ON a.DoctorID = d.DoctorIDGROUP BY p.PatientID, p.FirstName, p.LastName, d.FirstName, d.LastName;
CREATE OR REPLACE VIEW PatientLatestAppointment ASSELECT    p.PatientID,    p.FirstName || ' ' || p.LastName AS PatientName,    MAX(a.AppointmentDate) AS LatestAppointmentDate,    d.FirstName || ' ' || d.LastName AS DoctorNameFROM Patient pJOIN Appointment a ON p.PatientID = a.PatientIDJOIN Doctor d ON a.DoctorID = d.DoctorIDGROUP BY p.PatientID, p.FirstName, p.LastName, d.FirstName, d.LastName
                                                *
ERROR at line 1:
ORA-00905: missing keyword


SQL> CREATE OR REPLACE VIEW PatientLatestAppointment AS
  2  SELECT
  3      p.PatientID,
  4      p.FirstName || ' ' || p.LastName AS PatientName,
  5      MAX(a.AppointmentDate) AS LatestAppointmentDate,
  6      d.FirstName || ' ' || d.LastName AS DoctorName
  7  FROM Patient p
  8  JOIN Appointment a ON p.PatientID = a.PatientID
  9  JOIN Doctor d ON a.DoctorID = d.DoctorID
 10  GROUP BY p.PatientID, p.FirstName, p.LastName, d.FirstName, d.LastName;

View created.

SQL> CREATE OR REPLACE VIEW PatientLatestAppointment AS
  2  SELECT
  3      p.PatientID,
  4      p.FirstName || ' ' || p.LastName AS PatientName,
  5      MAX(a.AppointmentDate) AS LatestAppointmentDate,
  6      d.FirstName || ' ' || d.LastName AS DoctorName
  7  FROM Patient p
  8  JOIN Appointment a ON p.PatientID = a.PatientID
  9  JOIN Doctor d ON a.DoctorID = d.DoctorID
 10  GROUP BY p.PatientID, p.FirstName, p.LastName, d.FirstName, d.LastName;

View created.

SQL> CREATE OR REPLACE VIEW PatientLatestAppointment AS
  2  SELECT
  3      p.PatientID,
  4      p.FirstName || ' ' || p.LastName AS PatientName,
  5      MAX(a.AppointmentDate) AS LatestAppointmentDate,
  6      d.FirstName || ' ' || d.LastName AS DoctorName
  7  FROM Patient p
  8  JOIN Appointment a ON p.PatientID = a.PatientID
  9  JOIN Doctor d ON a.DoctorID = d.DoctorID
 10  GROUP BY p.PatientID, p.FirstName, p.LastName;
    d.FirstName || ' ' || d.LastName AS DoctorName
    *
ERROR at line 6:
ORA-00979: not a GROUP BY expression


SQL> CREATE OR REPLACE VIEW PatientLatestAppointment AS
  2  SELECT
  3      PatientID,
  4      PatientName,
  5      AppointmentDate AS LatestAppointmentDate,
  6      DoctorName
  7  FROM (
  8      SELECT
  9          p.PatientID,
 10          p.FirstName || ' ' || p.LastName AS PatientName,
 11          a.AppointmentDate,
 12          d.FirstName || ' ' || d.LastName AS DoctorName,
 13          ROW_NUMBER() OVER (PARTITION BY p.PatientID ORDER BY a.AppointmentDate DESC) AS rn
 14      FROM Patient p
 15      JOIN Appointment a ON p.PatientID = a.PatientID
 16      JOIN Doctor d ON a.DoctorID = d.DoctorID
 17  )
 18  WHERE rn = 1;

View created.

SQL> CREATE OR REPLACE PROCEDURE AdmitPatient(
  2      p_PatientID IN NUMBER,
  3      p_RoomID IN NUMBER,
  4      p_AdmissionDate IN DATE
  5  ) AS
  6  BEGIN
  7      INSERT INTO Admission(AdmissionID, PatientID, RoomID, AdmissionDate)
  8      VALUES (ADMISSION_SEQ.NEXTVAL, p_PatientID, p_RoomID, p_AdmissionDate);
  9
 10      -- If you want to track room occupancy, add a column IS_OCCUPIED (Y/N) in Room table or handle differently
 11      -- Example (if column exists):
 12      -- UPDATE Room SET IS_OCCUPIED = 'Y' WHERE RoomID = p_RoomID;
 13
 14      COMMIT;
 15  END;
 16  /

Warning: Procedure created with compilation errors.

SQL> SHOW ERRORS PROCEDURE AdmitPatient;
Errors for PROCEDURE ADMITPATIENT:

LINE/COL ERROR
-------- -----------------------------------------------------------------
7/5      PL/SQL: SQL Statement ignored
8/13     PL/SQL: ORA-02289: sequence does not exist
SQL> CREATE SEQUENCE ADMISSION_SEQ
  2    START WITH 1
  3    INCREMENT BY 1
  4    NOCACHE
  5    NOCYCLE;

Sequence created.

SQL> CREATE OR REPLACE PROCEDURE AdmitPatient(
  2      p_PatientID IN NUMBER,
  3      p_RoomID IN NUMBER,
  4      p_AdmissionDate IN DATE
  5  ) AS
  6  BEGIN
  7      INSERT INTO Admission(AdmissionID, PatientID, RoomID, AdmissionDate)
  8      VALUES (ADMISSION_SEQ.NEXTVAL, p_PatientID, p_RoomID, p_AdmissionDate);
  9
 10      -- If you want to track room occupancy, add a column IS_OCCUPIED (Y/N) in Room table or handle differently
 11      -- Example (if column exists):
 12      -- UPDATE Room SET IS_OCCUPIED = 'Y' WHERE RoomID = p_RoomID;
 13
 14      COMMIT;
 15  END;
 16  /

Procedure created.

SQL> CREATE OR REPLACE FUNCTION GetPatientAge(p_PatientID IN NUMBER) RETURN NUMBER IS
  2      v_DateOfBirth DATE;
  3      v_Age NUMBER;
  4  BEGIN
  5      SELECT DateOfBirth INTO v_DateOfBirth FROM Patient WHERE PatientID = p_PatientID;
  6      v_Age := TRUNC(MONTHS_BETWEEN(SYSDATE, v_DateOfBirth) / 12);
  7      RETURN v_Age;
  8  EXCEPTION
  9      WHEN NO_DATA_FOUND THEN
 10          RETURN NULL;
 11  END;
 12  /

Function created.

SQL> CREATE OR REPLACE TRIGGER trg_admission_before_insert
  2  BEFORE INSERT ON Admission
  3  FOR EACH ROW
  4  BEGIN
  5      IF :NEW.AdmissionID IS NULL THEN
  6          SELECT ADMISSION_SEQ.NEXTVAL INTO :NEW.AdmissionID FROM dual;
  7      END IF;
  8
  9      -- Optional: update room occupancy if you have that column
 10      -- UPDATE Room SET IS_OCCUPIED = 'Y' WHERE RoomID = :NEW.RoomID;
 11  END;
 12  /

Trigger created.

SQL> SELECT
  2      d.DoctorID,
  3      d.FirstName || ' ' || d.LastName AS DoctorName,
  4      COUNT(a.AppointmentID) AS NumberOfAppointments
  5  FROM Doctor d
  6  LEFT JOIN Appointment a ON d.DoctorID = a.DoctorID
  7  GROUP BY d.DoctorID, d.FirstName, d.LastName;

  DOCTORID
----------
DOCTORNAME
--------------------------------------------------------------------------------
NUMBEROFAPPOINTMENTS
--------------------
         1
Dr. John Ngabo
                   1

         2
Dr. Aline Uwera
                   1

  DOCTORID
----------
DOCTORNAME
--------------------------------------------------------------------------------
NUMBEROFAPPOINTMENTS
--------------------


SQL> SELECT
  2      p.PatientID,
  3      p.FirstName || ' ' || p.LastName AS PatientName
  4  FROM Patient p
  5  LEFT JOIN Appointment a ON p.PatientID = a.PatientID
  6  WHERE a.AppointmentID IS NULL;

 PATIENTID
----------
PATIENTNAME
--------------------------------------------------------------------------------
         1
Alice Mugisha

         2
Jean Dusabe


SQL> CREATE OR REPLACE PROCEDURE DischargePatient(
  2    p_AdmissionID IN NUMBER,
  3    p_DischargeDate IN DATE
  4  ) AS
  5  BEGIN
  6    UPDATE Admission
  7    SET DischargeDate = p_DischargeDate
  8    WHERE AdmissionID = p_AdmissionID;
  9
 10    -- Update room availability if you track it
 11
 12    COMMIT;
 13  END;
 14  /

Procedure created.

SQL> CREATE OR REPLACE TRIGGER trg_update_room_occupancy
  2  AFTER INSERT ON Admission
  3  FOR EACH ROW
  4  BEGIN
  5    -- Example if Room has IS_OCCUPIED column
  6    UPDATE Room
  7    SET IS_OCCUPIED = 'Y'
  8    WHERE RoomID = :NEW.RoomID;
  9  END;
 10  /

Trigger created.

SQL> EXEC AdmitPatient(1, 101, SYSDATE);
BEGIN AdmitPatient(1, 101, SYSDATE); END;

*
ERROR at line 1:
ORA-00001: unique constraint (WED_27476_HONORINE.SYS_C008228) violated
ORA-06512: at "WED_27476_HONORINE.ADMITPATIENT", line 7
ORA-06512: at line 1


SQL> SELECT MAX(AdmissionID) FROM Admission;

MAX(ADMISSIONID)
----------------
               2

SQL> DROP SEQUENCE admission_seq;

Sequence dropped.

SQL>
SQL> CREATE SEQUENCE admission_seq
  2  START WITH 6
  3  INCREMENT BY 1
  4  NOCACHE;

Sequence created.

SQL> EXEC AdmitPatient(1, 101, SYSDATE);

PL/SQL procedure successfully completed.

SQL> CREATE OR REPLACE TRIGGER trg_update_room_occupancy
  2  AFTER INSERT ON Admission
  3  FOR EACH ROW
  4  BEGIN
  5      UPDATE Room
  6      SET IS_OCCUPIED = 'Y'
  7      WHERE RoomID = :NEW.RoomID;
  8  END;
  9  /

Trigger created.

SQL> CREATE OR REPLACE VIEW PatientsWithoutAppointments AS
  2  SELECT p.PatientID, p.FirstName, p.LastName
  3  FROM Patient p
  4  WHERE NOT EXISTS (
  5      SELECT 1 FROM Appointment a
  6      WHERE a.PatientID = p.PatientID
  7  );

View created.

SQL> CREATE OR REPLACE VIEW DoctorWorkload AS
  2  SELECT d.DoctorID, d.FirstName || ' ' || d.LastName AS DoctorName,
  3         COUNT(a.AppointmentID) AS TotalAppointments
  4  FROM Doctor d
  5  LEFT JOIN Appointment a ON d.DoctorID = a.DoctorID
  6  GROUP BY d.DoctorID, d.FirstName, d.LastName;

View created.

SQL> INSERT INTO Department (DepartmentID, DepartmentName) VALUES (1, 'Cardiology');
INSERT INTO Department (DepartmentID, DepartmentName) VALUES (1, 'Cardiology')
*
ERROR at line 1:
ORA-00001: unique constraint (WED_27476_HONORINE.SYS_C008222) violated


SQL> INSERT INTO Department (DepartmentID, DepartmentName) VALUES (2, 'Neurology');
INSERT INTO Department (DepartmentID, DepartmentName) VALUES (2, 'Neurology')
*
ERROR at line 1:
ORA-00001: unique constraint (WED_27476_HONORINE.SYS_C008222) violated


SQL> COMMIT;

Commit complete.

SQL> SELECT * FROM Department;

DEPARTMENTID
------------
DEPARTMENTNAME
--------------------------------------------------------------------------------
LOCATION
--------------------------------------------------------------------------------
           1
Cardiology
Building A

           2
Neurology
Building B

DEPARTMENTID
------------
DEPARTMENTNAME
--------------------------------------------------------------------------------
LOCATION
--------------------------------------------------------------------------------

           3
Pediatrics
Building C


SQL> SELECT * FROM Department;

DEPARTMENTID
------------
DEPARTMENTNAME
--------------------------------------------------------------------------------
LOCATION
--------------------------------------------------------------------------------
           1
Cardiology
Building A

           2
Neurology
Building B

DEPARTMENTID
------------
DEPARTMENTNAME
--------------------------------------------------------------------------------
LOCATION
--------------------------------------------------------------------------------

           3
Pediatrics
Building C


SQL> INSERT INTO Department (DepartmentID, DepartmentName, Location)
  2  VALUES (4, 'Oncology', 'Building D');

1 row created.

SQL>
SQL> INSERT INTO Department (DepartmentID, DepartmentName, Location)
  2  VALUES (5, 'Orthopedics', 'Building E');

1 row created.

SQL>
SQL> COMMIT;

Commit complete.

SQL> INSERT INTO Doctor (DoctorID, FirstName, LastName, DepartmentID)
  2  VALUES (1, 'John', 'Doe', 1);
INSERT INTO Doctor (DoctorID, FirstName, LastName, DepartmentID)
                                                   *
ERROR at line 1:
ORA-00904: "DEPARTMENTID": invalid identifier


SQL> INSERT INTO Doctor (DoctorID, FirstName, LastName, DepartmentID)
  2  VALUES (2, 'Jane', 'Smith', 2);
INSERT INTO Doctor (DoctorID, FirstName, LastName, DepartmentID)
                                                   *
ERROR at line 1:
ORA-00904: "DEPARTMENTID": invalid identifier


SQL> COMMIT;

Commit complete.

SQL> DESC Doctor;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 DOCTORID                                  NOT NULL NUMBER
 FIRSTNAME                                          VARCHAR2(50)
 LASTNAME                                           VARCHAR2(50)
 SPECIALTY                                          VARCHAR2(50)
 CONTACTNUMBER                                      VARCHAR2(15)

SQL> DESC Doctor;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 DOCTORID                                  NOT NULL NUMBER
 FIRSTNAME                                          VARCHAR2(50)
 LASTNAME                                           VARCHAR2(50)
 SPECIALTY                                          VARCHAR2(50)
 CONTACTNUMBER                                      VARCHAR2(15)














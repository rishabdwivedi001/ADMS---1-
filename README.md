-- Clean slate
DROP TABLE IF EXISTS FeePayments;

-- Create FeePayments table
CREATE TABLE FeePayments (
    payment_id   INT PRIMARY KEY,
    student_name VARCHAR(100) NOT NULL,
    amount       DECIMAL(10,2) NOT NULL,
    payment_date DATE NOT NULL,
    CHECK (amount > 0)
);

------------------------------------------------------
-- Part A: Insert multiple payments (Atomicity demo)
------------------------------------------------------
START TRANSACTION;

INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (1, 'Ashish', 5000.00, '2024-06-01');

INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (2, 'Smaran', 4500.00, '2024-06-02');

INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (3, 'Vaibhav', 5500.00, '2024-06-03');

COMMIT;

-- Check result after Part A
SELECT * FROM FeePayments;

------------------------------------------------------
-- Part B: Rollback after failure (duplicate ID + negative)
------------------------------------------------------
START TRANSACTION;

-- Valid insert
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (4, 'Kiran', 4000.00, '2024-06-04');

-- Invalid insert: duplicate ID and negative amount
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (1, 'Ashish', -3000.00, '2024-06-05');

-- Rollback entire transaction
ROLLBACK;

-- Check result after Part B
SELECT * FROM FeePayments;

------------------------------------------------------
-- Part C: Partial failure (NULL student_name)
------------------------------------------------------
START TRANSACTION;

-- Valid record
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (5, 'Ravi', 4800.00, '2024-06-06');

-- Invalid record (NULL not allowed)
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (6, NULL, 5200.00, '2024-06-07');

ROLLBACK;

-- Check result after Part C
SELECT * FROM FeePayments;

------------------------------------------------------
-- Part D: ACID compliance (all properties together)
------------------------------------------------------
START TRANSACTION;

-- Valid insert
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (7, 'Meena', 6000.00, '2024-06-08');

-- Invalid insert (duplicate ID)
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (2, 'Rahul', 4500.00, '2024-06-09');

ROLLBACK;

-- Final state of table (only committed valid records remain)
SELECT * FROM FeePayments;

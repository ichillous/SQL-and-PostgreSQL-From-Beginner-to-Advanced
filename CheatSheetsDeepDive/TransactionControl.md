# MySQL Transaction Control Cheatsheet

## Overview

Transaction Control in MySQL allows you to manage database transactions, ensuring data integrity and consistency. Key concepts and commands include:

- START TRANSACTION / BEGIN
- COMMIT
- ROLLBACK
- SAVEPOINT
- Transaction Isolation Levels

## Basic Transaction Control

### START TRANSACTION / BEGIN

These commands are used to start a new transaction.

```sql
START TRANSACTION;
-- or
BEGIN;
```

### COMMIT

COMMIT is used to save changes made in a transaction.

```sql
COMMIT;
```

### ROLLBACK

ROLLBACK is used to undo changes made in a transaction.

```sql
ROLLBACK;
```

## Examples

### Simple Transaction

```sql
-- Start a new transaction
START TRANSACTION;

-- Perform operations
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

-- If everything is okay, commit the transaction
COMMIT;

-- If there's an issue, rollback the transaction
-- ROLLBACK;
```

### Intermediate: Using TRY-CATCH with Transactions

```sql
START TRANSACTION;

BEGIN TRY
    -- Perform operations
    INSERT INTO orders (customer_id, total_amount) VALUES (1, 500);
    UPDATE inventory SET stock = stock - 5 WHERE product_id = 101;
    
    -- If everything is okay, commit the transaction
    COMMIT;
END TRY
BEGIN CATCH
    -- If there's an error, rollback the transaction
    ROLLBACK;
    
    -- Optionally, re-throw the error
    SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'An error occurred during the transaction';
END CATCH;
```

### Advanced: Using SAVEPOINT

SAVEPOINTs allow you to create points within a transaction that you can roll back to.

```sql
START TRANSACTION;

INSERT INTO customers (name, email) VALUES ('John Doe', 'john@example.com');
SAVEPOINT customer_added;

INSERT INTO orders (customer_id, total_amount) VALUES (LAST_INSERT_ID(), 100);
SAVEPOINT order_added;

-- Oops, we made a mistake with the order. Let's roll back to after we added the customer
ROLLBACK TO SAVEPOINT customer_added;

-- Add the correct order
INSERT INTO orders (customer_id, total_amount) VALUES (LAST_INSERT_ID(), 200);

COMMIT;
```

### Expert: Handling Deadlocks

```sql
DELIMITER //

CREATE PROCEDURE transfer_funds(
    IN from_account INT,
    IN to_account INT,
    IN amount DECIMAL(10, 2)
)
BEGIN
    DECLARE exit handler for sqlexception
    BEGIN
        ROLLBACK;
        RESIGNAL;
    END;

    DECLARE exit handler for 1213 -- Error code for deadlock
    BEGIN
        -- Wait for a bit and retry
        DO SLEEP(0.01);
        CALL transfer_funds(from_account, to_account, amount);
    END;

    START TRANSACTION;

    -- Lock rows in a consistent order to prevent deadlocks
    SELECT @dummy := 0 FROM accounts 
    WHERE id IN (from_account, to_account) 
    ORDER BY id FOR UPDATE;

    UPDATE accounts SET balance = balance - amount WHERE id = from_account;
    UPDATE accounts SET balance = balance + amount WHERE id = to_account;

    COMMIT;
END //

DELIMITER ;

-- Usage
CALL transfer_funds(1, 2, 100.00);
```

## Transaction Isolation Levels

MySQL supports four transaction isolation levels:

1. READ UNCOMMITTED
2. READ COMMITTED
3. REPEATABLE READ (default in InnoDB)
4. SERIALIZABLE

### Setting Isolation Level

```sql
-- Set for the next transaction
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- Set for all future transactions in the session
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- Set globally (affects new connections)
SET GLOBAL TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

### Example: Demonstrating Isolation Levels

```sql
-- Terminal 1
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
START TRANSACTION;
SELECT * FROM accounts WHERE id = 1;

-- Terminal 2
START TRANSACTION;
UPDATE accounts SET balance = balance + 100 WHERE id = 1;
-- Don't commit yet

-- Terminal 1 (will see the uncommitted change in READ UNCOMMITTED)
SELECT * FROM accounts WHERE id = 1;

-- Terminal 2
ROLLBACK;

-- Terminal 1 (will see the original value after rollback)
SELECT * FROM accounts WHERE id = 1;
COMMIT;
```

## Best Practices

1. Keep transactions as short as possible to reduce lock contention.
2. Always use transactions for operations that must be atomic.
3. Be cautious with long-running transactions, as they can impact database performance.
4. Choose the appropriate isolation level based on your application's needs.
5. Handle deadlocks in your application code, possibly with retry logic.
6. Use savepoints for complex transactions where partial rollbacks might be necessary.
7. Ensure all possible code paths in a transaction end with either a COMMIT or ROLLBACK.
8. Be aware of how your ORM or database abstraction layer handles transactions.
9. Regularly review and optimize your transaction-heavy code.
10. Monitor transaction-related metrics in your database for performance tuning.

Remember, while transactions are crucial for data integrity, they also come with performance overhead. Always test thoroughly in an environment similar to production to ensure your transactional code performs well under load.

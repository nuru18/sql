Before INSERT trigger - для проверки корректности вводимых данных.
sql
Copy code
CREATE TRIGGER trg_BeforeInsert_Employees
ON Employees
BEFORE INSERT
AS
BEGIN
    IF EXISTS (SELECT * FROM inserted WHERE Salary < 0)
    BEGIN
        RAISERROR('Salary cannot be negative.', 16, 1)
        ROLLBACK TRANSACTION
    END
END
Этот триггер будет вызван перед вставкой новых строк в таблицу "Employees". Если вставляемая строка содержит отрицательное значение зарплаты, триггер вернет ошибку и откатит транзакцию.

After UPDATE trigger - для записи изменений в лог.
sql
Copy code
CREATE TRIGGER trg_AfterUpdate_Employees
ON Employees
AFTER UPDATE
AS
BEGIN
    INSERT INTO EmployeesLog (EmployeeId, OldSalary, NewSalary, UpdateDate)
    SELECT Id, Salary AS OldSalary, i.Salary AS NewSalary, GETDATE()
    FROM deleted d
    INNER JOIN inserted i ON d.Id = i.Id
END
Этот триггер будет вызван после обновления строк в таблице "Employees". Он запишет изменения в лог "EmployeesLog", включая идентификатор сотрудника, старую зарплату, новую зарплату и дату обновления.

Instead of DELETE trigger - для запрета удаления строк.
sql
Copy code
CREATE TRIGGER trg_InsteadOfDelete_Employees
ON Employees
INSTEAD OF DELETE
AS
BEGIN
    RAISERROR('Deleting employees is not allowed.', 16, 1)
END
Этот триггер будет вызван вместо удаления строк из таблицы "Employees". Он вернет ошибку, запрещающую удаление строк.

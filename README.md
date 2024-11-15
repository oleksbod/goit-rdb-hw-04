# goit-rdb-hw-04

1. Створіть базу даних для керування бібліотекою книг згідно зі структурою, наведеною нижче. 
Використовуйте DDL-команди для створення необхідних таблиць та їх зв'язків.
 
Структура БД

a) Назва схеми — “LibraryManagement”

b) Таблиця "authors":
- author_id (INT, автоматично зростаючий PRIMARY KEY)
- author_name (VARCHAR)
	
c) Таблиця "genres":
- genre_id (INT, автоматично зростаючий PRIMARY KEY)
- genre_name (VARCHAR)
	
d) Таблиця "books":
- book_id (INT, автоматично зростаючий PRIMARY KEY)
- title (VARCHAR)
- publication_year (YEAR)
- author_id (INT, FOREIGN KEY зв'язок з "Authors")
- genre_id (INT, FOREIGN KEY зв'язок з "Genres")
	
e) Таблиця "users":
- user_id (INT, автоматично зростаючий PRIMARY KEY)
- username (VARCHAR)
- email (VARCHAR)
	
f) Таблиця "borrowed_books":
- borrow_id (INT, автоматично зростаючий PRIMARY KEY)
- book_id (INT, FOREIGN KEY зв'язок з "Books")
- user_id (INT, FOREIGN KEY зв'язок з "Users")
- borrow_date (DATE)
- return_date (DATE)
	
```
CREATE SCHEMA LibraryManagement;

USE LibraryManagement;

CREATE TABLE authors (
    author_id INT AUTO_INCREMENT PRIMARY KEY,
    author_name VARCHAR(255) NOT NULL
);

CREATE TABLE genres (
    genre_id INT AUTO_INCREMENT PRIMARY KEY,
    genre_name VARCHAR(255) NOT NULL
);

CREATE TABLE books (
    book_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    publication_year YEAR,
    author_id INT NOT NULL,
    genre_id INT NOT NULL,
    FOREIGN KEY (author_id) REFERENCES authors(author_id),
    FOREIGN KEY (genre_id) REFERENCES genres(genre_id)
);

CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL
);

CREATE TABLE borrowed_books (
    borrow_id INT AUTO_INCREMENT PRIMARY KEY,
    book_id INT NOT NULL,
    user_id INT NOT NULL,
    borrow_date DATE,
    return_date DATE,
    FOREIGN KEY (book_id) REFERENCES books(book_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

```

2. Заповніть таблиці простими видуманими тестовими даними. 
Достатньо одного-двох рядків у кожну таблицю.
```
USE LibraryManagement;

INSERT INTO authors (author_name) 
VALUES 
('J.K. Rowling'), 
('George Orwell');

INSERT INTO genres (genre_name) 
VALUES 
('Fantasy'), 
('Dystopian');

INSERT INTO books (title, publication_year, author_id, genre_id) 
VALUES 
('Harry Potter and the Sorcerers Stone', 1997, 1, 1), 
('1984', 1949, 2, 2);

INSERT INTO users (username, email) 
VALUES 
('john_doe', 'john.doe@example.com'), 
('jane_smith', 'jane.smith@example.com');

INSERT INTO borrowed_books (book_id, user_id, borrow_date, return_date) 
VALUES 
(1, 1, '2024-11-01', '2024-11-10'), 
(2, 2, '2024-11-05', NULL);

```

3. Перейдіть до бази даних, з якою працювали у темі 3. 
Напишіть запит за допомогою операторів FROM та INNER JOIN, що об’єднує всі таблиці даних, 
які ми завантажили з файлів: order_details, orders, customers, products, categories, employees, shippers, suppliers. 
Для цього ви маєте знайти спільні ключі.
Перевірте правильність виконання запиту.
```
USE Mydb;

SELECT od.order_id,
    o.customer_id, c.name,
    o.employee_id, e.first_name, e.last_name,
    o.shipper_id, s.name,
    od.product_id, p.name,
    p.supplier_id, sup.name,
    p.category_id, cat.name
FROM 
    order_details AS od
INNER JOIN 
    orders AS o ON od.order_id = o.id
INNER JOIN 
    customers AS c ON o.customer_id = c.id
INNER JOIN 
    employees AS e ON o.employee_id = e.employee_id
INNER JOIN 
    shippers AS s ON o.shipper_id = s.id
INNER JOIN 
    products AS p ON od.product_id = p.id
INNER JOIN 
    suppliers AS sup ON p.supplier_id = sup.id
INNER JOIN 
    categories AS cat ON p.category_id = cat.id;

```

4. Виконайте запити, перелічені нижче.
- Визначте, скільки рядків ви отримали (за допомогою оператора COUNT).
```
USE Mydb;

SELECT COUNT(*) AS total_rows
FROM order_details AS od
INNER JOIN orders AS o ON od.order_id = o.id
INNER JOIN customers AS c ON o.customer_id = c.id
INNER JOIN employees AS e ON o.employee_id = e.employee_id
INNER JOIN shippers AS s ON o.shipper_id = s.id
INNER JOIN products AS p ON od.product_id = p.id
INNER JOIN suppliers AS sup ON p.supplier_id = sup.id
INNER JOIN categories AS cat ON p.category_id = cat.id;
```
- Змініть декілька операторів INNER на LEFT чи RIGHT. Визначте, що відбувається з кількістю рядків. 
Чому? Напишіть відповідь у текстовому файлі.
```
USE Mydb;

SELECT COUNT(*) AS total_rows
FROM order_details AS od
LEFT JOIN orders AS o ON od.order_id = o.id
LEFT JOIN customers AS c ON o.customer_id = c.id
LEFT JOIN employees AS e ON o.employee_id = e.employee_id
LEFT JOIN shippers AS s ON o.shipper_id = s.id
INNER JOIN products AS p ON od.product_id = p.id
LEFT JOIN suppliers AS sup ON p.supplier_id = sup.id
RIGHT JOIN categories AS cat ON p.category_id = cat.id;
```
- Оберіть тільки ті рядки, де employee_id > 3 та ≤ 10.
```
USE Mydb;

SELECT od.order_id,
       o.customer_id, c.name AS customer_name,
       o.employee_id, e.first_name, e.last_name,
       o.shipper_id, s.name AS shipper_name,
       od.product_id, p.name AS product_name,
       p.supplier_id, sup.name AS supplier_name,
       p.category_id, cat.name AS category_name
FROM order_details AS od
INNER JOIN orders AS o ON od.order_id = o.id
INNER JOIN customers AS c ON o.customer_id = c.id
INNER JOIN employees AS e ON o.employee_id = e.employee_id
INNER JOIN shippers AS s ON o.shipper_id = s.id
INNER JOIN products AS p ON od.product_id = p.id
INNER JOIN suppliers AS sup ON p.supplier_id = sup.id
INNER JOIN categories AS cat ON p.category_id = cat.id
WHERE o.employee_id BETWEEN 4 AND 11

```
- Згрупуйте за іменем категорії, порахуйте кількість рядків у групі, 
середню кількість товару (кількість товару знаходиться в order_details.quantity)
```
USE Mydb;

SELECT 
    cat.name AS category_name,
    COUNT(*) AS total_rows,
    AVG(od.quantity) AS avg_quantity
FROM order_details AS od
INNER JOIN products AS p ON od.product_id = p.id
INNER JOIN categories AS cat ON p.category_id = cat.id
GROUP BY cat.name

```
- Відфільтруйте рядки, де середня кількість товару більша за 21.
```
USE Mydb;

SELECT 
    cat.name AS category_name,
    COUNT(*) AS total_rows,
    AVG(od.quantity) AS avg_quantity
FROM order_details AS od
INNER JOIN products AS p ON od.product_id = p.id
INNER JOIN categories AS cat ON p.category_id = cat.id
GROUP BY cat.name
HAVING AVG(od.quantity) > 21
```
- Відсортуйте рядки за спаданням кількості рядків.
```
USE Mydb;

SELECT 
    cat.name AS category_name,
    COUNT(*) AS total_rows,
    AVG(od.quantity) AS avg_quantity
FROM order_details AS od
INNER JOIN products AS p ON od.product_id = p.id
INNER JOIN categories AS cat ON p.category_id = cat.id
GROUP BY cat.name
HAVING AVG(od.quantity) > 21
ORDER BY total_rows DESC;
```
- Виведіть на екран (оберіть) чотири рядки з пропущеним першим рядком.
```
USE Mydb;

SELECT 
    cat.name AS category_name,
    COUNT(*) AS total_rows,
    AVG(od.quantity) AS avg_quantity
FROM order_details AS od
INNER JOIN products AS p ON od.product_id = p.id
INNER JOIN categories AS cat ON p.category_id = cat.id
GROUP BY cat.name
HAVING AVG(od.quantity) > 21
ORDER BY total_rows DESC
LIMIT 4 OFFSET 1;

```
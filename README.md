# Week-7a
Create the normalized table
CREATE TABLE ProductDetail_1NF (
    OrderID INT,
    CustomerName VARCHAR(255),
    Product VARCHAR(255)
);

 Insert data into the 1NF table by splitting the Products column
INSERT INTO ProductDetail_1NF (OrderID, CustomerName, Product)
SELECT 
    OrderID,
    CustomerName,
    TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(Products, ',', numbers.n + 1), ',', -1)) AS Product
FROM 
    ProductDetail
JOIN (
    SELECT 0 AS n UNION ALL SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4
) numbers
ON CHAR_LENGTH(Products) - CHAR_LENGTH(REPLACE(Products, ',', '')) >= numbers.n
WHERE Products IS NOT NULL AND Products <> '';

-- Create the Customer table to remove partial dependency
CREATE TABLE Customer (
    OrderID INT PRIMARY KEY,
    CustomerName VARCHAR(255)
);

 Insert unique customer data based on OrderID
INSERT INTO Customer (OrderID, CustomerName)
SELECT DISTINCT OrderID, CustomerName
FROM OrderDetails;

 Create OrderProduct table for product-specific information
CREATE TABLE OrderProduct (
    OrderID INT,
    Product VARCHAR(255),
    PRIMARY KEY (OrderID, Product),
    FOREIGN KEY (OrderID) REFERENCES Customer(OrderID)
);

 Insert order-product relationships
INSERT INTO OrderProduct (OrderID, Product)
SELECT OrderID, Product
FROM OrderDetails;

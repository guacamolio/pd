# pd
pd
1. uzdevums ✓ (tu jau uzrakstīji pareizi)
sqlCREATE DATABASE veikals;
2. uzdevums ✓ (pareizi)
sqlDROP DATABASE katalogs;
3. uzdevums — CREATE TABLE Items un Orders (pēc ER diagrammas):
sqlCREATE TABLE user (
    user_id INT PRIMARY KEY,
    username VARCHAR(16),
    email VARCHAR(255),
    password VARCHAR(32),
    create_time TIMESTAMP
);

CREATE TABLE category (
    category_id INT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE Items (
    Items_id INT PRIMARY KEY,
    item_name VARCHAR(45),
    Items_description VARCHAR(500),
    category_category_id INT,
    In_stock INT,
    FOREIGN KEY (category_category_id) REFERENCES category(category_id)
);

CREATE TABLE Orders (
    Orders_id INT PRIMARY KEY,
    user_user_id INT,
    Created_date VARCHAR(9),
    Order_status INT,
    FOREIGN KEY (user_user_id) REFERENCES user(user_id)
);

CREATE TABLE Orders_has_Items (
    Orders_Orders_id INT,
    Items_Items_id INT,
    Item_qty INT,
    PRIMARY KEY (Orders_Orders_id, Items_Items_id),
    FOREIGN KEY (Orders_Orders_id) REFERENCES Orders(Orders_id),
    FOREIGN KEY (Items_Items_id) REFERENCES Items(Items_id)
);
4. uzdevums — Ārējā atslēga, primārās atslēgas, kurš nestrādā:

Ārējā atslēga: Emp_ID (tabulā Department)
Primārā atslēga Employee tabulā: Emp_ID; Department tabulā: Dep_ID
Kurš nestrādā: Jānis (Emp_ID = 3, nav neviena Department ieraksta ar Emp_ID = 3)

4. uzdevums turpinājums — CREATE TABLE ar FK:
sqlCREATE TABLE Employee (
    Emp_ID INT PRIMARY KEY,
    Name VARCHAR(50),
    Address VARCHAR(100)
);

CREATE TABLE Department (
    Dep_ID INT PRIMARY KEY,
    Dname VARCHAR(50),
    Emp_ID INT,
    FOREIGN KEY (Emp_ID) REFERENCES Employee(Emp_ID)
);
5. uzdevums — Pievieno Edgaru:
sqlINSERT INTO Employee (Emp_ID, Name, Address) VALUES (4, 'Edgars', 'Ventspils');
INSERT INTO Department (Dep_ID, Dname, Emp_ID) VALUES (4, 'IT', 4);
6. uzdevums — Dzēš Dinu:
sqlDELETE FROM Employee WHERE Name = 'Dina';
7. uzdevums — Pievieno lauku Country:
sqlALTER TABLE Employee ADD Country VARCHAR(50);
8. uzdevums — Dzēš visus datus no Department (neiznicina tabulu):
sqlDELETE FROM Department;
-- vai arī:
TRUNCATE TABLE Department;

9. uzdevums — Normalizācija (1NF → 2NF → 3NF):
Sākotnējā tabula satur: Sēklu grupas nosaukums, Cena par vienību, Nepārdotās vienību skaits, Izplatītāja nosaukums, tālrunis, e-pasts.
1NF — tabula jau ir 1NF (nav atkārtojošos grupu).
2NF — atdala izplatītāju (izplatītāja dati nav atkarīgi no sēklu grupas):
Seklu_grupa(grupa_id PK, nosaukums, cena, nepardotas_vienibu_skaits, izplatitajs_id FK)
Izplatitajs(izplatitajs_id PK, nosaukums, talrunis, epasts)
3NF — tālrunis un e-pasts ir atkarīgi tikai no izplatītāja nosaukuma → jau atrisināts ar 2NF sadalījumu. Abas tabulas ir 3NF.

10. uzdevums — Trigeris:
sqlDELIMITER //
CREATE TRIGGER check_stock_before_order
BEFORE INSERT ON Orders_has_Items
FOR EACH ROW
BEGIN
    DECLARE available INT;
    SELECT In_stock INTO available FROM Items WHERE Items_id = NEW.Items_Items_id;
    IF available < NEW.Item_qty THEN
        SIGNAL SQLSTATE '45000' SET message_text = 'Nav pietiekami daudz preces noliktavā';
    END IF;
END;
//
DELIMITER ;

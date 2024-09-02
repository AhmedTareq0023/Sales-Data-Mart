# Sales-Data-Mart
USE EO_AdventureWorksDW2014;

# DDL Queries

CREATE TABLE Dim_Product (
	product_key int not null identity(1,1), -- surrogate key, system key
	product_id int not null, -- alternate key, business key
	product_name nvarchar(50),
	product_description nvarchar(400),
	product_subcategory nvarchar(50),
	product_category nvarchar(50),
	color nvarchar(15),
	model_name nvarchar(50),
	reorder_point smallint,
	standard_cost money,

	-- Metadata
	source_system_code tinyint not null,

	-- SCD
	start_date datetime not null DEFAULT (GETDATE()),
	end_date datetime,
	is_current tinyint not null DEFAULT (1),

	constraint pk_dim_product primary key clustered (product_key)
)


# Creating Indexes

CREATE INDEX dim_product_product_id
ON Dim_Product(product_id)

CREATE INDEX dim_product_product_category
ON Dim_Product(product_category)

-- insert unknown record
set identity_insert Dim_Product on

INSERT INTO Dim_Product(product_key,product_id,product_name,product_description,product_subcategory,product_category,
color,model_name,reorder_point,standard_cost,source_system_code,start_date,end_date,is_current)
VALUES(0,0,'UnKnown','UnKnown','UnKnown','UnKnown','UnKnown','UnKnown',0,0,0,'1900-01-01',NULL,1)

set identity_insert Dim_Product off

----------------------------------------------------------------------------------------------------------------------

CREATE TABLE Dim_Customer (
	customer_key int not null identity(1,1),
	customer_id int not null,
	customer_name nvarchar(150),
	address1 nvarchar(100),
	address2 nvarchar(100),
	city nvarchar(30),
	phone nvarchar(25),
	-- birth_date date,
	-- marital_status char(10),
	-- gender char(1),
	-- yearly_income money,
	-- education varchar(50),

	-- Metadata
	source_system_code tinyint not null,

	-- SCD
	start_date datetime not null DEFAULT (GETDATE()),
	end_date datetime,
	is_current tinyint not null DEFAULT (1),

	constraint pk_dim_customer primary key clustered (customer_key)
)

-- create indexes
CREATE INDEX dim_customer_customer_id
ON Dim_Customer(customer_id)

CREATE INDEX dim_product_city
ON Dim_Customer(city)

-- insert unknown record
set identity_insert Dim_Customer on

INSERT INTO Dim_Customer(customer_key,customer_id,customer_name,address1,address2,city,
phone,source_system_code,start_date,end_date,is_current)
VALUES(0,0,'UnKnown','UnKnown','UnKnown','UnKnown','UnKnown',0,'1900-01-01',NULL,1)

set identity_insert Dim_Customer off

CREATE TABLE Dim_Territory
  (
     territory_key int NOT NULL identity(1, 1),
     territory_id int NOT NULL,
     territory_name nvarchar(50),
     territory_country nvarchar(400),
     territory_group nvarchar(50),

	 -- Metadata
     --source_system_code tinyint NOT NULL,

	 -- SCD
     --start_date datetime NOT NULL DEFAULT (GETDATE()),
     --end_date datetime,
     --is_current tinyint NOT NULL DEFAULT (1),

     constraint pk_dim_territory primary key clustered (territory_key)
  )


-- create indexes
CREATE INDEX Dim_Territory_territory_id
ON Dim_Territory(territory_id)

-- insert unknown record
set identity_insert Dim_Territory on

INSERT INTO Dim_Territory(territory_key,territory_id,territory_name,territory_country,territory_group,source_system_code,
start_date,end_date,is_current)
VALUES (0,0,'Unknown','Unknown','Unknown',0,'1900-01-01',NULL,1)

set identity_insert Dim_Territory off

--Table .. Dim_Dates --> Created into Excel Source with the required structure and filled with all dates
-- Then get here with ssis tool.

CREATE TABLE Fact_Sales (
	product_key int not null,
	customer_key int not null,
	territory_key int not null,
	order_date_key int not null,
	sales_order_id int not null,
	line_number int not null,	
	quantity int,
	unit_price money,
	unit_cost money,	
	freight money,
	extened_sales money,
	extened_cost money,
	created_at datetime not null DEFAULT (GETDATE())

	constraint pk_Fact_Sales primary key clustered (sales_order_id,line_number),

	constraint fk_Fact_Sales_Dim_Product foreign key (product_key)
	references Dim_Product (product_key),

	constraint fk_Fact_Sales_Dim_customer foreign key (customer_key)
	references Dim_Customer (customer_key),

	constraint fk_Fact_Sales_Dim_Territory foreign key (territory_key)
	references Dim_Territory (territory_key),

	constraint fk_Fact_Sales_Dim_Date foreign key (order_date_key)
	references Dim_Date (date_key)
)

-- create indexes
CREATE INDEX Fact_Sales_Dim_Product
ON Fact_Sales (product_key)

CREATE INDEX Fact_Sales_Dim_Customer
ON Fact_Sales (customer_key)

CREATE INDEX Fact_Sales_Dim_Territory
ON Fact_Sales (territory_key)

CREATE INDEX Fact_Sales_Dim_Date
ON Fact_Sales (order_date_key)



-- test
SELECT * FROM Dim_Product
SELECT * FROM Dim_Dates
SELECT * FROM Dim_Territory
SELECT * FROM Dim_Customer


---------------------------------------------------------------------------------------------


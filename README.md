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

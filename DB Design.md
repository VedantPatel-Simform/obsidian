Canteen Management
- User ( )
- Menu 
- Ingredients
- Inventory
- Orders
- Order items
- Customer Reviews

```sql
CREATE TYPE rolenum AS ENUM('Manager', 'Inventory Manager', 'Food Manager', 'Accountant', 'Supplier');

CREATE TABLE IF NOT EXISTS Users (
	id SERIAL PRIMARY KEY,
	name VARCHAR(20) NOT NULL,
	email VARCHAR(100) UNIQUE NOT NULL,
	phone_number VARCHAR(10) UNIQUE NOT NULL,
	Role roleEnum NOT NULL
);

CREATE TABLE IF NOT EXISTS Menu (
	id SERIAL PRIMARY KEY,
	name VARCHAR(50) NOT NULL,
	ingredients VARCHAR(20)[],
	price DECIMAL(7,2) NOT NULL,
	is_available BOOLEAN DEFAULT TRUE
);

CREATE TABLE IF NOT EXISTS Ingredients (
	id SERIAL PRIMARY KEY,
	name VARCHAR(20) NOT NULL,
	stock INTEGER NOT NULL CHECK(stock >= 0)
);

CREATE TABLE IF NOT EXISTS Inventory (
	id SERIAL PRIMARY KEY,
	ingredient_id INTEGER NOT NULL REFERENCES Ingredients(id) ON DELETE RESTRICT,
	supplier_id INTEGER NOT NULL REFERENCES Users(id) ON DELETE SET NULL,
	qty INTEGER NOT NULL
	created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE IF NOT EXISTS Orders (
	id SERIAL PRIMARY KEY,
	customer_id INTEGER NOT NULL REFERENCES Users(id) ON DELETE SET NULL,
	order_time TIMESTAMPTZ NOT NULL DEFAULT NOW(),
	total_amount DECIMAL(9,2) NOT NULL CHECK (totalAmount >= 0)
);

CREATE TABLE IF NOT EXISTS OrderItem (
	order_id INTEGER NOT NULL REFERENCES Orders(id) ON DELETE CASCADE,
	item_id INTEGER NOT NULL REFERENCES Menu(id) ON DELETE RESTRICT,
	quantity INTEGER NOT NULL CHECK (quantity > 0),
	price DECIMAL(7,2) NOT NULL,
	CONSTRAINT uq_order_item UNIQUE (order_id, item_id)
);

CREATE TABLE IF NOT EXISTS CustomerReviews (
	id SERIAL PRIMARY KEY,
	customer_id INTEGER NOT NULL REFERENCES Users(id) ON DELETE SET NULL,
	order_id INTEGER NOT NULL REFERENCES Orders(id) ON DELETE CASCADE,
	rating INTEGER NOT NULL CHECK (rating BETWEEN 1 AND 5),
	comment VARCHAR(200),
	createdAt TIMESTAMPTZ NOT NULL DEFAULT NOW(),
);
```


# Online-resturant-service
from flask import Flask, jsonify, request
import sqlite3

app = Flask(__name__)

# Database setup
conn = sqlite3.connect('restaurant.db')
c = conn.cursor()
c.execute('''CREATE TABLE IF NOT EXISTS products 
             (id INTEGER PRIMARY KEY, name TEXT, price REAL)''')
c.execute('''CREATE TABLE IF NOT EXISTS orders 
             (id INTEGER PRIMARY KEY, product_id INTEGER, quantity INTEGER)''')
conn.commit()
conn.close()

# Product CRUD operations
@app.route('/products', methods=['GET'])
def get_products():
    conn = sqlite3.connect('restaurant.db')
    c = conn.cursor()
    c.execute("SELECT * FROM products")
    products = c.fetchall()
    conn.close()
    return jsonify(products)

@app.route('/products', methods=['POST'])
def create_product():
    data = request.json
    conn = sqlite3.connect('restaurant.db')
    c = conn.cursor()
    c.execute("INSERT INTO products (name, price) VALUES (?, ?)", (data['name'], data['price']))
    conn.commit()
    conn.close()
    return jsonify({'message': 'Product created successfully'}), 201

@app.route('/products/<int:product_id>', methods=['PUT'])
def update_product(product_id):
    data = request.json
    conn = sqlite3.connect('restaurant.db')
    c = conn.cursor()
    c.execute("UPDATE products SET name = ?, price = ? WHERE id = ?", (data['name'], data['price'], product_id))
    conn.commit()
    conn.close()
    return jsonify({'message': 'Product updated successfully'})

@app.route('/products/<int:product_id>', methods=['DELETE'])
def delete_product(product_id):
    conn = sqlite3.connect('restaurant.db')
    c = conn.cursor()
    c.execute("DELETE FROM products WHERE id = ?", (product_id,))
    conn.commit()
    conn.close()
    return jsonify({'message': 'Product deleted successfully'})

# Order operations
@app.route('/orders', methods=['POST'])
def place_order():
    data = request.json
    conn = sqlite3.connect('restaurant.db')
    c = conn.cursor()
    c.execute("INSERT INTO orders (product_id, quantity) VALUES (?, ?)", (data['product_id'], data['quantity']))
    conn.commit()
    conn.close()
    return jsonify({'message': 'Order placed successfully'}), 201

@app.route('/orders', methods=['GET'])
def list_orders():
    conn = sqlite3.connect('restaurant.db')
    c = conn.cursor()
    c.execute("SELECT * FROM orders")
    orders = c.fetchall()
    conn.close()
    return jsonify(orders)

if __name__ == '__main__':
    app.run(debug=True)

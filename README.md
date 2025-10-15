# Shopping Cart
# Name :  Hemapriya K
# Reg.No: 212223040066

Task- Shopping Cart App
Problem:

Create a Shopping Cart app where users can:

Add items to cart

Remove items

View total cost

Apply discounts

Use:

useReducer → to manage cart items and their quantities (ADD_ITEM, REMOVE_ITEM, CLEAR_CART)

useMemo → to compute total cart price and discounts efficiently (only when items change)

useCallback → to memoize addToCart and removeFromCart functions passed to child components (like <Product />)

useRef → to keep track of how many times the “Checkout” button was clicked (without triggering re-renders)

Hints:

useReducer stores all cart logic centrally

useMemo returns totalPrice

useRef holds checkoutCount

useCallback stabilizes cart operation functions

# App.js
```
import React, { useReducer, useMemo, useCallback, useRef } from "react";
import "./App.css";

// Cart reducer
const initialCart = [];

function cartReducer(state, action) {
  switch (action.type) {
    case "ADD_ITEM":
      const existingItem = state.find(item => item.id === action.payload.id);
      if (existingItem) {
        return state.map(item =>
          item.id === action.payload.id
            ? { ...item, quantity: item.quantity + 1 }
            : item
        );
      } else {
        return [...state, { ...action.payload, quantity: 1 }];
      }
    case "REMOVE_ITEM":
      return state
        .map(item =>
          item.id === action.payload.id
            ? { ...item, quantity: item.quantity - 1 }
            : item
        )
        .filter(item => item.quantity > 0);
    case "CLEAR_CART":
      return [];
    default:
      return state;
  }
}

// Products
const PRODUCTS = [
  { id: 1, name: "Apple", price: 30 },
  { id: 2, name: "Banana", price: 20 },
  { id: 3, name: "Orange", price: 25 },
];

function App() {
  const [cart, dispatch] = useReducer(cartReducer, initialCart);
  const checkoutCount = useRef(0);

  const { totalPrice, discountedPrice } = useMemo(() => {
    const total = cart.reduce((sum, item) => sum + item.price * item.quantity, 0);
    const discount = total > 100 ? total * 0.1 : 0;
    return { totalPrice: total, discountedPrice: total - discount };
  }, [cart]);

  const addToCart = useCallback(item => dispatch({ type: "ADD_ITEM", payload: item }), [dispatch]);
  const removeFromCart = useCallback(item => dispatch({ type: "REMOVE_ITEM", payload: item }), [dispatch]);

  const handleCheckout = () => {
    checkoutCount.current += 1;
    alert(`Checked out! Total items: ${cart.length}, Total price: $${discountedPrice.toFixed(2)}, Checkout clicked ${checkoutCount.current} times`);
  };

  return (
    <div className="container">
      <h1>🛒 Shopping Cart</h1>
      <div className="main">
        <ProductList addToCart={addToCart} />
        <Cart
          cart={cart}
          removeFromCart={removeFromCart}
          totalPrice={totalPrice}
          discountedPrice={discountedPrice}
          onClear={() => dispatch({ type: "CLEAR_CART" })}
        />
      </div>
      <button className="checkout-btn" onClick={handleCheckout}>Checkout</button>
    </div>
  );
}

// Product List
function ProductList({ addToCart }) {
  return (
    <div className="product-list">
      <h2>Products</h2>
      {PRODUCTS.map(product => (
        <div key={product.id} className="card">
          <span>{product.name} - ${product.price}</span>
          <button className="add-btn" onClick={() => addToCart(product)}>Add</button>
        </div>
      ))}
    </div>
  );
}

// Cart Component
function Cart({ cart, removeFromCart, totalPrice, discountedPrice, onClear }) {
  return (
    <div className="cart">
      <h2>Cart</h2>
      {cart.length === 0 ? (
        <p className="empty">Cart is empty</p>
      ) : (
        <div>
          {cart.map(item => (
            <div key={item.id} className="card">
              <span>{item.name} x {item.quantity} = ${item.price * item.quantity}</span>
              <button className="remove-btn" onClick={() => removeFromCart(item)}>Remove</button>
            </div>
          ))}
          <div className="summary">
            <p>Total: ${totalPrice.toFixed(2)}</p>
            <p className={discountedPrice < totalPrice ? "discount" : ""}>Discounted: ${discountedPrice.toFixed(2)}</p>
          </div>
          <button className="clear-btn" onClick={onClear}>Clear Cart</button>
        </div>
      )}
    </div>
  );
}

export default App;

```

# App.css
```
body {
  font-family: Arial, sans-serif;
  background-color: #f0f2f5;
  margin: 0;
}

.container {
  max-width: 900px;
  margin: 0 auto;
  padding: 20px;
  text-align: center;
}

h1 {
  font-size: 2.5rem;
  margin-bottom: 20px;
  color: #333;
}

.main {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 20px;
}

.product-list, .cart {
  flex: 1;
  min-width: 300px;
}

.card {
  background-color: #fff;
  padding: 12px 16px;
  margin-bottom: 10px;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.1);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.add-btn {
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 6px 12px;
  cursor: pointer;
}

.add-btn:hover {
  background-color: #0056b3;
}

.remove-btn {
  background-color: #dc3545;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 6px 12px;
  cursor: pointer;
}

.remove-btn:hover {
  background-color: #b02a37;
}

.clear-btn {
  margin-top: 10px;
  padding: 8px 14px;
  background-color: #6c757d;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.clear-btn:hover {
  background-color: #5a6268;
}

.checkout-btn {
  padding: 12px 20px;
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 5px;
  font-size: 1rem;
  cursor: pointer;
}

.checkout-btn:hover {
  background-color: #218838;
}

.summary {
  margin-top: 12px;
  font-weight: bold;
  text-align: left;
}

.discount {
  color: green;
}

.empty {
  color: #777;
  font-style: italic;
}

```

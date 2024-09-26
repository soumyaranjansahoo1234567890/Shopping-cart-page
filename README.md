// src/components/Header.js
import React from 'react';

const Header = ({ cartCount, onCartClick }) => {
  return (
    <header>
      <h1>Plant Shop</h1>
      <div>
        <button onClick={onCartClick}>
          🛒 {cartCount}
        </button>
      </div>
    </header>
  );
};

export default Header;
// src/components/ProductListing.js
import React from 'react';
import plantsData from '../data/plants';

const ProductListing = ({ addToCart }) => {
  const categories = [...new Set(plantsData.map(plant => plant.category))];

  return (
    <div>
      {categories.map(category => (
        <div key={category}>
          <h2>{category}</h2>
          <div className="plant-list">
            {plantsData.filter(plant => plant.category === category).map(plant => (
              <div key={plant.id} className="plant-item">
                <img src={plant.image} alt={plant.name} />
                <h3>{plant.name}</h3>
                <p>${plant.price}</p>
                <button onClick={() => addToCart(plant)}>Add to Cart</button>
              </div>
            ))}
          </div>
        </div>
      ))}
    </div>
  );
};

export default ProductListing;
// src/components/ShoppingCart.js
import React from 'react';

const ShoppingCart = ({ cartItems, increaseItem, decreaseItem, removeItem }) => {
  const totalCost = cartItems.reduce((total, item) => total + item.price * item.quantity, 0);
  const totalItems = cartItems.reduce((total, item) => total + item.quantity, 0);

  return (
    <div>
      <h2>Shopping Cart</h2>
      <p>Total Plants: {totalItems}</p>
      <p>Total Cost: ${totalCost.toFixed(2)}</p>
      <button>Checkout (Coming Soon)</button>
      <div>
        {cartItems.map(item => (
          <div key={item.id}>
            <img src={item.image} alt={item.name} />
            <h3>{item.name}</h3>
            <p>Price: ${item.price}</p>
            <p>Quantity: {item.quantity}</p>
            <button onClick={() => increaseItem(item.id)}>+</button>
            <button onClick={() => decreaseItem(item.id)}>-</button>
            <button onClick={() => removeItem(item.id)}>Remove</button>
          </div>
        ))}
      </div>
    </div>
  );
};

export default ShoppingCart;
// src/App.js
import React, { useState } from 'react';
import Header from './components/Header';
import ProductListing from './components/ProductListing';
import ShoppingCart from './components/ShoppingCart';

const App = () => {
  const [cart, setCart] = useState([]);

  const addToCart = (plant) => {
    setCart(prevCart => {
      const existing = prevCart.find(item => item.id === plant.id);
      if (existing) {
        return prevCart.map(item => 
          item.id === plant.id ? { ...item, quantity: item.quantity + 1 } : item
        );
      }
      return [...prevCart, { ...plant, quantity: 1 }];
    });
  };

  const increaseItem = (id) => {
    setCart(prevCart => 
      prevCart.map(item => 
        item.id === id ? { ...item, quantity: item.quantity + 1 } : item
      )
    );
  };

  const decreaseItem = (id) => {
    setCart(prevCart => 
      prevCart.map(item => 
        item.id === id ? { ...item, quantity: Math.max(1, item.quantity - 1) } : item
      )
    );
  };

  const removeItem = (id) => {
    setCart(prevCart => prevCart.filter(item => item.id !== id));
  };

  return (
    <div>
      <Header cartCount={cart.reduce((total, item) => total + item.quantity, 0)} />
      <ProductListing addToCart={addToCart} />
      <ShoppingCart cartItems={cart} increaseItem={increaseItem} decreaseItem={decreaseItem} removeItem={removeItem} />
    </div>
  );
};

export default App;
/* src/index.css */
body {
  font-family: Arial, sans-serif;
}

.plant-list {
  display: flex;
  flex-wrap: wrap;
}

.plant-item {
  border: 1px solid #ccc;
  margin: 10px;
  padding: 10px;
  text-align: center;
  width: 150px;
}

button {
  margin-top: 10px;
}

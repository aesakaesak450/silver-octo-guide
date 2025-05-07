// Project: Sidika Fashions - Modern E-commerce Website
// Tech Stack: React, Tailwind CSS, React Router, Context API

// 1. Entry Point (main.jsx)
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

// 2. App.jsx
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import Shop from './pages/Shop';
import ProductDetails from './pages/ProductDetails';
import About from './pages/About';
import Contact from './pages/Contact';
import Cart from './pages/Cart';
import Checkout from './pages/Checkout';
import Navbar from './components/Navbar';
import Footer from './components/Footer';
import { CartProvider } from './context/CartContext';

function App() {
  return (
    <CartProvider>
      <Router>
        <div className="min-h-screen flex flex-col">
          <Navbar />
          <main className="flex-grow">
            <Routes>
              <Route path="/" element={<Home />} />
              <Route path="/shop" element={<Shop />} />
              <Route path="/product/:id" element={<ProductDetails />} />
              <Route path="/about" element={<About />} />
              <Route path="/contact" element={<Contact />} />
              <Route path="/cart" element={<Cart />} />
              <Route path="/checkout" element={<Checkout />} />
            </Routes>
          </main>
          <Footer />
        </div>
      </Router>
    </CartProvider>
  );
}

export default App;

// 3. Context API (context/CartContext.jsx)
import { createContext, useContext, useState } from 'react';

const CartContext = createContext();

export const CartProvider = ({ children }) => {
  const [cartItems, setCartItems] = useState([]);

  const addToCart = (product) => {
    setCartItems([...cartItems, product]);
  };

  const removeFromCart = (id) => {
    setCartItems(cartItems.filter(item => item.id !== id));
  };

  return (
    <CartContext.Provider value={{ cartItems, addToCart, removeFromCart }}>
      {children}
    </CartContext.Provider>
  );
};

export const useCart = () => useContext(CartContext);

// 4. Sample ProductCard.jsx
import { Link } from 'react-router-dom';
import { useCart } from '../context/CartContext';

const ProductCard = ({ product }) => {
  const { addToCart } = useCart();
  return (
    <div className="border p-4 rounded-lg shadow hover:shadow-lg">
      <img src={product.image} alt={product.name} className="h-48 w-full object-cover mb-2" />
      <h2 className="text-lg font-bold">{product.name}</h2>
      <p className="text-gray-600">KES {product.price}</p>
      <div className="mt-2 flex gap-2">
        <button onClick={() => addToCart(product)} className="bg-yellow-500 text-white px-4 py-1 rounded">
          Add to Cart
        </button>
        <Link to={`/product/${product.id}`} className="text-blue-600">View</Link>
      </div>
    </div>
  );
};

export default ProductCard;

// 5. Shop.jsx (product catalog)
import ProductCard from '../components/ProductCard';

const sampleProducts = [
  { id: 1, name: 'Wedding Dress', price: 15000, image: '/assets/wedding.jpg' },
  { id: 2, name: 'Kids Uniform', price: 3500, image: '/assets/uniform.jpg' },
  { id: 3, name: 'Custom Ankara Wear', price: 8000, image: '/assets/ankara.jpg' },
];

const Shop = () => {
  return (
    <div className="p-8 grid grid-cols-1 md:grid-cols-3 gap-6">
      {sampleProducts.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
};

export default Shop;

// 6. Cart.jsx
import { useCart } from '../context/CartContext';

const Cart = () => {
  const { cartItems, removeFromCart } = useCart();
  return (
    <div className="p-6">
      <h2 className="text-2xl font-bold mb-4">Your Cart</h2>
      {cartItems.length === 0 ? <p>Your cart is empty.</p> : (
        <ul>
          {cartItems.map(item => (
            <li key={item.id} className="mb-2 flex justify-between items-center">
              <span>{item.name} - KES {item.price}</span>
              <button onClick={() => removeFromCart(item.id)} className="text-red-500">Remove</button>
            </li>
          ))}
        </ul>
      )}
    </div>
  );
};

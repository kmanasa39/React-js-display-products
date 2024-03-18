prgrm1: signup
import React, { useState } from "react";

function Signup() {
  const [formData, setFormData] = useState({
    email: "", // required
    password: "", // required
    username: "" // optional
  });

  function handleSubmit(e) {
    e.preventDefault();
    fetch("https://dummyjson.com/users/add", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(formData)
    })
      .then((res) => res.json())
      .then((data) => console.log(data));
  }

  function handleChange(e) {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  }

  return (
    <div>
      <h1>Signup Form</h1>
      <form onSubmit={handleSubmit}>
        <label>
          Email:
          <input
            type="email"
            value={formData.email}
            name="email"
            onChange={(e) => handleChange(e)}
          />
        </label>
        <label>
          Password:
          <input
            type="password"
            value={formData.password}
            name="password"
            onChange={(e) => handleChange(e)}
          />
        </label>
        <label>
          Confirm Password:
          <input
            type="password"
            value={formData.confirmPassword}
            name="confirmPassword"
            onChange={(e) => handleChange(e)}
          />
        </label>
        <label>
          Username:
          <input
            type="text"
            value={formData.username}
            name="username"
            onChange={(e) => handleChange(e)}
          />
        </label>
        <button type="submit">Sign Up</button>
      </form>
    </div>
  );
}

export default Signup;
pgrm 2: login 
import React, { useState } from "react";

function Login() {
  const [formData, setFormData] = useState({
    email: "", // required
    password: "", // required
    username: "" // optional
  });

  function handleSubmit(e) {
    e.preventDefault();
    fetch("https://dummyjson.com/auth/login", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(formData)
    })
      .then((res) => res.json())
      .then((data) => {
        if (data.error) {
          alert(data.error);
        } else {
          localStorage.setItem("token", data.token);
          // Redirect to the product list page
        }
      });
  }

  function handleChange(e) {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  }

  return (
    <div>
      <h1>Login Form</h1>
      <form onSubmit={handleSubmit}>
        <label>
          Email:
          <input
            type="email"
            value={formData.email}
            name="email"
            onChange={(e) => handleChange(e)}
          />
        </label>
        <label>
          Password:
          <input
            type="password"
            value={formData.password}
            name="password"
            onChange={(e) => handleChange(e)}
          />
        </label>
        <label>
          Username:
          <input
            type="text"
            value={formData.username}
            name="username"
            onChange={(e) => handleChange(e)}
          />
        </label>
        <button type="submit">Login</button>
      </form>
    </div>
  );
}

export default Login;
pgrm3:
step 1:
>npx create-react-app my-app
>cd my-app

step 2: npm install axios react-router-dom jwt-decode

step3: src auth services
import axios from 'axios';

const apiUrl = 'https://dummyjson.com';

export const register = async (user) => {
  const response = await axios.post(`${apiUrl}/auth/users/add`, user);
  if (response.data.user) {
    localStorage.setItem('user', JSON.stringify(response.data.user));
  }
  return response.data;
};

export const login = async (email, password) => {
  const response = await axios.post(`${apiUrl}/auth/login`, { email, password });
  if (response.data.user) {
    localStorage.setItem('user', JSON.stringify(response.data.user));
  }
  return response.data;
};

export const logout = () => {
  localStorage.removeItem('user');
};

export const getCurrentUser = () => {
  return JSON.parse(localStorage.getItem('user'));
};

export const requireAuth = (Component) => {
  return (props) => {
    const user = getCurrentUser();
    if (!user) {
      return <Redirect to={{ pathname: '/login' }} />;
    }
    return <Component {...props} />;
  };
};
step 4:  src API SERVICES
import axios from 'axios';
import jwtDecode from 'jwt-decode';

const apiUrl = 'https://dummyjson.com';

export const apiCall = async (endpoint, method, data, token) => {
  const config = {
    headers: {
      Authorization: `Bearer ${token}`,
    },
  };

  try {
    const response = await axios[method](`${apiUrl}/auth/${endpoint}`, data, config);
    return response.data;
  } catch (error) {
    if (error.response && error.response.status === 401) {
      logout();
    }
    throw error;
  }
};

export const setAuthToken = (token) => {
  if (token) {
    axios.defaults.headers.common['Authorization'] = `Bearer ${token}`;
  } else {
    delete axios.defaults.headers.common['Authorization'];
  }
};

export const getDecodedToken = () => {
  const token = localStorage.getItem('user');
  if (token) {
    return jwtDecode(token);
  }
  return null;
};
STEP 5: LOGIN JS
import React, { useState } from 'react';
import { useHistory } from 'react-router-dom';
import { login } from '../services/authService';

const Login = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState('');
  const history = useHistory();

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const { data } = await login(email, password);
      if (data.user) {
        history.push('/');
      } else {
        setError(data.message);
      }
    } catch (ex) {
      setError(ex.message);
    }
  };

  return (
    <div>
      <h1>Login</h1>
      <form onSubmit={handleSubmit
      Sure, let's create a list of 10 sample product names and display them when the user logs in. When the user enters a product name, we'll filter the list and display the matching products. Here's how we can do it:
      
// App.js
import React, { useState } from 'react';
import Login from './components/Login';
import ProductList from './components/ProductList';

function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [searchTerm, setSearchTerm] = useState('');

  const handleLogin = () => {
    // Perform authentication logic here
    setIsLoggedIn(true);
  };

  return (
    <div className="App">
      <h1>Welcome to Our Online Store</h1>
      {!isLoggedIn ? (
        <Login onLogin={handleLogin} />
      ) : (
        <ProductList searchTerm={searchTerm} />
      )}
    </div>
  );
}

export default App;
// components/Login.js
import React, { useState } from 'react';

function Login({ onLogin }) {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    // Perform login logic here
    onLogin();
  };

  return (
    <div>
      <h2>Login</h2>
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          placeholder="Username"
          value={username}
          onChange={(e) => setUsername(e.target.value)}
        />
        <input
          type="password"
          placeholder="Password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
        />
        <button type="submit">Login</button>
      </form>
    </div>
  );
}

export default Login;
To implement the functionality where the user can search for products by name or description, we can modify the ProductList component to include product descriptions and update the filtering logic to consider both product names and descriptions. Here's the updated code:

jsx
// components/ProductList.js
import React, { useState } from 'react';

const products = [
  { name: 'Smartphone', description: 'A mobile device with advanced features' },
  { name: 'Laptop', description: 'A portable computer for various tasks' },
  { name: 'Tablet', description: 'A handheld device with a touchscreen interface' },
  { name: 'Headphones', description: 'Audio devices worn over the ears' },
  { name: 'Smartwatch', description: 'A wearable device with smart functionalities' },
  { name: 'Camera', description: 'A device for capturing photographs and videos' },
  { name: 'Printer', description: 'A machine for printing documents and images' },
  { name: 'Speaker', description: 'Audio output device for producing sound' },
  { name: 'Monitor', description: 'A display screen for computers or other devices' },
  { name: 'Keyboard', description: 'Input device used for typing characters' },
];

function ProductList() {
  const [searchTerm, setSearchTerm] = useState('');

  const filteredProducts = products.filter((product) =>
    product.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
    product.description.toLowerCase().includes(searchTerm.toLowerCase())
  );

  return (
    <div>
      <h2>Product List</h2>
      <input
        type="text"
        placeholder="Search products by name or description..."
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
      />
      <ul>
        {filteredProducts.map((product, index) => (
          <li key={index}>
            <strong>{product.name}</strong>: {product.description}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default ProductList;
To implement pagination for the product list, we can add controls to navigate through different pages of products. Here's how we can do it:
// components/ProductList.js
import React, { useState } from 'react';

const products = [
  { name: 'Smartphone', description: 'A mobile device with advanced features' },
  { name: 'Laptop', description: 'A portable computer for various tasks' },
  { name: 'Tablet', description: 'A handheld device with a touchscreen interface' },
  { name: 'Headphones', description: 'Audio devices worn over the ears' },
  { name: 'Smartwatch', description: 'A wearable device with smart functionalities' },
  { name: 'Camera', description: 'A device for capturing photographs and videos' },
  { name: 'Printer', description: 'A machine for printing documents and images' },
  { name: 'Speaker', description: 'Audio output device for producing sound' },
  { name: 'Monitor', description: 'A display screen for computers or other devices' },
  { name: 'Keyboard', description: 'Input device used for typing characters' },
  // Add more products as needed
];

const productsPerPage = 5; // Number of products to display per page

function ProductList() {
  const [searchTerm, setSearchTerm] = useState('');
  const [currentPage, setCurrentPage] = useState(1);

  // Calculate total number of pages
  const totalPages = Math.ceil(products.length / productsPerPage);

  // Calculate start and end index of products for current page
  const startIndex = (currentPage - 1) * productsPerPage;
  const endIndex = Math.min(startIndex + productsPerPage, products.length);

  // Filtered and paginated products
  const filteredProducts = products.filter((product) =>
    product.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
    product.description.toLowerCase().includes(searchTerm.toLowerCase())
  ).slice(startIndex, endIndex);

  // Handle page navigation
  const goToPage = (pageNumber) => {
    setCurrentPage(pageNumber);
  };

  return (
    <div>
      <h2>Product List</h2>
      <input
        type="text"
        placeholder="Search products by name or description..."
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
      />
      <ul>
        {filteredProducts.map((product, index) => (
          <li key={index}>
            <strong>{product.name}</strong>: {product.description}
          </li>
        ))}
      </ul>
      <div>
        {/* Previous page button */}
        <button disabled={currentPage === 1} onClick={() => goToPage(currentPage - 1)}>
          Previous
        </button>
        {/* Page numbers */}
        {Array.from({ length: totalPages }, (_, index) => (
          <button key={index} onClick={() => goToPage(index + 1)}>{index + 1}</button>
        ))}
        {/* Next page button */}
        <button disabled={currentPage === totalPages} onClick={() => goToPage(currentPage + 1)}>
          Next
        </button>
      </div>
    </div>
  );
}

export default ProductList;
o display detailed information about a product, including its name, description, price, and image, we can enhance the ProductList component to show a modal or expandable section with this information when the user clicks on a product. Here's how we can do it:
// components/ProductList.js
import React, { useState } from 'react';

const products = [
  { id: 1, name: 'Smartphone', description: 'A mobile device with advanced features', price: '$599', image: 'smartphone.jpg' },
  { id: 2, name: 'Laptop', description: 'A portable computer for various tasks', price: '$999', image: 'laptop.jpg' },
  // Add more products as needed
];

const productsPerPage = 5; // Number of products to display per page

function ProductList() {
  const [searchTerm, setSearchTerm] = useState('');
  const [currentPage, setCurrentPage] = useState(1);
  const [selectedProduct, setSelectedProduct] = useState(null);

  // Calculate total number of pages
  const totalPages = Math.ceil(products.length / productsPerPage);

  // Calculate start and end index of products for current page
  const startIndex = (currentPage - 1) * productsPerPage;
  const endIndex = Math.min(startIndex + productsPerPage, products.length);

  // Filtered and paginated products
  const filteredProducts = products.filter((product) =>
    product.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
    product.description.toLowerCase().includes(searchTerm.toLowerCase())
  ).slice(startIndex, endIndex);

  // Handle page navigation
  const goToPage = (pageNumber) => {
    setCurrentPage(pageNumber);
  };

  // Handle click on a product to show detailed information
  const handleProductClick = (product) => {
    setSelectedProduct(product);
  };

  // Close modal or detailed product view
  const closeModal = () => {
    setSelectedProduct(null);
  };

  return (
    <div>
      <h2>Product List</h2>
      <input
        type="text"
        placeholder="Search products by name or description..."
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
      />
      <ul>
        {filteredProducts.map((product) => (
          <li key={product.id} onClick={() => handleProductClick(product)}>
            <strong>{product.name}</strong>: {product.description}
          </li>
        ))}
      </ul>
      <div>
        {/* Previous page button */}
        <button disabled={currentPage === 1} onClick={() => goToPage(currentPage - 1)}>
          Previous
        </button>
        {/* Page numbers */}
        {Array.from({ length: totalPages }, (_, index) => (
          <button key={index} onClick={() => goToPage(index + 1)}>{index + 1}</button>
        ))}
        {/* Next page button */}
        <button disabled={currentPage === totalPages} onClick={() => goToPage(currentPage + 1)}>
          Next
        </button>
      </div>
      {/* Modal or detailed product view */}
      {selectedProduct && (
        <div className="modal">
          <div className="modal-content">
            <span className="close" onClick={closeModal}>&times;</span>
            <h2>{selectedProduct.name}</h2>
            <p>Description: {selectedProduct.description}</p>
            <p>Price: {selectedProduct.price}</p>
            <img src={selectedProduct.image} alt={selectedProduct.name} />
          </div>
        </div>
      )}
    </div>
  );
}

export default ProductList;
And then in the ProductList.css file:

css
/* ProductList.css */
.product-list-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.product-list {
  list-style: none;
  padding: 0;
}

.product-list li {
  cursor: pointer;
  padding: 10px;
  border-bottom: 1px solid #ddd;
}

.product-list li:hover {
  background-color: #f0f0f0;
}

.pagination {
  margin-top: 20px;
}

.pagination button {
  margin-right: 5px;
}

.modal {
  display: none;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal-content {
  background-color: #fefefe;
  margin: 15% auto;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
}

.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
  cursor: pointer;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}

And then in the ProductList.css file:

css
/* ProductList.css */
.product-list-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.product-list {
  list-style: none;
  padding: 0;
}

.product-list li {
  cursor: pointer;
  padding: 10px;
  border-bottom: 1px solid #ddd;
}

.product-list li:hover {
  background-color: #f0f0f0;
}

.pagination {
  margin-top: 20px;
}

.pagination button {
  margin-right: 5px;
}

.modal {
  display: none;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal-content {
  background-color: #fefefe;
  margin: 15% auto;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
}

.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
  cursor: pointer;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}

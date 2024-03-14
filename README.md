To display products in a React.js application, you typically follow these steps:

1. **Set Up Project Structure**: Create a new React.js project or navigate to your existing project.

2. **Create Product Data**: Define your product data either directly in your component or fetch it from an API.

3. **Create a Product Component**: Build a component to represent each product. This component should receive product data as props and render it accordingly.

4. **Display Products**: Render the Product Component for each product in your data.

Here's a basic example:

```jsx
// Product.js
import React from 'react';

const Product = ({ name, price, description }) => {
  return (
    <div className="product">
      <h3>{name}</h3>
      <p>{description}</p>
      <p>Price: ${price}</p>
    </div>
  );
};

export default Product;
```

```jsx
// ProductList.js
import React from 'react';
import Product from './Product';

const ProductList = ({ products }) => {
  return (
    <div className="product-list">
      {products.map((product, index) => (
        <Product key={index} {...product} />
      ))}
    </div>
  );
};

export default ProductList;
```

```jsx
// App.js
import React from 'react';
import ProductList from './ProductList';

const products = [
  { name: 'Product 1', price: 10, description: 'Description for Product 1' },
  { name: 'Product 2', price: 20, description: 'Description for Product 2' },
  { name: 'Product 3', price: 30, description: 'Description for Product 3' },
];

const App = () => {
  return (
    <div className="App">
      <h1>Product Catalog</h1>
      <ProductList products={products} />
    </div>
  );
};

export default App;
```

This code will display a list of products with their names, prices, and descriptions. Adjust the structure and styling according to your specific needs.

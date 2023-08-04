# nodeJsTestProject1

// server.js
const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors');
const mongoose = require('mongoose');

const app = express();
const port = process.env.PORT || 3000;

// Middleware
app.use(cors());
app.use(bodyParser.json());

// MongoDB connection
mongoose.connect('mongodb://localhost:27017/productsdb', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

const productSchema = new mongoose.Schema({
  amount: { type: Number, required: true },
  product: { type: String, required: true },
});

const Product = mongoose.model('Product', productSchema);

// Routes
app.post('/create-product', (req, res) => {
  const { amount, product } = req.body;
  const newProduct = new Product({ amount, product });

  newProduct.save((err, product) => {
    if (err) {
      return res.status(500).json({ error: 'Error creating product' });
    }

    return res.status(201).json(product);
  });
});

app.get('/get-products', (req, res) => {
  Product.find({}, (err, products) => {
    if (err) {
      return res.status(500).json({ error: 'Error retrieving products' });
    }

    return res.status(200).json(products);
  });
});

app.delete('/delete-product/:id', (req, res) => {
  const productId = req.params.id;

  Product.findByIdAndDelete(productId, (err, product) => {
    if (err) {
      return res.status(500).json({ error: 'Error deleting product' });
    }

    if (!product) {
      return res.status(404).json({ message: 'Product not found' });
    }

    return res.status(200).json({ message: 'Product deleted successfully' });
  });
});

app.listen(port, () => {
  console.log(`Server listening on port ${port}`);
});

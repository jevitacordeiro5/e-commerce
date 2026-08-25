<?php

$cartFile = __DIR__ . '/cart_data.json';

// GET - only return JSON when API is requested
if ($_SERVER['REQUEST_METHOD'] === 'GET' && isset($_GET['api'])) {

    header('Content-Type: application/json');

    if (file_exists($cartFile)) {
        echo file_get_contents($cartFile);
    } else {
        echo '[]';
    }

    exit;
}

// POST - save cart
if ($_SERVER['REQUEST_METHOD'] === 'POST') {

    header('Content-Type: application/json');

    $input = json_decode(file_get_contents('php://input'), true);

    if (isset($input['cart']) && is_array($input['cart'])) {

        file_put_contents(
            $cartFile,
            json_encode($input['cart'], JSON_PRETTY_PRINT)
        );

        echo json_encode([
            'status' => 'ok',
            'message' => 'Cart saved successfully'
        ]);

    } else {

        http_response_code(400);

        echo json_encode([
            'error' => 'Invalid cart data'
        ]);
    }

    exit;
}

?>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ShopNest · product catalog</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
    }
    body {
      background: #f8fafc;
      color: #0b1a2e;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
    }
    .navbar {
      background: #ffffff;
      padding: 1rem 2rem;
      box-shadow: 0 4px 12px rgba(0,0,0,0.03);
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      justify-content: space-between;
      border-bottom: 1px solid #e9edf2;
    }
    .logo {
      font-size: 1.8rem;
      font-weight: 700;
      letter-spacing: -0.5px;
      color: #0a1e2f;
    }
    .logo i { color: #3b82f6; margin-right: 6px; }
    .nav-links {
      display: flex;
      gap: 2.2rem;
      font-weight: 500;
    }
    .nav-links a {
      text-decoration: none;
      color: #1e2f40;
      transition: 0.2s;
      font-size: 1rem;
      padding-bottom: 4px;
      border-bottom: 2px solid transparent;
      cursor: pointer;
    }
    .nav-links a:hover, .nav-links a.active {
      color: #2563eb;
      border-bottom-color: #2563eb;
    }
    .cart-badge {
      background: #eef2f6;
      padding: 0.5rem 1rem;
      border-radius: 40px;
      font-size: 0.95rem;
      display: flex;
      align-items: center;
      gap: 8px;
    }
    .cart-badge i { color: #2563eb; }
    #cart-count {
      font-weight: 700;
      background: #2563eb;
      color: white;
      padding: 0 10px;
      border-radius: 30px;
      line-height: 1.8;
      min-width: 28px;
      text-align: center;
    }
    .app-container {
      max-width: 1300px;
      margin: 0 auto;
      padding: 1.5rem 2rem;
      flex: 1;
    }
    .page { display: none; animation: fade 0.25s ease; }
    .page.active { display: block; }
    @keyframes fade {
      0% { opacity: 0.3; transform: translateY(6px); }
      100% { opacity: 1; transform: translateY(0); }
    }
    .catalog-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 2rem;
      flex-wrap: wrap;
    }
    .catalog-header h2 { font-size: 2rem; font-weight: 600; }
    .catalog-header p { color: #475569; }

    /* Category Filter Buttons */
    .category-filters {
      display: flex;
      gap: 0.8rem;
      flex-wrap: wrap;
      margin-bottom: 1.5rem;
    }
    .category-filters button {
      padding: 0.5rem 1.5rem;
      border-radius: 40px;
      border: 2px solid #e9edf2;
      background: white;
      font-weight: 600;
      font-size: 0.9rem;
      cursor: pointer;
      transition: 0.2s;
      color: #1e2f40;
    }
    .category-filters button:hover {
      border-color: #2563eb;
      color: #2563eb;
    }
    .category-filters button.active {
      background: #2563eb;
      color: white;
      border-color: #2563eb;
    }

    /* 3×3 Grid Layout */
    .product-grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 2rem 1.5rem;
    }

    /* Responsive: 2 columns on tablets */
    @media (max-width: 992px) {
      .product-grid {
        grid-template-columns: repeat(2, 1fr);
      }
    }

    /* Responsive: 1 column on mobile */
    @media (max-width: 600px) {
      .product-grid {
        grid-template-columns: 1fr;
      }
    }

    .product-card {
      background: white;
      border-radius: 24px;
      padding: 1.2rem 1rem 1.5rem;
      box-shadow: 0 8px 20px rgba(0,0,0,0.02);
      transition: 0.25s ease;
      border: 1px solid #eef2f6;
      display: flex;
      flex-direction: column;
    }
    .product-card:hover {
      transform: translateY(-6px);
      box-shadow: 0 18px 30px -12px rgba(0,0,0,0.08);
      border-color: #dce2eb;
    }
    .product-image {
      width: 100%;
      aspect-ratio: 1 / 1;
      background: #f1f5f9;
      border-radius: 20px;
      display: flex;
      align-items: center;
      justify-content: center;
      margin-bottom: 1rem;
      overflow: hidden;
      position: relative;
    }
    .product-image img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      transition: 0.2s;
      display: block;
    }
    .product-image .image-placeholder {
      position: absolute;
      font-size: 3rem;
      color: #94a3b8;
      opacity: 0.3;
    }
    .product-category-badge {
      display: inline-block;
      font-size: 0.7rem;
      font-weight: 600;
      text-transform: uppercase;
      letter-spacing: 0.5px;
      padding: 0.2rem 0.8rem;
      border-radius: 20px;
      background: #eef2f6;
      color: #475569;
      margin-bottom: 0.3rem;
      align-self: flex-start;
    }
    .product-category-badge.electronics {
      background: #dbeafe;
      color: #2563eb;
    }
    .product-category-badge.dresses {
      background: #fce7f3;
      color: #db2777;
    }
    .product-category-badge.accessories {
      background: #d1fae5;
      color: #059669;
    }
    .product-info {
      display: flex;
      flex-direction: column;
      gap: 0.3rem;
    }
    .product-title { font-weight: 600; font-size: 1.1rem; color: #0b1a2e; }
    .product-price { font-weight: 700; color: #2563eb; font-size: 1.2rem; }
    .product-desc { font-size: 0.85rem; color: #64748b; margin: 0.2rem 0 0.4rem; }
    .add-btn {
      margin-top: 0.8rem;
      background: #f1f5f9;
      border: none;
      padding: 0.6rem 0;
      border-radius: 40px;
      font-weight: 600;
      color: #1e293b;
      cursor: pointer;
      transition: 0.2s;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 6px;
      font-size: 0.9rem;
    }
    .add-btn:hover { background: #2563eb; color: white; }
    .add-btn:active { transform: scale(0.96); }
    .cart-page h2 { font-size: 2rem; margin-bottom: 0.5rem; }
    .cart-items { margin: 1.8rem 0; display: flex; flex-direction: column; gap: 1rem; }
    .cart-item {
      background: white;
      border-radius: 20px;
      padding: 1rem 1.5rem;
      display: flex;
      align-items: center;
      justify-content: space-between;
      border: 1px solid #e9edf2;
      flex-wrap: wrap;
    }
    .cart-item-info {
      display: flex;
      align-items: center;
      gap: 1.2rem;
    }
    .cart-item-thumb {
      width: 56px;
      height: 56px;
      background: #f1f5f9;
      border-radius: 16px;
      display: flex;
      align-items: center;
      justify-content: center;
      overflow: hidden;
      flex-shrink: 0;
    }
    .cart-item-thumb img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
    .cart-item-name { font-weight: 600; }
    .cart-item-price { font-weight: 600; color: #2563eb; }
    .cart-item-remove {
      background: none;
      border: none;
      color: #94a3b8;
      font-size: 1.2rem;
      cursor: pointer;
      transition: 0.2s;
      padding: 0 8px;
    }
    .cart-item-remove:hover { color: #dc2626; }
    .cart-total {
      text-align: right;
      font-size: 1.5rem;
      font-weight: 600;
      border-top: 2px dashed #dce2eb;
      padding-top: 1.5rem;
      margin-top: 0.5rem;
    }
    .empty-cart {
      background: white;
      border-radius: 30px;
      padding: 3rem;
      text-align: center;
      color: #64748b;
    }

    /* About Page Styles */
    .about-page { max-width: 900px; margin: 0 auto; }
    .about-page h2 { 
      font-size: 2.5rem; 
      margin-bottom: 0.5rem;
      color: #0a1e2f;
    }
    .about-page .subtitle {
      font-size: 1.1rem;
      color: #64748b;
      margin-bottom: 2rem;
      border-bottom: 3px solid #2563eb;
      padding-bottom: 0.5rem;
      display: inline-block;
    }
    .about-page .about-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 2rem;
      margin: 2rem 0;
    }
    .about-page .about-card {
      background: white;
      padding: 1.5rem;
      border-radius: 20px;
      border: 1px solid #eef2f6;
      transition: 0.3s;
    }
    .about-page .about-card:hover {
      box-shadow: 0 8px 25px rgba(0,0,0,0.05);
      transform: translateY(-2px);
    }
    .about-page .about-card i {
      font-size: 2rem;
      color: #2563eb;
      margin-bottom: 0.8rem;
    }
    .about-page .about-card h3 {
      font-size: 1.1rem;
      margin-bottom: 0.5rem;
      color: #0a1e2f;
    }
    .about-page .about-card p {
      color: #475569;
      line-height: 1.6;
      font-size: 0.95rem;
    }
    .about-page .mission-text {
      background: #f1f5f9;
      padding: 2rem;
      border-radius: 20px;
      margin: 2rem 0;
      border-left: 4px solid #2563eb;
    }
    .about-page .mission-text p {
      color: #1e293b;
      line-height: 1.8;
      font-size: 1.05rem;
    }
    .about-page .stats {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 1.5rem;
      margin: 2rem 0;
    }
    .about-page .stat-item {
      text-align: center;
      background: white;
      padding: 1.5rem;
      border-radius: 16px;
      border: 1px solid #eef2f6;
    }
    .about-page .stat-item .number {
      font-size: 2rem;
      font-weight: 700;
      color: #2563eb;
    }
    .about-page .stat-item .label {
      color: #64748b;
      font-size: 0.9rem;
      margin-top: 0.3rem;
    }

    @media (max-width: 700px) {
      .about-page .about-grid {
        grid-template-columns: 1fr;
      }
      .about-page .stats {
        grid-template-columns: 1fr;
      }
      .navbar { flex-direction: column; gap: 0.8rem; align-items: stretch; }
      .nav-links { justify-content: center; gap: 1.2rem; flex-wrap: wrap; }
      .cart-badge { justify-content: center; }
      .catalog-header { flex-direction: column; align-items: start; gap: 0.5rem; }
      .category-filters { justify-content: center; }
    }

    .footer {
      background: white;
      border-top: 1px solid #e9edf2;
      padding: 1.5rem 2rem;
      text-align: center;
      font-size: 0.9rem;
      color: #64748b;
      margin-top: 2rem;
    }
    .sync-status {
      font-size: 0.8rem;
      color: #64748b;
      margin-left: 10px;
    }
    .sync-status.synced { color: #22c55e; }
    .sync-status.syncing { color: #f59e0b; }
  </style>
</head>
<body>
  <header class="navbar">
    <div class="logo">
      <i class="fas fa-store-alt"></i> ShopNest
    </div>
    <div class="nav-links">
      <a class="active" data-page="catalog"><i class="fas fa-th"></i> Catalog</a>
      <a data-page="cart"><i class="fas fa-shopping-cart"></i> Cart</a>
      <a data-page="about"><i class="fas fa-info-circle"></i> About</a>
    </div>
    <div class="cart-badge">
      <i class="fas fa-shopping-bag"></i>
      <span id="cart-count">0</span>
      <span id="syncStatus" class="sync-status synced"><i class="fas fa-check-circle"></i></span>
    </div>
  </header>

  <div class="app-container">
    <section id="page-catalog" class="page active">
      <div class="catalog-header">
        <div>
          <h2>✨ our collection</h2>
          <p>electronics · dresses · accessories</p>
        </div>
        <span style="background:#eef2f6; padding:0.4rem 1.2rem; border-radius:30px; font-size:0.9rem;">
          <i class="fas fa-tag"></i> <span id="productCount">15</span> products
        </span>
      </div>

      <!-- Category Filters -->
      <div class="category-filters">
        <button class="active" data-category="all">All</button>
        <button data-category="electronics">📱 Electronics</button>
        <button data-category="dresses">👗 Dresses</button>
        <button data-category="accessories">👜 Accessories</button>
      </div>

      <div class="product-grid" id="productGrid"></div>
    </section>

    <section id="page-cart" class="page cart-page">
      <h2><i class="fas fa-shopping-cart" style="color:#2563eb;"></i> your cart</h2>
      <div id="cartContainer">
        <div class="empty-cart"><i class="fas fa-box-open" style="font-size:2.4rem; opacity:0.4; display:block; margin-bottom:1rem;"></i> cart is empty</div>
      </div>
    </section>

    <section id="page-about" class="page about-page">
      <h2>🏡 About ShopNest</h2>
      <div class="subtitle">Your one-stop destination for quality products</div>
      
      <div class="mission-text">
        <p><strong>Our Mission:</strong> At ShopNest, we're dedicated to curating the finest selection of electronics, dresses, and accessories. We believe in quality, affordability, and exceptional customer service. Every product in our catalog is handpicked to ensure it meets our high standards of style and functionality.</p>
      </div>

      <div class="about-grid">
        <div class="about-card">
          <i class="fas fa-tshirt"></i>
          <h3>Curated Collections</h3>
          <p>From trendy dresses to the latest gadgets, our collections are carefully curated to bring you the best in fashion and technology.</p>
        </div>
        <div class="about-card">
          <i class="fas fa-shipping-fast"></i>
          <h3>Fast & Reliable Delivery</h3>
          <p>We partner with trusted logistics partners to ensure your orders reach you quickly and safely, no matter where you are.</p>
        </div>
        <div class="about-card">
          <i class="fas fa-shield-alt"></i>
          <h3>Secure Shopping</h3>
          <p>Your security matters to us. Shop with confidence knowing your data is protected with industry-standard encryption.</p>
        </div>
        <div class="about-card">
          <i class="fas fa-headset"></i>
          <h3>24/7 Customer Support</h3>
          <p>Our dedicated support team is always here to help with any questions about your orders, returns, or product inquiries.</p>
        </div>
      </div>

      <div class="stats">
        <div class="stat-item">
          <div class="number">15+</div>
          <div class="label">Premium Products</div>
        </div>
        <div class="stat-item">
          <div class="number">3</div>
          <div class="label">Product Categories</div>
        </div>
        <div class="stat-item">
          <div class="number">100%</div>
          <div class="label">Satisfaction Guarantee</div>
        </div>
      </div>

      <div style="background: linear-gradient(135deg, #2563eb, #3b82f6); color: white; padding: 2rem; border-radius: 20px; margin-top: 1.5rem; text-align: center;">
        <h3 style="margin-bottom: 0.5rem; font-size: 1.5rem;">✨ Why Choose ShopNest?</h3>
        <p style="font-size: 1.05rem; opacity: 0.95;">Quality products, competitive prices, and an exceptional shopping experience — all in one place. Start exploring our collection today!</p>
        <div style="margin-top: 1rem; display: flex; gap: 1rem; justify-content: center; flex-wrap: wrap;">
          <span style="background: rgba(255,255,255,0.2); padding: 0.3rem 1.2rem; border-radius: 30px;">✓ Free shipping on orders $50+</span>
          <span style="background: rgba(255,255,255,0.2); padding: 0.3rem 1.2rem; border-radius: 30px;">✓ 30-day return policy</span>
          <span style="background: rgba(255,255,255,0.2); padding: 0.3rem 1.2rem; border-radius: 30px;">✓ Secure payments</span>
        </div>
      </div>
    </section>
  </div>

  <footer class="footer">
    <i class="fas fa-code"></i> ShopNest · full‑stack capstone · 2026
  </footer>

  <script>
    (function() {
      "use strict";

      // ----- PRODUCTS WITH CATEGORIES (Electronics + Dresses + Accessories) -----
      const products = [
        // ELECTRONICS
        { id: 1, name: "Wireless Earbuds Pro", price: 49.99, desc: "Noise cancelling, IPX5, 24hr battery", category: "electronics", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQOMAsfVcTCiZ_ikcxzUTc4iU1CgskO3-viWHwpeTLMjQ&s" },
        { id: 2, name: "Smart Watch Series 8", price: 89.99, desc: "Heart rate, GPS, sleep tracking", category: "electronics", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQuzSxuNQG8B1d24E5EJKfawcocYuw93LNMiJMeruGeuA&s=10" },
        { id: 3, name: "Bluetooth Speaker", price: 34.99, desc: "Waterproof, 20W, 360° sound", category: "electronics", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSvD9jlvU3rGhus-hZAOyjflbV3Q-_wafAZ4332BCYyug&s=10" },
        { id: 4, name: "Laptop Stand", price: 29.99, desc: "Adjustable, aluminum, foldable", category: "electronics", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRnA-ZxX3-AGUziVlzFB1-CZmol5xmX-ULhavh-d_iVcg&s=10" },
        { id: 5, name: "USB-C Hub", price: 24.99, desc: "7-in-1, 4K HDMI, 100W PD", category: "electronics", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRy36fI-ty-bhduM419-V_FGlZXxxJtZHWdj9-Vjl-iqQ&s=10" },
        { id: 6, name: "Mechanical Keyboard", price: 59.99, desc: "RGB, blue switches, TKL", category: "electronics", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTGg9ZRYdaaifXJdwCpUlIeq_xZZJd9XWcmVsD8jkGmSg&s=10" },

        // DRESSES
        { id: 7, name: "Floral Maxi Dress", price: 45.99, desc: "100% cotton, summer floral print", category: "dresses", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTIy55XsXCIR0eFSvBwcObuOi9-y7VNEoiYUpTIJkk0Cg&s=10" },
        { id: 8, name: "Little Black Dress", price: 39.99, desc: "Elegant, knee-length, satin", category: "dresses", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQV3wftgj4MVcRwEMjQLt8lxqukY6WxMpgX6YVXJW3vMA&s=10" },
        { id: 9, name: "Bohemian Sundress", price: 34.99, desc: "Flowy, embroidered, S-L", category: "dresses", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR789_i2ZgTW_Ljw85rLomfaMAeuL3StCcnHZImuZuAEQ&s=10" },
        { id: 10, name: "Silk Evening Gown", price: 79.99, desc: "Luxurious silk, floor-length", category: "dresses", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRU8FHwX96MrSTKJQcdyOjtLy1v4m0Ha841k6dlhx-UMw&s=10" },

        // ACCESSORIES
        { id: 11, name: "Leather Crossbody Bag", price: 29.99, desc: "Genuine leather, adjustable strap", category: "accessories", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSa-LEbFtOB6ml3Y3pn60pnBGqgGPY6RWWdLYDuCA0lbg&s=10" },
        { id: 12, name: "Sunglasses", price: 19.99, desc: "UV400, polarized, unisex", category: "accessories", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRBZr3xLVF7yggYIwmRwJBcfaiwlBWHqPrx_CzmaSrfxA&s=10" },
        { id: 13, name: "Wool Blend Scarf", price: 14.99, desc: "Warm, plaid pattern, one size", category: "accessories", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQPUBVLo6BBMHMTNbwG-7XRKCB72csBexXaXrr8HfFrTg&s=10" },
        { id: 14, name: "Leather Wallet", price: 24.99, desc: "RFID blocking, 6 card slots", category: "accessories", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR3U01J6lke1yUcyVrsJAvEqhUDnGe-mMVEnQMNT95ijw&s=10" },
        { id: 15, name: "Genuine Leather Belt", price: 12.99, desc: "Adjustable, classic design", category: "accessories", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTPsEaX7lqVTSEtRipl8IFSEDlTiWL0J1u3eKI_kIAv5A&s=10" }
      ];

      let cart = [];
      let currentCategory = 'all';
      const API_URL = window.location.href.split('?')[0];

      // DOM refs
      const productGrid = document.getElementById('productGrid');
      const cartContainer = document.getElementById('cartContainer');
      const cartCountEl = document.getElementById('cart-count');
      const syncStatus = document.getElementById('syncStatus');
      const navLinks = document.querySelectorAll('.nav-links a');
      const categoryButtons = document.querySelectorAll('.category-filters button');
      const productCountEl = document.getElementById('productCount');

      // ----- SYNC CART WITH PHP BACKEND -----
      async function syncCartToServer(cartData) {
        try {
          syncStatus.innerHTML = '<i class="fas fa-spinner fa-spin"></i>';
          syncStatus.className = 'sync-status syncing';
          
          const response = await fetch(API_URL, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ cart: cartData })
          });
          
          if (!response.ok) throw new Error('Server error');
          const result = await response.json();
          
          syncStatus.innerHTML = '<i class="fas fa-check-circle"></i>';
          syncStatus.className = 'sync-status synced';
          return result;
        } catch (error) {
          console.error('Sync error:', error);
          syncStatus.innerHTML = '<i class="fas fa-exclamation-triangle"></i>';
          syncStatus.className = 'sync-status';
          return null;
        }
      }

      async function loadCartFromServer() {
        try {
          const response = await fetch(API_URL);
          if (!response.ok) throw new Error('Failed to load cart');
          const data = await response.json();
          if (Array.isArray(data)) {
            cart = data;
            localStorage.setItem('shopnest_cart', JSON.stringify(cart));
            updateCartUI();
            renderCartPage();
          }
        } catch (error) {
          console.error('Load cart error:', error);
          const local = localStorage.getItem('shopnest_cart');
          if (local) {
            cart = JSON.parse(local);
            updateCartUI();
          }
        }
      }

      // ----- CART OPERATIONS -----
      function addToCart(product) {
        const existing = cart.find(item => item.id === product.id);
        if (existing) {
          existing.qty = (existing.qty || 1) + 1;
        } else {
          cart.push({ ...product, qty: 1 });
        }
        saveCart();
      }

      function removeFromCart(id) {
        cart = cart.filter(item => item.id !== id);
        saveCart();
      }

      function saveCart() {
        localStorage.setItem('shopnest_cart', JSON.stringify(cart));
        syncCartToServer(cart);
        updateCartUI();
        renderCartPage();
      }

      function getTotalItems() {
        return cart.reduce((sum, item) => sum + (item.qty || 1), 0);
      }

      function getTotalPrice() {
        return cart.reduce((sum, item) => sum + (item.price * (item.qty || 1)), 0);
      }

      // ----- RENDER FUNCTIONS -----
      function updateCartUI() {
        cartCountEl.textContent = getTotalItems();
      }

      function getCategoryBadgeClass(category) {
        const classes = {
          'electronics': 'electronics',
          'dresses': 'dresses',
          'accessories': 'accessories'
        };
        return classes[category] || '';
      }

      function getCategoryIcon(category) {
        const icons = {
          'electronics': '📱',
          'dresses': '👗',
          'accessories': '👜'
        };
        return icons[category] || '';
      }

      function renderProducts(category = 'all') {
        if (!productGrid) return;
        
        const filtered = category === 'all' 
          ? products 
          : products.filter(p => p.category === category);
        
        productCountEl.textContent = filtered.length;

        if (filtered.length === 0) {
          productGrid.innerHTML = `
            <div style="grid-column: 1 / -1; text-align: center; padding: 3rem; color: #64748b;">
              <i class="fas fa-box-open" style="font-size: 2rem; display: block; margin-bottom: 0.5rem;"></i>
              No products in this category
            </div>
          `;
          return;
        }

        productGrid.innerHTML = filtered.map(p => `
          <div class="product-card" data-id="${p.id}">
            <div class="product-image">
              <img src="${p.img}" alt="${p.name}" loading="lazy" 
                   onerror="this.parentElement.innerHTML = '<i class=\\'fas fa-image\\' style=\\'font-size: 3rem; color: #94a3b8; opacity: 0.3;\\'></i>'">
            </div>
            <span class="product-category-badge ${getCategoryBadgeClass(p.category)}">
              ${getCategoryIcon(p.category)} ${p.category}
            </span>
            <div class="product-info">
              <div class="product-title">${p.name}</div>
              <div class="product-price">$${p.price.toFixed(2)}</div>
              <div class="product-desc">${p.desc}</div>
              <button class="add-btn" data-id="${p.id}"><i class="fas fa-plus-circle"></i> add to cart</button>
            </div>
          </div>
        `).join('');

        document.querySelectorAll('.add-btn').forEach(btn => {
          btn.addEventListener('click', function() {
            const id = parseInt(this.dataset.id);
            const product = products.find(p => p.id === id);
            if (product) addToCart(product);
          });
        });
      }

      function renderCartPage() {
        if (!cartContainer) return;
        if (cart.length === 0) {
          cartContainer.innerHTML = `<div class="empty-cart"><i class="fas fa-box-open" style="font-size:2.4rem; opacity:0.4; display:block; margin-bottom:1rem;"></i> cart is empty</div>`;
          return;
        }

        let html = `<div class="cart-items">`;
        cart.forEach(item => {
          html += `
            <div class="cart-item">
              <div class="cart-item-info">
                <div class="cart-item-thumb">
                  <img src="${item.img}" alt="${item.name}" loading="lazy" 
                       onerror="this.parentElement.innerHTML = '<i class=\\'fas fa-image\\' style=\\'font-size: 1.5rem; color: #94a3b8;\\'></i>'">
                </div>
                <div>
                  <div class="cart-item-name">${item.name}</div>
                  <div style="font-size:0.8rem; color:#64748b;">${item.category}</div>
                  <div style="font-size:0.9rem; color:#64748b;">qty: ${item.qty || 1}</div>
                </div>
              </div>
              <div style="display:flex; align-items:center; gap:1rem;">
                <span class="cart-item-price">$${(item.price * (item.qty || 1)).toFixed(2)}</span>
                <button class="cart-item-remove" data-id="${item.id}"><i class="fas fa-trash-alt"></i></button>
              </div>
            </div>
          `;
        });
        html += `</div>`;
        html += `<div class="cart-total">total: $${getTotalPrice().toFixed(2)}</div>`;
        cartContainer.innerHTML = html;

        document.querySelectorAll('.cart-item-remove').forEach(btn => {
          btn.addEventListener('click', function() {
            const id = parseInt(this.dataset.id);
            removeFromCart(id);
          });
        });
      }

      // ----- CATEGORY FILTER -----
      function filterByCategory(category) {
        currentCategory = category;
        categoryButtons.forEach(btn => {
          btn.classList.toggle('active', btn.dataset.category === category);
        });
        renderProducts(category);
      }

      categoryButtons.forEach(btn => {
        btn.addEventListener('click', function() {
          filterByCategory(this.dataset.category);
        });
      });

      // ----- NAVIGATION -----
      function navigateTo(pageId) {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        const target = document.getElementById(`page-${pageId}`);
        if (target) target.classList.add('active');

        navLinks.forEach(link => {
          link.classList.remove('active');
          if (link.dataset.page === pageId) link.classList.add('active');
        });

        if (pageId === 'cart') renderCartPage();
        if (pageId === 'catalog') renderProducts(currentCategory);
      }

      navLinks.forEach(link => {
        link.addEventListener('click', function(e) {
          e.preventDefault();
          navigateTo(this.dataset.page);
        });
      });

      // ----- INIT -----
      async function init() {
        await loadCartFromServer();
        renderProducts('all');
        updateCartUI();
        navigateTo('catalog');
        
        setInterval(() => {
          if (cart.length > 0) {
            syncCartToServer(cart);
          }
        }, 30000);
      }

      init();
    })();
  </script>

  
</body>
</html>

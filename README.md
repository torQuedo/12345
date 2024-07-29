<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>QR TABLING</title>
    <style>
        body {
            margin: 0;
            font-family: Arial, sans-serif;
            background: linear-gradient(to right, #000000, #434343);
            color: rgb(255, 255, 255);
            overflow-x: hidden;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            padding: 20px;
            text-align: center;
        }
        .restaurant-name {
            font-size: 2.5em;
            margin: 0;
            padding: 15px;
            position: sticky;
            top: 0;
            background: rgba(0, 0, 0, 0.8);
            z-index: 100;
            color: #574851;
            border-radius: 15px;
            text-shadow: 1px 1px 5px #ff0000;
        }
        .restaurant-name.first-page {
            animation: shake 0.5s infinite alternate;
        }
        @keyframes shake {
            from { transform: translateX(-2px); }
            to { transform: translateX(2px); }
        }
        .menu-section {
            background: rgba(0, 0, 0, 0.1);
            padding: 20px;
            margin-bottom: 20px;
            border: 3px solid transparent;
            border-radius: 20px;
            background-clip: padding-box;
            position: relative;
        }
        .menu-section::before {
            content: '';
            position: absolute;
            top: -3px;
            left: -3px;
            right: -3px;
            bottom: -3px;
            border-radius: 20px;
            background: linear-gradient(45deg, #000000, #3f3f3f);
            z-index: -1;
        }
        .menu-section h3 {
            margin: 0;
            padding-bottom: 10px;
            position: relative;
            background: rgba(192, 192, 192, 0.6);
            border-radius: 15px;
            display: inline-block;
            padding: 10px 15px;
        }
        .menu-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: rgba(255, 255, 255, 0.2);
            padding: 10px 20px;
            border-radius: 12px;
            margin-bottom: 10px;
        }
        .item-name {
            flex: 1;
            text-align: left;
        }
        .item-controls {
            display: flex;
            align-items: center;
        }
        .button {
            background-color: #ff6f61;
            border: none;
            color: white;
            padding: 10px 15px;
            text-align: center;
            text-decoration: none;
            display: inline-block;
            font-size: 16px;
            margin: 10px 2px;
            cursor: pointer;
            border-radius: 12px;
        }
        .button:hover {
            background-color: #ff3d2e;
        }
        .cart {
            margin-top: 20px;
        }
        .cart-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: rgba(255, 255, 255, 0.2);
            padding: 10px 20px;
            border-radius: 12px;
            margin-bottom: 10px;
        }
        .cart-total {
            font-size: 1.2em;
            margin-top: 10px;
        }
        .hidden {
            display: none;
        }
        .order-placed {
            display: none;
            font-size: 2em;
            margin-top: 20px;
            color: #ff6f61;
            position: relative;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .order-placed-message {
            position: relative;
            text-align: center;
        }
        .confetti {
            position: absolute;
            width: 10px;
            height: 10px;
            display: flex;
            justify-content: center;
            background-color: var(--color);
            animation: confetti-animation 1s ease-out forwards;
            opacity: 0;
        }
        @keyframes confetti-animation {
            0% {
                transform: translate(0, 0) scale(1);
                opacity: 1;
            }
            100% {
                transform: translate(var(--x), var(--y)) scale(0.5);
                opacity: 0;
            }
        }
        .popup {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.8);
            color: #fff;
            padding: 20px;
            border-radius: 12px;
            z-index: 1000;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="restaurant-name first-page">Cafe Vairagi</div>
        <div id="menu">
            <div class="menu-section" id="starters">
                <h3>Starters</h3>
            </div>
            <div class="menu-section" id="main-course">
                <h3>Main Course</h3>
            </div>
            <div class="menu-section" id="biryanis">
                <h3>Biryanis</h3>
            </div>
            <div class="menu-section" id="desserts">
                <h3>Desserts</h3>
            </div>
            <div class="menu-section" id="beverages">
                <h3>Beverages</h3>
            </div>
        </div>
        <div id="cart-page" class="hidden">
            <div class="cart" id="cart">
                <h3>Cart</h3>
            </div>
            <button class="button" onclick="placeOrder()">Place Order</button>
            <button class="button" onclick="goBack()">Back</button>
        </div>
        <button class="button" id="view-cart-btn" onclick="viewCart()">View Cart</button>
        <div id="order-placed-page" class="hidden">
            <div id="order-placed" class="order-placed">
                <div class="order-placed-message">Your Order Placed</div>
            </div>
        </div>
        <div id="order-details-page" class="hidden">
            <div class="container">
                <h3>Ordered Items</h3>
                <div id="ordered-items"></div>
                <button class="button" onclick="orderAgain()">Order Again</button>
            </div>
        </div>
    </div>
    <div id="transparent-popup" class="popup hidden">Please select items.</div>
    <script>
        const menu = {
            starters: [
                { id: 1, name: "Spring Rolls", price: 5.99 },
                { id: 2, name: "Garlic Bread", price: 4.99 },
                { id: 3, name: "Bruschetta", price: 6.49 },
                { id: 4, name: "Stuffed Mushrooms", price: 7.99 },
                { id: 5, name: "Cheese Sticks", price: 5.49 },
                { id: 6, name: "Onion Rings", price: 4.49 },
                { id: 7, name: "Mozzarella Sticks", price: 6.99 },
                { id: 8, name: "Chicken Wings", price: 8.99 },
                { id: 9, name: "Potato Skins", price: 5.99 },
                { id: 10, name: "Spinach Artichoke Dip", price: 7.49 }
            ],
            mainCourse: [
                { id: 11, name: "Grilled Chicken", price: 12.99 },
                { id: 12, name: "Beef Steak", price: 19.99 },
                { id: 13, name: "Salmon Fillet", price: 14.99 },
                { id: 14, name: "Pasta Alfredo", price: 11.99 },
                { id: 15, name: "Vegetable Stir Fry", price: 10.49 },
                { id: 16, name: "BBQ Ribs", price: 16.99 },
                { id: 17, name: "Shrimp Scampi", price: 13.49 },
                { id: 18, name: "Lamb Chops", price: 20.99 },
                { id: 19, name: "Chicken Parmesan", price: 12.49 },
                { id: 20, name: "Beef Tacos", price: 9.99 }
            ],
            biryanis: [
                { id: 21, name: "Chicken Biryani", price: 9.99 },
                { id: 22, name: "Veg Biryani", price: 8.99 },
                { id: 23, name: "Lamb Biryani", price: 11.99 },
                { id: 24, name: "Prawn Biryani", price: 12.49 },
                { id: 25, name: "Mutton Biryani", price: 13.99 },
                { id: 26, name: "Egg Biryani", price: 7.99 },
                { id: 27, name: "Fish Biryani", price: 10.49 },
                { id: 28, name: "Paneer Biryani", price: 9.49 },
                { id: 29, name: "Hyderabadi Biryani", price: 14.49 },
                { id: 30, name: "Dum Biryani", price: 11.49 }
            ],
            desserts: [
                { id: 31, name: "Chocolate Cake", price: 5.99 },
                { id: 32, name: "Ice Cream", price: 3.99 },
                { id: 33, name: "Cheesecake", price: 6.49 },
                { id: 34, name: "Brownies", price: 4.99 },
                { id: 35, name: "Tiramisu", price: 5.49 },
                { id: 36, name: "Apple Pie", price: 4.99 },
                { id: 37, name: "Panna Cotta", price: 6.99 },
                { id: 38, name: "Lemon Tart", price: 5.49 },
                { id: 39, name: "Raspberry Sorbet", price: 4.49 },
                { id: 40, name: "Creme Brulee", price: 7.49 }
            ],
            beverages: [
                { id: 41, name: "Coca Cola", price: 1.99 },
                { id: 42, name: "Lemonade", price: 2.99 },
                { id: 43, name: "Orange Juice", price: 2.49 },
                { id: 44, name: "Iced Tea", price: 2.79 },
                { id: 45, name: "Coffee", price: 1.49 },
                { id: 46, name: "Hot Chocolate", price: 2.59 },
                { id: 47, name: "Milkshake", price: 3.49 },
                { id: 48, name: "Green Tea", price: 1.99 },
                { id: 49, name: "Sprite", price: 1.99 },
                { id: 50, name: "Energy Drink", price: 3.99 }
            ]
        };

        let cart = [];
        let order = [];

        document.addEventListener('DOMContentLoaded', () => {
            displayMenuItems();
        });

        function displayMenuItems() {
            displayItems(menu.starters, 'starters');
            displayItems(menu.mainCourse, 'main-course');
            displayItems(menu.biryanis, 'biryanis');
            displayItems(menu.desserts, 'desserts');
            displayItems(menu.beverages, 'beverages');
        }

        function displayItems(items, sectionId) {
            const section = document.getElementById(sectionId);
            items.forEach(item => {
                const div = document.createElement('div');
                div.className = 'menu-item';
                div.innerHTML = `<span class="item-name">${item.name}</span>
                                 <div class="item-controls">
                                     <button class="button" onclick="decrementItem(${item.id}, '${item.name}', ${item.price})">-</button>
                                     <span id="quantity-${item.id}">0</span>
                                     <button class="button" onclick="addToCart(${item.id}, '${item.name}', ${item.price})">+</button>
                                     <span>₹${item.price.toFixed(2)}</span>`;
                section.appendChild(div);
            });
        }

        function addToCart(id, name, price) {
            const item = cart.find(item => item.id === id);
            if (item) {
                item.quantity++;
            } else {
                cart.push({ id, name, price, quantity: 1 });
            }
            updateQuantity(id);
            displayCart();
        }

        function decrementItem(id, name, price) {
            const item = cart.find(item => item.id === id);
            if (item) {
                item.quantity--;
                if (item.quantity <= 0) {
                    cart = cart.filter(item => item.id !== id);
                }
                updateQuantity(id);
                displayCart();
            }
        }

        function updateQuantity(id) {
            const quantityElement = document.getElementById(`quantity-${id}`);
            if (quantityElement) {
                const item = cart.find(item => item.id === id);
                quantityElement.textContent = item ? item.quantity : 0;
            }
        }

        function displayCart() {
            const cartDiv = document.getElementById('cart');
            cartDiv.innerHTML = '<h3>Cart</h3>';
            cart.forEach(item => {
                const div = document.createElement('div');
                div.className = 'cart-item';
                div.innerHTML = `<span>${item.name} x${item.quantity}</span>
                                 <span>₹${(item.price * item.quantity).toFixed(2)}</span>`;
                cartDiv.appendChild(div);
            });
            const total = cart.reduce((sum, item) => sum + item.price * item.quantity, 0);
            cartDiv.innerHTML += `<div class="cart-total">Total: ₹${total.toFixed(2)}</div>`;
        }

        function viewCart() {
            document.querySelector('.restaurant-name').classList.remove('first-page');
            document.getElementById('menu').style.display = 'none';
            document.getElementById('view-cart-btn').style.display = 'none';
            document.getElementById('cart-page').style.display = 'block';
            displayCart();
        }

        function goBack() {
            document.querySelector('.restaurant-name').classList.add('first-page');
            document.getElementById('menu').style.display = 'block';
            document.getElementById('view-cart-btn').style.display = 'inline-block';
            document.getElementById('cart-page').style.display = 'none';
        }

        function placeOrder() {
            const total = cart.reduce((sum, item) => sum + item.price * item.quantity, 0);
            if (total === 0) {
                const transparentPopup = document.getElementById('transparent-popup');
                transparentPopup.classList.remove('hidden');
                setTimeout(() => {
                    transparentPopup.classList.add('hidden');
                }, 3000); // Hide popup after 3 seconds
                return;
            }

            // Show the order placed message
            document.getElementById('menu').style.display = 'none';
            document.getElementById('view-cart-btn').style.display = 'none';
            document.getElementById('cart-page').style.display = 'none';
            document.getElementById('order-placed-page').style.display = 'block';

            // Create confetti effect
            const orderPlacedDiv = document.getElementById('order-placed');
            orderPlacedDiv.innerHTML = '<div class="order-placed-message">Your Order Placed</div>'; // Reset confetti
            for (let i = 0; i < 100; i++) {
                const confetti = document.createElement('div');
                confetti.className = 'confetti';
                confetti.style.setProperty('--color', getRandomColor());
                confetti.style.setProperty('--x', `${Math.random() * 2000 - 1000}px`);
                confetti.style.setProperty('--y', `${Math.random() * 2000 - 1000}px`);
                orderPlacedDiv.appendChild(confetti);
            }

            // Save the order for the 4th page
            order = [...cart];

            // Reset the cart after placing the order
            cart = [];

            // Display ordered items after a delay of 10 seconds
            setTimeout(() => {
                document.getElementById('order-placed-page').style.display = 'none';
                document.getElementById('order-details-page').style.display = 'block';
                displayOrderedItems();
            }, 10000);
        }

        function displayOrderedItems() {
            const orderedItemsDiv = document.getElementById('ordered-items');
            orderedItemsDiv.innerHTML = '';
            // Display previously ordered items
            order.forEach(item => {
                const div = document.createElement('div');
                div.className = 'cart-item';
                div.innerHTML = `<span>${item.name} x${item.quantity}</span>
                                 <span>₹${(item.price * item.quantity).toFixed(2)}</span>`;
                orderedItemsDiv.appendChild(div);
            });
            const total = order.reduce((sum, item) => sum + item.price * item.quantity, 0);
            orderedItemsDiv.innerHTML += `<div class="cart-total">Total: ₹${total.toFixed(2)}</div>`;
        }

        function orderAgain() {
            document.querySelector('.restaurant-name').classList.add('first-page');
            document.getElementById('menu').style.display = 'block';
            document.getElementById('view-cart-btn').style.display = 'inline-block';
            document.getElementById('cart-page').style.display = 'none';
            document.getElementById('order-details-page').style.display = 'none';
        }

        function getRandomColor() {
            const colors = ['#ff6f61', '#ffcc00', '#66b3ff', '#99ff99', '#ff66b3'];
            return colors[Math.floor(Math.random() * colors.length)];
        }
    </script>
</body>
</html>

# Reader's Heaven — Online Book Shop

A MERN stack bookshop prototype developed for my **2021 bachelor's thesis, “Role-Based Access Control in E-Commerce Web Application,”** at Jiangxi Normal University. The project explores how customer and administrator roles can be enforced in a full-stack e-commerce application. Its emphasis is on authentication, authorization, and the separation of permissions across the API and user interface.

## What the application does

Visitors can browse books and product details. Registered customers can manage a cart, use the sandbox checkout interface, and view their own order history. Administrators can manage products and categories and view order records.

| Capability | Visitor | Customer | Administrator |
| --- | :---: | :---: | :---: |
| Browse products and categories | Yes | Yes | Yes |
| Register and sign in | Yes | Yes | Yes |
| Save a cart and view personal order history | — | Yes | — |
| Manage products, categories, and product images | — | — | Yes |
| View all payment records | — | — | Yes |

New accounts receive the customer role (`role: 0`). An administrator uses `role: 1`, which must be assigned by a trusted operator in the database; there is no admin promotion screen. Express middleware checks the access token and, for administrative routes, the user's role. The React interface also shows pages and controls according to the current role.

## Implementation

- **Frontend:** React, React Router, Axios, and CSS.
- **Backend:** Node.js and Express API with MongoDB and Mongoose models for users, products, categories, and payments.
- **Authentication:** Passwords are hashed with `bcrypt`; JSON Web Tokens are used for access and refresh flows. The refresh token is stored in an HTTP-only cookie.
- **Images and checkout:** Cloudinary handles product image uploads; the checkout UI uses `react-paypal-express-checkout` in sandbox mode.

The source is organized into `client/` for the React app and `models/`, `controllers/`, `routes/`, and `middleware/` for the server. The development client proxies API requests to the Express server.

## Run locally

You need Node.js, npm, a MongoDB instance, and a Cloudinary account if you want to test image uploads.

```bash
git clone https://github.com/M-Tanvir-Islam/WebApp-Online-Book-shop.git
cd WebApp-Online-Book-shop
npm ci
cd client
npm ci
cd ..
cp .env.example .env
```

Edit `.env` to provide your own MongoDB connection, independent random signing secrets, and Cloudinary credentials. The example uses port `5000` and a local MongoDB database. Never commit the populated file.

Start the API and React client in separate terminals:

```bash
npm run dev
```

```bash
cd client
npm start
```

The React development server normally opens at <http://localhost:3000>; its proxy forwards API requests to <http://localhost:5000>. For the sandbox checkout button, replace the placeholder sandbox app ID in `client/src/components/mainpages/cart/PaypalButton.js` with your own PayPal sandbox client ID.

## Access control in the API

| Routes | Access |
| --- | --- |
| `GET /api/products`, `GET /api/category` | Public |
| `POST /user/register`, `POST /user/login` | Public |
| `GET /user/infor`, `PATCH /user/addcart`, `GET /user/history` | Signed-in user |
| `POST /api/payment` | Signed-in user |
| `POST /api/products`, `PUT /api/products/:id`, `DELETE /api/products/:id` | Administrator |
| `POST /api/category`, `PUT /api/category/:id`, `DELETE /api/category/:id` | Administrator |
| `POST /api/upload`, `POST /api/destroy`, `GET /api/payment` | Administrator |

Protected endpoints expect an access token in the `Authorization` header. The UI provides registration, sign-in, product browsing, cart, category management, and order history pages on top of these routes.

## Project scope

This repository is an academic prototype, not a production store. The PayPal component contains placeholder client IDs; the server records payment details supplied by the client and does **not** independently verify transactions with PayPal. It should not be used to accept real orders or payments without that validation and a separate security review.

The repository previously included an environment file with credentials. The tracked file was removed, but its contents remain in the original Git history. Any credentials from that file must be rotated before reusing this project.

**Thesis:** MD Tanvir Islam, *Role-Based Access Control in E-Commerce Web Application*, bachelor's graduation project, Jiangxi Normal University, 2021.

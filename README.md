# 🛒 App Store — Multi-Role Store Management System

A cross-platform **Flutter** application for managing store operations across multiple user roles. Built as a final-year project (PFA), it connects to a **Flask REST API** backend and covers the full lifecycle of products, purchase invoices, suppliers, and customer shopping.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features by Role](#features-by-role)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [API Configuration](#api-configuration)
- [Dependencies](#dependencies)

---

## Overview

**App Store** is a role-based store management mobile/desktop application built with Flutter. Upon login, the app automatically routes each user to a dedicated dashboard based on their assigned role:

| Role | Description |
|----------|--------------------------------------------------------------|
| 👤 Customer | Browse products, manage cart, view account |
| 👷 Employee | Manage inventory, create purchase invoices, run OCR & stock reconciliation |
| 🚚 Supplier | View issued invoices, upload delivery documents |
| 🧑‍💼 Manager | Full oversight: monitor all invoices, confirm/reject deliveries |

---

## Features by Role

### 👤 Customer
- Browse paginated product catalog with search and category filters
- Sort products by recency or price
- Shopping cart management
- Account profile management (name, phone, password)

### 👷 Employee
- View and manage product inventory
- Add new products (name, category, price, stock, image URL)
- Create purchase invoices linked to suppliers
- View all invoices with status filters (`pending`, `confirmed`, `rejected`, `delivered`)
- **OCR Verification** — trigger AI-powered OCR on uploaded delivery documents
- **Stock Reconciliation** — reconcile confirmed quantities with current inventory

### 🚚 Supplier
- View all invoices issued to them
- Filter invoices by status
- Upload delivery documents (PDF) with delivery date and notes
- View per-invoice statistics and delivery history

### 🧑‍💼 Manager
- View all invoices across all suppliers and employees
- Confirm or reject invoices (with optional rejection reason)
- Access a manager dashboard with KPIs

---

## Architecture

The app follows the **MVC (Model-View-Controller)** pattern:

```
lib/
├── main.dart              # Entry point — bootstraps the app & auth session
├── models/                # Data models (AppUser, Product, Supplier, PurchaseInvoice…)
├── controllers/           # Business logic & API calls
├── views/                 # UI screens organized by role
│   ├── auth/              # Login screen
│   ├── customer/          # Customer home & account views
│   ├── employee/          # Employee dashboard
│   ├── supplier/          # Supplier dashboard
│   └── manager/           # Manager dashboard
├── utils/
│   ├── api_provider.dart  # Centralized HTTP client (REST calls)
│   ├── constants.dart     # Base URL & image resolver
│   ├── role_router.dart   # Redirects user to correct home screen by role
│   ├── toast.dart         # Global toast/snackbar utility
│   └── localstorage.dart  # Shared preferences wrapper
└── widgets/               # Reusable UI components
```

### Role Routing Flow

```
App Launch
    └── AuthSession (persisted session)
            ├── Not logged in  → LoginView
            ├── role: customer → HomeView
            ├── role: employee → EmployeeHomeView
            ├── role: supplier → SupplierHomeView
            └── role: manager  → ManagerHomeView
```

---

## Tech Stack

| Layer | Technology |
|------------|--------------------------------------|
| Frontend | Flutter (Dart) — Material Design 3 |
| Backend | Flask (Python) REST API |
| HTTP Client | `package:http` |
| State | `ChangeNotifier` + `setState` |
| Storage | `shared_preferences` |
| Camera | `camera` package |
| File Picker | `file_picker` package |
| OCR | Backend AI service via REST |

---

## Project Structure

```
app_store/
├── lib/
│   ├── controllers/
│   │   ├── auth_controller.dart
│   │   ├── auth_session.dart
│   │   ├── cart_controller.dart
│   │   ├── employee_controller.dart
│   │   ├── manager_controller.dart
│   │   ├── product_controller.dart
│   │   ├── supplier_controller.dart
│   │   └── user_controller.dart
│   ├── models/
│   │   ├── app_user.dart
│   │   ├── manager_dashboard.dart
│   │   ├── product.dart
│   │   ├── purchase_invoice.dart
│   │   └── supplier.dart
│   ├── utils/
│   │   ├── api_provider.dart
│   │   ├── constants.dart
│   │   ├── localstorage.dart
│   │   ├── manager_api_provider.dart
│   │   ├── role_router.dart
│   │   └── toast.dart
│   ├── views/
│   │   ├── auth/login_view.dart
│   │   ├── customer/home_view.dart
│   │   ├── customer/account_view.dart
│   │   ├── employee/employee_home_view.dart
│   │   ├── supplier/supplier_home_view.dart
│   │   └── manager/manager_home_view.dart
│   └── main.dart
├── images/
│   └── logo.png
├── pubspec.yaml
└── README.md
```

---

## Getting Started

### Prerequisites

- [Flutter SDK](https://flutter.dev/docs/get-started/install) `^3.13.2`
- Dart SDK `^3.13.2`
- A running instance of the Flask backend

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/your-username/app_store.git
   cd app_store
   ```

2. **Install dependencies**
   ```bash
   flutter pub get
   ```

3. **Configure the API URL** (see [API Configuration](#api-configuration))

4. **Run the app**
   ```bash
   # Android / iOS
   flutter run

   # Web
   flutter run -d chrome

   # Windows desktop
   flutter run -d windows
   ```

---

## API Configuration

The base URL for the Flask backend is defined in [`lib/utils/constants.dart`](lib/utils/constants.dart):

```dart
class Constants {
  static const String baseUrl = "http://127.0.0.1:5000";
}
```

> ⚠️ Change `baseUrl` to match your backend server address before running on a physical device or deploying.

### Key API Endpoints

| Method | Endpoint | Description |
|--------|------------------------------------------|-------------------------------|
| POST | `/login` | Authenticate user |
| GET | `/products/` | List products (paginated) |
| POST | `/products/` | Add a new product |
| GET | `/products/categories` | Fetch product categories |
| GET | `/users/:id` | Get user profile |
| PUT | `/users/:id` | Update user profile |
| PUT | `/users/:id/password` | Change password |
| GET | `/users/suppliers` | List all suppliers |
| POST | `/invoices/` | Create purchase invoice |
| GET | `/invoices/all` | Get all invoices (manager) |
| GET | `/invoices/supplier/:id` | Get invoices for a supplier |
| PUT | `/invoices/:id/confirm` | Confirm an invoice |
| PUT | `/invoices/:id/reject` | Reject an invoice |
| POST | `/invoices/deliveries` | Upload delivery document |
| POST | `/invoices/:id/verify-ocr` | Run OCR on delivery document |
| POST | `/invoices/:id/reconcile-stock` | Reconcile stock quantities |

---

## Dependencies

```yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^1.6.0               # HTTP client for REST API calls
  shared_preferences: ^2.5.5  # Local session persistence
  camera: ^0.12.0+2           # Camera access for document scanning
  file_picker: ^13.1.0        # File selection for PDF upload
  dropdown_button2: ^3.1.0    # Enhanced dropdown widgets
  cupertino_icons: ^1.0.8     # iOS-style icons
```

---

## Contributing

This project was developed as a **Final Year Project (PFA)**. Contributions and suggestions are welcome.

1. Fork the project
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

<p align="center">Made with ❤️ using Flutter</p>

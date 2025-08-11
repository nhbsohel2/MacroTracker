# MacroTracker

MacroTracker is a **Flask-based full-stack web application** for tracking daily calories and macronutrients.  
It provides **multi-user support**, **per-user data isolation**, and an **admin approval system** for new accounts.  
The app stores all data in a local **SQLite** database and uses **SQLAlchemy ORM** for database operations.

---

## Features

- **Multi-User System**
  - New user registrations require admin approval before login.
  - Per-user data separation — products, meals, and consumptions are private to each account.
  
- **Product & Meal Management**
  - Manual product creation with macros per 100g/mL.
  - CRUD operations on products and meals.
  - Assign consumptions to meals or unassigned snacks.
  
- **Tracking & Visualization**
  - Daily dashboard with macros summary.
  - Meal-wise breakdown of calories, protein, carbs, and fats.
  - History view grouped by date and meal.

- **Administrative Tools**
  - User management: approve, remove, and view all registered users.
  - Automatic reassignment of all existing data to the admin account at initialization.

---

## Technical Details

### **Architecture**
- **Backend:** [Flask](https://flask.palletsprojects.com/) (Python micro-framework)
- **Frontend:** Jinja2 templating engine + HTML5 + CSS3
- **Database:** SQLite (via [SQLAlchemy ORM](https://www.sqlalchemy.org/))
- **Forms & Validation:** Flask request parsing with manual validation logic
- **Authentication:** Session-based login using Werkzeug password hashing
- **Authorization:** Role-based (admin vs. normal users)
- **Data Isolation:** All DB queries filter by `user_id` to ensure separation

### **Key Components**
1. **`app.py`**
   - Initializes Flask application and database.
   - Defines all routes for authentication, product/meal/consumption CRUD, and admin tools.
   - Contains startup logic to:
     - Create an admin account if not found.
     - Migrate or initialize the database schema.
     - Assign all pre-existing data to admin.
     - Remove any other users.
     
2. **`models.py` (inside app.py inlined)**
   - Defines SQLAlchemy ORM models:
     - **User** – with fields: `username`, `full_name`, `email` (unique), `is_admin`, `admin_approved`
     - **Product** – macros per 100g/mL
     - **Meal** – linked to user and date
     - **Consumption** – linking product, meal, and quantity

3. **Templates (`templates/`)**
   - **`layout.html`** – base HTML layout and navigation bar with conditional rendering for logged-in/admin users.
   - **`dashboard.html`** – displays today’s meals and macro breakdown.
   - **`history.html`** – grouped consumption history.
   - **`signup.html`, `login.html`** – authentication forms.
   - **`users.html`, `pending_requests.html`** – admin views.

4. **Static (`static/style.css`)**
   - Modern responsive design for dashboard, forms, and admin panels.
   - Flexbox layout for navigation.
   - Table styling for admin/user data.

5. **Database Layer**
   - **SQLite** file specified by `DATABASE_FILE` env var or defaults to `calorie.db`.
   - Enforces `UNIQUE` constraints on `username` and `email`.
   - Handles `IntegrityError` exceptions to prevent duplicate email/username registration.

### **Security**
- Passwords hashed with Werkzeug’s `generate_password_hash()`.
- Session security via Flask `SECRET_KEY`.
- Unique email & username enforced at both:
  - Application level (pre-commit checks)
  - Database level (`unique=True` constraints + IntegrityError handling)
- Admin-only routes protected via a custom `@admin_required` decorator.

---



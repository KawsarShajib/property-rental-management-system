# PropRental — Property Rental & Management System

A Django-based property rental platform. Property owners list properties;
tenants search, request to rent, and leave reviews once accepted.

## Features

- **Authentication**: register, login, logout, profile update, two roles
  (Property Owner / Tenant) chosen at signup.
- **Property CRUD**: owners create/edit/delete their own listings only,
  enforced at both the view and middleware level.
- **Search & filters**: by location, property type, and min/max rent.
- **Rental requests**: tenants request a property; owners accept/reject;
  tenants cancel pending requests. Business rules enforced:
  - only logged-in tenants can request
  - a tenant can't request their own property
  - only the owner can accept/reject
  - no duplicate pending requests for the same property/tenant
- **Dashboards**: separate stat dashboards for owners and tenants.
- **Reviews**: 1–5 star rating + comment, only after an accepted request,
  one review per tenant per property, shown on the property detail page.
- **Custom middleware** (`accounts/middleware.py`): blocks owner-only and
  tenant-only URLs by role, redirecting with a flash message.
- **Django Admin**: Users, Properties, Rental Requests and Reviews are all
  registered with search, filters, and list-editable fields.

## Project layout

```
property_management/   # project settings & root urls
accounts/               # custom User model, auth views, middleware, dashboards
properties/              # Property model, CRUD, search/filter, listing
rentals/                 # RentalRequest & Review models, request workflow
templates/               # all HTML templates (Bootstrap 5)
static/css/style.css     # custom styling
media/                   # uploaded images (property photos, profile pics)
```

## Setup

```bash
python3 -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt

python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

## Login with Users created :

| Username | Password  | Notes                  |
|----------|-----------|------------------------|
| admin    | admin  | Superuser |
| kawsar    | Sonali@123   | Sample user |
| tenant    | User@123   | Sample user |
| koushik    | Owner@123   | Sample user |
| anonymous  | Tenant@123  | Sample user |

Visit `http://127.0.0.1:8000/` to browse properties, `/admin/` for the
admin site, `/accounts/register/` to create an owner or tenant account.

## Notes on design choices

- **Custom User model** (`accounts.User`) extends `AbstractUser` with a
  `role` field rather than using Groups/Permissions, since the two roles
  (Owner/Tenant) map directly onto distinct dashboards and workflows.
- **Two layers of authorization**: view-level checks (e.g. "only the
  property's owner may edit it") plus a request-level middleware that
  blocks whole URL prefixes by role — demonstrating Django middleware
  alongside standard permission checks.
- **RentalRequest.status** uses `PENDING / ACCEPTED / REJECTED / CANCELLED`
  so cancelled requests are kept for history rather than deleted.
- SQLite is used by default for simplicity; swap `DATABASES` in
  `property_management/settings.py` for Postgres/MySQL in production, and
  set a real `SECRET_KEY` via an environment variable before deploying.

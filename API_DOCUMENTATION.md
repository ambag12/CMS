# Django School CMS - API Documentation

## Quick Fix Applied ✅

**Issue Fixed:** `TemplateDoesNotExist: django_filters/rest_framework/form.html`

**Solution:** Added `django_filters` to `INSTALLED_APPS` in `settings.py`

The server should now work correctly. The error was occurring because the DRF browsable API was trying to render filter forms but couldn't find the templates.

---

## API Base URL

- **Development:** `http://localhost:8000`
- **API Prefix:** `/api/users/`

---

## Authentication

This API uses **JWT (JSON Web Token)** authentication.

### Login

**Endpoint:** `POST /api/users/login/`

**Request Body:**
```json
{
    "email": "admin@example.com",
    "password": "yourpassword"
}
```

**Response:**
```json
{
    "token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
    "id": 1,
    "email": "admin@example.com",
    "username": "Admin User",
    "first_name": "Admin",
    "last_name": "User",
    "isAdmin": true,
    "isAccountant": false,
    "isTeacher": false,
    "isParent": false
}
```

### Using the Token

Include the token in the Authorization header for all protected endpoints:

```
Authorization: Bearer <your_token_here>
```

---

## API Endpoints Overview

### Users (`/api/users/`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/users/` | List all users (excluding parents) | ❌ |
| POST | `/users/` | Create a new user | ❌ |
| GET | `/users/{id}/` | Get user by ID | ✅ |
| PUT | `/users/{id}/` | Update user | ✅ |
| DELETE | `/users/{id}/` | Delete user | ✅ |
| GET | `/profile/` | Get current user profile | ✅ |

**Filtering:** You can filter users by `first_name`, `middle_name`, and `last_name`:
- `/api/users/users/?first_name=john`
- `/api/users/users/?last_name=doe`

### Teachers (`/api/users/teachers/`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/teachers/` | List all teachers | ❌ |
| POST | `/teachers/` | Create a new teacher | ❌ |
| GET | `/teachers/{id}/` | Get teacher by ID | ✅ |
| PUT | `/teachers/{id}/` | Update teacher | ✅ |
| DELETE | `/teachers/{id}/` | Delete teacher | ✅ |
| POST | `/teachers/bulk-upload/` | Bulk upload teachers from Excel | ❌ |

**Filtering:** You can filter teachers by `first_name`, `middle_name`, and `last_name`:
- `/api/users/teachers/?first_name=sarah`

### Accountants (`/api/users/accountants/`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/accountants/` | List all accountants | ❌ |
| POST | `/accountants/` | Create a new accountant | ❌ |
| GET | `/accountants/{id}/` | Get accountant by ID | ✅ |
| PUT | `/accountants/{id}/` | Update accountant | ✅ |
| DELETE | `/accountants/{id}/` | Delete accountant | ✅ |

### Parents (`/api/users/parents/`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/parents/` | List all parents | ❌ |
| POST | `/parents/` | Create a new parent | ❌ |
| GET | `/parents/{id}/` | Get parent by ID | ✅ |
| PUT | `/parents/{id}/` | Update parent | ✅ |
| DELETE | `/parents/{id}/` | Delete parent | ✅ |

**Filtering:** You can filter parents by `first_name`, `middle_name`, and `last_name`:
- `/api/users/parents/?first_name=mary`

---

## Sample Data for API Calls

### 1. Create User

```json
{
    "email": "newuser@example.com",
    "password": "SecurePass123",
    "first_name": "John",
    "middle_name": "Michael",
    "last_name": "Doe",
    "phone_number": "+255712345678"
}
```

### 2. Create Teacher

```json
{
    "first_name": "sarah",
    "middle_name": "elizabeth",
    "last_name": "johnson",
    "email": "sarah.johnson@school.com",
    "phone_number": "+255713456789",
    "empId": "TCH20250001",
    "short_name": "SJ",
    "subject_specialization": ["mathematics", "physics"],
    "address": "123 main street, dar es salaam",
    "gender": "female",
    "date_of_birth": "1985-05-15",
    "salary": 800000,
    "national_id": "19850515-12345-67890-12",
    "nssf_number": "NS1234567",
    "tin_number": "TIN123456"
}
```

**Important Notes for Teachers:**
- `subject_specialization` must be an **array of subject names** that already exist in the database
- Subjects should be in lowercase
- A corresponding `CustomUser` account is automatically created when a teacher is created
- Default password format: `Complex.{last 4 digits of empId}`
- Example: If `empId` is "TCH20250001", password will be "Complex.0001"

### 3. Create Accountant

```json
{
    "first_name": "james",
    "middle_name": "robert",
    "last_name": "williams",
    "email": "james.williams@school.com",
    "phone_number": "+255714567890",
    "empId": "ACC20250001",
    "address": "789 finance street, dar es salaam",
    "gender": "male",
    "date_of_birth": "1980-08-20",
    "salary": 1200000,
    "national_id": "19800820-12345-67890-13",
    "nssf_number": "NS7654321",
    "tin_number": "TIN654321"
}
```

**Important Notes for Accountants:**
- A corresponding `CustomUser` account is automatically created
- Default password format: `Complex.{last 4 digits of empId}`
- User is automatically added to "accountant" group
- Email and phone number must be unique

### 4. Create Parent

```json
{
    "first_name": "mary",
    "middle_name": "ann",
    "last_name": "smith",
    "email": "mary.smith@example.com",
    "phone_number": "+255715678901",
    "address": "456 parent avenue, dar es salaam",
    "gender": "female",
    "date_of_birth": "1975-03-10"
}
```

**Important Notes for Parents:**
- A corresponding `CustomUser` account is automatically created
- Email and phone number must be unique
- Parent response includes `children_details` with list of associated children

---

## Bulk Upload Teachers (Excel)

**Endpoint:** `POST /api/users/teachers/bulk-upload/`

**Content-Type:** `multipart/form-data`

**Required Excel Columns:**
1. `first_name`
2. `middle_name`
3. `last_name`
4. `phone_number`
5. `employment_id`
6. `short_name`
7. `subject_specialization` (comma-separated subject names)
8. `address`
9. `gender`
10. `date_of_birth`
11. `salary`

**Excel Example:**

| first_name | middle_name | last_name | phone_number | employment_id | short_name | subject_specialization | address | gender | date_of_birth | salary |
|------------|-------------|-----------|--------------|---------------|------------|----------------------|---------|--------|---------------|--------|
| sarah | elizabeth | johnson | +255713456789 | TCH20250001 | SJ | mathematics,physics | 123 main st | female | 1985-05-15 | 800000 |
| john | michael | doe | +255713456790 | TCH20250002 | JD | chemistry | 456 oak st | male | 1982-03-20 | 750000 |

**Response:**
```json
{
    "message": "2 teachers successfully uploaded.",
    "not_created": []
}
```

---

## Field Validations

### Common Validations
- **Email:** Must be unique and valid email format
- **Phone Number:** Must be unique
- **Gender:** "male" or "female"
- **Date of Birth:** Format: "YYYY-MM-DD"

### Teacher-Specific
- **Subject Specialization:** Must reference existing subjects in the database
- **Employment ID (empId):** Must be unique, max 8 characters
- **Short Name:** Typically 2-3 uppercase letters

### Accountant-Specific
- **NSSF Number:** 9 characters
- **TIN Number:** 9 characters
- **Employment ID (empId):** Must be unique, max 8 characters

---

## Error Responses

### 400 Bad Request
```json
{
    "email": ["A teacher with this email already exists."],
    "phone_number": ["A teacher with this phone number already exists."]
}
```

### 401 Unauthorized
```json
{
    "detail": "Authentication credentials were not provided."
}
```

### 404 Not Found
```json
{
    "detail": "Not found."
}
```

### 500 Internal Server Error
```json
{
    "error": "Server Error",
    "detail": "An unexpected error occurred on the server."
}
```

---

## Using the Postman Collection

1. **Import the Collection:**
   - Open Postman
   - Click "Import"
   - Select the `POSTMAN_API_Collection.json` file
   - Click "Import"

2. **Set Environment Variables:**
   - The collection includes two variables:
     - `base_url`: Default is `http://localhost:8000`
     - `access_token`: Auto-populated after login

3. **Login First:**
   - Run the "Authentication > Login" request
   - The token will be automatically saved to the `access_token` variable
   - All subsequent authenticated requests will use this token

4. **Test the APIs:**
   - Browse through the folders (Users, Teachers, Accountants, Parents)
   - Modify the sample data as needed
   - Send requests to test the API

---

## Testing with cURL

### Login
```bash
curl -X POST http://localhost:8000/api/users/login/ \
  -H "Content-Type: application/json" \
  -d '{"email": "admin@example.com", "password": "yourpassword"}'
```

### List Teachers
```bash
curl -X GET http://localhost:8000/api/users/teachers/
```

### Create Teacher
```bash
curl -X POST http://localhost:8000/api/users/teachers/ \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -d '{
    "first_name": "sarah",
    "last_name": "johnson",
    "email": "sarah.johnson@school.com",
    "subject_specialization": ["mathematics"],
    "empId": "TCH001",
    "short_name": "SJ",
    "salary": 800000
  }'
```

---

## Notes

- **Password Generation:** For teachers, accountants, and parents, passwords are auto-generated using the format `Complex.{last 4 digits of empId}` or `Complex.0000` as fallback
- **User Groups:** Users are automatically added to appropriate groups (teacher, accountant, parent)
- **Related Records:** Creating a teacher/accountant/parent automatically creates a corresponding `CustomUser` record
- **Filtering:** All list endpoints support case-insensitive filtering by name fields
- **Pagination:** Not currently enabled, but can be added if needed

---

## Troubleshooting

### Issue: "TemplateDoesNotExist: django_filters/rest_framework/form.html"
**Solution:** This has been fixed by adding `django_filters` to `INSTALLED_APPS`

### Issue: "Subject does not exist"
**Solution:** Make sure the subjects referenced in `subject_specialization` exist in the database first

### Issue: "Email already exists"
**Solution:** Each email must be unique across all users

### Issue: Authentication required
**Solution:** Make sure to include the Authorization header: `Authorization: Bearer <token>`

# Django School CMS - Quick Start Guide

## ✅ Issue Fixed

The `TemplateDoesNotExist` error has been resolved by adding `django_filters` to `INSTALLED_APPS` in `settings.py`.

Your API endpoints should now work properly!

---

## 📁 New Files Created

1. **POSTMAN_API_Collection.json** - Complete Postman collection with all API endpoints
2. **API_DOCUMENTATION.md** - Comprehensive API documentation
3. **TEACHER_BULK_UPLOAD_TEMPLATE.md** - Guide for bulk uploading teachers
4. **QUICK_START.md** - This file

---

## 🚀 Quick Test

### 1. Test if server is running:

```bash
curl http://localhost:8000/api/users/users/
```

Expected response: `[]` (empty array if no users exist)

### 2. Import Postman Collection:

1. Open Postman
2. Click **Import** button
3. Select `POSTMAN_API_Collection.json`
4. The collection will appear in your sidebar

### 3. Test an API endpoint:

**Using Postman:**
- Navigate to: `Users > List Users`
- Click **Send**
- You should see a list of users (or empty array)

**Using cURL:**
```bash
curl -X GET http://localhost:8000/api/users/users/
```

---

## 📋 Available API Endpoints

### Base URL: `http://localhost:8000/api/users/`

| Resource | Endpoints |
|----------|-----------|
| **Auth** | `/login/`, `/profile/` |
| **Users** | `/users/`, `/users/{id}/` |
| **Teachers** | `/teachers/`, `/teachers/{id}/`, `/teachers/bulk-upload/` |
| **Accountants** | `/accountants/`, `/accountants/{id}/` |
| **Parents** | `/parents/`, `/parents/{id}/` |

---

## 🔐 Authentication Flow

### Step 1: Login

```bash
curl -X POST http://localhost:8000/api/users/login/ \
  -H "Content-Type: application/json" \
  -d '{
    "email": "your-email@example.com",
    "password": "your-password"
  }'
```

**Response:**
```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "id": 1,
  "email": "your-email@example.com",
  "username": "Your Name",
  ...
}
```

### Step 2: Use the token for protected endpoints

```bash
curl -X GET http://localhost:8000/api/users/profile/ \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

---

## 📝 Sample API Requests

### Create a Teacher

```bash
curl -X POST http://localhost:8000/api/users/teachers/ \
  -H "Content-Type: application/json" \
  -d '{
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
  }'
```

### Create an Accountant

```bash
curl -X POST http://localhost:8000/api/users/accountants/ \
  -H "Content-Type: application/json" \
  -d '{
    "first_name": "james",
    "middle_name": "robert",
    "last_name": "williams",
    "email": "james.williams@school.com",
    "phone_number": "+255714567890",
    "empId": "ACC20250001",
    "address": "789 finance street, dar es salaam",
    "gender": "male",
    "date_of_birth": "1980-08-20",
    "salary": 1200000
  }'
```

### Create a Parent

```bash
curl -X POST http://localhost:8000/api/users/parents/ \
  -H "Content-Type: application/json" \
  -d '{
    "first_name": "mary",
    "middle_name": "ann",
    "last_name": "smith",
    "email": "mary.smith@example.com",
    "phone_number": "+255715678901",
    "address": "456 parent avenue, dar es salaam",
    "gender": "female",
    "date_of_birth": "1975-03-10"
  }'
```

### Filter Teachers by Name

```bash
curl -X GET "http://localhost:8000/api/users/teachers/?first_name=sarah"
```

---

## 🔍 Browsable API

Django REST Framework provides a browsable API interface. Visit any endpoint in your browser:

- http://localhost:8000/api/users/users/
- http://localhost:8000/api/users/teachers/
- http://localhost:8000/api/users/accountants/
- http://localhost:8000/api/users/parents/

You can interact with the API directly from your browser!

---

## 📊 Using the Bulk Upload Feature

### For Teachers:

1. Create an Excel file with the required columns (see `TEACHER_BULK_UPLOAD_TEMPLATE.md`)
2. Upload via Postman:
   - Method: POST
   - URL: `http://localhost:8000/api/users/teachers/bulk-upload/`
   - Body: form-data
   - Key: `file` (type: File)
   - Value: Select your Excel file

**Or using cURL:**
```bash
curl -X POST http://localhost:8000/api/users/teachers/bulk-upload/ \
  -F "file=@teachers_upload.xlsx"
```

---

## 🎯 Key Features

### Auto-Generated User Accounts

When you create:
- **Teacher** → CustomUser account auto-created with `is_teacher=True`
- **Accountant** → CustomUser account auto-created with `is_accountant=True`
- **Parent** → CustomUser account auto-created with `is_parent=True`

### Auto-Generated Passwords

For teachers, accountants, and parents:
- Format: `Complex.{last 4 digits of empId}`
- Example: If empId = "TCH20250001", password = "Complex.0001"
- Fallback: `Complex.0000` if empId is not available

### Auto-Generated Emails (Bulk Upload Only)

For bulk teacher uploads:
- Format: `{first_name}.{last_name}@hayatul.com`
- Example: sarah.johnson@hayatul.com

---

## 🔧 Filtering Options

All list endpoints support filtering:

### Users
- `/api/users/users/?first_name=john`
- `/api/users/users/?last_name=doe`
- `/api/users/users/?middle_name=michael`

### Teachers
- `/api/users/teachers/?first_name=sarah`
- `/api/users/teachers/?last_name=johnson`

### Parents
- `/api/users/parents/?first_name=mary`
- `/api/users/parents/?last_name=smith`

---

## ⚠️ Important Notes

### For Teachers:
- `subject_specialization` must reference **existing subjects** in the database
- Use an array: `["mathematics", "physics"]`
- Subject names must be in **lowercase**

### Unique Constraints:
- **Email** must be unique across all users
- **Phone number** must be unique
- **Employment ID (empId)** must be unique

### Required Fields:
Vary by endpoint - check `API_DOCUMENTATION.md` for details

---

## 📚 Documentation Files

| File | Description |
|------|-------------|
| `API_DOCUMENTATION.md` | Complete API reference |
| `POSTMAN_API_Collection.json` | Postman collection |
| `TEACHER_BULK_UPLOAD_TEMPLATE.md` | Bulk upload guide |
| `QUICK_START.md` | This quick start guide |

---

## 🐛 Troubleshooting

### Server not responding?
```bash
# Check if server is running
ps aux | grep "manage.py runserver"

# Restart if needed
python manage.py runserver
```

### "Subject does not exist" error?
Make sure subjects are created in the database first before creating teachers.

### "Email already exists" error?
Each email must be unique. Use a different email address.

### Still getting template errors?
The fix has been applied to `settings.py`. You may need to restart the development server:
1. Stop the server (Ctrl+C)
2. Run: `python manage.py runserver`

---

## ✨ Next Steps

1. **Import the Postman collection** to test all endpoints
2. **Read the full documentation** in `API_DOCUMENTATION.md`
3. **Test creating a teacher** with the sample data provided
4. **Try the filtering** feature with query parameters
5. **Test bulk upload** with the Excel template

---

## 📞 Need Help?

- Check `API_DOCUMENTATION.md` for detailed endpoint information
- Review `TEACHER_BULK_UPLOAD_TEMPLATE.md` for bulk upload specifics
- Test with Postman collection for interactive API exploration

Happy coding! 🚀

# Teacher Bulk Upload Excel Template

## Instructions

This template is for bulk uploading teachers via the API endpoint: `POST /api/users/teachers/bulk-upload/`

## Required Columns (in exact order)

1. **first_name** - Teacher's first name (lowercase)
2. **middle_name** - Teacher's middle name (lowercase)
3. **last_name** - Teacher's last name (lowercase)
4. **phone_number** - Phone number (e.g., +255712345678)
5. **employment_id** - Unique employment ID (max 8 characters)
6. **short_name** - Short name/initials (uppercase, e.g., SJ)
7. **subject_specialization** - Comma-separated subject names (must exist in database)
8. **address** - Full address (lowercase)
9. **gender** - Either "male" or "female"
10. **date_of_birth** - Format: YYYY-MM-DD
11. **salary** - Numeric salary amount

## Sample Data

```
first_name,middle_name,last_name,phone_number,employment_id,short_name,subject_specialization,address,gender,date_of_birth,salary
sarah,elizabeth,johnson,+255713456789,TCH00001,SJ,"mathematics,physics",123 main street dar es salaam,female,1985-05-15,800000
john,michael,doe,+255713456790,TCH00002,JD,chemistry,456 oak avenue dar es salaam,male,1982-03-20,750000
mary,ann,williams,+255713456791,TCH00003,MW,"biology,chemistry",789 science road dar es salaam,female,1988-11-25,820000
robert,james,brown,+255713456792,TCH00004,RB,"english,literature",321 language lane dar es salaam,male,1980-07-10,900000
emily,grace,davis,+255713456793,TCH00005,ED,"history,geography",654 social studies street dar es salaam,female,1987-09-18,780000
```

## Important Notes

1. **Email Auto-Generation:** 
   - Emails are automatically generated: `{first_name}.{last_name}@hayatul.com`
   - Example: sarah.johnson@hayatul.com

2. **Password Auto-Generation:**
   - Passwords follow the format: `Complex.{last 4 digits of employment_id}`
   - Example: If employment_id is "TCH00001", password will be "Complex.0001"

3. **Subject Validation:**
   - All subjects in `subject_specialization` must already exist in the database
   - Use exact subject names (case-sensitive, lowercase)
   - Separate multiple subjects with commas (no spaces after commas)

4. **Uniqueness:**
   - `employment_id` must be unique
   - Auto-generated `email` must be unique (derived from first_name and last_name)
   - `phone_number` must be unique

5. **User Account:**
   - A corresponding CustomUser account is automatically created for each teacher
   - Teachers are automatically added to the "teacher" group

6. **Response:**
   - The API returns:
     - Number of successfully created teachers
     - List of rows that failed with error messages

## Excel File Format

Create an Excel file (.xlsx) with:
- First row: Column headers (exactly as listed above)
- Subsequent rows: Teacher data
- Save as: `teachers_upload.xlsx`

## Example Excel Structure

| first_name | middle_name | last_name | phone_number | employment_id | short_name | subject_specialization | address | gender | date_of_birth | salary |
|------------|-------------|-----------|--------------|---------------|------------|----------------------|---------|--------|---------------|--------|
| sarah | elizabeth | johnson | +255713456789 | TCH00001 | SJ | mathematics,physics | 123 main street dar es salaam | female | 1985-05-15 | 800000 |
| john | michael | doe | +255713456790 | TCH00002 | JD | chemistry | 456 oak avenue dar es salaam | male | 1982-03-20 | 750000 |

## Common Errors

### "Email already exists"
- The combination of first_name and last_name generates a duplicate email
- Change either first_name or last_name to make it unique

### "Phone number already exists"
- Use a different phone number

### "Subject does not exist"
- Make sure all subjects are created in the database first
- Check spelling and case (should be lowercase)

### "Employment ID already exists"
- Use a unique employment ID for each teacher

## Testing the Upload

### Using Postman:
1. Set request type to POST
2. URL: `http://localhost:8000/api/users/teachers/bulk-upload/`
3. Body type: form-data
4. Key: `file` (type: File)
5. Value: Select your Excel file
6. Send the request

### Using cURL:
```bash
curl -X POST http://localhost:8000/api/users/teachers/bulk-upload/ \
  -F "file=@teachers_upload.xlsx"
```

## Success Response Example

```json
{
    "message": "5 teachers successfully uploaded.",
    "not_created": []
}
```

## Partial Success Response Example

```json
{
    "message": "3 teachers successfully uploaded.",
    "not_created": [
        {
            "first_name": "john",
            "last_name": "doe",
            "email": "john.doe@hayatul.com",
            "error": "Email 'john.doe@hayatul.com' already exists."
        },
        {
            "first_name": "mary",
            "last_name": "smith",
            "subject_specialization": "invalid_subject",
            "error": "Subject 'invalid_subject' does not exist."
        }
    ]
}
```

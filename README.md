# การปรับใช้ Django บน Vercel

คู่มือนี้อธิบายขั้นตอนการปรับใช้โปรเจกต์ Django บน Vercel

## ข้อกำหนดเบื้องต้น

- Python 3.12+
- Django
- บัญชี Vercel

## ขั้นตอนการปรับใช้

### 1. แก้ไข `wsgi.py`

ในไฟล์ `./project/wsgi.py` ให้แน่ใจว่าคุณมีโค้ดดังต่อไปนี้:

```python
from django.core.wsgi import get_wsgi_application

application = get_wsgi_application()

app = get_wsgi_application()  # เพิ่มบรรทัดนี้
```

### 2. กำหนดค่า `settings.py`

- **Allow Hosts**

  อัปเดต `ALLOWED_HOSTS` ในไฟล์ `./project/settings.py`:

```python
ALLOWED_HOSTS = ["127.0.0.1", ".vercel.app"]
```

- **คอมเมนต์ `DATABASES`**

  คอมเมนต์การกำหนดค่าของ `DATABASES`:

```python
# DATABASES = {
#     'default': {
#         'ENGINE': 'django.db.backends.sqlite3',
#         'NAME': BASE_DIR / 'db.sqlite3',
#     }
# }
```

- **การกำหนดค่าไฟล์สแตติก**

  กำหนดค่าไฟล์สแตติก:

```python
STATIC_URL = 'static/'
STATICFILES_DIRS = [Path.joinpath(BASE_DIR, 'static')]
STATIC_ROOT = Path.joinpath(BASE_DIR, 'staticfiles_build', 'static')
```

### 3. สร้าง `build_files.sh`

สร้างไฟล์ที่ชื่อว่า `build_files.sh` พร้อมเนื้อหาดังนี้:

```sh
echo "Building the project..."
python3.12 -m pip install -r requirements.txt

echo "Collect Static..."
python3.12 manage.py collectstatic --noinput --clear
```

### 4. สร้าง `vercel.json`

สร้างไฟล์ที่ชื่อว่า `vercel.json` ด้วยเนื้อหาดังนี้ เปลี่ยน `<project_name>` เป็นชื่อโปรเจกต์ Django ของคุณ:

```json
{
    "version": 2,
    "builds": [
    {
        "src": "<project_name>/wsgi.py",
        "use": "@vercel/python"
    },
    {
        "src": "build_files.sh",
        "use": "@vercel/static-build",
        "config": {
        "distDir": "staticfiles_build"
        }
    }
    ],
    "routes": [
    {
        "src": "/static/(.*)",
        "dest": "/static/$1"
    },
    {
        "src": "/(.*)",
        "dest": "<project_name>/wsgi.py"
    }
    ]
}
```

### 5. สร้าง `requirements.txt`

สร้างไฟล์ที่ชื่อว่า `requirements.txt` ด้วยเนื้อหาดังนี้:

```txt
Django
```

## การปรับใช้

1. ดันการเปลี่ยนแปลงของคุณไปยังรีโพซิทอรี Git ของคุณ
2. เชื่อมต่อรีโพซิทอรีของคุณกับ Vercel
3. Vercel จะทำการสร้างและปรับใช้โปรเจกต์ Django ของคุณโดยอัตโนมัติ

## การแก้ปัญหา

- ตรวจสอบให้แน่ใจว่าคุณใช้เวอร์ชันของ Python และ Django ที่ถูกต้อง
- ตรวจสอบบันทึกของ Vercel สำหรับข้อผิดพลาดในการปรับใช้
- ตรวจสอบการกำหนดค่าใน `vercel.json` และ `settings.py`

---
# Django Vercel Deployment

This guide explains how to deploy a Django project on Vercel.

## Prerequisites

- Python 3.12+
- Django
- Vercel account

## Deployment Steps

### 1. Modify `wsgi.py`

In `./project/wsgi.py`, ensure you have the following code:

```python
from django.core.wsgi import get_wsgi_application

application = get_wsgi_application()

app = get_wsgi_application()  # Add this line
```

### 2. Configure `settings.py`

- **Allow Hosts**

  Update `ALLOWED_HOSTS` in `./project/settings.py`:

```python
ALLOWED_HOSTS = ["127.0.0.1", ".vercel.app"]
```

- **Comment Out `DATABASES`**

  Comment out the `DATABASES` configuration:

```python
# DATABASES = {
#     'default': {
#         'ENGINE': 'django.db.backends.sqlite3',
#         'NAME': BASE_DIR / 'db.sqlite3',
#     }
# }
```

- **Static Files Configuration**

  Configure static files settings:

```python
STATIC_URL = 'static/'
STATICFILES_DIRS = [Path.joinpath(BASE_DIR, 'static')]
STATIC_ROOT = Path.joinpath(BASE_DIR, 'staticfiles_build', 'static')
```

### 3. Create `build_files.sh`

Create a file named `build_files.sh` with the following content:

```sh
echo "Building the project..."
python3.12 -m pip install -r requirements.txt

echo "Collect Static..."
python3.12 manage.py collectstatic --noinput --clear
```

### 4. Create `vercel.json`

Create a file named `vercel.json` with the following content. Replace `<project_name>` with your Django project name:

```json
{
    "version": 2,
    "builds": [
    {
        "src": "<project_name>/wsgi.py",
        "use": "@vercel/python"
    },
    {
        "src": "build_files.sh",
        "use": "@vercel/static-build",
        "config": {
        "distDir": "staticfiles_build"
        }
    }
    ],
    "routes": [
    {
        "src": "/static/(.*)",
        "dest": "/static/$1"
    },
    {
        "src": "/(.*)",
        "dest": "<project_name>/wsgi.py"
    }
    ]
}
```

### 5. Create `requirements.txt`

Create a `requirements.txt` file with the following content:

```txt
Django
```

## Deploying

1. Push your changes to your Git repository.
2. Connect your repository to Vercel.
3. Vercel will automatically build and deploy your Django project.

## Troubleshooting

- Ensure Python and Django versions are correct.
- Check Vercel logs for deployment errors.
- Verify the configuration of `vercel.json` and `settings.py`.

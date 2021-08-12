# django跨域解决方案

1. 安装`django-cors-headers`

   

2. settings.py文件中配置：

```python
INSTALLED_APPS = [
    ......
    ......
    'corsheaders',
]

MIDDLEWARE = [
    ......
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
]
CORS_ALLOW_CREDENTIALS = True
CORS_ORIGIN_ALLOW_ALL = True


CORS_ALLOW_METHODS = (
  'DELETE',
  'GET',
  'OPTIONS',
  'PATCH',
  'POST',
  'PUT',
  'VIEW',
)
CORS_ALLOW_HEADERS = (
  'XMLHttpRequest',
  'X_FILENAME',
  'accept-encoding',
  'authorization',
  'content-type',
  'dnt',
  'origin',
  'user-agent',
  'x-csrftoken',
  'x-requested-with',
  'Pragma',
)
```
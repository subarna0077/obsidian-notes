# 🔑 JWT Authentication in Django REST Framework

## 📌 What is JWT?

**JWT** (JSON Web Token) is a compact, self-contained way of securely transmitting information between parties as a JSON object.

- Used for **stateless authentication**.
- Commonly used in REST APIs.
- Consists of three parts: **Header.Payload.Signature**

Example:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9  
.eyJ1c2VyX2lkIjoxLCJ1c2VybmFtZSI6InRlc3QifQ  
.sflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c


---

## ⚙️ How to Use JWT in DRF

### 1️⃣ Install package
```bash
pip install djangorestframework-simplejwt
```

### 2️⃣ Add to `settings.py`

``` python

INSTALLED_APPS = ['rest_framework','rest_framework_simplejwt',]  
REST_FRAMEWORK = {
	'DEFAULT_AUTHENTICATION_CLASSES': (  
		'rest_framework_simplejwt.authentication.JWTAuthentication',     
		)}
```

### 3️⃣ Add Token URLs in `urls.py`

``` python
from rest_framework_simplejwt.views import
(TokenObtainPairView,TokenRefreshView)  
urlpatterns = [path('api/token/', TokenObtainPairView.as_view(),
name='token_obtain_pair'),     
path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'), ] 
```

## 4️⃣ Get Access & Refresh Token

Request:
``` python
POST /api/token/
Content-Type: application/json

{
    "username": "test",
    "password": "test"
}

```

Response:
```json
{
    "access": "<ACCESS_TOKEN>",
    "refresh": "<REFRESH_TOKEN>"
}

```

## 5️⃣ Use Access Token in Requests

```http
GET /api/some-protected-route/
Authorization: Bearer <ACCESS_TOKEN>

```

## 6️⃣ Refresh Token
```http 
POST /api/token/refresh/
Content-Type: application/json

{
    "refresh": "<REFRESH_TOKEN>"
}

```

## Key Points

- **Access token** → Short-lived, used for most requests.
    
- **Refresh token** → Long-lived, used to get a new access token.
    
- Server does **not store session** → JWT is stateless.
    
- Keep tokens **secure** (store in `httpOnly` cookies or secure storage)


## 📎 Related Concepts

- [[Authentication in DRF]]
    
- [[Permission Classes]]
    
- [[Token Based Authentication]]
    
- [[Session Authentication vs JWT]]
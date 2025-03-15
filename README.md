# ThungThongPOS API Documentation (เอกสารประกอบ API ของ ThungThongPOS)

This document provides a comprehensive guide to the ThungThongPOS API, detailing all available endpoints and their usage. This API allows developers to interact with the ThungThongPOS system, managing various aspects such as users, tables, menus, orders, and more.

## Authentication (การยืนยันตัวตน)

The ThungThongPOS API uses JSON Web Tokens (JWT) for authentication. To access protected endpoints, you must include a valid JWT in the `Authorization` header of your HTTP request. The format is as follows:

```
Authorization: Bearer <your_token>
```

Replace `<your_token>` with the actual JWT you obtained. You can obtain a token by sending a POST request to the `/api/v1/users/auth/login` endpoint (details on this endpoint are provided later in this document).  Many endpoints also require specific permissions, which are checked after a valid token is provided.  These required permissions are noted in the documentation for each endpoint.

### User Authentication (การยืนยันตัวตนผู้ใช้งาน)

#### Endpoint: `/users/auth/register`

**Method:** POST

**Description:** (THAI: ลงทะเบียนผู้ใช้งานใหม่. **(Requires Authentication - Only Admin)**)

**Request:**

```json
{
    "email": "string",
    "username": "string",
    "name": "string",
    "phone": "string",
    "affiliate_merchant_id": "string",
    "pos_pin": "string",
    "password": "string"
}
```

**Response:**

```json
{
    "username": "string",
    "status": "string",
    "registeredAt": "string"
}
```
---
#### Endpoint: `/users/auth/login`

**Method:** POST

**Description:** (THAI: เข้าสู่ระบบ (ชื่อผู้ใช้/รหัสผ่าน).)

**Request:**
* `username`: string (Form data)
* `password`: string (Form data)
* `grant_type`: string (Form data, must be "password")
* `scope`: string (Form data, represents merchant_id)

**Response:**

```json
{
    "token": "string",
    "refreshToken": "string",
    "tokenType": "string",
    "expiredIn": "string"
}
```
---
#### Endpoint: `/users/auth/pos-login`

**Method:** POST

**Description:** (THAI: เข้าสู่ระบบด้วย PIN (สำหรับ POS).)

**Request:**
* `PIN`: string (Form data)
* `merchant_id`: string (Form data)

**Response:**

```json
{
    "token": "string",
    "refreshToken": "string",
    "tokenType": "string",
    "expiredIn": "string"
}
```
---

#### Endpoint: `/users/auth/me`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลผู้ใช้ปัจจุบัน)

**Request:**

*   `token`: string (Header: `Authorization: Bearer <token>`)

**Response:**
```json
{
  "sub": "string",
  "permissions": [],
  "exp": "number"
}
```
---

---

# Bank Accounts API (การจัดการบัญชีธนาคาร)

## Endpoint: `/bank-accounts`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลบัญชีธนาคารทั้งหมดของร้านค้า. **(Requires Authentication)**)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "bankName": "string",
        "accountHolderName": "string",
        "accountNumber": "string",
        "status": "integer (สถานะ)",
        "isPrimary": "boolean (บัญชีหลักหรือไม่)",
        "qrCode": "string (รหัส QR)",
        "merchantId": "string (รหัสร้านค้า)",
        "createdAt": "string (วันเวลาที่สร้าง)",
        "updatedAt": "string (วันเวลาที่แก้ไขล่าสุด)"
    },
    ...
]
```

---

## Endpoint: `/bank-accounts/create`

**Method:** POST

**Description:** (THAI: สร้างบัญชีธนาคารใหม่. **(Requires Authentication)**)

**Request:**

```json
{
    "bankName": "string",
    "accountHolderName": "string",
    "accountNumber": "string",
    "status": "integer",
    "isPrimary": "boolean",
    "qrCode": "string",
    "merchantId": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "bankName": "string",
    "accountHolderName": "string",
    "accountNumber": "string",
    "status": "integer",
    "isPrimary": "boolean",
    "qrCode": "string",
    "merchantId": "string",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/bank-accounts/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดบัญชีธนาคาร. **(Requires Authentication)**)

**Request:**

*   `account_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "bankName": "string",
    "accountHolderName": "string",
    "accountNumber": "string",
    "status": "integer",
    "isPrimary": "boolean",
    "qrCode": "string",
    "merchantId": "string",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/bank-accounts/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดบัญชีธนาคาร. **(Requires Authentication)**)

**Request:**

*   `account_id`: string (URL parameter)

```json
{
    "bankName": "string",
    "accountHolderName": "string",
    "accountNumber": "string",
    "status": "integer",
    "isPrimary": "boolean",
    "qrCode": "string",
    "merchantId": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "bankName": "string",
    "accountHolderName": "string",
    "accountNumber": "string",
    "status": "integer",
    "isPrimary": "boolean",
    "qrCode": "string",
    "merchantId": "string",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/bank-accounts/delete`

**Method:** DELETE

**Description:** (THAI: ลบบัญชีธนาคาร. **(Requires Authentication)**)

**Request:**

*   `account_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```

### Food Add-ons Options (ตัวเลือกของตัวเลือกเพิ่มเติม)

#### Endpoint: `/foods/add-on/options`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลตัวเลือกของตัวเลือกเพิ่มเติมทั้งหมดของร้านค้า.)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "name": dict,
        "description": dict,
        "price": float,
        "status": int,
        "position": int,
        "merchantId": str,
        "createdAt": "string",
        "updatedAt": "string"
    },
    ...
]
```

---

#### Endpoint: `/foods/add-on/options/create`

**Method:** POST

**Description:** (THAI: สร้างตัวเลือกของตัวเลือกเพิ่มเติมใหม่. **(Requires Authentication: `write:addon-options:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "name": dict,
    "description": dict,
    "price": float,
    "status": int,
    "merchantId": str
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": dict,
    "description": dict,
    "price": float,
    "status": int,
    "position": int,
    "merchantId": str,
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/add-on/options/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดตัวเลือกของตัวเลือกเพิ่มเติม.)

**Request:**

*   `option_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "name": dict,
    "description": dict,
    "price": float,
    "status": int,
    "position": int,
    "merchantId": str,
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/add-on/options/edit`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดตัวเลือกของตัวเลือกเพิ่มเติม. **(Requires Authentication: `write:addon-options:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

*   `option_id`: string (URL parameter)

```json
{
    "name": dict,
    "description": dict,
    "price": float,
    "status": int,
    "merchantId": str,
    "position": int
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": dict,
    "description": dict,
    "price": float,
    "status": int,
    "position": int,
    "merchantId": str,
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/add-on/options/delete`

**Method:** DELETE

**Description:** (THAI: ลบตัวเลือกของตัวเลือกเพิ่มเติม. **(Requires Authentication: `delete:addon-options:{merchant_id}` or `write:all:{merchant_id}` or `admin:{merchant_id}` permission)**)

**Request:**

*   `option_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```
---

### Food Add-ons (ตัวเลือกเพิ่มเติมสำหรับอาหาร)

##

### Food Add-ons (ตัวเลือกเพิ่มเติมสำหรับอาหาร)

#### Endpoint: `/foods/add-on`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลตัวเลือกเพิ่มเติม (add-ons) ทั้งหมดของร้านค้า.)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "categoryId": "string",
        "name": {
            "th": "string",
            "en": "string"
        },
        "description": {
            "th": "string",
            "en": "string"
        },
        "merchantId": "string",
        "isPreset": "integer",
        "isRequired": "integer",
        "minOptionsRequiredToSelect": "integer",
        "maxOptionsRequiredToSelect": "integer",
        "options": [
            {
                "id": "string",
                "name": {
                    "th": "string",
                    "en": "string"
                },
                "price": "float",
                "status": "integer"
            }
        ],
        "status": "integer",
        "createdAt": "string",
        "updatedAt": "string"
    },
    ...
]
```

---

#### Endpoint: `/foods/add-on/create`

**Method:** POST

**Description:** (THAI: สร้างตัวเลือกเพิ่มเติม (add-on) ใหม่. **(Requires Authentication: `write:food-addons:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "status": "integer",
    "merchantId": "string",
    "isRequired": "integer",
    "isPreset": "integer",
    "minOptionsRequiredToSelect": "integer",
    "maxOptionsRequiredToSelect": "integer",
    "options": [
      {
        "name": {
          "th": "string",
          "en": "string"
        },
        "price": "float"
      }
    ],
    "addOnType": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "isPreset": "integer",
    "isRequired": "integer",
    "minOptionsRequiredToSelect": "integer",
    "maxOptionsRequiredToSelect": "integer",
    "options": [
        {
            "id": "string",
            "name": {
                "th": "string",
                "en": "string"
            },
            "price": "float",
            "status": "integer"
        }
    ],
    "status": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/add-on/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดตัวเลือกเพิ่มเติม (add-on).)

**Request:**

*   `add_on_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "isPreset": "integer",
    "isRequired": "integer",
    "minOptionsRequiredToSelect": "integer",
    "maxOptionsRequiredToSelect": "integer",
    "options": [
        {
            "id": "string",
            "name": {
                "th": "string",
                "en": "string"
            },
            "price": "float",
            "status": "integer"
        }
    ],
    "status": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/add-on/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดตัวเลือกเพิ่มเติม (add-on). **(Requires Authentication: `write:food-addon:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

* `add_on_id`: string (URL parameter)

```json
{
  "position": "integer",
  "categoryId": "string",
  "name": {
    "th": "string",
    "en": "string"
  },
  "description": {
    "th": "string",
    "en": "string"
  },
  "status": "integer",
  "merchantId": "string",
  "isRequired": "integer",
  "isPreset": "integer",
  "minOptionsRequiredToSelect": "integer",
  "maxOptionsRequiredToSelect": "integer",
  "options": [
    {
      "name": {
        "th": "string",
        "en": "string"
      },
      "price": "float"
    }
  ],
    "addOnType": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "isPreset": "integer",
    "isRequired": "integer",
    "minOptionsRequiredToSelect": "integer",
    "maxOptionsRequiredToSelect": "integer",
    "options": [
        {
            "id": "string",
            "name": {
                "th": "string",
                "en": "string"
            },
            "price": "float",
            "status": "integer"
        }
    ],
    "status": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/add-on/delete`

**Method:** DELETE

**Description:** (THAI: ลบตัวเลือกเพิ่มเติม (add-on). **(Requires Authentication: `delete:food-addons:{merchant_id}` or `write:all:{merchant_id}` or `admin:{merchant_id}` permission)**)

**Request:**

*   `add_on_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```

### Utilities (ยูทิลิตี้)

#### Endpoint: `/utilities/images/{category}/{filename}`

**Method:** GET

**Description:** (THAI: ดึงไฟล์รูปภาพ.)

**Request:**

*   `category`: string (URL parameter)
*   `filename`: string (URL parameter)

**Response:**

`FileResponse`
---

### Users (ผู้ใช้งาน)

#### Endpoint: `/users`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลผู้ใช้งานทั้งหมดของร้านค้า. **(Requires Authentication: `admin:{merchant_id}` permission)**)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "username": str,
        "name": str,
        "merchantId": str,
        "phone": str,
        "email": str,
        "posPIN": str,
        "permissions": "list[str]",
        "createdAt": "string",
        "updatedAt": "string"
    },
    ...
]
```

---

#### Endpoint: `/users/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดผู้ใช้งาน (สามารถดึงด้วย username, user_id หรือ pos_id). **(Requires Authentication: `admin:{merchant_id}` permission)**)

**Request:**

*   `merchant_id`: string (URL parameter)
*   One of:
    *   `username`: string (URL parameter)
    *   `user_id`: string (URL parameter)
    *   `pos_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "username": str,
    "name": str,
    "merchantId": str,
    "phone": str,
    "email": str,
    "posPIN": str,
    "permissions": "list[str]",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/users/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดผู้ใช้งาน. **(Requires Authentication: `admin:{merchant_id}` permission)**)

**Request:**

* `user_id`: string (URL Parameter)

```json
{
    "merchant_id": str,
    "phone": str,
    "email": str,
    "posPIN": str,
    "name": str,
    "permissions": "list[str]"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "username": str,
    "name": str,
    "merchantId": str,
    "phone": str,
    "email": str,
    "posPIN": str,
    "permissions": "list[str]",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/users/delete`

**Method:** DELETE

**Description:** (THAI: ลบผู้ใช้งาน. **(Requires Authentication: `admin:{merchant_id}` permission)**)

**Request:**

*   `user_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**
```json
{
    "id": "string (UUID)",
    "username": str,
    "name": str,
    "merchantId": str,
    "phone": str,
    "email": str,
    "posPIN": str,
    "permissions": "list[str]",
    "createdAt": "string",
    "updatedAt": "string"
}
```
---

### Users (ผู้ใช้งาน)

#### Endpoint: `/users`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลผู้ใช้งานทั้งหมดของร้านค้า. **(Requires Authentication: `admin:{merchant_id}` permission)**)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "username": str,
        "name": str,
        "merchantId": str,
        "phone": str,
        "email": str,
        "posPIN": str,
        "permissions": "list[str]",
        "createdAt": "string",
        "updatedAt": "string"
    },
    ...
]
```

---

#### Endpoint: `/users/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดผู้ใช้งาน (สามารถดึงด้วย username, user_id หรือ pos_id). **(Requires Authentication: `admin:{merchant_id}` permission)**)

**Request:**

*   `merchant_id`: string (URL parameter)
*   One of:
    *   `username`: string (URL parameter)
    *   `user_id`: string (URL parameter)
    *   `pos_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "username": str,
    "name": str,
    "merchantId": str,
    "phone": str,
    "email": str,
    "posPIN": str,
    "permissions": "list[str]",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/users/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดผู้ใช้งาน. **(Requires Authentication: `admin:{merchant_id}` permission)**)

**Request:**

* `user_id`: string (URL Parameter)

```json
{
    "merchant_id": str,
    "phone": str,
    "email": str,
    "posPIN": str,
    "name": str,
    "permissions": "list[str]"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "username": str,
    "name": str,
    "merchantId": str,
    "phone": str,
    "email": str,
    "posPIN": str,
    "permissions": "list[str]",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/users/delete`

**Method:** DELETE

**Description:** (THAI: ลบผู้ใช้งาน. **(Requires Authentication: `admin:{merchant_id}` permission)**)

**Request:**

*   `user_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**
```json
{
    "id": "string (UUID)",
    "username": str,
    "name": str,
    "merchantId": str,
    "phone": str,
    "email": str,
    "posPIN": str,
    "permissions": "list[str]",
    "createdAt": "string",
    "updatedAt": "string"
}
```
---

### Food Ingredients (ส่วนผสมอาหาร)

#### Endpoint: `/foods/ingredients`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลส่วนผสมอาหารทั้งหมดของร้านค้า.)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "name": dict,
        "description": dict,
        "price": float,
        "status": int,
        "position": int,
        "merchantId": str,
        "createdAt": "string",
        "updatedAt": "string"
    },
    ...
]
```

---

#### Endpoint: `/foods/ingredients/create`

**Method:** POST

**Description:** (THAI: สร้างส่วนผสมอาหารใหม่. **(Requires Authentication: `write:food-ingredients:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "name": dict,
    "description": dict,
    "price": float,
    "status": int,
    "merchantId": str
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": dict,
    "description": dict,
    "price": float,
    "status": int,
    "position": int,
    "merchantId": str,
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/ingredients/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดส่วนผสมอาหาร.)

**Request:**

*   `ingredient_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "name": dict,
    "description": dict,
    "price": float,
    "status": int,
    "position": int,
    "merchantId": str,
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/ingredients/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดส่วนผสมอาหาร. **(Requires Authentication: `write:food-ingredients:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

*   `ingredient_id`: string (URL parameter)

```json
{
    "name": dict,
    "description": dict,
    "price": float,
    "status": int,
    "merchantId": str,
    "position": int
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": dict,
    "description": dict,
    "price": float,
    "status": int,
    "position": int,
    "merchantId": str,
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/ingredients/delete`

**Method:** DELETE

**Description:** (THAI: ลบส่วนผสมอาหาร. **(Requires Authentication: `write:food-ingredients:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

*   `ingredient_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```

### Workforce Shifts (กะการทำงาน)

#### Endpoint: `/shift`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลกะการทำงานทั้งหมดของร้านค้า. **(Requires Authentication: `read:shift:{merchant_id}` or `read:all:{merchant_id}` permission)**)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "merchantId": str,
        "initialCashAmount": float,
        "closingCashAmount": float,
        "openedBy": str,
        "closedBy": str,
        "openedAt": "string",
        "closedAt": "string"
    },
    ...
]
```

---

#### Endpoint: `/shift/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดกะการทำงาน. **(Requires Authentication: `read:shift:{merchant_id}` or `read:all:{merchant_id}` permission)**)

**Request:**

*   `shift_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "merchantId": str,
    "initialCashAmount": float,
    "closingCashAmount": float,
    "openedBy": str,
    "closedBy": str,
    "openedAt": "string",
    "closedAt": "string"
}
```

---

#### Endpoint: `/shift/open`

**Method:** POST

**Description:** (THAI: เปิดกะการทำงานใหม่. **(Requires Authentication: `write:shift:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "merchant_id": str,
    "cash_amount": float
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "merchantId": str,
    "initialCashAmount": float,
    "closingCashAmount": float,
    "openedBy": str,
    "closedBy": str,
    "openedAt": "string",
    "closedAt": "string"
}
```

---

#### Endpoint: `/shift/close`

**Method:** POST

**Description:** (THAI: ปิดกะการทำงาน. **(Requires Authentication: `write:shift:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "shift_id": str,
    "merchant_id": str,
    "cash_amount": float
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "merchantId": str,
    "initialCashAmount": float,
    "closingCashAmount": float,
    "openedBy": str,
    "closedBy": str,
    "openedAt": "string",
    "closedAt": "string"
}
```
---

### Workforce Shifts (กะการทำงาน)

#### Endpoint: `/shift`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลกะการทำงานทั้งหมดของร้านค้า. **(Requires Authentication: `read:shift:{merchant_id}` or `read:all:{merchant_id}` permission)**)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "merchantId": str,
        "initialCashAmount": float,
        "closingCashAmount": float,
        "openedBy": str,
        "closedBy": str,
        "openedAt": "string",
        "closedAt": "string"
    },
    ...
]
```

---

#### Endpoint: `/shift/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดกะการทำงาน. **(Requires Authentication: `read:shift:{merchant_id}` or `read:all:{merchant_id}` permission)**)

**Request:**

*   `shift_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "merchantId": str,
    "initialCashAmount": float,
    "closingCashAmount": float,
    "openedBy": str,
    "closedBy": str,
    "openedAt": "string",
    "closedAt": "string"
}
```

---

#### Endpoint: `/shift/open`

**Method:** POST

**Description:** (THAI: เปิดกะการทำงานใหม่. **(Requires Authentication: `write:shift:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "merchant_id": str,
    "cash_amount": float
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "merchantId": str,
    "initialCashAmount": float,
    "closingCashAmount": float,
    "openedBy": str,
    "closedBy": str,
    "openedAt": "string",
    "closedAt": "string"
}
```

---

#### Endpoint: `/shift/close`

**Method:** POST

**Description:** (THAI: ปิดกะการทำงาน. **(Requires Authentication: `write:shift:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "shift_id": str,
    "merchant_id": str,
    "cash_amount": float
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "merchantId": str,
    "initialCashAmount": float,
    "closingCashAmount": float,
    "openedBy": str,
    "closedBy": str,
    "openedAt": "string",
    "closedAt": "string"
}
```
---
---

---

# Foods API (การจัดการเมนูอาหาร)

## Endpoint: `/foods`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลเมนูอาหารทั้งหมดของร้านค้า.)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "categoryId": "string (รหัสหมวดหมู่)",
        "name": {
            "th": "string (ชื่อเมนู, ภาษาไทย)",
            "en": "string (ชื่อเมนู, ภาษาอังกฤษ)"
        },
        "description": {
            "th": "string (คำอธิบาย, ภาษาไทย)",
            "en": "string (คำอธิบาย, ภาษาอังกฤษ)"
        },
        "price": "float (ราคา)",
        "status": "integer (สถานะ)",
        "ingredients": ["string (รหัสส่วนผสม)", ...],
        "addons": ["string (รหัสตัวเลือกเพิ่มเติม)", ...],
        "isPopular": "integer (สถานะยอดนิยม)",
        "isRecommended": "integer (สถานะแนะนำ)",
        "images": ["string (Base64)", ...],
        "menuType": "string (ประเภทเมนู: 'Foods', 'Beverages', 'Desserts', 'Others')",
        "merchantId": "string (รหัสร้านค้า)",
        "position": "integer (ลำดับ)",
        "kitchenIds": ["string (รหัสครัว)", ...],
        "minRequiredToSelectIngredients": "integer",
        "maxRequiredToSelectIngredients": "integer",
        "createdAt": "string (วันเวลาที่สร้าง)",
        "updatedAt": "string (วันเวลาที่แก้ไขล่าสุด)"
    },
    ...
]
```

---

## Endpoint: `/foods/create`

**Method:** POST

**Description:** (THAI: สร้างเมนูอาหารใหม่. **(Requires Authentication)**)

**Request:**

```json
{
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "price": "float",
    "status": "integer",
    "ingredients": ["string", ...],
    "addons": ["string", ...],
    "isPopular": "integer",
    "isRecommended": "integer",
    "images": ["string", ...],
    "menuType": "string",
    "merchantId": "string",
    "kitchenIds": ["string", ...],
    "minRequiredToSelectIngredients": "integer",
    "maxRequiredToSelectIngredients": "integer"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "price": "float",
    "status": "integer",
    "ingredients": ["string", ...],
    "addons": ["string", ...],
    "isPopular": "integer",
    "isRecommended": "integer",
    "images": ["string", ...],
    "menuType": "string",
    "merchantId": "string",
    "position": "integer",
    "kitchenIds": ["string", ...],
    "minRequiredToSelectIngredients": "integer",
    "maxRequiredToSelectIngredients": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/foods/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดเมนูอาหาร.)

**Request:**

*   `food_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "price": "float",
    "status": "integer",
    "ingredients": ["string", ...],
    "addons": ["string", ...],
    "isPopular": "integer",
    "isRecommended": "integer",
    "images": ["string", ...],
    "menuType": "string",
    "merchantId": "string",
    "position": "integer",
    "kitchenIds": ["string", ...],
    "minRequiredToSelectIngredients": "integer",
    "maxRequiredToSelectIngredients": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/foods/edit-menu-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดเมนูอาหาร. **(Requires Authentication)**)

**Request:**
* `food_id`: string (URL parameter)

```json
{
 "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "price": "float",
    "status": "integer",
    "ingredients": ["string", ...],
    "addons": ["string", ...],
    "isPopular": "integer",
    "isRecommended": "integer",
    "images": ["string", ...],
    "menuType": "string",
    "merchantId": "string",
    "kitchenIds": ["string", ...],
    "minRequiredToSelectIngredients": "integer",
    "maxRequiredToSelectIngredients": "integer",
    "position": "integer"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "price": "float",
    "status": "integer",
    "ingredients": ["string", ...],
    "addons": ["string", ...],
    "isPopular": "integer",
    "isRecommended": "integer",
    "images": ["string", ...],
    "menuType": "string",
    "merchantId": "string",
    "position": "integer",
    "kitchenIds": ["string", ...],
    "minRequiredToSelectIngredients": "integer",
    "maxRequiredToSelectIngredients": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```
---

## Endpoint: `/foods/delete`

**Method:** DELETE

**Description:** (THAI: ลบเมนูอาหาร. **(Requires Authentication)**)

**Request:**

*   `food_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```

### Table Zones (โซนโต๊ะ)

#### Endpoint: `/tables/zones`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลโซนโต๊ะทั้งหมดของร้านค้า. **(Requires Authentication: `read:table-zones:{merchant_id}` or `read:all:{merchant_id}` permission)**)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "name": dict,
        "description": dict,
        "color": str,
        "merchantId": str,
        "createdAt": "string",
        "updatedAt": "string"
    },
    ...
]
```

---

#### Endpoint: `/tables/zones/create`

**Method:** POST

**Description:** (THAI: สร้างโซนโต๊ะใหม่. **(Requires Authentication: `write:table-zones:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "name": dict,
    "description": dict,
    "color": str,
    "merchantId": str
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": dict,
    "description": dict,
    "color": str,
    "merchantId": str,
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/tables/zones/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดโซนโต๊ะ. **(Requires Authentication)**)

**Request:**

*   `zone_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "name": dict,
    "description": dict,
    "color": str,
    "merchantId": str,
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/tables/zones/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดโซนโต๊ะ. **(Requires Authentication: `write:table-zones:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

*   `zone_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)
```json
{
    "name": dict,
    "description": dict,
    "color": str
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": dict,
    "description": dict,
    "color": str,
    "merchantId": str,
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/tables/zones/delete`

**Method:** DELETE

**Description:** (THAI: ลบโซนโต๊ะ. **(Requires Authentication: `write:table-zones:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

*   `zone_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```
---

### Table Categories (หมวดหมู่โต๊ะ)

#### Endpoint: `/tables/categories`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลหมวดหมู่โต๊ะทั้งหมดของร้านค้า. **(Requires Authentication: `read:table-categories:{merchant_id}` or `read:all:{merchant_id}` permission)**)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "name": dict,
        "color": str,
        "merchantId": str,
        "createdAt": "string",
        "updatedAt": "string"
    },
    ...
]
```

---

#### Endpoint: `/tables/categories/create`

**Method:** POST

**Description:** (THAI: สร้างหมวดหมู่โต๊ะใหม่. **(Requires Authentication: `write:table-categories:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "name": dict,
    "color": str,
    "merchantId": str
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": dict,
    "color": str,
    "merchantId": str,
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/tables/categories/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดหมวดหมู่โต๊ะ. **(Requires Authentication)**)

**Request:**

*   `category_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
 "id": "string (UUID)",
    "name": dict,
    "color": str,
    "merchantId": str,
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/tables/categories/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดหมวดหมู่โต๊ะ. **(Requires Authentication: `write:table-categories:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**
*  `category_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

```json
{
    "name": dict,
    "color": str
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": dict,
    "color": str,
    "merchantId": str,
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/tables/categories/delete`

**Method:** DELETE

**Description:** (THAI: ลบหมวดหมู่โต๊ะ. **(Requires Authentication: `write:table-categories:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

*   `category_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```
---

---
### Food Add-ons (ตัวเลือกเพิ่มเติมสำหรับอาหาร)

#### Endpoint: `/foods/add-on`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลตัวเลือกเพิ่มเติม (add-ons) ทั้งหมดของร้านค้า.)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "categoryId": "string",
        "name": {
            "th": "string",
            "en": "string"
        },
        "description": {
            "th": "string",
            "en": "string"
        },
        "merchantId": "string",
        "isPreset": "integer",
        "isRequired": "integer",
        "minOptionsRequiredToSelect": "integer",
        "maxOptionsRequiredToSelect": "integer",
        "options": [
            {
                "id": "string",
                "name": {
                    "th": "string",
                    "en": "string"
                },
                "price": "float",
                "status": "integer"
            }
        ],
        "status": "integer",
        "createdAt": "string",
        "updatedAt": "string"
    },
    ...
]
```

---

#### Endpoint: `/foods/add-on/create`

**Method:** POST

**Description:** (THAI: สร้างตัวเลือกเพิ่มเติม (add-on) ใหม่. **(Requires Authentication: `write:food-addons:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "status": "integer",
    "merchantId": "string",
    "isRequired": "integer",
    "isPreset": "integer",
    "minOptionsRequiredToSelect": "integer",
    "maxOptionsRequiredToSelect": "integer",
    "options": [
      {
        "name": {
          "th": "string",
          "en": "string"
        },
        "price": "float"
      }
    ],
    "addOnType": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "isPreset": "integer",
    "isRequired": "integer",
    "minOptionsRequiredToSelect": "integer",
    "maxOptionsRequiredToSelect": "integer",
    "options": [
        {
            "id": "string",
            "name": {
                "th": "string",
                "en": "string"
            },
            "price": "float",
            "status": "integer"
        }
    ],
    "status": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/add-on/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดตัวเลือกเพิ่มเติม (add-on).)

**Request:**

*   `add_on_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "isPreset": "integer",
    "isRequired": "integer",
    "minOptionsRequiredToSelect": "integer",
    "maxOptionsRequiredToSelect": "integer",
    "options": [
        {
            "id": "string",
            "name": {
                "th": "string",
                "en": "string"
            },
            "price": "float",
            "status": "integer"
        }
    ],
    "status": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/add-on/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดตัวเลือกเพิ่มเติม (add-on). **(Requires Authentication: `write:food-addon:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

* `add_on_id`: string (URL parameter)

```json
{
  "position": "integer",
  "categoryId": "string",
  "name": {
    "th": "string",
    "en": "string"
  },
  "description": {
    "th": "string",
    "en": "string"
  },
  "status": "integer",
  "merchantId": "string",
  "isRequired": "integer",
  "isPreset": "integer",
  "minOptionsRequiredToSelect": "integer",
  "maxOptionsRequiredToSelect": "integer",
  "options": [
    {
      "name": {
        "th": "string",
        "en": "string"
      },
      "price": "float"
    }
  ],
    "addOnType": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "isPreset": "integer",
    "isRequired": "integer",
    "minOptionsRequiredToSelect": "integer",
    "maxOptionsRequiredToSelect": "integer",
    "options": [
        {
            "id": "string",
            "name": {
                "th": "string",
                "en": "string"
            },
            "price": "float",
            "status": "integer"
        }
    ],
    "status": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/add-on/delete`

**Method:** DELETE

**Description:** (THAI: ลบตัวเลือกเพิ่มเติม (add-on). **(Requires Authentication: `delete:food-addons:{merchant_id}` or `write:all:{merchant_id}` or `admin:{merchant_id}` permission)**)

**Request:**

*   `add_on_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```

### Table Zones (โซนโต๊ะ)

#### Endpoint: `/tables/zones`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลโซน

---
### Food Add-ons (ตัวเลือกเพิ่มเติมสำหรับอาหาร)

#### Endpoint: `/foods/add-on`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลตัวเลือกเพิ่มเติม (add-ons) ทั้งหมดของร้านค้า.)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "categoryId": "string",
        "name": {
            "th": "string",
            "en": "string"
        },
        "description": {
            "th": "string",
            "en": "string"
        },
        "merchantId": "string",
        "isPreset": "integer",
        "isRequired": "integer",
        "minOptionsRequiredToSelect": "integer",
        "maxOptionsRequiredToSelect": "integer",
        "options": [
            {
                "id": "string",
                "name": {
                    "th": "string",
                    "en": "string"
                },
                "price": "float",
                "status": "integer"
            }
        ],
        "status": "integer",
        "createdAt": "string",
        "updatedAt": "string"
    },
    ...
]
```

---

#### Endpoint: `/foods/add-on/create`

**Method:** POST

**Description:** (THAI: สร้างตัวเลือกเพิ่มเติม (add-on) ใหม่. **(Requires Authentication: `write:food-addons:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "status": "integer",
    "merchantId": "string",
    "isRequired": "integer",
    "isPreset": "integer",
    "minOptionsRequiredToSelect": "integer",
    "maxOptionsRequiredToSelect": "integer",
    "options": [
      {
        "name": {
          "th": "string",
          "en": "string"
        },
        "price": "float"
      }
    ],
    "addOnType": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "isPreset": "integer",
    "isRequired": "integer",
    "minOptionsRequiredToSelect": "integer",
    "maxOptionsRequiredToSelect": "integer",
    "options": [
        {
            "id": "string",
            "name": {
                "th": "string",
                "en": "string"
            },
            "price": "float",
            "status": "integer"
        }
    ],
    "status": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/add-on/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดตัวเลือกเพิ่มเติม (add-on).)

**Request:**

*   `add_on_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "isPreset": "integer",
    "isRequired": "integer",
    "minOptionsRequiredToSelect": "integer",
    "maxOptionsRequiredToSelect": "integer",
    "options": [
        {
            "id": "string",
            "name": {
                "th": "string",
                "en": "string"
            },
            "price": "float",
            "status": "integer"
        }
    ],
    "status": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/add-on/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดตัวเลือกเพิ่มเติม (add-on). **(Requires Authentication: `write:food-addon:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

* `add_on_id`: string (URL parameter)

```json
{
  "position": "integer",
  "categoryId": "string",
  "name": {
    "th": "string",
    "en": "string"
  },
  "description": {
    "th": "string",
    "en": "string"
  },
  "status": "integer",
  "merchantId": "string",
  "isRequired": "integer",
  "isPreset": "integer",
  "minOptionsRequiredToSelect": "integer",
  "maxOptionsRequiredToSelect": "integer",
  "options": [
    {
      "name": {
        "th": "string",
        "en": "string"
      },
      "price": "float"
    }
  ],
    "addOnType": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "isPreset": "integer",
    "isRequired": "integer",
    "minOptionsRequiredToSelect": "integer",
    "maxOptionsRequiredToSelect": "integer",
    "options": [
        {
            "id": "string",
            "name": {
                "th": "string",
                "en": "string"
            },
            "price": "float",
            "status": "integer"
        }
    ],
    "status": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/add-on/delete`

**Method:** DELETE

**Description:** (THAI: ลบตัวเลือกเพิ่มเติม (add-on). **(Requires Authentication: `delete:food-addons:{merchant_id}` or `write:all:{merchant_id}` or `admin:{merchant_id}` permission)**)

**Request:**

*   `add_on_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```

---

# Order API (การจัดการคำสั่งซื้อ)

## Endpoint: `/order/dine-in`

**Method:** POST

**Description:** (THAI: สั่งอาหารสำหรับการรับประทานในร้าน (ลูกค้า).)

**Request:**

```json
{
    "foods": [
        {
            "id": "string (รหัสเมนู)",
            "quantity": "integer (จำนวน)",
            "add_ons": [
                {
                    "id": "string (รหัสตัวเลือกเพิ่มเติม)",
                    "options": [
                        {
                            "id": "string (รหัสตัวเลือก)",
                            "price": "float (ราคา)"
                        }
                    ]
                }
            ],
            "remark": "string (หมายเหตุ)"
        }
    ],
    "order_category": "string",
    "table_id": "string",
    "order_remark": "string",
    "merchant_id": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "orderAt": "string (วันเวลาที่สั่ง)",
    "customer": "string | dict",
    "customerName": "string",
    "customerLineName": "string",
    "totalFoodPrice": "float",
    "orderType": "string",
    "processStatus": "string",
    "status": "dict",
    "receipt": "string",
    "totalQuantity": "integer",
    "table": "string | int",
    "deliveryAddress": "dict",
    "deliveryRemark": "string",
    "items": "list",
    "isCheckedOut": "integer",
    "coverSheet": "string",
    "paymentSlip": "string",
    "deliveryFee": "float",
    "orderRemark": "string",
    "kitchenTicket": "string"
}
```

---

# WebSocket API

These endpoints use the WebSocket protocol for real-time communication.

## Endpoint: `/socket/server-device`

**Method:** WebSocket

**Description:** (THAI: WebSocket สำหรับอุปกรณ์เซิร์ฟเวอร์. ไคลเอนต์เชื่อมต่อและสามารถลงทะเบียน node ด้วยคำสั่ง `REGISTER_NODE` พร้อม `node_id`.)

**Request:**

*   `command`: "REGISTER_NODE" (optional)
*   `node_id`: "string" (required if `command` is "REGISTER_NODE")

**Response:**

(WebSocket connection, no specific JSON response model unless there's an error)

---

# Tables API (การจัดการโต๊ะ)

## Endpoint: `/tables`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลโต๊ะทั้งหมดของร้านค้า. **(Requires Authentication: `read:tables:{merchant_id}` or `read:all:{merchant_id}` permission)**)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "tableName": "string (ชื่อโต๊ะ)",
        "seats": "integer (จำนวนที่นั่ง)",
        "zoneId": "string (รหัสโซน)",
        "tableType": "string (รหัสประเภท/หมวดหมู่โต๊ะ)",
        "merchantId": "string (รหัสร้านค้า)",
        "remark": "string (หมายเหตุ)",
        "qrCode": "string (URL ของ QR Code)",
        "isAvailable": "boolean (สถานะว่าง)",
        "createdAt": "string (วันเวลาที่สร้าง)",
        "updatedAt": "string (วันเวลาที่แก้ไขล่าสุด)"
    },
    ...
]
```

---

## Endpoint: `/tables/create`

**Method:** POST

**Description:** (THAI: สร้างโต๊ะใหม่. **(Requires Authentication: `write:tables:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "tableName": "string",
    "seats": "integer",
    "zoneId": "string",
    "tableType": "string",
    "merchantId": "string",
    "remark": "string (optional)"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "tableName": "string",
    "seats": "integer",
    "zoneId": "string",
    "tableType": "string",
    "merchantId": "string",
    "remark": "string",
    "qrCode": "string",
    "isAvailable": "boolean",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/tables/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดโต๊ะ.)

**Request:**

*   `table_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "tableName": "string",
    "seats": "integer",
    "zoneId": "string",
    "tableType": "string",
    "merchantId": "string",
    "remark": "string",
    "qrCode": "string",
    "isAvailable": "boolean",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/tables/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดโต๊ะ. **(Requires Authentication: `write:tables:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

* `table_id`: string (URL parameter)
* `merchant_id`: string (URL parameter)

```json
{
    "tableName": "string",
    "seats": "integer",
    "zoneId": "string",
    "tableType": "string",
    "remark": "string",
    "isAvailable": "boolean"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "tableName": "string",
    "seats": "integer",
    "zoneId": "string",
    "tableType": "string",
    "merchantId": "string",
    "remark": "string",
    "qrCode": "string",
    "isAvailable": "boolean",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/tables/delete`

**Method:** DELETE

**Description:** (THAI: ลบโต๊ะ. **(Requires Authentication: `write:tables:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

*   `table_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```

---

## Endpoint: `/socket/kitchen-main`

**Method:** WebSocket

**Description:** (THAI: WebSocket สำหรับอุปกรณ์หลักในครัว. ไคลเอนต์เชื่อมต่อและสามารถส่งข้อมูลได้.)

**Request:**

(WebSocket connection, accepts JSON data)

**Response:**

(WebSocket connection, no specific JSON response model)

---

## Endpoint: `/socket/kitchen-device`

**Method:** WebSocket

**Description:** (THAI: WebSocket สำหรับอุปกรณ์ในครัว. ไคลเอนต์เชื่อมต่อและสามารถลงทะเบียนครัวด้วยคำสั่ง `REGISTER_KITCHEN` พร้อม `node_id` และ `kitchen_id`.)

**Request:**

*   `command`: "REGISTER_KITCHEN" (optional)
*   `node_id`: "string" (required if `command` is "REGISTER_KITCHEN")
*   `kitchen_id`: "string" (required if `command` is "REGISTER_KITCHEN")

**Response:**

(WebSocket connection, no specific JSON response model unless there's an error)

---

## Endpoint: `/socket/busboy-device`

**Method:** WebSocket

**Description:** (THAI: WebSocket สำหรับอุปกรณ์ของพนักงานเก็บโต๊ะ. ไคลเอนต์เชื่อมต่อและสามารถลงทะเบียนด้วยคำสั่ง `REGISTER_BUSBOY` พร้อม `node_id`.)

**Request:**

*   `command`: "REGISTER_BUSBOY" (optional)
*   `node_id`: "string" (required if `command` is "REGISTER_BUSBOY")

**Response:**

(WebSocket connection, no specific JSON response model unless there's an error)

---



---

## Endpoint: `/order/delivery`

**Method:** POST

**Description:** (THAI: สั่งอาหารสำหรับการจัดส่ง. **(Requires Authentication: `write:order:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "foods": [
        {
            "id": "string",
            "quantity": "integer",
            "add_ons": [
                {
                    "id": "string",
                    "options": [
                        {
                            "id": "string",
                            "price": "float"
                        }
                    ]
                }
            ],
            "remark": "string"
        }
    ],
    "order_category": "string",
    "slip": "string",
    "delivery_address": "dict | string",
    "delivery_remark": "string",
    "merchant_id": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "orderAt": "string",
    "customer": "string | dict",
    "customerName": "string",
    "customerLineName": "string",
    "totalFoodPrice": "float",
    "orderType": "string",
    "processStatus": "string",
    "status": "dict",
    "receipt": "string",
    "totalQuantity": "integer",
    "table": "string | int",
    "deliveryAddress": "dict",
    "deliveryRemark": "string",
    "items": "list",
    "isCheckedOut": "integer",
    "coverSheet": "string",
    "paymentSlip": "string",
    "deliveryFee": "float",
    "orderRemark": "string",
    "kitchenTicket": "string"
}
```

---

## Endpoint: `/order/takeaway`

**Method:** POST

**Description:** (THAI: สั่งอาหารสำหรับซื้อกลับบ้าน. **(Requires Authentication: `write:order:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "foods": [
        {
            "id": "string",
            "quantity": "integer",
            "add_ons": [
                {
                    "id": "string",
                    "options": [
                        {
                            "id": "string",
                            "price": "float"
                        }
                    ]
                }
            ],
            "remark": "string"
        }
    ],
    "order_category": "string",
    "take_away_customer_name": "string",
    "order_remark": "string",
    "merchant_id": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "orderAt": "string",
    "customer": "string | dict",
    "customerName": "string",
    "customerLineName": "string",
    "totalFoodPrice": "float",
    "orderType": "string",
    "processStatus": "string",
    "status": "dict",
    "receipt": "string",
    "totalQuantity": "integer",
    "table": "string | int",
    "deliveryAddress": "dict",
    "deliveryRemark": "string",
    "items": "list",
    "isCheckedOut": "integer",
    "coverSheet": "string",
    "paymentSlip": "string",
    "deliveryFee": "float",
    "orderRemark": "string",
    "kitchenTicket": "string"
}
```

---

## Endpoint: `/order/waiter-order`

**Method:** POST

**Description:** (THAI: สั่งอาหารสำหรับการรับประทานในร้าน (โดยพนักงาน). **(Requires Authentication: `write:order:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**
```json
{
    "foods": [
        {
            "id": "string",
            "quantity": "integer",
            "add_ons": [
                {
                    "id": "string",
                    "options": [
                        {
                            "id": "string",
                            "price": "float"
                        }
                    ]
                }
            ],
            "remark": "string"
        }
    ],
    "order_category": "string",
    "table_id": "string",
    "order_remark": "string",
    "merchant_id": "string",
    "node_id": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "orderAt": "string",
    "customer": "string | dict",
    "customerName": "string",
    "customerLineName": "string",
    "totalFoodPrice": "float",
    "orderType": "string",
    "processStatus": "string",
    "status": "dict",
    "receipt": "string",
    "totalQuantity": "integer",
    "table": "string | int",
    "deliveryAddress": "dict",
    "deliveryRemark": "string",
    "items": "list",
    "isCheckedOut": "integer",
    "coverSheet": "string",
    "paymentSlip": "string",
    "deliveryFee": "float",
    "orderRemark": "string",
    "kitchenTicket": "string"
}
```

---

## Endpoint: `/order/update-status`

**Method:** PATCH

**Description:** (THAI: อัปเดตสถานะของรายการสั่งอาหาร. **(Requires Authentication: `write:order:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

*   `order_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)
*   `target_status`: string (one of: 'cooking', 'completed', 'cancelled')

**Response:**

```json
{
    "id": "string (UUID)",
    "orderAt": "string",
    "customer": "string | dict",
    "customerName": "string",
    "customerLineName": "string",
    "totalFoodPrice": "float",
    "orderType": "string",
    "processStatus": "string",
    "status": "dict",
    "receipt": "string",
    "totalQuantity": "integer",
    "table": "string | int",
    "deliveryAddress": "dict",
    "deliveryRemark": "string",
    "items": "list",
    "isCheckedOut": "integer",
    "coverSheet": "string",
    "paymentSlip": "string",
    "deliveryFee": "float",
    "orderRemark": "string",
    "kitchenTicket": "string"
}
```

---

# Order API (การจัดการคำสั่งซื้อ)

## Endpoint: `/order/dine-in`

**Method:** POST

**Description:** (THAI: สั่งอาหารสำหรับการรับประทานในร้าน (ลูกค้า).)

**Request:**

```json
{
    "foods": [
        {
            "id": "string (รหัสเมนู)",
            "quantity": "integer (จำนวน)",
            "add_ons": [
                {
                    "id": "string (รหัสตัวเลือกเพิ่มเติม)",
                    "options": [
                        {
                            "id": "string (รหัสตัวเลือก)",
                            "price": "float (ราคา)"
                        }
                    ]
                }
            ],
            "remark": "string (หมายเหตุ)"
        }
    ],
    "order_category": "string",
    "table_id": "string",
    "order_remark": "string",
    "merchant_id": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "orderAt": "string (วันเวลาที่สั่ง)",
    "customer": "string | dict",
    "customerName": "string",
    "customerLineName": "string",
    "totalFoodPrice": "float",
    "orderType": "string",
    "processStatus": "string",
    "status": "dict",
    "receipt": "string",
    "totalQuantity": "integer",
    "table": "string | int",
    "deliveryAddress": "dict",
    "deliveryRemark": "string",
    "items": "list",
    "isCheckedOut": "integer",
    "coverSheet": "string",
    "paymentSlip": "string",
    "deliveryFee": "float",
    "orderRemark": "string",
    "kitchenTicket": "string"
}
```

---

## Endpoint: `/order/delivery`

**Method:** POST

**Description:** (THAI: สั่งอาหารสำหรับการจัดส่ง. **(Requires Authentication: `write:order:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "foods": [
        {
            "id": "string",
            "quantity": "integer",
            "add_ons": [
                {
                    "id": "string",
                    "options": [
                        {
                            "id": "string",
                            "price": "float"
                        }
                    ]
                }
            ],
            "remark": "string"
        }
    ],
    "order_category": "string",
    "slip": "string",
    "delivery_address": "dict | string",
    "delivery_remark": "string",
    "merchant_id": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "orderAt": "string",
    "customer": "string | dict",
    "customerName": "string",
    "customerLineName": "string",
    "totalFoodPrice": "float",
    "orderType": "string",
    "processStatus": "string",
    "status": "dict",
    "receipt": "string",
    "totalQuantity": "integer",
    "table": "string | int",
    "deliveryAddress": "dict",
    "deliveryRemark": "string",
    "items": "list",
    "isCheckedOut": "integer",
    "coverSheet": "string",
    "paymentSlip": "string",
    "deliveryFee": "float",
    "orderRemark": "string",
    "kitchenTicket": "string"
}
```

---

## Endpoint: `/order/takeaway`

**Method:** POST

**Description:** (THAI: สั่งอาหารสำหรับซื้อกลับบ้าน. **(Requires Authentication: `write:order:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "foods": [
        {
            "id": "string",
            "quantity": "integer",
            "add_ons": [
                {
                    "id": "string",
                    "options": [
                        {
                            "id": "string",
                            "price": "float"
                        }
                    ]
                }
            ],
            "remark": "string"
        }
    ],
    "order_category": "string",
    "take_away_customer_name": "string",
    "order_remark": "string",
    "merchant_id": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "orderAt": "string",
    "customer": "string | dict",
    "customerName": "string",
    "customerLineName": "string",
    "totalFoodPrice": "float",
    "orderType": "string",
    "processStatus": "string",
    "status": "dict",
    "receipt": "string",
    "totalQuantity": "integer",
    "table": "string | int",
    "deliveryAddress": "dict",
    "deliveryRemark": "string",
    "items": "list",
    "isCheckedOut": "integer",
    "coverSheet": "string",
    "paymentSlip": "string",
    "deliveryFee": "float",
    "orderRemark": "string",
    "kitchenTicket": "string"
}
```

---

## Endpoint: `/order/waiter-order`

**Method:** POST

**Description:** (THAI: สั่งอาหารสำหรับการรับประทานในร้าน (โดยพนักงาน). **(Requires Authentication: `write:order:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**
```json
{
    "foods": [
        {
            "id": "string",
            "quantity": "integer",
            "add_ons": [
                {
                    "id": "string",
                    "options": [
                        {
                            "id": "string",
                            "price": "float"
                        }
                    ]
                }
            ],
            "remark": "string"
        }
    ],
    "order_category": "string",
    "table_id": "string",
    "order_remark": "string",
    "merchant_id": "string",
    "node_id": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "orderAt": "string",
    "customer": "string | dict",
    "customerName": "string",
    "customerLineName": "string",
    "totalFoodPrice": "float",
    "orderType": "string",
    "processStatus": "string",
    "status": "dict",
    "receipt": "string",
    "totalQuantity": "integer",
    "table": "string | int",
    "deliveryAddress": "dict",
    "deliveryRemark": "string",
    "items": "list",
    "isCheckedOut": "integer",
    "coverSheet": "string",
    "paymentSlip": "string",
    "deliveryFee": "float",
    "orderRemark": "string",
    "kitchenTicket": "string"
}
```

---

## Endpoint: `/order/update-status`

**Method:** PATCH

**Description:** (THAI: อัปเดตสถานะของรายการสั่งอาหาร. **(Requires Authentication: `write:order:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

*   `order_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)
*   `target_status`: string (one of: 'cooking', 'completed', 'cancelled')

**Response:**

```json
{
    "id": "string (UUID)",
    "orderAt": "string",
    "customer": "string | dict",
    "customerName": "string",
    "customerLineName": "string",
    "totalFoodPrice": "float",
    "orderType": "string",
    "processStatus": "string",
    "status": "dict",
    "receipt": "string",
    "totalQuantity": "integer",
    "table": "string | int",
    "deliveryAddress": "dict",
    "deliveryRemark": "string",
    "items": "list",
    "isCheckedOut": "integer",
    "coverSheet": "string",
    "paymentSlip": "string",
    "deliveryFee": "float",
    "orderRemark": "string",
    "kitchenTicket": "string"
}
```

---

# Order API (การจัดการคำสั่งซื้อ)



---

# Cashflow Transactions API (การจัดการรายการเดินบัญชี)

## Endpoint: `/cashflow-transaction`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลรายการเดินบัญชีทั้งหมดของร้านค้า. สามารถกรองตามประเภทรายการได้ (`withdraw` สำหรับถอน, `deposit` สำหรับฝาก, หรือ `all` สำหรับทั้งหมด). **(Requires Authentication)**)

**Request:**

*   `merchant_id`: string (Query parameter)
*   `tnx_type`: string (Query parameter, optional, defaults to 'all') - Can be 'withdraw', 'deposit', or 'all'.

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "cashAmount": "float",
        "by": "string (ผู้ดำเนินการ)",
        "transactionType": "string (ประเภทรายการ: 'withdraw' หรือ 'deposit')",
        "merchantId": "string (รหัสร้านค้า)",
        "performedAt": "string (วันเวลาที่ทำรายการ)"
    },
    ...
]
```

---

## Endpoint: `/cashflow-transaction/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดรายการเดินบัญชีตาม `tnx_id` และ `merchant_id`. **(Requires Authentication)**)

**Request:**

*   `merchant_id`: string (Query parameter)
*   `tnx_id`: string (Query parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "cashAmount": "float",
    "by": "string (ผู้ดำเนินการ)",
    "transactionType": "string (ประเภทรายการ: 'withdraw' หรือ 'deposit')",
    "merchantId": "string (รหัสร้านค้า)",
    "performedAt": "string (วันเวลาที่ทำรายการ)"
}

---

# Tables API (การจัดการโต๊ะ)

## Endpoint: `/tables`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลโต๊ะทั้งหมดของร้านค้า. **(Requires Authentication: `read:tables:{merchant_id}` or `read:all:{merchant_id}` permission)**)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "tableName": "string (ชื่อโต๊ะ)",
        "seats": "integer (จำนวนที่นั่ง)",
        "zoneId": "string (รหัสโซน)",
        "tableType": "string (รหัสประเภท/หมวดหมู่โต๊ะ)",
        "merchantId": "string (รหัสร้านค้า)",
        "remark": "string (หมายเหตุ)",
        "qrCode": "string (URL ของ QR Code)",
        "isAvailable": "boolean (สถานะว่าง)",
        "createdAt": "string (วันเวลาที่สร้าง)",
        "updatedAt": "string (วันเวลาที่แก้ไขล่าสุด)"
    },
    ...
]
```

---

## Endpoint: `/tables/create`

**Method:** POST

**Description:** (THAI: สร้างโต๊ะใหม่. **(Requires Authentication: `write:tables:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "tableName": "string",
    "seats": "integer",
    "zoneId": "string",
    "tableType": "string",
    "merchantId": "string",
    "remark": "string (optional)"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "tableName": "string",
    "seats": "integer",
    "zoneId": "string",
    "tableType": "string",
    "merchantId": "string",
    "remark": "string",
    "qrCode": "string",
    "isAvailable": "boolean",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/tables/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดโต๊ะ.)

**Request:**

*   `table_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "tableName": "string",
    "seats": "integer",
    "zoneId": "string",
    "tableType": "string",
    "merchantId": "string",
    "remark": "string",
    "qrCode": "string",
    "isAvailable": "boolean",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/tables/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดโต๊ะ. **(Requires Authentication: `write:tables:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

* `table_id`: string (URL parameter)
* `merchant_id`: string (URL parameter)

```json
{
    "tableName": "string",
    "seats": "integer",
    "zoneId": "string",
    "tableType": "string",
    "remark": "string",
    "isAvailable": "boolean"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "tableName": "string",
    "seats": "integer",
    "zoneId": "string",
    "tableType": "string",
    "merchantId": "string",
    "remark": "string",
    "qrCode": "string",
    "isAvailable": "boolean",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/tables/delete`

**Method:** DELETE

**Description:** (THAI: ลบโต๊ะ. **(Requires Authentication: `write:tables:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

*   `table_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```
```

---

# WebSocket API

These endpoints use the WebSocket protocol for real-time communication.



# WebSocket API

These endpoints use the WebSocket protocol for real-time communication.

## Endpoint: `/socket/server-device`

**Method:** WebSocket

**Description:** (THAI: WebSocket สำหรับอุปกรณ์เซิร์ฟเวอร์. ไคลเอนต์เชื่อมต่อและสามารถลงทะเบียน node ด้วยคำสั่ง `REGISTER_NODE` พร้อม `node_id`.)

**Request:**

*   `command`: "REGISTER_NODE" (optional)
*   `node_id`: "string" (required if `command` is "REGISTER_NODE")

**Response:**

(WebSocket connection, no specific JSON response model unless there's an error)

---

## Endpoint: `/socket/kitchen-main`

**Method:** WebSocket

**Description:** (THAI: WebSocket สำหรับอุปกรณ์หลักในครัว. ไคลเอนต์เชื่อมต่อและสามารถส่งข้อมูลได้.)

**Request:**

(WebSocket connection, accepts JSON data)

**Response:**

(WebSocket connection, no specific JSON response model)

---

## Endpoint: `/socket/kitchen-device`

**Method:** WebSocket

**Description:** (THAI: WebSocket สำหรับอุปกรณ์ในครัว. ไคลเอนต์เชื่อมต่อและสามารถลงทะเบียนครัวด้วยคำสั่ง `REGISTER_KITCHEN` พร้อม `node_id` และ `kitchen_id`.)

**Request:**

*   `command`: "REGISTER_KITCHEN" (optional)
*   `node_id`: "string" (required if `command` is "REGISTER_KITCHEN")
*   `kitchen_id`: "string" (required if `command` is "REGISTER_KITCHEN")

**Response:**

(WebSocket connection, no specific JSON response model unless there's an error)

---

## Endpoint: `/socket/busboy-device`

**Method:** WebSocket

**Description:** (THAI: WebSocket สำหรับอุปกรณ์ของพนักงานเก็บโต๊ะ. ไคลเอนต์เชื่อมต่อและสามารถลงทะเบียนด้วยคำสั่ง `REGISTER_BUSBOY` พร้อม `node_id`.)

**Request:**

*   `command`: "REGISTER_BUSBOY" (optional)
*   `node_id`: "string" (required if `command` is "REGISTER_BUSBOY")

**Response:**

(WebSocket connection, no specific JSON response model unless there's an error)

---

# Merchants API (การจัดการร้านค้า)



# Merchants API (การจัดการร้านค้า)

## Endpoint: `/merchants`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลร้านค้าทั้งหมด. การดำเนินการนี้จะดึงข้อมูลจากฐานข้อมูล MongoDB โดยใช้ `find` method. *หมายเหตุ: ต้องมีสิทธิ์ `admin`.*)

**Request:**

None

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "name": {
            "th": "string (ชื่อร้านค้า, ภาษาไทย)",
            "en": "string (ชื่อร้านค้า, ภาษาอังกฤษ)"
        },
        "description": {
            "th": "string (คำอธิบาย, ภาษาไทย)",
            "en": "string (คำอธิบาย, ภาษาอังกฤษ)"
        },
        "size": "string (ขนาด: XS, S, SM, MD, L, XL, XXL)",
        "location": "string (ที่ตั้ง)",
        "operationTime": {
            "monday": "string",
            "tuesday": "string",
            "wednesday": "string",
            "thursday": "string",
            "friday": "string",
            "saturday": "string",
            "sunday": "string"
        },
        "createdAt": "string (วันเวลาที่สร้าง)",
        "updatedAt": "string (วันเวลาที่แก้ไขล่าสุด)"
    },
    ...
]
```

---

## Endpoint: `/merchants/create`

**Method:** POST

**Description:** (THAI: สร้างร้านค้าใหม่. การดำเนินการนี้จะเพิ่มเอกสารใหม่ในคอลเลกชัน `Merchants` ในฐานข้อมูล MongoDB โดยใช้ `insert_one` method. *หมายเหตุ: ต้องมีสิทธิ์ `admin`.*)

**Request:**

```json
{
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "size": "string",
    "location": "string",
    "operationTime": {
        "monday": "string",
        "tuesday": "string",
        "wednesday": "string",
        "thursday": "string",
        "friday": "string",
        "saturday": "string",
        "sunday": "string"
    }
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "size": "string",
    "location": "string",
    "operationTime": {
        "monday": "string",
        "tuesday": "string",
        "wednesday": "string",
        "thursday": "string",
        "friday": "string",
        "saturday": "string",
        "sunday": "string"
    },
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/merchants/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดร้านค้าตาม `merchant_id` ที่ระบุ. การดำเนินการนี้จะดึงข้อมูลจากฐานข้อมูล MongoDB โดยใช้ `find_one` method. *หมายเหตุ: ต้องมีสิทธิ์ `admin` หรือสิทธิ์ `read:merchant:{merchant_id}` หรือ `read:all:{merchant_id}`.*)

**Request:**

*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "size": "string",
    "location": "string",
    "operationTime": {
        "monday": "string",
        "tuesday": "string",
        "wednesday": "string",
        "thursday": "string",
        "friday": "string",
        "saturday": "string",
        "sunday": "string"
    },
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/merchants/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดร้านค้า. การดำเนินการนี้จะอัปเดตเอกสารในคอลเลกชัน `Merchants` ในฐานข้อมูล MongoDB โดยใช้ `find_one_and_update` method. *หมายเหตุ: ต้องมีสิทธิ์ `admin` หรือสิทธิ์ `write:merchant:{merchant_id}` หรือ `write:all:{merchant_id}`.*)

**Request:**

* `merchant_id`: string (URL parameter)

```json
{
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "size": "string",
    "location": "string",
    "operationTime": {
        "monday": "string",
        "tuesday": "string",
        "wednesday": "string",
        "thursday": "string",
        "friday": "string",
        "saturday": "string",
        "sunday": "string"
    }
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "size": "string",
    "location": "string",
    "operationTime": {
        "monday": "string",
        "tuesday": "string",
        "wednesday": "string",
        "thursday": "string",
        "friday": "string",
        "saturday": "string",
        "sunday": "string"
    },
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/merchants/delete`

**Method:** DELETE

**Description:** (THAI: ลบร้านค้า. การดำเนินการนี้จะลบเอกสารออกจากคอลเลกชัน `Merchants` ในฐานข้อมูล MongoDB โดยใช้ `find_one_and_delete` method. *หมายเหตุ: ต้องมีสิทธิ์ `admin`.*)

**Request:**

*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```

---

# Kitchens API (การจัดการครัว)

## Endpoint: `/kitchens`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลครัวทั้งหมดของร้านค้า. **(Requires Authentication)**)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "name": {
            "th": "string (ชื่อครัว, ภาษาไทย)",
            "en": "string (ชื่อครัว, ภาษาอังกฤษ)"
        },
        "description": {
            "th": "string (คำอธิบาย, ภาษาไทย)",
            "en": "string (คำอธิบาย, ภาษาอังกฤษ)"
        },
        "merchantId": "string (รหัสร้านค้า)",
        "kitchenType": "string (ประเภทครัว)",
        "status": "string (สถานะ: 'active' หรือ 'inactive')",
        "createdAt": "string (วันเวลาที่สร้าง)",
        "updatedAt": "string (วันเวลาที่แก้ไขล่าสุด)"
    },
    ...
]
```

---

## Endpoint: `/kitchens/create`

**Method:** POST

**Description:** (THAI: สร้างครัวใหม่. **(Requires Authentication)**)

**Request:**

```json
{
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "kitchenType": "string",
    "status": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "kitchenType": "string",
    "status": "string",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/kitchens/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดครัว. **(Requires Authentication)**)

**Request:**

*   `kitchen_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "kitchenType": "string",
    "status": "string",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/kitchens/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดครัว. **(Requires Authentication)**)

**Request:**

* `kitchen_id`: string (URL parameter)
* `merchant_id`: string (URL parameter)

```json
{
  "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "kitchenType": "string",
    "status": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "kitchenType": "string",
    "status": "string",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/kitchens/delete`

**Method:** DELETE

**Description:** (THAI: ลบครัว. **(Requires Authentication)**)

**Request:**

*   `kitchen_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```

---

# Foods API (การจัดการเมนูอาหาร)

## Endpoint: `/foods`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลเมนูอาหารทั้งหมดของร้านค้า.)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "categoryId": "string (รหัสหมวดหมู่)",
        "name": {
            "th": "string (ชื่อเมนู, ภาษาไทย)",
            "en": "string (ชื่อเมนู, ภาษาอังกฤษ)"
        },
        "description": {
            "th": "string (คำอธิบาย, ภาษาไทย)",
            "en": "string (คำอธิบาย, ภาษาอังกฤษ)"
        },
        "price": "float (ราคา)",
        "status": "integer (สถานะ)",
        "ingredients": ["string (รหัสส่วนผสม)", ...],
        "addons": ["string (รหัสตัวเลือกเพิ่มเติม)", ...],
        "isPopular": "integer (สถานะยอดนิยม)",
        "isRecommended": "integer (สถานะแนะนำ)",
        "images": ["string (Base64)", ...],
        "menuType": "string (ประเภทเมนู: 'Foods', 'Beverages', 'Desserts', 'Others')",
        "merchantId": "string (รหัสร้านค้า)",
        "position": "integer (ลำดับ)",
        "kitchenIds": ["string (รหัสครัว)", ...],
        "minRequiredToSelectIngredients": "integer",
        "maxRequiredToSelectIngredients": "integer",
        "createdAt": "string (วันเวลาที่สร้าง)",
        "updatedAt": "string (วันเวลาที่แก้ไขล่าสุด)"
    },
    ...
]
```

---

## Endpoint: `/foods/create`

**Method:** POST

**Description:** (THAI: สร้างเมนูอาหารใหม่. **(Requires Authentication)**)

**Request:**

```json
{
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "price": "float",
    "status": "integer",
    "ingredients": ["string", ...],
    "addons": ["string", ...],
    "isPopular": "integer",
    "isRecommended": "integer",
    "images": ["string", ...],
    "menuType": "string",
    "merchantId": "string",
    "kitchenIds": ["string", ...],
    "minRequiredToSelectIngredients": "integer",
    "maxRequiredToSelectIngredients": "integer"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "price": "float",
    "status": "integer",
    "ingredients": ["string", ...],
    "addons": ["string", ...],
    "isPopular": "integer",
    "isRecommended": "integer",
    "images": ["string", ...],
    "menuType": "string",
    "merchantId": "string",
    "position": "integer",
    "kitchenIds": ["string", ...],
    "minRequiredToSelectIngredients": "integer",
    "maxRequiredToSelectIngredients": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/foods/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดเมนูอาหาร.)

**Request:**

*   `food_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "price": "float",
    "status": "integer",
    "ingredients": ["string", ...],
    "addons": ["string", ...],
    "isPopular": "integer",
    "isRecommended": "integer",
    "images": ["string", ...],
    "menuType": "string",
    "merchantId": "string",
    "position": "integer",
    "kitchenIds": ["string", ...],
    "minRequiredToSelectIngredients": "integer",
    "maxRequiredToSelectIngredients": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/foods/edit-menu-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดเมนูอาหาร. **(Requires Authentication)**)

**Request:**
* `food_id`: string (URL parameter)

```json
{
 "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "price": "float",
    "status": "integer",
    "ingredients": ["string", ...],
    "addons": ["string", ...],
    "isPopular": "integer",
    "isRecommended": "integer",
    "images": ["string", ...],
    "menuType": "string",
    "merchantId": "string",
    "kitchenIds": ["string", ...],
    "minRequiredToSelectIngredients": "integer",
    "maxRequiredToSelectIngredients": "integer",
    "position": "integer"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "categoryId": "string",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "price": "float",
    "status": "integer",
    "ingredients": ["string", ...],
    "addons": ["string", ...],
    "isPopular": "integer",
    "isRecommended": "integer",
    "images": ["string", ...],
    "menuType": "string",
    "merchantId": "string",
    "position": "integer",
    "kitchenIds": ["string", ...],
    "minRequiredToSelectIngredients": "integer",
    "maxRequiredToSelectIngredients": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```
---

## Endpoint: `/foods/delete`

**Method:** DELETE

**Description:** (THAI: ลบเมนูอาหาร. **(Requires Authentication)**)

**Request:**

*   `food_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```

---

### Food Categories (หมวดหมู่อาหาร)

#### Endpoint: `/foods/categories`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลหมวดหมู่อาหารทั้งหมดของร้านค้า.)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "name": {
            "th": "string (ชื่อหมวดหมู่, ภาษาไทย)",
            "en": "string (ชื่อหมวดหมู่, ภาษาอังกฤษ)"
        },
        "description": {
            "th": "string (คำอธิบาย, ภาษาไทย)",
            "en": "string (คำอธิบาย, ภาษาอังกฤษ)"
        },
        "merchantId": "string (รหัสร้านค้า)",
        "position": "integer (ลำดับ)",
        "status": "integer (สถานะ)",
        "createdAt": "string (วันเวลาที่สร้าง)",
        "updatedAt": "string (วันเวลาที่แก้ไขล่าสุด)",
        "menus": [
            {
                "id": "string (UUID of menu item)",
                "name": {
                    "th": "string",
                    "en": "string"
                },
                "description": {
                    "th": "string",
                    "en": "string"
                },
                "price": "float",
                "status": "integer",
                "ingredients": ["string", ...],
                "addons": ["string", ...],
                "isPopular": "integer",
                "isRecommended": "integer",
                "images": ["string", ...],
                "menuType": "string"
            }
        ]
    },
    ...
]
```

---

#### Endpoint: `/foods/categories/create`

**Method:** POST

**Description:** (THAI: สร้างหมวดหมู่อาหารใหม่. **(Requires Authentication: `write:food-categories:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

```json
{
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "status": "integer",
    "merchantId": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "position": "integer",
    "status": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/categories/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดหมวดหมู่อาหาร.)

**Request:**

*   `category_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "position": "integer",
    "status": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/categories/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดหมวดหมู่อาหาร. **(Requires Authentication: `write:food-categories:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

* `cat_id`: string (URL parameter)

```json
{
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "status": "integer",
    "merchant_id": "string",
    "position": "integer"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "merchantId": "string",
    "position": "integer",
    "status": "integer",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

#### Endpoint: `/foods/categories/delete`

**Method:** DELETE

**Description:** (THAI: ลบหมวดหมู่อาหาร. **(Requires Authentication: `write:food-categories:{merchant_id}` or `write:all:{merchant_id}` permission)**)

**Request:**

*   `cat_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "status": "ok",
    "rows_affected": 1
}
```

---

# Delivery Locations API (การจัดการสถานที่จัดส่ง)

## Endpoint: `/delivery-locations`

**Method:** GET

**Description:** (THAI: ดึงข้อมูลสถานที่จัดส่งทั้งหมดของร้านค้า. **(Requires Authentication)**)

**Request:**

*   `merchant_id`: string (Query parameter)

**Response:**

```json
[
    {
        "id": "string (UUID)",
        "name": {
            "th": "string (ชื่อสถานที่, ภาษาไทย)",
            "en": "string (ชื่อสถานที่, ภาษาอังกฤษ)"
        },
        "description": {
            "th": "string (คำอธิบาย, ภาษาไทย)",
            "en": "string (คำอธิบาย, ภาษาอังกฤษ)"
        },
        "deliveryFee": "float (ค่าจัดส่ง)",
        "status": "integer (สถานะ)",
        "deliveryAreas": [
            {
                "areaName": "string",
                "deliveryFee": "float"
            },
            ...
        ],
        "address": "string (ที่อยู่)",
        "freeDeliveryMinimumOrderPrice": "float (ยอดสั่งซื้อขั้นต่ำสำหรับการจัดส่งฟรี)",
        "merchantId": "string (รหัสร้านค้า)",
        "createdAt": "string (วันเวลาที่สร้าง)",
        "updatedAt": "string (วันเวลาที่แก้ไขล่าสุด)"
    },
    ...
]
```

---

## Endpoint: `/delivery-locations/create`

**Method:** POST

**Description:** (THAI: สร้างสถานที่จัดส่งใหม่. **(Requires Authentication)**)

**Request:**

```json
{
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "deliveryFee": "float",
    "status": "integer",
    "deliveryAreas": [
        {
            "areaName": "string",
            "deliveryFee": "float"
        },
        ...
    ],
    "address": "string",
    "freeDeliveryMinimumOrderPrice": "float",
    "merchantId": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "deliveryFee": "float",
    "status": "integer",
    "deliveryAreas": [
        {
            "areaName": "string",
            "deliveryFee": "float"
        },
        ...
    ],
    "address": "string",
    "freeDeliveryMinimumOrderPrice": "float",
    "merchantId": "string",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/delivery-locations/detail`

**Method:** GET

**Description:** (THAI: ดึงรายละเอียดสถานที่จัดส่ง. **(Requires Authentication)**)

**Request:**

*   `location_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
    "id": "string (UUID)",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "deliveryFee": "float",
    "status": "integer",
    "deliveryAreas": [
        {
            "areaName": "string",
            "deliveryFee": "float"
        },
        ...
    ],
    "address": "string",
    "freeDeliveryMinimumOrderPrice": "float",
    "merchantId": "string",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/delivery-locations/edit-detail`

**Method:** PATCH

**Description:** (THAI: แก้ไขรายละเอียดสถานที่จัดส่ง. **(Requires Authentication)**)

**Request:**

* `location_id`: string (URL parameter)

```json
{
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "deliveryFee": "float",
    "status": "integer",
    "deliveryAreas": [
        {
            "areaName": "string",
            "deliveryFee": "float"
        },
        ...
    ],
    "address": "string",
    "freeDeliveryMinimumOrderPrice": "float",
    "merchantId": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "name": {
        "th": "string",
        "en": "string"
    },
    "description": {
        "th": "string",
        "en": "string"
    },
    "deliveryFee": "float",
    "status": "integer",
    "deliveryAreas": [
        {
            "areaName": "string",
            "deliveryFee": "float"
        },
        ...
    ],
    "address": "string",
    "freeDeliveryMinimumOrderPrice": "float",
    "merchantId": "string",
    "createdAt": "string",
    "updatedAt": "string"
}
```

---

## Endpoint: `/delivery-locations/delete`

**Method:** DELETE

**Description:** (THAI: ลบสถานที่จัดส่ง. **(Requires Authentication)**)

**Request:**

*   `location_id`: string (URL parameter)
*   `merchant_id`: string (URL parameter)

**Response:**

```json
{
   "status": "ok",
   "rows_affected": 1
}
```

---

## Endpoint: `/cashflow-transaction/create`

**Method:** POST

**Description:** (THAI: สร้างรายการเดินบัญชีใหม่ (ฝาก/ถอน). **(Requires Authentication)**)

**Request:**

```json
{
    "cashAmount": "float",
    "transactionType": "string (ประเภทรายการ: 'withdraw' หรือ 'deposit')",
    "merchantId": "string"
}
```

**Response:**

```json
{
    "id": "string (UUID)",
    "cashAmount": "float",
    "by": "string (ผู้ดำเนินการ)",
    "transactionType": "string (ประเภทรายการ: 'withdraw' หรือ 'deposit')",
    "merchantId": "string (รหัสร้านค้า)",
    "performedAt": "string (วันเวลาที่ทำรายการ)"
}
```

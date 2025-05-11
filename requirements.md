# AirBnB Clone Backend - Requirements Specification

## Table of Contents
1. [User Authentication System](#1-user-authentication-system)
2. [Property Management System](#2-property-management-system)
3. [Booking System](#3-booking-system)

## 1. User Authentication System

### Overview
The User Authentication System will handle user registration, login, password management, and session control for all users in the system.

### API Endpoints

#### 1.1 User Registration
- **Endpoint**: `POST /api/v1/auth/register`
- **Description**: Creates a new user account in the system
- **Request Body**:
  ```json
  {
    "first_name": "string",
    "last_name": "string",
    "email": "string",
    "password": "string",
    "phone_number": "string",
    "role": "guest|host"
  }
  ```
- **Response (Success - 201 Created)**:
  ```json
  {
    "user_id": "uuid",
    "first_name": "string",
    "last_name": "string",
    "email": "string",
    "role": "guest|host",
    "created_at": "timestamp"
  }
  ```
- **Response (Error - 400 Bad Request)**:
  ```json
  {
    "error": "string",
    "message": "string"
  }
  ```

#### 1.2 User Login
- **Endpoint**: `POST /api/v1/auth/login`
- **Description**: Authenticates a user and returns a token
- **Request Body**:
  ```json
  {
    "email": "string",
    "password": "string"
  }
  ```
- **Response (Success - 200 OK)**:
  ```json
  {
    "access_token": "string",
    "token_type": "Bearer",
    "expires_in": "number",
    "user": {
      "user_id": "uuid",
      "first_name": "string",
      "last_name": "string",
      "email": "string",
      "role": "guest|host|admin"
    }
  }
  ```
- **Response (Error - 401 Unauthorized)**:
  ```json
  {
    "error": "string",
    "message": "string"
  }
  ```

#### 1.3 Password Reset Request
- **Endpoint**: `POST /api/v1/auth/forgot-password`
- **Description**: Initiates password reset process
- **Request Body**:
  ```json
  {
    "email": "string"
  }
  ```
- **Response (Success - 200 OK)**:
  ```json
  {
    "message": "Password reset instructions sent to your email"
  }
  ```
- **Response (Error - 404 Not Found)**:
  ```json
  {
    "error": "string",
    "message": "string"
  }
  ```

#### 1.4 Password Reset Confirmation
- **Endpoint**: `POST /api/v1/auth/reset-password`
- **Description**: Completes password reset with token validation
- **Request Body**:
  ```json
  {
    "token": "string",
    "new_password": "string"
  }
  ```
- **Response (Success - 200 OK)**:
  ```json
  {
    "message": "Password updated successfully"
  }
  ```
- **Response (Error - 400 Bad Request)**:
  ```json
  {
    "error": "string",
    "message": "string"
  }
  ```

#### 1.5 Logout
- **Endpoint**: `POST /api/v1/auth/logout`
- **Description**: Invalidates the current user session
- **Headers**: `Authorization: Bearer {token}`
- **Response (Success - 200 OK)**:
  ```json
  {
    "message": "Logged out successfully"
  }
  ```
- **Response (Error - 401 Unauthorized)**:
  ```json
  {
    "error": "string",
    "message": "string"
  }
  ```

### Validation Rules
1. **Email**:
   - Must be a valid email format
   - Must be unique in the system
   - Maximum length: 255 characters

2. **Password**:
   - Minimum length: 8 characters
   - Must contain at least one uppercase letter, one lowercase letter, one number, and one special character
   - Maximum length: 128 characters

3. **First Name and Last Name**:
   - Required fields
   - Alphanumeric characters, spaces, hyphens, and apostrophes allowed
   - Length: 2-50 characters each

4. **Phone Number**:
   - Optional field
   - If provided, must be a valid phone number format (E.164 recommended)
   - Length: 5-20 characters

5. **Role**:
   - Required field
   - Must be one of: "guest", "host" (admin role is assigned internally only)

### Performance Criteria
1. **Response Time**:
   - Registration and login endpoints must respond within 500ms under normal load
   - All other authentication endpoints must respond within 1 second

2. **Throughput**:
   - The system must handle at least 100 authentication requests per second

3. **Availability**:
   - 99.9% uptime for authentication services

4. **Security**:
   - All passwords must be securely hashed using bcrypt or Argon2
   - Authentication tokens must expire after 24 hours
   - Failed login attempts must be rate-limited (maximum 5 attempts before temporary lockout)
   - All authentication endpoints must use HTTPS

5. **Scalability**:
   - The system must maintain performance metrics with up to 1 million registered users

## 2. Property Management System

### Overview
The Property Management System enables hosts to create, update, manage, and delete property listings on the platform.

### API Endpoints

#### 2.1 Create Property
- **Endpoint**: `POST /api/v1/properties`
- **Description**: Creates a new property listing
- **Headers**: `Authorization: Bearer {token}`
- **Request Body**:
  ```json
  {
    "name": "string",
    "description": "string",
    "location": "string",
    "price_per_night": "decimal",
    "amenities": ["string"],
    "images": ["string (URLs)"],
    "max_guests": "integer"
  }
  ```
- **Response (Success - 201 Created)**:
  ```json
  {
    "property_id": "uuid",
    "host_id": "uuid",
    "name": "string",
    "description": "string",
    "location": "string",
    "price_per_night": "decimal",
    "amenities": ["string"],
    "images": ["string (URLs)"],
    "max_guests": "integer",
    "created_at": "timestamp",
    "updated_at": "timestamp"
  }
  ```
- **Response (Error - 400 Bad Request)**:
  ```json
  {
    "error": "string",
    "message": "string"
  }
  ```

#### 2.2 Get All Properties
- **Endpoint**: `GET /api/v1/properties`
- **Description**: Retrieves all property listings with optional filtering
- **Query Parameters**:
  - `location`: string (optional)
  - `price_min`: decimal (optional)
  - `price_max`: decimal (optional)
  - `page`: integer (optional, default=1)
  - `limit`: integer (optional, default=20)
- **Response (Success - 200 OK)**:
  ```json
  {
    "total": "integer",
    "page": "integer",
    "limit": "integer",
    "properties": [
      {
        "property_id": "uuid",
        "host_id": "uuid",
        "name": "string",
        "description": "string",
        "location": "string",
        "price_per_night": "decimal",
        "images": ["string (URLs)"],
        "rating": "decimal",
        "created_at": "timestamp"
      }
    ]
  }
  ```

#### 2.3 Get Property by ID
- **Endpoint**: `GET /api/v1/properties/{property_id}`
- **Description**: Retrieves detailed information about a specific property
- **Path Parameters**:
  - `property_id`: uuid
- **Response (Success - 200 OK)**:
  ```json
  {
    "property_id": "uuid",
    "host_id": "uuid",
    "host_info": {
      "user_id": "uuid",
      "first_name": "string",
      "last_name": "string"
    },
    "name": "string",
    "description": "string",
    "location": "string",
    "price_per_night": "decimal",
    "amenities": ["string"],
    "images": ["string (URLs)"],
    "max_guests": "integer",
    "rating": "decimal",
    "reviews": [
      {
        "review_id": "uuid",
        "user_id": "uuid",
        "rating": "integer",
        "comment": "string",
        "created_at": "timestamp"
      }
    ],
    "created_at": "timestamp",
    "updated_at": "timestamp"
  }
  ```
- **Response (Error - 404 Not Found)**:
  ```json
  {
    "error": "string",
    "message": "Property not found"
  }
  ```

#### 2.4 Update Property
- **Endpoint**: `PUT /api/v1/properties/{property_id}`
- **Description**: Updates property information
- **Headers**: `Authorization: Bearer {token}`
- **Path Parameters**:
  - `property_id`: uuid
- **Request Body**:
  ```json
  {
    "name": "string",
    "description": "string",
    "location": "string",
    "price_per_night": "decimal",
    "amenities": ["string"],
    "images": ["string (URLs)"],
    "max_guests": "integer"
  }
  ```
- **Response (Success - 200 OK)**:
  ```json
  {
    "property_id": "uuid",
    "host_id": "uuid",
    "name": "string",
    "description": "string",
    "location": "string",
    "price_per_night": "decimal",
    "amenities": ["string"],
    "images": ["string (URLs)"],
    "max_guests": "integer",
    "created_at": "timestamp",
    "updated_at": "timestamp"
  }
  ```
- **Response (Error - 403 Forbidden)**:
  ```json
  {
    "error": "string",
    "message": "You do not have permission to update this property"
  }
  ```

#### 2.5 Delete Property
- **Endpoint**: `DELETE /api/v1/properties/{property_id}`
- **Description**: Removes a property listing
- **Headers**: `Authorization: Bearer {token}`
- **Path Parameters**:
  - `property_id`: uuid
- **Response (Success - 204 No Content)**
- **Response (Error - 403 Forbidden)**:
  ```json
  {
    "error": "string",
    "message": "You do not have permission to delete this property"
  }
  ```

#### 2.6 Add Property Review
- **Endpoint**: `POST /api/v1/properties/{property_id}/reviews`
- **Description**: Adds a review to a property
- **Headers**: `Authorization: Bearer {token}`
- **Path Parameters**:
  - `property_id`: uuid
- **Request Body**:
  ```json
  {
    "rating": "integer",
    "comment": "string"
  }
  ```
- **Response (Success - 201 Created)**:
  ```json
  {
    "review_id": "uuid",
    "property_id": "uuid",
    "user_id": "uuid",
    "rating": "integer",
    "comment": "string",
    "created_at": "timestamp"
  }
  ```
- **Response (Error - 400 Bad Request)**:
  ```json
  {
    "error": "string",
    "message": "string"
  }
  ```

### Validation Rules
1. **Property Name**:
   - Required field
   - Length: 5-100 characters

2. **Description**:
   - Required field
   - Minimum length: 20 characters
   - Maximum length: 2000 characters

3. **Location**:
   - Required field
   - Should include city and country at minimum
   - Maximum length: 255 characters

4. **Price Per Night**:
   - Required field
   - Must be a positive decimal number
   - Maximum 2 decimal places
   - Range: 1.00 - 10000.00

5. **Amenities**:
   - Optional array field
   - If provided, must contain at least one amenity
   - Each amenity string maximum length: 50 characters

6. **Images**:
   - At least one image URL required
   - Maximum 20 images per property
   - Each URL must be valid and accessible
   - Maximum URL length: 2048 characters

7. **Max Guests**:
   - Required field
   - Integer between 1 and 20

8. **Reviews**:
   - Rating must be an integer between 1 and 5
   - Comment minimum length: 10 characters
   - Comment maximum length: 1000 characters
   - User must have completed a stay at the property to leave a review

### Performance Criteria
1. **Response Time**:
   - Property listing creation/update must respond within 1 second
   - Property search and retrieval must respond within 500ms

2. **Throughput**:
   - The system must handle at least 500 property search queries per second
   - The system must handle at least 50 property creation/update requests per second

3. **Availability**:
   - 99.9% uptime for property management services

4. **Search Performance**:
   - Property search must return results within 300ms for up to 1000 concurrent users
   - Property search must support pagination (default page size: 20 items)

5. **Image Handling**:
   - Image URLs must be validated and accessible
   - Property details with images must load within 1 second

6. **Security**:
   - Only authenticated hosts can create, update, or delete properties
   - Only the original host or an admin can modify or delete a property

## 3. Booking System

### Overview
The Booking System manages property reservations, including availability checking, booking creation, modification, cancellation, and payment processing.

### API Endpoints

#### 3.1 Check Property Availability
- **Endpoint**: `GET /api/v1/properties/{property_id}/availability`
- **Description**: Checks if a property is available for specific dates
- **Path Parameters**:
  - `property_id`: uuid
- **Query Parameters**:
  - `start_date`: date (YYYY-MM-DD)
  - `end_date`: date (YYYY-MM-DD)
- **Response (Success - 200 OK)**:
  ```json
  {
    "property_id": "uuid",
    "is_available": "boolean",
    "available_dates": [
      {
        "date": "YYYY-MM-DD",
        "status": "available|booked"
      }
    ],
    "price_per_night": "decimal",
    "total_nights": "integer",
    "total_price": "decimal"
  }
  ```
- **Response (Error - 400 Bad Request)**:
  ```json
  {
    "error": "string",
    "message": "string"
  }
  ```

#### 3.2 Create Booking
- **Endpoint**: `POST /api/v1/bookings`
- **Description**: Creates a new booking for a property
- **Headers**: `Authorization: Bearer {token}`
- **Request Body**:
  ```json
  {
    "property_id": "uuid",
    "start_date": "YYYY-MM-DD",
    "end_date": "YYYY-MM-DD",
    "number_of_guests": "integer"
  }
  ```
- **Response (Success - 201 Created)**:
  ```json
  {
    "booking_id": "uuid",
    "property_id": "uuid",
    "user_id": "uuid",
    "start_date": "YYYY-MM-DD",
    "end_date": "YYYY-MM-DD",
    "total_price": "decimal",
    "status": "pending",
    "created_at": "timestamp",
    "payment_link": "string (URL)"
  }
  ```
- **Response (Error - 400 Bad Request)**:
  ```json
  {
    "error": "string",
    "message": "string"
  }
  ```

#### 3.3 Get User Bookings
- **Endpoint**: `GET /api/v1/users/me/bookings`
- **Description**: Retrieves all bookings made by the authenticated user
- **Headers**: `Authorization: Bearer {token}`
- **Query Parameters**:
  - `status`: string (optional, "pending|confirmed|canceled")
  - `page`: integer (optional, default=1)
  - `limit`: integer (optional, default=10)
- **Response (Success - 200 OK)**:
  ```json
  {
    "total": "integer",
    "page": "integer",
    "limit": "integer",
    "bookings": [
      {
        "booking_id": "uuid",
        "property": {
          "property_id": "uuid",
          "name": "string",
          "location": "string",
          "image": "string (URL)"
        },
        "start_date": "YYYY-MM-DD",
        "end_date": "YYYY-MM-DD",
        "total_price": "decimal",
        "status": "pending|confirmed|canceled",
        "created_at": "timestamp"
      }
    ]
  }
  ```

#### 3.4 Get Host Bookings
- **Endpoint**: `GET /api/v1/users/me/host-bookings`
- **Description**: Retrieves all bookings for the authenticated host's properties
- **Headers**: `Authorization: Bearer {token}`
- **Query Parameters**:
  - `status`: string (optional, "pending|confirmed|canceled")
  - `property_id`: uuid (optional)
  - `page`: integer (optional, default=1)
  - `limit`: integer (optional, default=10)
- **Response (Success - 200 OK)**:
  ```json
  {
    "total": "integer",
    "page": "integer",
    "limit": "integer",
    "bookings": [
      {
        "booking_id": "uuid",
        "property": {
          "property_id": "uuid",
          "name": "string"
        },
        "guest": {
          "user_id": "uuid",
          "first_name": "string",
          "last_name": "string"
        },
        "start_date": "YYYY-MM-DD",
        "end_date": "YYYY-MM-DD",
        "total_price": "decimal",
        "status": "pending|confirmed|canceled",
        "created_at": "timestamp"
      }
    ]
  }
  ```

#### 3.5 Get Booking Details
- **Endpoint**: `GET /api/v1/bookings/{booking_id}`
- **Description**: Retrieves detailed information about a specific booking
- **Headers**: `Authorization: Bearer {token}`
- **Path Parameters**:
  - `booking_id`: uuid
- **Response (Success - 200 OK)**:
  ```json
  {
    "booking_id": "uuid",
    "property": {
      "property_id": "uuid",
      "name": "string",
      "location": "string",
      "host_id": "uuid",
      "host_name": "string",
      "image": "string (URL)"
    },
    "user_id": "uuid",
    "start_date": "YYYY-MM-DD",
    "end_date": "YYYY-MM-DD",
    "total_nights": "integer",
    "price_per_night": "decimal",
    "total_price": "decimal",
    "status": "pending|confirmed|canceled",
    "created_at": "timestamp",
    "payment": {
      "payment_id": "uuid",
      "amount": "decimal",
      "payment_date": "timestamp",
      "payment_method": "credit_card|paypal|stripe",
      "payment_status": "pending|completed"
    }
  }
  ```
- **Response (Error - 404 Not Found)**:
  ```json
  {
    "error": "string",
    "message": "Booking not found"
  }
  ```

#### 3.6 Update Booking Status
- **Endpoint**: `PATCH /api/v1/bookings/{booking_id}/status`
- **Description**: Updates the status of a booking (for hosts or admins)
- **Headers**: `Authorization: Bearer {token}`
- **Path Parameters**:
  - `booking_id`: uuid
- **Request Body**:
  ```json
  {
    "status": "confirmed|canceled"
  }
  ```
- **Response (Success - 200 OK)**:
  ```json
  {
    "booking_id": "uuid",
    "status": "confirmed|canceled",
    "updated_at": "timestamp"
  }
  ```
- **Response (Error - 403 Forbidden)**:
  ```json
  {
    "error": "string",
    "message": "You do not have permission to update this booking"
  }
  ```

#### 3.7 Cancel Booking
- **Endpoint**: `POST /api/v1/bookings/{booking_id}/cancel`
- **Description**: Cancels a booking (for guests)
- **Headers**: `Authorization: Bearer {token}`
- **Path Parameters**:
  - `booking_id`: uuid
- **Response (Success - 200 OK)**:
  ```json
  {
    "booking_id": "uuid",
    "status": "canceled",
    "canceled_at": "timestamp",
    "refund_amount": "decimal",
    "refund_status": "processing|completed"
  }
  ```
- **Response (Error - 400 Bad Request)**:
  ```json
  {
    "error": "string",
    "message": "Booking cannot be canceled (past cancellation window)"
  }
  ```

#### 3.8 Process Payment
- **Endpoint**: `POST /api/v1/bookings/{booking_id}/payments`
- **Description**: Processes payment for a booking
- **Headers**: `Authorization: Bearer {token}`
- **Path Parameters**:
  - `booking_id`: uuid
- **Request Body**:
  ```json
  {
    "payment_method": "credit_card|paypal|stripe",
    "payment_details": {
      // Payment method specific details
    }
  }
  ```
- **Response (Success - 201 Created)**:
  ```json
  {
    "payment_id": "uuid",
    "booking_id": "uuid",
    "amount": "decimal",
    "payment_date": "timestamp",
    "payment_method": "credit_card|paypal|stripe",
    "payment_status": "completed",
    "receipt_url": "string (URL)"
  }
  ```
- **Response (Error - 400 Bad Request)**:
  ```json
  {
    "error": "string",
    "message": "Payment processing failed"
  }
  ```

### Validation Rules
1. **Booking Dates**:
   - Required fields
   - `start_date` must be today or a future date
   - `end_date` must be after `start_date`
   - Minimum stay: 1 night
   - Maximum stay: 90 nights
   - Dates must be in YYYY-MM-DD format

2. **Number of Guests**:
   - Required field
   - Must be a positive integer
   - Must not exceed the property's `max_guests` value

3. **Property Availability**:
   - The property must be available for all dates between `start_date` and `end_date` inclusive
   - No overlapping bookings allowed

4. **Booking Status**:
   - Must be one of: "pending", "confirmed", "canceled"
   - Only hosts and admins can change status to "confirmed"
   - Both guests and hosts can change status to "canceled" (with different cancellation policies)

5. **Payment**:
   - Payment amount must match the booking's total price
   - Payment method must be one of the supported methods: "credit_card", "paypal", "stripe"

### Performance Criteria
1. **Response Time**:
   - Availability checking must respond within 500ms
   - Booking creation must respond within 1 second
   - Payment processing must respond within 3 seconds

2. **Throughput**:
   - The system must handle at least 100 booking requests per second
   - The system must process at least 50 payment transactions per second

3. **Availability**:
   - 99.99% uptime for booking and payment services
   - Zero double-bookings allowed

4. **Concurrency Handling**:
   - The system must handle concurrent booking attempts for the same property and dates
   - Implement optimistic locking or other concurrency control mechanisms

5. **Transaction Integrity**:
   - All booking and payment operations must be ACID-compliant
   - Failed payments must not result in confirmed bookings

6. **Security**:
   - All payment information must be securely handled and PCI-DSS compliant
   - Booking details must only be accessible to the guest, property host, and admins

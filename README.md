# Advanced CAPTCHA API Documentation

This document provides detailed information about the API endpoints for the Advanced CAPTCHA system.

## Base URL

All API requests should be made to:

```
http://localhost:3001
```

## Endpoints

### 1. Generate CAPTCHA

Generates a new CAPTCHA challenge.

- **URL:** `/captcha`
- **Method:** `GET`
- **Response Format:** JSON

#### Response

```json
{
  "id": "string",
  "image": "string",
  "dragChar": "string"
}
```

- `id`: A unique identifier for this CAPTCHA challenge
- `image`: A base64-encoded string of the CAPTCHA image
- `dragChar`: The character that needs to be dragged in the frontend

#### Example Request

```bash
curl http://localhost:3001/captcha
```

#### Example Response

```json
{
  "id": "a1b2c3d4e5f6g7h8i9j0",
  "image": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...",
  "dragChar": "@"
}
```

### 2. Verify CAPTCHA

Verifies the user's solution to the CAPTCHA challenge.

- **URL:** `/verify`
- **Method:** `POST`
- **Content-Type:** `application/json`

#### Request Body

```json
{
  "id": "string",
  "userInput": "string",
  "dragPosition": number
}
```

- `id`: The unique identifier received from the generate CAPTCHA endpoint
- `userInput`: The text entered by the user
- `dragPosition`: The position where the user dropped the drag character (0-4)

#### Response

```json
{
  "success": boolean
}
```

- `success`: `true` if the CAPTCHA was solved correctly, `false` otherwise

#### Example Request

```bash
curl -X POST http://localhost:3001/verify \
  -H "Content-Type: application/json" \
  -d '{"id":"a1b2c3d4e5f6g7h8i9j0","userInput":"ab3d@","dragPosition":3}'
```

#### Example Response

```json
{
  "success": true
}
```

## Error Handling

The API uses standard HTTP status codes to indicate the success or failure of requests.

- `200 OK`: The request was successful
- `400 Bad Request`: The request was invalid or cannot be served
- `500 Internal Server Error`: The server encountered an unexpected condition

In case of an error, the response will include a JSON object with an `error` field describing the issue.

Example error response:

```json
{
  "error": "Invalid or expired CAPTCHA"
}
```

## Usage Instructions

1. **Generate a CAPTCHA:**
   - Make a GET request to `/captcha`
   - Store the received `id` for later use
   - Display the `image` to the user
   - Use the `dragChar` for the drag-and-drop functionality

2. **Verify user's solution:**
   - Collect the user's text input
   - Determine the position where the user dropped the drag character (0-4)
   - Make a POST request to `/verify` with the `id`, `userInput`, and `dragPosition`
   - Check the `success` field in the response to determine if the CAPTCHA was solved correctly

3. **Security Considerations:**
   - Always use HTTPS in production to encrypt data in transit
   - Implement rate limiting to prevent brute-force attacks
   - Do not reuse CAPTCHA IDs; each should be for one-time use only

## Integration Example

Here's a basic example of how to integrate this API using JavaScript:

```javascript
async function generateCaptcha() {
  const response = await fetch('http://localhost:3001/captcha');
  const data = await response.json();
  // Store the ID and display the image and drag character to the user
  return data;
}

async function verifyCaptcha(id, userInput, dragPosition) {
  const response = await fetch('http://localhost:3001/verify', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ id, userInput, dragPosition }),
  });
  const data = await response.json();
  return data.success;
}

// Usage
(async () => {
  const captcha = await generateCaptcha();
  // ... Display captcha to user and get their input ...
  const result = await verifyCaptcha(captcha.id, userInput, dragPosition);
  if (result) {
    console.log('CAPTCHA solved successfully!');
  } else {
    console.log('CAPTCHA solution incorrect. Please try again.');
  }
})();
```

This documentation provides a comprehensive guide for integrating the Advanced CAPTCHA API into your application. For any further questions or support, please contact the API maintainers.

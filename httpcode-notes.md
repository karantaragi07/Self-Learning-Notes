# 🌐 HTTP Status Codes – Complete Notes

HTTP status codes are grouped into **5 categories** based on their **first digit**.  
Each category has common codes you should know for development and interviews.

---

## 🔹 1xx – Informational
- Request was **received and understood**.  
- Server is still **processing** the request.  
- Rarely seen in practice.  

### Common Codes
- **100 Continue** → Initial part of request received, client should continue.  
- **101 Switching Protocols** → Protocol change requested by client is accepted.  
- **102 Processing** → Server has received request and is processing, but no response yet.  

---

## 🔹 2xx – Success
- Request was **successfully received, understood, and processed**.  

### Common Codes
- **200 OK** → Standard success response.  
- **201 Created** → Resource successfully created.  
- **202 Accepted** → Request accepted for processing, not completed yet.  
- **204 No Content** → Request processed successfully, no response body.  

---

## 🔹 3xx – Redirection
- Further action needed to **complete the request**.  
- Often used for **URL redirects**.  

### Common Codes
- **301 Moved Permanently** → Resource moved to a new permanent URL.  
- **302 Found** → Temporary redirect.  
- **304 Not Modified** → Cached version can be used, no need to download again.  
- **307 Temporary Redirect** → Similar to `302`, but keeps request method (e.g., POST stays POST).  
- **308 Permanent Redirect** → Like `301`, but keeps request method.  

---

## 🔹 4xx – Client Error
- Error occurred on the **client’s side**.  
- Usually due to **bad requests, missing data, or lack of permissions**.  

### Common Codes
- **400 Bad Request** → Invalid syntax or malformed request.  
- **401 Unauthorized** → Authentication required (or failed).  
- **403 Forbidden** → Client authenticated but not allowed to access resource.  
- **404 Not Found** → Resource not found on the server.  
- **405 Method Not Allowed** → HTTP method not supported by resource.  
- **408 Request Timeout** → Client took too long to send request.  
- **429 Too Many Requests** → Client sent too many requests in a given time.  

---

## 🔹 5xx – Server Error
- Error occurred on the **server’s side** while processing request.  

### Common Codes
- **500 Internal Server Error** → Generic server failure.  
- **501 Not Implemented** → Server does not support requested functionality.  
- **502 Bad Gateway** → Invalid response from upstream server.  
- **503 Service Unavailable** → Server overloaded or down for maintenance.  
- **504 Gateway Timeout** → Upstream server did not respond in time.  
- **505 HTTP Version Not Supported** → Server doesn’t support requested HTTP version.  

---


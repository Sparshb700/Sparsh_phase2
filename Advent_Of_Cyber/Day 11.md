# XSS — Merry XSSMas

**Vulnerability Analysis:** Cross-Site Scripting (XSS) occurs when a web application fails to properly sanitize or escape user input, allowing malicious JavaScript to be executed in the context of a victim's browser. This can lead to session hijacking, defacement, or credential theft.

- **Reflected XSS:** The malicious script is "reflected" off the web server, typically via a URL parameter or form submission, and executed immediately.
    
- **Stored XSS:** The script is permanently stored on the target server (e.g., in a database or comment section) and executes every time a user views the infected page.
    
- **Defense Mechanisms:** Using `textContent` instead of `innerHTML`, implementing `HttpOnly` cookie flags, and robust output encoding.
    

## Solve:
#### `stored attack`
1. To confirm **Reflected XSS**, I targeted the search functionality. By injecting a script into the search query, I verified that the application reflects input directly into the HTML response without sanitization.
    
    - **Payload used:** `<script>alert(atob("VEhNe0V2aWxfQnVubnl9"))</script>`
    - **Flag:** `THM{Evil_Bunny}`

2. To confirm **Stored XSS**, I used the message portal intended for `McSkidy`. Since messages are saved to the backend database, the payload remains persistent for any user viewing the message logs.
    
    - **Payload used:** `<script>alert('Stored Meow Meow')</script>`
    - **Flag:** `THM{Evil_Stored_Egg}`
# AI in Security - `old sAInt nick`


## 1.
**Complete the AI showcase by progressing through all of the stages. What is the flag presented to you?**

Red Team --> One python script was provided to exploit vulnerability in web page.
Blue Team --> Analyze the logs
Software --> Identified and showcased vulnerabilities in code,

### `THM{AI_MANIA}`

## 2.
**Execute the exploit provided by the red team agent against the vulnerable web application hosted at `10.48.173.85:5000`. What flag is provided in the script's output after it?
Remember, you will need to update the IP address placeholder in the script with the IP of your vulnerable machine (`10.48.173.85:5000`)**

After running the exploit script I got this output:
```html
thecosmic@fedora ~/tryhackme $ python3 script.py
Response Status Code: 200

Response Headers:
  Date: Tue, 09 Dec 2025 09:19:56 GMT
  Server: Apache/2.4.65 (Debian)
  X-Powered-By: PHP/8.1.33
  Expires: Thu, 19 Nov 1981 08:52:00 GMT
  Cache-Control: no-store, no-cache, must-revalidate
  Pragma: no-cache
  Vary: Accept-Encoding
  Content-Encoding: gzip
  Content-Length: 540
  Keep-Alive: timeout=5, max=99
  Connection: Keep-Alive
  Content-Type: text/html; charset=UTF-8

Response Body:
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard - SQLi Lab</title>
    <link href="assets/css/bootstrap.min.css" rel="stylesheet">
    <link href="assets/css/style.css" rel="stylesheet">
</head>
<body class="dashboard-body">
    <div class="dashboard-container">
        <div class="welcome-banner">
            <h1>Welcome, admin!</h1>
            <p>You have successfully logged in to the system.</p>
        </div>


        <div class="alert alert-success alert-dismissible fade show" role="alert">
            <h4 class="alert-heading">Exploit Successful!</h4>
            <hr>
            <p class="mb-0"><code>FLAG: THM{SQLI_EXPLOIT}</code></p>
            <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
        </div>

        <a href="logout.php" class="btn btn-danger">Logout</a>
    </div>

    <script src="assets/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```
	This had the flag

### `FLAG: THM{SQLI_EXPLOIT}`

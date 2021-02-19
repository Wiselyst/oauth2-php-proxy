# OAuth2 Proxy for PHP

This package is a lightweight proxy for implementing the OAuth2 flow in Single Page Applications using PHP.

## Installation

Add the repository to your package.json:
```json
    "repositories":[
        {
            "type": "vcs",
            "url": "git@github.com:Wiselyst/oauth2-php-proxy.git"
        }
    ],
```
then require the package using composer:
```bash
composer require wiselyst/oauth2-php-proxy
```

## Usage
```php
try{
    // Initialize the proxy
    $proxy = new OAuth2Proxy();
    
    // Enable grant types
    $proxy->enableGrantType('password');
    $proxy->enableGrantType('refresh_token');

    // Enable CSRF Protection (optional)
    $proxy->csrfProtection(true);
    
    // OAuth remote server
    $proxy->setApiHost('http://localhost:8000/myapp');
    $proxy->setClientCredentials('clientid', 'clientsecret');
    
    // Single Page Application directory
    $proxy->setSpaDir(__DIR__ . '/static');
        
    // Run!
    $proxy->run();
}catch(Exception $e){
    // Do something with your exception...
}
```

### Supported flows and grant types
The following grant types are supported: authorization_code, refresh_token, client_credentials and password.

### Require authentication (authorization_code only)
It is possible to require the client to be authenticated in order to process Single Page Application requests:
```php
$proxy->requireAuthentication();
```

### CSRF Protection
If enabled, cross-site request forgeries protection, will set a cookie named `XSRF-TOKEN` with a token. The token must be sent as a request header on each /api or /token request.

Example (jQuery):
```js
$.ajax{ // or use $.ajaxSetup
    // ...
    headers: {
        'X-SRF-TOKEN': Cookie.get('XSRF-TOKEN') // See js-cookie
    }
    // ...
}
```
Angular Http Client or Axios will automatically include the `XSRF-TOKEN` value.

### Proxy endpoints
1. /callback - Authorization code callback
2. /redirect - Redirect for authorization code request
3. /token - Token request
4. /api/ - Proxy API to remote server

Everything else is rewritten to the Single Page Application. 

### OAuth endpoints
The default OAuth2 endpoints can be overwritten as follows:
```php
OAuth2Proxy::REMOTE_TOKEN_ENDPOINT = '/token';
OAuth2Proxy::REMOTE_AUTHORIZE_ENDPOINT = '/authorize';
```
### Server configuration
The server should be configured to rewrite all requests to your proxy script

#### Apache (.htaccess)
```apache
<IfModule mod_rewrite.c>
	RewriteEngine On

    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^ index.php [NC,L]
</IfModule>
```

#### Nginx
```nginx
location / {
  if (!-e $request_filename){
    rewrite ^(.*)$ /index.php break;
  }
}
```
### Suggested directory structure
```
- public (Document root)
--- .htaccess (Apache only)
--- index.php

- static (SPA dir)
- vendor
- package.json
```

## Improvements
- Support CORS policy
- Complete scope support
- Proxy endpoints customization
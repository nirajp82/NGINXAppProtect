# NGINX Request Flow with NGINX App Protect

## Table of Contents

1. [Introduction](#introduction)
2. [Request Flow Overview](#request-flow-overview)
   1. [Initial Request Processing by NGINX](#initial-request-processing-by-nginx)
   2. [NGINX App Protect Modules](#nginx-app-protect-modules)
   3. [Processing Order for App Protect DoS and WAF](#processing-order-for-app-protect-dos-and-waf)
3. [NGINX Context: Loading NGINX App Protect WAF Module](#nginx-context-loading-nginx-app-protect-waf-module)

---

## Introduction

The NGINX process looks at the incoming HTTP request first and decides whether to reject it or allow it to continue to the NGINX App Protect modules to further investigate the request and reject if necessary.

For example, if the incoming request packet header is malformed, then NGINX will drop the request before NGINX App Protect (DoS/WAF) gets to look at the request. There are other NGINX modules that run after App Protect WAF, the most common being NGINX access (allow/deny per IP) and NGINX auth.

---

## Request Flow Overview

### Initial Request Processing by NGINX

When an incoming HTTP request is received, the NGINX process first inspects the request. If the request is malformed or deemed suspicious, NGINX may reject it outright, preventing further inspection by other security modules. For instance, if the packet header is malformed, NGINX will drop the request before it reaches NGINX App Protect (which includes both the DoS and WAF modules).

### NGINX App Protect Modules

NGINX App Protect provides two primary security modules for analyzing incoming requests:

- **App Protect DoS (Denial of Service)**: This module inspects requests for signs of DDoS or other denial of service attacks.
- **App Protect WAF (Web Application Firewall)**: This module inspects requests for security threats targeting application vulnerabilities, such as SQL injection, cross-site scripting (XSS), etc.

---

### Processing Order for App Protect DoS and WAF

If you have both NGINX App Protect DoS and NGINX App Protect WAF installed, then the processing order for an incoming request is as follows:

1. **NGINX Initial Check**: NGINX looks at the request first and if it doesn’t drop the request, it will pass it to the App Protect DoS module.
2. **App Protect DoS Module**: The App Protect DoS module inspects the request. If it doesn’t reject the request, it passes the request to the App Protect WAF module.
3. **App Protect WAF Module**: The App Protect WAF module inspects the request for web application attacks.
4. **NGINX Final Processing**: If the request is not rejected by either of the App Protect modules, control goes back to NGINX for final handling (such as load balancing, serving content, etc.).

---

## NGINX Context: Loading NGINX App Protect WAF Module

App Protect works like other NGINX modules in that once you’ve installed it, you still need to load it through the NGINX main configuration file (`/etc/nginx/nginx.conf`). Use the `load_module` directive to do this. Then you also need to turn it on using the `app_protect_enable` directive.

### Example Configuration:

1. **Loading the Module**: In the `nginx.conf` file, add the following line to load the App Protect WAF module:

   ```nginx
   load_module modules/ngx_http_app_protect_module.so;
   ```

2. **Enabling App Protect**: To enable the App Protect WAF, add the following directive:

   ```nginx
   app_protect_enable on;
   ```

This will activate the App Protect WAF module and allow it to inspect incoming HTTP requests.

--- 

Let me know if you need further changes!

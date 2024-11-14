# NGINX Request Flow with NGINX App Protect

## Table of Contents

1. [Introduction](#introduction)
2. [Request Flow Overview](#request-flow-overview)
   1. [Initial Request Processing by NGINX](#initial-request-processing-by-nginx)
   2. [NGINX App Protect Modules](#nginx-app-protect-modules)
   3. [Processing Order for App Protect DoS and WAF](#processing-order-for-app-protect-dos-and-waf)
3. [NGINX Context](#NGINX-Context)
4. [Loading NGINX App Protect WAF Module](#loading-nginx-app-protect-waf-module)

---

## Introduction

The NGINX process looks at the incoming HTTP request first and decides whether to reject it or allow it to continue to the NGINX App Protect modules to further investigate the request and reject if necessary.

For example, if the incoming request packet header is malformed, then NGINX will drop the request before NGINX App Protect (DoS/WAF) gets to look at the request. There are other NGINX modules that run after App Protect WAF, the most common being NGINX access (allow/deny per IP) and NGINX auth.

---

## Request Flow Overview

The NGINX process looks at the incoming HTTP request first and decides whether to reject it or allow it to continue to the NGINX App Protect modules to further investigate the request and reject if necessary.

For example, if the incoming request packet header is malformed, then NGINX will drop the request before NGINX App Protect (DoS/WAF) gets to look at the request. There are other NGINX modules that run after App Protect WAF, the most common being NGINX access (allow/deny per IP) and NGINX auth.

If you have both NGINX App Protect DoS and NGINX App Protect WAF installed then the processing order for an incoming request is: NGINX looks at the request first and if it doesn't drop the request, it will pass it to the App Protect DoS module, which will look at the request, and then if it doesn't reject the request, it will then pass the request on to the App Protect WAF module and if it's not rejected then control goes back to NGINX.

---

## NGINX Context: 

Each Nginx configuration file should follow certain structure so that it can be parsed by the runtime. There are two types of configurations in Nginx: directive and block. 

- Directive: A directive is a single line configuration that ends with a semicolon.
- Block: A block is a container for a group of directives that are enclosed by curly brackets / braces. A block is assigned a name that precedes the opening curly bracket. The block name indicates the circumstances that necessitates grouping of the enclosed directives. Hence, a block is often referred to as a context.
![image](https://github.com/user-attachments/assets/eb2898dd-a271-4936-96f0-5b0440bfa99c)

![image](https://github.com/user-attachments/assets/648c7064-4d07-4fbf-a251-6e36bec329ac)


## Loading NGINX App Protect WAF Module

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


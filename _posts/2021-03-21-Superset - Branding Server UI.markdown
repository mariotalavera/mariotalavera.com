---
layout: post
title:  "Superset - Branding Server UI"
date:   2021-03-21 00:01:00 -0500
categories: superset
published: true
---

This overview shows how to brand Apache's Superset with your company's logo, etc.

## Brand-able Items

1. Upper left logo
1. Loading animation (<b>Note 1</b>)
1. Company name
1. Browser's favicon

### Notes
 
1. The animation file name, **loading.gif**, is hardcoded (ver 1.0 at least).  In this case, just save rename the original gif and replace with our file.

```bash
superset/superset/static/assets/images/loading.gif
```

## Media Files Location

```bash
superset/superset/static/assets/images
```

<!-- ## CSS Files Location

```bash
superset/superset/static/assets/css?!?!?!
``` -->

## Configuration File Location

```bash
superset/docker/pythonpath_dev/superset_config.py
```

## Config Details

```bash
APP_NAME = "Company Name"

FAVICONS = [{"href": "/static/assets/images/company_favicon.png"}]

APP_ICON = "/static/assets/images/company_logo.jpg"
```

# Cloudfront

- To force SSL on your CF site, you need to set the _Viewer Protocol Policy_ to
    - Redirect HTTP to HTTPS
    - HTTPS only
- You can add certain headers like _country, address, timezone, etc_: `Cloudfront-Viewer-Country`.
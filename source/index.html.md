---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://www.upfeat.com/'>Documentation by Upfeat</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Introduction

Welcome to the Insert Coupon Blocks API! You can use our API to access Upfeat Coupon API endpoints, which can get information on various merchants, and promotions in our database.

Our API has predictable resource-oriented URLs, accepts form-encoded request bodies, returns JSON responses, and uses standard HTTP response codes and authentication.

# Authentication

> To authorize, use this code to get jwt:

```shell
# First, request a JSON Web Token (JWT)
curl -X POST "https://coupon-api.upfeat.com/block-insert/auth" \
  -H "Content-Type:application/json" \
  --data '{"username": example_user,"password": example_password}'

# Use the token string returned from auth server to make requests
curl -X POST "https://coupon-api.upfeat.com/block-insert" \
  -H "Content-Type:application/json" \
  -H "Authorization: Bearer bearer_token" \
  --data '{"article_html": html_snippet}'
```

> Make sure to replace `example_user` and `example_password` with your credential.

Authentication to the API is performed via bearer token Authentication. 

Before you make request to Insert Coupon Blocks API, you will need to obtain a JSON web token from auth server by including provided username and password in the payload.
We will authenticate to your web server and will remain logged in for the duration of the action.

`Authorization: Bearer bearer_token`

# Insert Coupon Blocks

## Create a Signed JWT Token

```shell
curl -X POST "https://coupon-api.upfeat.com/block-insert/auth" \
  -H "Content-Type:application/json" \
  --data '{"username": example_user,"password": example_password}'
```

> The above command returns JSON structured like this:

```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImZvcmJlcyIsInBhc3N3b3JkIjoiNihQckE2bVdYOjMkXWooRCIsImlhdCI6MTYxODI1ODkwNywiZXhwIjoxNjE4MjgwNTA3fQ.SpnX4I0ydxBc-g5lVmrZ-GAr1YErxhIgnC2n6Lp61fM",
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImZvcmJlcyIsInBhc3N3b3JkIjoiNihQckE2bVdYOjMkXWooRCIsImlhdCI6MTYxODI1ODkwN30.K9U2ENxigV8lAFfefEmUg6umIR8tjNiUJeBBegMn6D4"
}
```

This endpoint creates a token.

### HTTP Request

`POST https://coupon-api.upfeat.com/block-insert/auth`

### JSON Body Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- |-----------
username | Yes | String | The username of the user requesting the token.
password | Yes | String | The password of the user requesting the token.

## Request Insert Coupon Blocks response

```shell
curl -X POST "https://coupon-api.upfeat.com/block-insert" \
  -H "Content-Type:application/json" \
  -H "Authorization: Bearer bearer_token" \
  --data '{"article_html": "<article class=\"pay-wall-content content_5fc7dc3eb8e1fa000630ca4e current-page\" id=\"article-container-0\"><fbs-ad class=\"ntv-contentd\" position=\"ntv-contentd\" ad-id=\"ntv-contentd\" batched=\"\" display-called=\"\" style=\"\"><div id=\"ntv-contentd\" aria-hidden=\"true\" data-google-query-id=\"CL2S0LD72-8CFYmaAQodIQ0IBQ\">......</div></div></main></article>"}'
```

> To insert content into the passed article_html:

```shell
curl -X POST "https://coupon-api.upfeat.com/block-insert" \
  -H "Content-Type:application/json" \
  -H "Authorization: Bearer bearer_token" \
  --data 
  '{
    "content_snippet": "<div> 
                          Get more {{ merchant_name }} coupons:
                          <a href=\"{{ coupon_page_url }}\"> 
                            {{ promo_count }} {{ coupon_term }} available for up to {{ popular_discount_percentage }} off
                          </a>
                        </div>", 
    "article_html": html_snippet
   }'
```

> The above command returns JSON structured like this:

```json
[
  {
    "productBoxMatch": {
      "selector": "#article-stream-0 > div:nth-child(2) > div:nth-child(2) > div:nth-child(3) > div:nth-child(1) > div:nth-child(14)",
      "merchantInfo": {
        "name": "eBay",
        "coupon_page_url": "https://forbes.upfeat.com/coupons/ebay.com/",
        "coupon_page_url_text": "eBay deals for at least 15% off",
        "promo_count": 31,
        "popular_discount_percentage": "15% Off",
        "popular_discount_title": "eBay deals for at least 15% off"
      }
    },
  },
  {
    "productBoxMatch": {
      "selector": "#article-stream-0 > div:nth-child(2) > div:nth-child(2) > div:nth-child(3) > div:nth-child(1) > div:nth-child(20)",
      "merchantInfo": {
            "name": "Walmart",
            "coupon_page_url": "https://forbes.upfeat.com/coupons/walmart.com/",
            "coupon_page_url_text": "Walmart sales and rollbacks for over 20% off",
            "promo_count": 40,
            "popular_discount_percentage": "20% Off",
            "popular_discount_title": "Walmart sales and rollbacks for over 20% off"
      }
    },
  },
   …… 
 ]
```

This endpoint retrieves a returns a list of json responses for the product boxes which has a valid merchant.

<aside class="warning">Note we send no response for a product box if we could not find a merchant in upfeat coupons</aside>

### HTTP Request

`POST https://coupon-api.upfeat.com/block-insert`

### Headers
Header | Description
--------- | -----------
Authorization | The token you retrieved by using username and password which prove who you are

### JSON Body Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- |-----------
article_html | Yes | String | The input should contains article html body element and we will detect product boxes.
content_snippet | No | String | If the parameter is set, the content will be used to insert content into the passed article_html and will be included in the output. The content_snippet content should be html, with placeholders for where to put content. Supported placeholders are as follows: <ul><li>merchant_name: The merchant's name</li><li>coupon_term: The coupon term to use for the merchant</li><li>discount_count: The number of discounts available</li><li>coupon_page_url: The URL for the corresponding merchant's coupon page on Forbes</li><li>coupon_page_url_text: The SEO optimized text that should be used in the link to the Forbes coupon page</li><li>promo_count: The total count of promotions on the Forbes coupon page</li><li>popular_discount_percentage: The most popular discount percentage for the merchant</li><li>popular_discount_title:  The title of the currently most popular discount on the Forbes coupon page (ie. 10% off clothing)</li></ul>


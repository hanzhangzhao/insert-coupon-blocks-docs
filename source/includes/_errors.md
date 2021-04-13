# Errors

The Insert Coupon Blocks API uses the following error codes:


Error Code | Meaning
---------- | -------
401 | Unauthorized Access Token -- The authentication header is missing in the request.
403 | Unauthorized Token -- Your auth token is invalid.
403 | Unauthorized User -- Your user password is wrong.
404 | User Not Found -- The specified user could not be found.
405 | Method Not Allowed -- You tried to make a request with an invalid method.
500 | Internal Server Error -- We had a problem with our server. Try again later.

# Safari CORS POST request issue
- It seems to be a problem through all versions of safari tested have been several versions between 14.x to 17.x

## The Basis of the Problem
When a CORS POST request is done, there is a preflight request initiated, to check if the server accepts this post request.

Many times when POST is used to call some action on the Server we have a JSON response we expect.

We call `response = await fetch(url, options)`
Now usually the Browser manages the preflight request in the background, thus we get response the status of 200 and have a `response.json()` promise available directly.

However in all known versions of safari, this is not the case sometimes. Sometimes we get the status of 204 without any content. As if the result of the preflight request is falsly returned to the fetch call.

## Checking a few things
- check with setting Access-Control-Max-Age [ ]
- is there any difference in how the Server handles the 
    - no difference in headers
    - check logs [ ]
- is there any difference between the requests?
    - content -> impossible to change should not be a real difference
    - path -> impossible to change should not be a real issue
    - length -> check this [x] no difference depending on length
    - specific content -> only hash [ ]
    

### Respone from /credentials request
```
Request URL: https://www.bilder-befunde.at/pwa-api/api/v1/credentials/
Request Method: POST
Status Code: 200  (from service worker)
Access-Control-Allow-Credentials: true
Access-Control-Allow-Headers: Content-Type, *
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Origin: https://pin-pwa-dev.bilder-befunde.at
Content-Length: 36
Content-Type: application/json
Date: Thu, 15 Feb 2024 09:12:37 GMT
Provisional headers are shown
Accept: */*
Content-Type: application/json
Origin: https://pin-pwa-dev.bilder-befunde.at
Referer: https://pin-pwa-dev.bilder-befunde.at/
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_6) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/15.4 Safari/605.1.15

{ 
    username: "1979-10-18", 
    hashedPw: "09ed4a6496e6912fb63658679fa67fd3aa875f0556b5192f98044e083952dbab",
    isMedic: false
    rememberMe: false
}
```

### Response from /data request
```
Request URL: https://www.bilder-befunde.at/pwa-api/api/v1/data/
Request Method: POST
Status Code: 204  (from service worker)
Access-Control-Allow-Credentials: true
Access-Control-Allow-Headers: Content-Type, *
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Origin: https://pin-pwa-dev.bilder-befunde.at
Date: Thu, 15 Feb 2024 09:25:53 GMT
Provisional headers are shown
Accept: */*
Content-Type: application/json
Origin: https://pin-pwa-dev.bilder-befunde.at
Referer: https://pin-pwa-dev.bilder-befunde.at/
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_6) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/15.5 Safari/605.1.15
```

### Response from /studies request
```
Request URL: https://www.bilder-befunde.at/pwa-api/api/v1/studies/
Request Method: POST
Status Code: 204  (from service worker)
Access-Control-Allow-Credentials: true
Access-Control-Allow-Headers: Content-Type, *
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Origin: https://pin-pwa-dev.bilder-befunde.at
Date: Thu, 15 Feb 2024 09:28:20 GMT
Provisional headers are shown
Accept: */*
Content-Type: application/json
Origin: https://pin-pwa-dev.bilder-befunde.at
Referer: https://pin-pwa-dev.bilder-befunde.at/
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_6) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/15.5 Safari/605.1.15
```
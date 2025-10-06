# Lentoparkki Remote Reservations

## About

Make Lentoparkki reservations remotely from our collaborators.

## Document

This interface was created 2025 for our favourite aviation company. The latest document can be found at https://github.com/Lentoparkki/remotereservation/

## Performace

The server has been rated at 300 req/s. More is available at request. See Contact info in the end.


# Scope

For Lentoparkki collaborators able to make and cencel reservations at Lentoparkki @ Ohtolankatu 17. 

# Tech

API access via REST and JSON.  A mock server is provided for general public, with API URL and API key available privately. 

Mock server is fully compatible with production server. Requests support both GET and POST parameters.


# Space query

Querying price for the parking and if there's space.

* http://apimock.lentoparkki.fi/spacerequest?key=fgDeQDIRwerhUn5I&from=202502021840&to=202503021900


## Request

| Parameter | Description |
| --- | --- |
| key | API key. Mock server uses fgDeQDIRwerhUn5I |
| from | 12-digit From datestamp. YYYYMMDDHHmm as in Years, Months, Days, Hours and minutes. All 0 numbers are used. |
| to | 12-digit From datestamp. YYYYMMDDHHmm as in Years, Months, Days, Hours and minutes. All 0 numbers are used. |

## Successful Response


```
HTTP/1.1 200 OK
Content-Type: application/json
X-Request-ID: trace-QTmoywXd

{
    "status": "AVAILABLE",
    "message": "We have space for the selected timeframe. You can proceed with the reservation.",
    "priceEUR" : 45.78,
    "mockheaders" : All request headers,
    "mockrequest" : Request parameters,
}
```


## Unsuccessful Responses, incomplete

### Parameter format problem
```
HTTP/1.1 400 Invalid Request
Content-Type: application/problem+json
X-Request-ID: trace-eTYaskh

{
    "message": "e.g. from not acceptable: ...",
    "status": "PARAM",
    "mockheaders" : All request headers,
    "mockrequest" : Request parameters,
}
```


### API key problem
```
HTTP/1.1 401 Unauthorized
Content-Type: application/problem+json
WWW-Authenticate: Bearer realm="API Access"
X-Request-ID: trace-eTYaskh

{
    "message": "The supplied API key is missing, expired, or invalid. Please check your token and try again.",
    "status": "TOKEN",
    "mockheaders" : All request headers,
    "mockrequest" : Request parameters,
}

```

### Lot is full

```
HTTP/1.1 409 Conflict
Content-Type: application/problem+json
X-Request-ID: trace-abc123

{
    "status": "UNAVAILABLE",
    "message": "Parking is not available for the requested timeframe",
    "mockheaders" : All request headers,
    "mockrequest" : Request parameters,

}
```

## Mock server difference

mockheaders and mockrequest parameters are only available from mock server. These are omitted from production server.




# Reservation

```
https://apimock.lentoparkki.fi/spacereservation?key=fgDeQDIRwerhUn5I&from=202502021840&to=202503021900&name=Testi%20K%C3%A4ytt%C3%A4j%C3%A4&email=user@domain.org &phone=358102295860&flight=AY1476&reservation=UU291T55

i.e.:

https://apimock.lentoparkki.fi/spacereservation
?key=fgDeQDIRwerhUn5I
&from=202502021840
&to=202503021900
&name=Testi%20K%C3%A4ytt%C3%A4j%C3%A4
&email=user@domain.org &phone=358102295860
&flight=AY1476
&reservation=UU291T55

```


## Parameters

| Parameter | example | Description |
| --- | --- | --- |
| key | fgDeQDIRwerhUn5I | API key. Mock server uses fgDeQDIRwerhUn5I |
| from | 202502021840 | 12-digit From datestamp. YYYYMMDDHHmm as in Years, Months, Days, Hours and minutes. All 0 numbers are filled. |
| to | 202503021900 |  12-digit From datestamp. YYYYMMDDHHmm as in Years, Months, Days, Hours and minutes. All 0 numbers are filled. |
| regnro  | GEG505 | String, uppercase, without -, hyphen or minus. |
| email | customer.email@lentoparkki.fi |  Email address to contact car owner for customer service.  |
| name | Testi%20K%C3%A4ytt%C3%A4j%C3%A4 |  Customer name, UTF-8. Urlencoded. |
| phone | 358102295860 |  Phone number to customer, international format without + or -. |
| flight | AY1476 | Flight number. No spaces or dashes.  |
| reservation | UU291T55 |  Your reservation number. No spaces or dashes.  |



## Successful Response

```
HTTP/1.1 201 Created
Content-Type: application/json
X-Request-ID: trace-E3xsZXAp

{
    "reservation_id": 1842582,
    "priceEUR": 45.78,
    "status": "CONFIRMED",
    "message": "We have reserved space for this vehicle. Thank you for doing business with Lentoparkki.",
    "mockheaders" : All request headers,
    "mockrequest" : Request parameters,
}
```


## Unsuccessful Responses, incomplete


### Parameter format problem
```
HTTP/1.1 400 Invalid Request
Content-Type: application/problem+json
X-Request-ID: trace-eTYaskh

{
    "message": "e.g. from not acceptable: ...",
    "status": "PARAM",
    "mockheaders" : All request headers,
    "mockrequest" : Request parameters,
}
```


### API key problem

```
HTTP/1.1 401 Unauthorized
Content-Type: application/problem+json
WWW-Authenticate: Bearer realm="API Access"
X-Request-ID: trace-eTYaskh

{

    "detail": "The supplied API key is missing, expired, or invalid. Please check your token and try again.",
    "status": "TOKEN",
    "mockheaders" : All request headers,
    "mockrequest" : Request parameters,
}
```



### Lot is full, no access.

```
HTTP/1.1 409 Conflict
Content-Type: application/problem+json
X-Request-ID: trace-abc123

{
    "message": "Parking is not available for the requested timeframe",
    "status": "UNAVAILABLE",
    "mockheaders" : All request headers,
    "mockrequest" : Request parameters,
}
```



## Mock server difference

If the minute in the from field is 00-29 the mock server will respond with appropriate Space Available response.

With minute in the from field in 30-59 the mock server will respond with appropriate parking space full. 

mockheaders and mockrequest parameters are only available from mock server. These are omitted from production server.





# Cancel

```
https://apimock.lentoparkki.fi/cancel?key=fgDeQDIRwerhUn5I&reservation=1842582

i.e.:

https://apimock.lentoparkki.fi/cancel
?key=fgDeQDIRwerhUn5I
&reservation=1842582

```


## Parameters

| Parameter | example | Description |
| --- | --- | --- |
| key | fgDeQDIRwerhUn5I | API key. Mock server uses fgDeQDIRwerhUn5I |
| reservation | 1842582 | our reservation id from /spacereservation. Only own reservations can be cancelled |

"Own" as in same collaborator.

## Successful Response

```
HTTP/1.1 202 Accepted
Content-Type: application/json
X-Request-ID: trace-82KKpzp

{
    "status": "ACCEPTED",
    "message": "The reservation has been cancelled and space has been freed for other use",
    "mockheaders" : All request headers,
    "mockrequest" : Request parameters,
}
```


## Unsuccessful Response, incomplete


### Parameter format problem
```
HTTP/1.1 400 Invalid Request
Content-Type: application/problem+json
X-Request-ID: trace-eTYaskh

{
    "message": "e.g. from not acceptable: ...",
    "status": "PARAM",
    "mockheaders" : All request headers,
    "mockrequest" : Request parameters,
}
```


### API key problem

```
HTTP/1.1 401 Unauthorized
Content-Type: application/problem+json
WWW-Authenticate: Bearer realm="API Access"
X-Request-ID: trace-eTYaskh

{

    "detail": "The supplied API key is missing, expired, or invalid. Please check your token and try again.",
    "status": "TOKEN",
    "mockheaders" : All request headers,
    "mockrequest" : Request parameters,
}
```

### Not Found

```
HTTP/1.1 404 Not Found
Content-Type: application/problem+json
X-Request-ID: trace-1KKaow98

{
    "message": "Requested reservation number yielded no results. ",
    "status": "NOTFOUND",
    "mockheaders" : All request headers,
    "mockrequest" : Request parameters,
}
```


## Mock server difference


If the minute in the from field is 00-29 the mock server will respond with appropriate Space Available response.

With minute in the from field in 30-59 the mock server will respond with appropriate parking space full. 

mockheaders and mockrequest parameters are only available from mock server. These are omitted from production server.


# Contact


Thank you for considering us.

Questions, Requests, Bugs, Complaints, Feedback etc :

lentoparkki.tech gmail.com

Account creation, collaboration etc

info a lentoparkki.fi or 010 229 5860


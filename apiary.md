FORMAT: 1A

# PayByPhone API
This is a list of PayByPhone Apis

## Response Codes
The common [HTTP Response Status Codes](http://tools.ietf.org/html/rfc7231#section-6) are used.

## Error Handling

Any code you write to call PayByPhone's APIs should expect to receive and handle errors from the service. A given error may be returned for multiple reasons, so it's a good idea to look at (and handle in code), the error messages returned with the error number.

400-series errors indicate that you cannot perform the requested action, the most common reasons being that you don't have permission or that a referred-to entity doesn't exist.

500-series errors indicate that a request didn't succeed, but may be retried. Though infrequent, these errors are to be expected as part of normal interaction with the service and should be explicitly handled with an exponential backoff algorithm. One such algorithm can be found at http://en.wikipedia.org/wiki/Truncated_binary_exponential_backoff.

Failed connection attempts should also be handled with exponential backoff.

## Versioning
Each endpoint in PayByPhone's API is separately versionable. The `X-Pbp-Version` header is used
to indicate which version of the endpoint the caller which to invoke. An example to invoke a
version 2 of a resource would be to add the following HTTP Header to the request `X-Pbp-Version: 2`

# Data Structures

## Money (object)
+ amount: `5.00` (number)
+ currencyCodeISO4217: `CAD` (string)


## QuoteItem (object)
+ quoteItemType: `parking` (string) - XXXXXXXX
+ name: `Parking` (string) - XXXXXXXX
+ costAmout (Money)


## GetParkingQuoteResponse (object)
+ locationNumber: `40002` (string) - XXXXXXXX
+ stallId: `123` (string) - XXXXXXXX
+ quoteDate: `2015-06-25T18:00:00-0000` (string) - XXXXXXXX
+ totalCost (Money) - XXXXXXXX
+ parkingAccountId: `56514ff5-44fc-463b-820f-1d47393e1703` (string) - XXXXXXXX
+ parkingStartTime: `2014-12-29T22:00:00-0000` (string) - XXXXXXXX
+ parkingExpiryTime: `2014-12-29T23:00:00-0000` (string) - XXXXXXXX
+ parkingDurationAdjustment (enum) - XXXXXXXX
    + NotAdjusted (string) - XXXXXXXX
    + DurationIncreased (string) - XXXXXXXX
    + DurationDecreased (string) - XXXXXXXX
    + DurationIncreasedPromptUserToConfirm (string) - XXXXXXXX
+ licensePlate: `ABC123` (string) - XXXXXXXX
+ quoteItems (array) - XXXXXXXX
    + (QuoteItem)


## PaymentAccount (object)
+ id: `+16046155905` (string) - E.164 phone number used to create user account.
+ maskedCardNumber: `411111------1111` (string) - Assigned by the API at the moment of creation.
+ paymentCardType: `DebitCard` (string) - Assigned by the API at the moment of creation.
+ nameOnCard: `JOHN L SMITH` (string) - Assigned by the API at the moment of creation.
+ expiryMonth: `2` (number) - Assigned by the API at the moment of creation.
+ expiryYear: `2018` (number) - Assigned by the API at the moment of creation.
+ startMonth: `2` (number) - Assigned by the API at the moment of creation.
+ startYear: `2012` (number) - Assigned by the API at the moment of creation.
+ issueNumber: `2` (number) - Assigned by the API at the moment of creation.


## PaymentAccounts (array)
+ (PaymentAccount) - An array of PaymentAccount objects


## CreatePaymentAccount (object)
+ countryCode (enum, required) - Country the card is registered in.
    + CA (string)
    + US (string)
    + AU (string)
    + FR (string)
    + CH (string)
    + GB (string)
+ cardNumber: `4111111111111111` (string, required) - The credit or debit card number.
+ expiryMonth: `2` (number, required) - Expiration month of the card.
+ expiryYear: `2018` (number, required) - Expiration year of the card.
+ nameOnCard: `JOHN L SMITH` (string) - The card holders name as it appears on the card.
+ cvv : `123` (string) - The Card Verification Value
+ issueNumber: `2` (number) - The issue number for the debit card.
+ startMonth: `2` (number) - Assigned by the API at the moment of creation.
+ startYear: `2012` (number) - Assigned by the API at the moment of creation.

# Group Identity And Access

## About
Identity And Access API provides Identity Management and Authorization resources

## Create user account [POST /identity/user/accounts]

Create a new User Account

**Response Codes**

- 201 - User account successfully created.
- 400 - Username or password does not meet validation requirements.
- 409 - Username is already in use.

+ Request (application/json)
    + Attributes
        + username: `+16046155905` (required, string) - E.164 formatted phone number
        + password: `1234` (required, string) - 6-20 character alphanumeric password
    + Body
    
            {
                "username": "+16046155905",
                "password": "1234"
            }

+ Response 201 (application/json)
    + Attributes
        + username: `+16046155905` (string) - E.164 phone number used to create user account.
        + userAccountId: `5fb93529-b3a1-486e-b23d-a14944bbe3f5` (string) - Assigned by the API at the moment of creation.
    + Body
    
            { 
                "username": "+16046155905",
                "userAccountId" : "5fb93529-b3a1-486e-b23d-a14944bbe3f5"
            }


## Cancel the active user account [DELETE /identity/user/accounts/{userAccountId}]

**Possible Response Codes**

- 200 - User account successfully cancelled.
- 401 - The access token has expired or is invalid.
- 403 - The user account it not active and cannot be cancelled.
- 403 - The member it not authorized to modify the specified user account.

+ Parameters
    + userAccountId: `5fb93529-b3a1-486e-b23d-a14944bbe3f5` (required, string) - User account to be cancelled.

+ Response 200


## Change user account password [PUT /identity/user/accounts/{userAccountId}/password]
Change the password used to authenticate a user account

**Possible Response Codes**

- 202 - Request is being processed
- 400 - The request is invalid.
- 403 - Access Denied.
- 500 - Shit hit the fan exception.

**Possible events**

- UserAccountPasswordChanged
    
        {
            "UserAccountId":"5fb93529-b3a1-486e-b23d-a14944bbe3f5",
            "$type":"UserAccountPasswordChanged"
        }

- UserAccountPasswordChangedFailed

        {
            "UserAccountId":"5fb93529-b3a1-486e-b23d-a14944bbe3f5",
            "FailureReason":"[CurrentPasswordIncorrectDoesNotMeetComplexityRequirements|UserNotFound|InternalServerError]",
            "$type":"UserAccountPasswordChangedFailed
        }

+ Parameters
    + userAccountId: `5fb93529-b3a1-486e-b23d-a14944bbe3f5` (required, string) - User account to be changed.

+ Request (application/json)
    + Attributes
        + userAccountId: `5fb93529-b3a1486eb23da14944bbe3f5` (required, string) - User account to be cancelled.
        + currentPassword: `1234` (required, string) - Current password of the user account.
        + newPassword: `5678` (required, string) - New password for the user account.
    + Body
    
            {
                "userAccountId": "8d21a912-561a-48ff-b31b-00d600a2544e",
                "currentPassword": "1234",
                "newPassword": "5678"
            }

+ Response 202
    + Headers
    
            Location: https://api.paybyphone.com/events/d92cfe7d-dd59-49d6-be1a-a3b3bb5d3e6a


## Get an access token and refresh token [POST /identity/token]

**Possible Response Codes**

- 200 - OK
- 400 - See Possible error codes

**Possible Error Codes**

- IncorrectUsername
- IncorrectPassword
- AccountLocked
- AccountSuspended
- `invalid_client`


+ Request Get token from Credentials
    + Attributes
        + `grant_type`: `password` (required, string) - The grant type
        + username: `+16046155905` (required, string) - The username corresponding to an existing user account
        + password: `iphone_app` (required, string) - The password that match the user account
        + `client_id`: `iphone_app` (required, string) - The client identifier
    + Headers

            Content-Type: application/x-www-form-urlencoded
    + Body

            grant_type=password
            &username=%2B16046155905
            &password=1234
            &client_id={your_app_client_id}
            
+ Request Get token from refresh token
    + Attributes
        + `grant_type`: `refresh_token` (required, string) - The grant type
        + `refresh_token`: `+16046155905` (required, string) - The refresh token
        + `client_id`: `iphone_app` (required, string) - The client identifier
    + Headers
    
            Content-Type: application/x-www-form-urlencoded
    + Body
    
            grant_type=refresh_token
            &refresh_token=F72ECD394B69405E95DCEA175D49A35D
            &client_id={your_app_client_id}

+ Response 200 (application/json)
    + Attributes
        + `access_token`:`eyJ0eXAiOiJ....` (string) - Encoded access token
        + `token_type`:`bearer` (string) - Type of the token
        + `expires_in`:`1209599` (string) - Lifetime of the access token in seconds
        + `refresh_token`:`F72ECD394B69405E95DCEA175D49A35D` (string) - A refresh token - will be the same as the refresh token used in the request.
    + Body
    
            {
                "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJtZW1iZXJpZCI6IjViNWNjNmQ1LWNjZmEtNDc1OS04OGI0LTM1NTRmMTg0ZjZiYiIsImFjdGl2ZXVzZXJhY2NvdW50IjoiMWI3ZTdmYTAtZTI1Yi00OTM3LWI0MjEtYTNkMTAwZDQ5ZDdkIiwiaXNzIjoiUGF5QnlQaG9uZSBJZGVudGl0eSBBbmQgQWNjZXNzIiwiYXVkIjoiaHR0cDovL2FwaS5wYXlieXBob25lLmNvbSIsImV4cCI6MTQxOTAzMjI3NywibmJmIjoxNDE3ODIyNjc3fQ.dYc4bPdwA9OB4FJCaaSpAWplyxZozOlNTdZkhX7qgQE",
                "token_type": "bearer",
                "expires_in": 1209599,
                "refresh_token": "F72ECD394B69405E95DCEA175D49A35D"
            }


## Revoke a refresh token [DELETE /identity/refresh_token/{refresh_token_id}]

**Possible Response Codes**
- 204
- 400
- 401
- 404 - The refresh token could not be found for the authenticated user.

+ Parameters
    + refresh_token_id: `F72ECD394B69405E95DCEA175D49A35D` (required, string) - Refresh token to revoke

+ Response 204


## Recover password [POST /identity/user/accounts/password/recover]

**Possible Response Codes**
- 204
- 400

+ Request (application/json)
    + Attributes
        + username: `+16046155905` (required, string) - Username to recover password for.
        + language: `en-US` (required, enum[string]) - Language in which password recovery message should be sent.
            + Members
                + `en-US`
                + `en-GB`
                + `fr-FR`
                + `fr-CA`
    + Body
    
            {
                "username":"+16046155905",
                "language":"en-US"
            }

+ Response 202
# Group Parking

## Get current or historic parking sessions [/parking/accounts/{accountId}/sessions/?periodType=Current]

## Get a user's current/historic parking sessions [GET]

**Possible Response Codes**

- 200 - OK
- 400 - Bad Request
- 401 - Unauthorized
- 403 - Forbidden

+ Request
    + Header
        Authorization: Bearer <token>


+ Parameters
    + accountId: `1b98d239-8de8-4111-9d7d-562fc31c193a` (required, string) - Parking account id.
    + periodType: 'Current' (required, enum[string]) - 
        + Members
            + `Current`
            + `Historic`
    + since: `2015-09-01T16:16:00.000Z` (optional, string) - Date and time when to retrieve the parking sessions from.
    + offset: '0' (optional, int) - Offset should be more than 0.
    + limit: '10' (optional, int) - Limit should be more than 0 and less than 50. 

+ Response 200 
    + Attributes (application/json)
        + `parkingSessionId`:`de9bd239-44f3-483e-9fd4-7c98567da200` (string) - The parking session ID, this is needed if you want to extend an existing parking session (NOTE: this property will soon be renamed to parkingSessionId)
        + `locationId`: `5594` (string) - The Lot ID where the user is parked
        + `vehicle`: (required) - The vehicle information associated with the parking session
            + `id`: 1234 (required, int) - The ID of the vehicle that is parked.
            + `licensePlate`: `ABC123` (string) - The license plate of the vehicle that is parked
            + `jurisdiction`: `BC` (required, string) - Jurisdiciton the parked vehicle is registered in. Valid jurisdictions are Countries and Regions supported by API#Provinces
            + `countryCode`: `CA` (optional, string) - Country ISO 3166 code of the location.
            + `type`: `motorcycle` (required, string) - Vehicle Type. Valid types are: car, motorcycle, electricMotorcycle, heavyGoodsVehicle
        + `startTime`: `2015-09-01T16:16:00.000Z` (string) - The total duration of the parking session from the start-time
        + `stall`: `82445` (string) - Stall where the vehicle is parked if the parking session took place in a stall, null otherwise.
        + `rateOption`: (required) - The rate option details associated with the parking session
            + `ratePolicyId`: `5594` (required, string) - The identifier of the rate policy applied to the parking session
            + `type`: `VIS` (required, string) - The eligibility type applied to the parking session
    + Body 
        [
            {
                "parkingSessionId":"de9bd239-44f3-483e-9fd4-7c98567da200",
                "locationId":"5594",
                "vehicle":{
                    "id":1234,
                    "licensePlate":"ABC123",
                    "jurisdiction":"BC",
                    "countryCode":"CA",
                    "type":"motorcycle"
                },
                "startTime":"2015-09-01T16:16:00.000Z",
                "stall":"82445",
                "rateOption":{
                    "ratePolicyId":"5594",
                    "type":"VIS"
                }
            }
        ]

## Start parking [/parking/accounts/{accountId}/sessions]

### Start a new parking session[POST]

**Possible Response Codes**

- 202 - Accepted
- 400 - Bad Request
- 401 - Unauthorized
- 403 - Forbidden

**Possible Validation Errors**

- ParkingAccountIdInvalidFormat
- LocationNotFound
- CvvMustNotBeEmpty
- CvvInvalidFormat
- LicensePlateMustNotBeEmpty
- LicensePlateInvalidFormat
- DurationInvalidFormat
- JsonReaderException

**Possible events**

- ParkingSessionCreated
    
        {
             "ParkingSessionId": "23fccd39-70c2-446f-915c-6d2362b64e58",
             "VehicleId": "12793707",
             "LocationId": "64123",
             "VehiclePlate": "GGG328",
             "Stall": "",
             "vehicleType": "car",
             "StartTime": "2015-01-28T20:54:00-0000",
             "ExpireTime": "2015-01-28T21:54:00-0000",
             "$type": "ParkingSessionCreated"
        }

- StartParkingFailed

        {
            "Data": [ ],
            "FailureReason":"VehicleAlreadyParked",
            "$type":"StartParkingFailed"
        }

+ Parameters
    + accountId: `1b98d239-8de8-4111-9d7d-562fc31c193a` (required, string) - Parking account id.

+ Request 
    + Attributes (application/json)
        + `locationId`:`70000` (required, string) - The lot ID of the location where the user wants to park
        + `stall`:`1` (required, string) - The stall number of the location where the user wants to park
        + `licensePlate`:`ABC123` (required, string) - The license plate of the vehicle that is parked
        + `duration`: (required, duration) -  The duration of the parking session
            + `timeUnit`: `Minutes` (required, string) - TimeUnit Enumeration name or value. "Minutes", "Hours", "Days", "Weeks", "Months", "HalfHour", "MidnightCurrentDay", "CalendarDays", "TimeBucket", "SingleHour", "WeekdaysStartMonday", "CalendarWeeks", "NotSpecified", "Unknown"
            + `quantity`: `1` (required, string) - How many minutes or hours the user wants to park
        + `cvv`:`111` (required, string) - The CV2 code from the user's payment card
        + `startTime`:`2015-07-28T20:40:00-0000` (required, string) - The start time of the transaction to be started. Must be within 3 minutes of the current time.
        + `ratePolicyId`:`70000` (required, string) - The rate policy Id for a personalized parking session.
    + Body 
        [
            {
                "locationId":"70000",
                "stall":"1",
                "licensePlate":"ABC123",
                "duration":{
                    "timeUnit":"Hours",
                    "quantity":"1"
                },
                "cvv":"111",
                "startTime": "2015-07-28T20:40:00-0000",
                "ratePolicyId":"70000"
            }
        ]

+ Response 202
    + Headers
    
        Location: https://api.paybyphone.com/events/workflow/d92cfe7d-dd59-49d6-be1a-a3b3bb5d3e6a


### Extend an existing parking session [PUT /parking/accounts/{accountId}/sessions/{activeParkingSessionId}]

**Possible Response Codes**

- 202 - Accepted
- 400 - Bad Request
- 401 - Unauthorized

**Possible Validation Errors**

- ParkingAccountIdInvalidFormat
- ActiveParkingSessionIsInvalid
- LocationNotFound
- CvvMustNotBeEmpty
- CvvInvalidFormat
- DurationInvalidFormat
- JsonReaderException

**Possible events**

- ParkingSessionExtended
    
        {
             "ParkingSessionId": "23fccd39-70c2-446f-915c-6d2362b64e58",
             "VehicleId": "12793707",
             "LocationId": "64123",
             "VehiclePlate": "GGG328",
             "Stall": "",
             "vehicleType": "car",
             "OldExpiryTime": "2015-01-28T20:54:00-0000",
             "NewExpiryTime": "2015-01-28T21:54:00-0000",
             "$type": "ParkingSessionExtended"
        }

- ExtendParkingFailed

        {
            "Data": [ ],
            "FailureReason":"PaymentDeclinedByGateway",
            "$type":"ExtendParkingFailed"
        }

+ Parameters
    + accountId: `1b98d239-8de8-4111-9d7d-562fc31c193a` (required, string) - Parking account id.
    + parkingSessionId: `2bf49b64-28cd-4fc7-96e4-a44b010bf6ea` (required, string) - The ID of the parking session to be extended, this must be an active session

+ Request 
    + Attributes (application/json)
        + `duration`: (required, duration) -  The duration of the parking session
            + `timeUnit`: `Minutes` (required, string) - TimeUnit Enumeration name or value. "Minutes", "Hours", "Days", "Weeks", "Months", "HalfHour", "MidnightCurrentDay", "CalendarDays", "TimeBucket", "SingleHour", "WeekdaysStartMonday", "CalendarWeeks", "NotSpecified", "Unknown"
            + `quantity`: `1` (required, string) - How many minutes or hours the user wants to park
        + `cvv`:`111` (required, string) - The CV2 code from the user's payment card
    + Headers
        Authorization: Bearer <token> 
    + Body 
        [
            {
                "parkingSessionId":"2bf49b64-28cd-4fc7-96e4-a44b010bf6ea",
                "duration":{
                    "timeUnit":Hours",
                    "quantity":"1"
                },
                "cvv":"111"
            }
        ]

+ Response 202
    + Headers
    
        Location: https://api.paybyphone.com/events/workflow/d92cfe7d-dd59-49d6-be1a-a3b3bb5d3e6a

## Manage vehicles [/parking/accounts/{accountId}/vehicles]

### Get all vehicles belonging to a parking account [GET]

Get all vehicles from a User's Account.

**Possible Response Codes**

- 200 - Ok
- 401 - Unauthorized
- 403 - Forbidden

+ Request Contract

    Header: Authorization: Bearer <token>

+ Response 200
    [
        {
            "id":"14890",
            "licensePlate":"TEST9233",
            "province":"BC",
            "country":"CA",
            "vehicleType":"car"
        },
        {
            "id":"14891",
            "licensePlate":"TEST9233",
            "province":"BC",
            "country":"CA",
            "vehicleType":"motorcycle"
        }
    ]

### Add a vehicle to a parking account [POST]

**Possible Response Codes**

- 202 - Accepted
- 400 - Bad Request
- 401 - Unauthorized
- 403 - Forbidden

**Possible events**

- VehicleAdded
    
        {
             "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
             "vehicleId": "12793707",
             "countryCode": "CA",
             "licensePlate": "GGG328",
             "jurisdiction": "MB",
             "vehicleType": "car",
             "$type": "VehicleAdded"
        }

- AddVehicleFailed

        {
            "Data": [ {"jurisdiction": "BC"}, {"CountryId": "AU"} ],
            "FailureReason":"jurisdictionDoesNotMatchCountry",
            "$type":"AddVehicleFailed"
        }

**Possible Validation Errors**

- LicensePlateInvalidFormat - License Plate is not a valid format. Minimum 2 characters, maximum 10 characters. A-Z and 0-9 characters only.
- LicensePlateMustNotBeEmpty - License Plate must not be empty.
- jurisdictionDoesNotExist - Jurisdiction does not exist. Valid jurisdictions are Countries and Regions supported by API#Provinces
- CountryCodeMustNotBeEmpty - Country Code must not be empty.
- CountryCodeDoesNotExist - Country Code does not exist. Valid country ids are: CA, US, GB, AU, FR, CH. See Countries and Regions supported by API#Countries
- VehicleTypeMustNotBeEmpty - VehicleType Vehicle Type must not be empty.
- VehicleTypeDoesNotExist - Vehicle Type does not exist. Valid types are: car, motorcycle, electricMotorcycle, heavyGoodsVehicle
- JsonReaderException - Provided json is in an invalid format
- JurisdictionDoesNotMatchCountry - Provided country does not contain provided jurisdiction.

+ Request

    {
        "licensePlate":"TEST9233",
        "jurisdiction":"BC",
        "countryCode":"CA",
        "vehicleType":"car"
    }

+ Response 202 (application/json)
    + Headers
    
        Location: https://api.paybyphone.com/events/workflow/d92cfe7d-dd59-49d6-be1a-a3b3bb5d3e6a




## Manage a vehicle belonging to a parking account [/parking/accounts/{accountId}/vehicles/{vehicleId}]


### Update a vehicle on a parking account [PUT]

**Possible Response Codes**

- 403 - Forbidden

**Possible events**

- VehicleUpdated    
        {
             "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
             "vehicleId": "12793707",
             "countryCode": "CA",
             "licensePlate": "GGG328",
             "jurisdiction": "MB",
             "vehicleType": "motorcycle",
             "$type": "VehicleUpdated"
        }

- UpdateVehicleFailed
        {
            "Data": [ {"jurisdiction": "BC"}, {"CountryId": "AU"} ],
            "FailureReason":"JurisdictionDoesNotMatchCountry",
            "$type":"AddVehicleFailed"
        }

**Validation Errors**

- VehicleNotFound - Vehicle with vehicle Id not found.
- LicensePlateInvalidFormat - License Plate is not a valid format. Minimum 2 characters, maximum 10 characters. A-Z and 0-9 characters only.
- LicensePlateMustNotBeEmpty - License Plate must not be empty.
- JurisdictionDoesNotExist - Jurisdiction does not exist. Valid jurisdictions are Countries and Regions supported by API#Provinces
- CountryCodeMustNotBeEmpty - Country Code must not be empty.
- CountryCodeDoesNotExist - Country Code does not exist. Valid country ids are: CA, US, GB, AU, FR, CH. See Countries and Regions supported by API#Countries
- VehicleTypeMustNotBeEmpty - Vehicle Type must not be empty.
- VehicleTypeDoesNotExist - Vehicle Type does not exist. Valid types are: car, motorcycle, electricMotorcycle, heavyGoodsVehicle
- VehicleIdInvalidFormat - Vehicle Id must be an integer.
- UserNotRegistered - Member not registered.
- JsonReaderException - Provided json is in an invalid format.

+ Request 

    Body:
        {
            "licensePlate":"TEST9233",
            "jurisdiction":"BC",
            "countryCode":"CA",
            "vehicleType":"car"
        }

+ Response 202 (application/json)
    + Headers
    
        Location: https://api.paybyphone.com/events/workflow/d92cfe7d-dd59-49d6-be1a-a3b3bb5d3e6a

### Delete a vehicle [DELETE]
Delete a vehicle from a parking account

**Possible Response Codes**

- 202 - Accepted
- 401 - Unauthorized
- 403 - Forbidden

**Possible events**

- VehicleDeleted    
        {
             "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
             "vehicleId": "12793707",
             "LegacyMemberUid": "18789426",
             "$type": "VehicleDeleted"
        }

- DeleteVehicleFailed
        {
            "Data": [ ],
            "FailureReason":"UserNotRegistered",
            "$type":"DeleteVehicleFailed"
        }

**Validation Errors**        
- VehicleNotFound - Vehicle with vehicle Id not found.
- JsonReaderException - Provided json is in an invalid format.
- UserNotRegistered - Member not registered.
- VehicleIdInvalidFormat - Vehicle Id must be an integer.

+ Response 202 (application/json)
    + Headers
    
        Location: https://api.paybyphone.com/events/workflow/d92cfe7d-dd59-49d6-be1a-a3b3bb5d3e6a

## Get parking preferences for the parking account [GET /parking/accounts/{accountId}/preferences]

**Possible Response Codes**

- 200 - OK
- 400 - Bad Request
- 401 - Unauthorized
- 403 - Forbidden

**Possible Error Codes**

- EmailInvalidFormat
- JsonReaderException

+ Parameters
    + accountId: `00000000-0000-0000-0000-000000000000` (required, string) - Parking account id.

+ Response 200 
    + Attributes (application/json)
        + `parkingAccountId`:`00000000-0000-0000-0000-000000000000` (string) - The parking account ID
        + `sendEmailReceipts`:`false` (string) - Does account holder wishes to get receipts from email
        + `sendTextReminders`:`false` (string) - Does account holder wishes to receive text reminder
        + `sendTextReceipts`:`false` (string) - Does account holder wishes to get receipts from text
        + `email`:`null` (string) - Email account for account holder
    + Body 
        {
            "parkingAccountId":"00000000-0000-0000-0000-000000000000",
            "sendEmailReceipts":false,
            "sendTextReminders":false,
            "sendTextReceipts":false,
            "email":null
        }

## Set parking preferences for the parking account [PUT /parking/accounts/{accountId}/preferences or POST /parking/accounts/{accountId}/preferences]

**Response Codes**

- 202 - OK
- 400 - Bad Request
- 401 - Unauthorized
- 403 - Forbidden

**Possible events**

- PreferencesCreated/PreferencesUpdated
    
        {
             "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
             "Email": "test@server.com",
             "SendTextReceipts": "true",
             "SendTextReminders": "true",
             "SendEmailReceipts": "true",
             "$type": "PreferencesCreated"
        }

- AddMemberPreferencesFailed or UpdateMemberPreferencesFailed

        {
            "FailureReason":"UserNotRegistered",
            "$type":"AddMemberPreferencesFailed"
        }

+ Request (application/json)
    + Body
    
            {
                "email":"email@-domain.com",
                "sendEmailReceipts":true,
                "sendTextReceipts":true,
                "sendTextReminders":true
            }

+ Response 202 (application/json)
    + Headers
    
        Location: https://api.paybyphone.com/events/workflow/d92cfe7d-dd59-49d6-be1a-a3b3bb5d3e6a

## Get or Create a parking account [/parking/accounts]

### Create a parking account for the member [POST]

**Response Codes**

- 202 - OK
- 401 - Unauthorized

**Possible events**

- ParkingAccountCreated
    
        {
            "ParkingAccountId":"23fccd39-70c2-446f-915c-6d2362b64e28",
            "MemberId":"23fccd39-70c2-446f-915c-6d2362b64e58",
            "$type":"ParkingAccountCreated"
        }

- CreateParkingAccountFailed

        {
            "Data": [ ],
            "FailureReason":"UserNotRegistered",
            "$type":"CreateParkingAccountFailed"
        }

+ Response 202 (application/json)
    + Headers
    
        Location: https://api.paybyphone.com/events/workflow/d92cfe7d-dd59-49d6-be1a-a3b3bb5d3e6a

### Get all parking accounts for the member [GET]

**Response Codes**

- 200 - OK
- 401 - Unauthorized

+ Response 200 (application/json)
    [
        {
            "id": "d6d1817e-98ee-4600-b82b-f1aace2abea5"
        }
    ]

## Get a parking quote [/parking/accounts/{parkingAccountGuid}/quote?locationId={locationId}&timeUnit={TimeUnit}&quantity={Quantity}&licensePlate={LicensePlate}&stall={stall}&isExtend={isExtend}&ratePolicyId={ratePolicyId}]

### Retrieve a parking quote detail [GET]

Retrieve a parking quote detail

**Response Codes**

- 200 - Ok.
- 400 - Bad Bad request.

**Responsee Failure Reasons**

- LocationInvalidFormat - LocationId could not be converted to an integer format.
- TimeUnitInvalidFormat - Time unit is not in the accepted time units enumeration.
- Value is not valid for Quantity - Error thrown when the quantity parameter could not be converted to an integer format.
- QuantityInvalidFormat - Quantity can not be zero.
- LicensePlateMustNotBeEmpty - When getting a quote in a location that has a wait-time-after-max-stay restriction, you need to provide the license plate. This is to enforce the wait-time-after-max-stay restriction.
- CannotExtendBecauseOfNoReturnRule - When getting a quote in a location that has a wait-time-after-max-stay restriction, if you parked for the maximum stay you will need to wait until you can start parking again in
that location.
- RatePolicyIdInvalidFormat - LocationId could not be converted to an integer format.
- RatePolicyNotFound - Rate Policy Id specified is not associated with the location or the license plate.

+ Parameters
    + parkingAccountGuid: `b12e8e08-ee59-4774-8062-a40401272436` (required, guid) - GUID of the user parking account.
    + locationId: `40002` (required, string) - Location ID. Note that this can not be a reverse lookup stall, such as in the location details endpoint
    + timeUnit: `Minutes` (required, string) - Time unit enumeration string, as described in Time Units table below. Either the name or the value.
        + 1 (string) - Minutes
        + 2 (string) - Hours
        + 3 (string) - Days
        + 4 (string) - Weeks
        + 5 (string) - Months
        + 6 (string) - HalfHour
        + 7 (string) - MidnightCurrentDay
        + 8 (string) - CalendarDays
        + 9 (string) - TimeBucket 
        + 10 (string) - SingleHour
        + 11 (string) - WeekdaysStartMonday
        + 12 (string) - CalendarWeeks
        + 0 (string) - NotSpecified/Unknown
        + Minutes (string)
        + Hours (string)
        + Days (string)
        + Weeks (string)
        + Months (string)
        + HalfHour (string)
        + MidnightCurrentDay (string)
        + CalendarDays (string)
        + TimeBucket (string)
        + SingleHour (string)
        + WeekdaysStartMonday (string)
        + CalendarWeeks (string)
        + NotSpecified (string)
        + Unknown (string)

    + quantity: `60` (required, int) - Number of time units.
    + licensePlate: `273cvn` (optional, string) - License plate being parked. Used to calculate promotion discounts.
    + stall: `52444` (optional, string) - Stall number of the current parking session. Used for extensions to ensure the app is extending the right session.
    + isExtend: `True` (optional, boolean) - True or False, resembling a boolean to indicate whether requesting the quote for an extension of a current parking session.
    + ratePolicyId: `75001` (optional, int) - The ID of the rate policy you want to use

+ Response

    + Attributes
        + locationId: `40006` (int) - The Location ID where the user is parked.
        + stall: `123` (string)  - Stall where the vehicle is parked if the parking session took place in a stall, null otherwise.
        + quoteDate: `2015-06-25T18:00:00-0000` (dateTime)  - Quote Time of the rate calculation, in UTC
        + totalCost: (money) - Total cost of the parkfig session
            + amount
            + currency           
        + parkingAccountId:  `56514ff5-44fc-463b-820f-1d47393e1703` (guid)
        + parkingStartTime: `2014-12-29T22:00:00-0000` (dateTime)  - Parking Start Time of the rate calculation, in UTC
        + parkingExpiryTime: `2014-12-29T23:00:00-0000` (dateTime)  - Parking Expiry Time of the rate calculation, in UTC
        + parkingDurationAdjustment: `NotAdjusted` (enum) 
            + NotAdjusted
            + DurationIncreased
            + DurationDecreased
        + licensePlate: `ABC123` (string) - The license plate of the vehicle that is parked
        + quoteItems : (array[string])
            + quoteItemType
                + promotion
            + name
            + costAmount
                + amount
                + currency
         
+ Response 200 (application/json)
    Content-Type: application/json

    {
        "locationId": "40006",
        "stall": "123",
        "quoteDate":"2015-06-25T18:00:00-0000",
        "totalCost": {
               "amount": 5.00,
               "currency": "CAD"
        },
        "parkingAccountId": "56514ff5-44fc-463b-820f-1d47393e1703",
        "parkingStartTime": "2014-12-29T22:00:00-0000",
        "parkingExpiryTime": "2014-12-29T23:00:00-0000",
        "patrkingDurationAdjustment": "NotAdjusted",
        "licensePlate": "ABC123",
        "quoteItems":[{
            "quoteItemType":"parking",
            "name": "Parking",
            "costAmout":{
                "amount":9.00,
                "currency":"CAD"
            }
        },{
            "quoteItemType":"convenienceFee",
            "name": "Convenience Fee",
            "costAmount": {
                "amount": 1.00,
                "currency": "CAD"
            }
        },{
            "quoteItemType":"promotion",
            "name": "COV electric vehicle discount.",
            "costAmount": {
                "amount": -5.00,
                "currency": "CAD"
            }
        }]
    }

## Get details parking rates [/parking/locations/{locationId}/ratePolicies?plates={list of license plates}]

### Get details about what rate policies can be applied at a location to the user's vehicles [GET]

**Response Failure Reasons**

-LocationInvalidFormat - LocationId could not be converted to an integer format.

+ Request

    Header: Authorization: Bearer <token>

+ Parameters
    + plates: `ABC123` (required, string) - License plates to get the rate policies for (different vehicles may be eligible for different parking rates).

+ Response 200 (application/json)
    Content-Type: application/json
    [
      {
        "ratePolicyId": 75001,
        "name": "Visiteur",
        "eligibilityType": "VIS",
        "isDefault": true,
        "eligiblePlates": [
          {
            "plate": "ABC123",
            "eligibleSectors": [
              "1E"
            ]
          }
        ]
      }
    ]

## Parking location search [/parking/locations/?advertisedLocationNumber={advertisedLocationNumber}&countryCode={countryCode}&stall={stall}]

### Find zero or more locations' details [GET]

**Response codes**
- 200 - OK
- 400 - Bad Request
- 404 - Not Found
- 500 - Internal Server Error

**Max Stays Remarks and Examples**
- MaxStayCouldNotBeVerified - The maximum stay could not be accurately determined. The app should not show any message regarding max stay at the parking step, before the user enters duration.
- NoMaxStay - The location does not have a max stay. User can park for any duration. The app should not show any message regarding max stay at the parking step, before the user enters duration.
- ParkingAllowed currentMaxStayInSeconds: 7200 - User can only park for 7200 seconds. The app is safe to show a message regarding this limit. 
- ParkingAllowed currentMaxStayInSeconds: 0 - Even though parking is allowed, we could not accurately determine the exact maximum allowed duration. The app should not show any message regarding max stay at the parking step, before the user enters duration.
- ParkingNotAllowed - Parking is not allowed at the time of calling this endpoint. The app is safe to show a "no parking at this time" message. 

+ Request
    Header: Authorization: Bearer <token>

+ Parameters
    + advertisedLocationNumber: `10` (required, string) - Can either be a Lot ID or a Stall, if the location supports reverse lookup.
    + countryCode: `CA` (optional, string) - Country ISO 3166 code of the location.
    + stall: `1` (optional, string) - If the location is stallbased then you can pass the stall number either to verify its existence or to get the location in case the stall belongs to another lot in the same group. 

+ Response

    + Attributes
        + status: `lotOpen` (enum) - Enumeration. Valid items are
            + lotOpen
            + lotClosed
            + lotYetToBeActivated
        + lotMessages: (string) - A collection if key/value pairs that contain messages and the culture they belong to.
    
+ Response 200
    Content-Type: application/json
        [{           
            "locationId": "12345",
            "name": "344th Street",
            "vendorName": "COV",
            "status": "lotOpen",
            "allowExtend": "false",
            "isStallBased": "true",
            "isPlateBased": "false",
            "currency": "CAD",
            "countryCode": "CA",
            "promptForCvv": "true",
            "lotMessages": [],
            "stall": 123,
            "isReverseLookup": "true"
        }]

## Parking location details [/parking/locations/{locationId}]

### Get an individual location's details [GET]

**Response codes**
- 200 - OK
- 400 - Bad Request
- 404 - Not Found
- 500 - Internal Server Error

**Max Stays Remarks and Examples**
- MaxStayCouldNotBeVerified - The maximum stay could not be accurately determined. The app should not show any message regarding max stay at the parking step, before the user enters duration.
- NoMaxStay - The location does not have a max stay. User can park for any duration. The app should not show any message regarding max stay at the parking step, before the user enters duration.
- ParkingAllowed currentMaxStayInSeconds: 7200 - User can only park for 7200 seconds. The app is safe to show a message regarding this limit. 
- ParkingAllowed currentMaxStayInSeconds: 0 - Even though parking is allowed, we could not accurately determine the exact maximum allowed duration. The app should not show any message regarding max stay at the parking step, before the user enters duration.
- ParkingNotAllowed - Parking is not allowed at the time of calling this endpoint. The app is safe to show a "no parking at this time" message. 

+ Request
    Header: Authorization: Bearer <token>

+ Parameters
    + locationId: `10` (required, string) - Must be a valid locationId.

+ Response

    + Attributes
        + status: `lotOpen` (enum) - Enumeration. Valid items are
            + lotOpen
            + lotClosed
            + lotYetToBeActivated
        + lotMessages: (string) - A collection if key/value pairs that contain messages and the culture they belong to.

+ Response 200
    Content-Type: application/json
        {
            "locationId": "12345",
            "name": "344th Street",
            "vendorName": "COV",
            "status": "lotOpen",
            "allowExtend": "false",
            "isStallBased": "true",
            "isPlateBased": "false",
            "currency": "CAD",
            "countryCode": "CA",
            "promptForCvv": "true",
            "lotMessages": [],
            "stall": 123,
            "isReverseLookup": "true"
        }

## Parking Rate Options [/parking/locations/{locationId}/rateOptions?parkingAccountId={parkingAccountId}&licensePlate={licensePlate}&startTime={startTime}]

### Get a Parking Rate Option [GET]

Both `parkingAccountId` and `licensePlate` are optional. But if you provide a `licensePlate` you *must* inform the `parkingAccountId` that owns that license plate, otherwise you'll get a 404 response.

**Response codes**
- 200 - OK
- 400 - Bad Request
- 404 - Not Found


**Remarks**

`/rateOptions` endpoint consumes the underlying `/restrictions` endpoint and will behave according to what it receives back from `/restrictions`:
- When `maxStayStatus` is `parkingAllowed`
    - `maxStayDuration` should have a `durationType` and `quantity`
    - `acceptedTimeUnits` should have the accepted time units.

- When `maxStayStatus` is `noMaxStay` then:
    - `maxStayDuration` is null
    - `acceptedTimeUnits` is should have the accepted time units

- When `maxStayStatus` is `parkingNotAllowed`
    - `maxStayDuration` is null
    - `acceptedTimeUnits` is null

+ Request
    Header: Authorization: Bearer <token>

+ Parameters
    + locationId: `75001` (required, string) - LocationId in the database
    + parkingAccountId: `b12e8e08-ee59-4774-8062-a40401272436` (optional, guid)
    + licensePlate: `ABC123` (optional, guid) - The license plate. When you provide a license plate you *must* provide a `parkingAccountId`

+ Response
    + Attributes
        + name: The name of the eligibility
        + type: The type of the eligibility
        + ratePolicyId: The ID of the rate policy
        + maxStayDuration:
            + durationType: The time unit of the duration
            + quantity: The amount of time units of the duration
        + maxStayStatus: The status of the max stay
        + acceptedTimeUnits: (array[string]) The time units accepted for this rate option
        + isDefault: (boolean) If it is the default rate option

+ Response 200 (application/json)
    Content-Type: application/json

    [
        {
            "name": "Professionel",
            "type": "PRO",
            "ratePolicyId": "75001",
            "maxStayDuration": {
              "durationType": "Minute",
              "quantity": 150
            },
            "maxStayStatus": "ParkingAllowed",
            "acceptedTimeUnits": [
                "Minutes",
                "Hours"
            ],
            "isDefault": false
      },
      {
            "name": "Visiteur",
            "type": "VIS",
            "ratePolicyId": "75001",
            "maxStayDuration": {
                "durationType": "Minute",
                "quantity": 120
            },
            "maxStayStatus": "ParkingAllowed",
            "acceptedTimeUnits": [
            "Minutes",
            "Hours"
            ],
            "isDefault": true
      }
    ]

# Group Payment Methods

Placeholder for Payment Methods endpoint documentation

## Payment account [/payment/accounts]

### Get payment accounts [GET]

**Response Codes**

- 200 - OK
- 401 - Unauthorized

+ Response 200 (application/json)
    + Attributes (PaymentAccounts)


## Get details about a payment account [GET /payment/accounts/{paymentAccountId}]

- 202 - Accepted
- 400 - Bad Request - The PaymentAccountId is in an invalid format.
- 401 - Unauthorized - You are not authorized to access the account.
- 404 - Not found - No payment account is found with this id.

+ Request (application/json)
    + Attributes
        + paymentAccountId: `d6d1817e-98ee-4600-b82b-f1aace2abea5` (string) - Payment account Id

+ Response 200 (application/json)
    + Attributes (PaymentAccount)


### Create a new payment account [POST]

- nameOnCard required for 2
- cvv required for 3
- for countries 4, issueNumber, startMonth and startYear is optional


**Possible Response Codes**

- 202 - Accepted
- 400 - Bad Request
- 401 - Unauthorized
- 409 - Conflict

+ Request (application/json)
    + Attributes (CreatePaymentAccount)

+ Response 202 (application/json)
    + Headers

        Location: https://api.paybyphone.com/events/workflow/d92cfe7d-dd59-49d6-be1a-a3b3bb5d3e6a


## Delete a payment account [DELETE /payment/accounts/{paymentAccountId}]

- 202 - Accepted
- 400 - Bad Request
- 401 - Unauthorized
- 404 - Not found

+ Request (application/json)
    + Attributes
        + paymentAccountId: `d6d1817e-98ee-4600-b82b-f1aace2abea5` (string) - Payment account Id

+ Response 202 (application/json)
# Group Notifications Placeholder

Placeholder for Notifications endpoint documentation
# Group Payment
## About
Payment API provides payment account related resources
# Group Payment Accounts

## Payment Accounts [/public/payment/accounts]

The Payment Accounts root resource

+ Model (application/json)

    JSON representation of Payment Account

    + Body

            { 
                "id": "72de36abf64447d3944bacae39ea1f7d",
                "paymentCardType": "Visa",
                "nameOnCard": "JOHN L SMITH",
                "expiryMonth": 1,
                "expiryYear": 2019,
                "startMonth": null,
                "startYear": null,
                "issueNumber": null
            }

### Create a new payment account for a member [POST]

The Member is identified by the **memberid** claim in the authorization bearer token

**Request Contract**

    Header: Authorization: Bearer <token>
    Content-Type: application/json
    {
        "cardNumber": "4111111111111111",
        "expiryMonth": 1,
        "expiryYear": 2019,
        "countryCode": "CA",
        "nameOnCard": "JOHN L SMITH",
        "cvv": 123,
        "issueNumber": 12,
        "startYear": 2010,
        "startMonth": 1
    }

**Mandatory Fields**

- countryCode (string : [ISO 3166-1 alpha-2](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2))
- number (string : [Bank card number](http://en.wikipedia.org/wiki/Bank_card_number))
- expiryYear (integer : >= current year - current year + 10)
- expiryMonth (integer : 1-12, > current month if current year)

**Mandatory Fields for North America**

- name (string)

**Mandatory Fields for France**

- cvv (integer : 3-4 digits)

**Optional Fields for United Kingdom**

- startMonth (integer : 1-12, <= current month if current year)
- startYear (integer : <= current year)
- issueNumber (integer : 2 digits)
        
**Possible Response Codes**

- 202
- 401
- 500

+ Request NA with name (application/json)

        {
            "cardNumber": "4111111111111111",
            "countryCode": "CA",            
            "expiryMonth": 6,
            "expiryYear": 2016,
            "nameOnCard": "JOHN L SMITH"
        }
+ Response 202 (application/json)
    + Header

            Location: http://api.dev.paybyphone.com/public/events/8d21a912-561a-48ff-b31b-00d600a2544e/

+ Request NA no name (application/json)

        {
            "cardNumber": "4111111111111111",
            "countryCode": "CA",            
            "expiryMonth": 6,
            "expiryYear": 2016
        }
+ Response 202 (application/json)
    + Header

            Location: http://api.dev.paybyphone.com/public/events/8d21a912-561a-48ff-b31b-00d600a2544e/

+ Request UK start mm/yy (application/json)

        {
            "cardNumber": "4111111111111111",
            "countryCode": "UK",
            "expiryMonth": 6,
            "expiryYear": 2016,
            "startMonth": 1,
            "startYear": 2012
        }
+ Response 202 (application/json)
    + Header

            Location: http://api.dev.paybyphone.com/public/events/8d21a912-561a-48ff-b31b-00d600a2544e/

+ Request UK issuenumber (application/json)

        {
            "cardNumber": "4111111111111111",
            "countryCode": "UK",            
            "expiryMonth": 6,
            "expiryYear": 2016,
            "issueNumber": 12
        }
+ Response 202 (application/json)
    + Header

            Location: http://api.dev.paybyphone.com/public/events/8d21a912-561a-48ff-b31b-00d600a2544e/
        
+ Request FR (application/json)

        {
            "cardNumber": "4111111111111111",
            "countryCode": "FR",            
            "expiryMonth": 6,
            "expiryYear": 2016,
            "cvv": 111
        }
+ Response 202 (application/json)
    + Header

            Location: http://api.dev.paybyphone.com/public/events/8d21a912-561a-48ff-b31b-00d600a2544e/

+ Response 401 (application/json)

        {"error": "Unauthorized"}

+ Response 500 (application/json)

        {"error": "Unhandled error"}
        
### Retrieve all payment accounts for member [GET]

Retrieve all Payment Accounts for a Member.

The Member is identified by the **memberid** claim in the authorization bearer token

**Request Contract**

    Header: Authorization: Bearer <token>

**Response Contract**
    
    Content-Type: application/json
    [
        {
            "id": "72de36abf64447d3944bacae39ea1f7d",
            "paymentCardType": "Visa",
            "nameOnCard": "Test",
            "expiryMonth": 1,
            "expiryYear": 2019,
            "startMonth": null,
            "startYear": null,
            "issueNumber": null
        }
    ]
    
**Possible Response Codes**

- 200
- 401
- 500

+ Request
    
        /public/payment/accounts
+ Response 200
        [Payment Accounts][]

+ Response 401 (application/json)

        {"error": "Unauthorized"}

+ Response 500 (application/json)

        {"error": "Unhandled error"}

## Payment Account [/public/payment/accounts/{paymentAccountId}]

An individual Payment Account resource

+ Parameters
    + paymentAccountId (required, guid) ... Guid `paymentAccountId` of the User Resource to perform action with. Has example value.

+ Model (application/json)

    JSON representation of Payment Account

    + Body

            { 
                "id": "72de36abf64447d3944bacae39ea1f7d",
                "paymentCardType": "Visa",
                "nameOnCard": "Test",
                "expiryMonth": 1,
                "expiryYear": 2019,
                "startMonth": null,
                "startYear": null,
                "issueNumber": null
            }
            
### Retrieve a payment account for member [GET]

Retrieve a Payment Account for a Member.

The Member is identified by the **memberid** claim in the authorization bearer token

Payment Account is identified by the `paymentAccountId`

**Request Contract**

    Header: Authorization: Bearer <token>

**Response Contract**
    
    Content-Type: application/json
    {
        "id": "72de36abf64447d3944bacae39ea1f7d",
        "paymentCardType": "Visa",
        "nameOnCard": "Test",
        "expiryMonth": 1,
        "expiryYear": 2019,
        "startMonth": null,
        "startYear": null,
        "issueNumber": null
    }
    
**Possible Response Codes**

- 200
- 400
- 401
- 404
- 500

+ Request paymentAccountId exists
    
        /public/payment/accounts/{paymentAccountId}
+ Response 200
        [Payment Account][]

+ Request paymentAccountId Does Not exists
        
        /public/payment/accounts/{paymentAccountId}
+ Response 404 (application/json)

        {"error": "Not found"}

+ Request paymentAccountId is not a Guid
        
        /public/payment/accounts/{paymentAccountId}
+ Response 400 (application/json)

        {"error": "PaymentAccountIdInvalidFormat"}

+ Response 401 (application/json)

        {"error": "Unauthorized"}

+ Response 500 (application/json)

        {"error": "Unhandled error"}

### Update a payment account for a member [PUT]

Update a Payment Account for a Member.

The Member is identified by the **memberid** claim in the authorization bearer token

Payment Account is identified by the `paymentAccountId`.

**Request Contract**

    Header: Authorization: Bearer <token>
    Content-Type: application/json
    {
        
        "expiryMonth": 1,
        "expiryYear": 2019,
        "countryCode": "CA",        
        "nameOnCard": "JOHN L SMITH",
        "cvv": 123,
        "startYear": 2010,
        "startMonth": 1,
        "issueNumber": 12
    }

**Mandatory Fields**

- country (string : [ISO 3166-1 alpha-2](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2))
- expiryYear (integer : >= current year - current year + 10)
- expiryMonth (integer : 1-12, > current month if current year)

**Mandatory Fields for North America**

- name (string)

**Mandatory Fields for France**

- cvv (integer : 3-4 digits)

**Optional Fields for United Kingdom**

- startMonth (integer : 1-12, <= current month if current year)
- startYear (integer : <= current year)
- issueNumber (integer : 2 digits)

**Possible Response Codes**

- 202
- 401
- 500 

+ Request NA with name (application/json)

        {
            "countryCode": "CA",
            "expiryMonth": 6,
            "expiryYear": 2016,
            "nameOnCard": "JOHN L SMITH"
        }
+ Response 202 (application/json)
    + Header

            Location: http://api.dev.paybyphone.com/public/events/8d21a912-561a-48ff-b31b-00d600a2544e/

+ Request NA no name (application/json)

        {
            "countryCode": "CA",
            "expiryMonth": 6,
            "expiryYear": 2016
        }
+ Response 202 (application/json)
    + Header

            Location: http://api.dev.paybyphone.com/public/events/8d21a912-561a-48ff-b31b-00d600a2544e/

+ Request UK start mm/yy (application/json)

        {
            "countryCode": "UK",
            "expiryMonth": 6,
            "expiryYear": 2016,
            "startMonth": 1,
            "startYear": 2012
        }
+ Response 202 (application/json)
    + Header

            Location: http://api.dev.paybyphone.com/public/events/8d21a912-561a-48ff-b31b-00d600a2544e

+ Request UK issuenumber (application/json)

        {
            "countryCode": "UK",
            "expiryMonth": 6,
            "expiryYear": 2016,
            "issueNumber": 12
        }
+ Response 202 (application/json)
    + Header

            Location: http://api.dev.paybyphone.com/public/events/8d21a912-561a-48ff-b31b-00d600a2544e

+ Request FR (application/json)

        {
            "countryCode": "FR",
            "expiryMonth": 6,
            "expiryYear": 2016,
            "cvv": 111
        }
+ Response 202 (application/json)
    + Header

            Location: http://api.dev.paybyphone.com/public/events/8d21a912-561a-48ff-b31b-00d600a2544e

+ Response 401 (application/json)

        {"error": "Unauthorized"}

+ Response 500 (application/json)

        {"error": "Unhandled error"}

### Delete a payment account for a member [DELETE]

Delete a Payment Account for a Member.

The Member is identified by the **memberid** claim in the authorization bearer token

Payment Account is identified by the `paymentAccountId`.

**Request Contract**

    Header: Authorization: Bearer <token>

**Possible Response Codes**

- 202
- 401
- 500

+ Request paymentAccountId Exists

        /public/payment/accounts/{paymentAccountId}
+ Response 202 (application/json)
    + Header

            Location: http://api.dev.paybyphone.com/public/events/8d21a912-561a-48ff-b31b-00d600a2544e/

+ Request paymentAccountId Does Not Exists

        /public/payment/accounts/{paymentAccountId}
+ Response 202 (application/json)
    + Header

            Location: http://api.dev.paybyphone.com/public/events/8d21a912-561a-48ff-b31b-00d600a2544e/

+ Response 401 (application/json)

        {"error": "Unauthorized"}

+ Response 500 (application/json)

        {"error": "Unhandled error"}
# Group Notifications2
## About
Notifications API provides Feed And Device related resources
# Group FeedItems
FeedItem resources

## Feeditems [/public/notifications/feedItems?since={since}&limit={limit}]
Get item(s) from the member activity feed starting from a specific UTC date and time, returned in reverse chronological order.

+ Parameters
    + since (required, string, `2014-06-10T08:56:04.0000Z`) ... Start UTC date and time from which to retrieve the activity feed items
    + limit (required, number, `50`) ... The maximum number of items to retrieve from the activity feed

+ Model (application/json)

    JSON representation of A Feeditem.

    + Body

            [{
                "subject": "SFMTA Advisory",
                "content": "Please be aware that MUNI will be closed for service upgrades on Wednesday nights from 3:00 am to 4:00 am.",
                "timeStampRfc3339": "2014-07-08T15:42:12Z",
                "imageUrlPath": "https://si0.twimg.com/profile_images/3288835160/696dc7d131ccb873b1c399dcb08d0c8c.jpeg",
                "moreDetailsUrl": "http://www.sfmta.com", 
                "type": "Service Update"
            },
            {
                "subject": "Parking Complete",
                "content": "Parking For:  \r\nLocation: 20506621 \r\nExpired: 03/07/2014 12:37 PM \r\nCost: $0.54\r\n",
                "timeStampRfc3339": "2014-07-03T12:38:35Z",
                "imageUrlPath": "http://paybyphone.co.uk/images/app_assets/pbp_icon.png",
                "moreDetailsUrl": "http://www.google.com",
                "type": "Parking History"
            }]
            
### Retrieve all feed items for a member [GET]

Member is identified by the userAccountId extracted from the access token.

**Request Contract**

    Header: Authorization: Bearer <token>
    
**Mandatory Fields**

- since
- limit

**Response Contract**

        Content-Type: application/json
        [{
                "subject": "",
                "content": "",
                "timeStampRfc3339": "",
                "imageUrlPath": "",
                "moreDetailsUrl": "", 
                "type": ""
        },
        {
                "subject": "",
                "content": "",
                "timeStampRfc3339": "",
                "imageUrlPath": "",
                "moreDetailsUrl": "", 
                "type": ""                
        }]

**Possible Response Codes**

- 200
- 400
- 401
- 500

+ Request all fields supplied
    
        All mandatory fields supplied
+ Response 200
    [Feeditems][]

+ Response 500
    
        Unhandled error

+ Response 400

        {"error": "The request is invalid"}

+ Request missing since field

        Missing {since}
+ Response 400

        {"error": "The request is invalid"}

+ Request missing limit field

        Missing {limit}
+ Response 400

        {"error": "The request is invalid"}

+ Request missing or invalid authorization

        Authoriazation header field is missing or bearer token is not valid
+ Response 401

        {"message":"Authorization has been denied for this request."}
# Group Devices
Device resources

## Devices [/public/notifications/devices]
The devices root resource

### Add a device for member [POST]

Register a device for a member.

Member is identified by the userAccountId extracted from the access token.

Device language is identified by the Accept-Language header, using first value and defaulting to 'en'.

**Request Contract**

    Header: Authorization: Bearer <token>
    Header: Accept-Language: en-ca,en-us;q=0.8,fr-ca;q=0.5,fr-fr;q=0.3
    Content-Type: application/x-www-form-urlencoded
    deviceToken=&type=&isOptedInForAdvertising=

**Mandatory Fields**

- deviceToken
- type  (possible values are: iPhone, iPad, AndroidPhone, AndroidTablet, BackBerryPhone, BlackBerryTablet, CarInDashDevice)

**Optional Fields**

- isOptedInForAdvertising   (optional, boolean, defaults to false)

**Possible Response Codes**

- 202
- 400
- 401
- 500

+ Request with mandatory fields (application/x-www-form-urlencoded)
    + Body
    
            deviceToken=1111-2222&type=iPhone&isOptedInForAdvertising=true
+ Response 202 (application/json)
    + Header

            Location: http://api.paybyphone.com/public/notifications/devices/1111-2222
    
+ Response 500
    
        Unhandled error

+ Request with no deviceToken (application/x-www-form-urlencoded)
    + Body
    
            deviceToken=type=iPhone&isOptedInForAdvertising=true
+ Response 500

        {"message": "An error has occurred."}

+ Request with no type (application/x-www-form-urlencoded)
    + Body
    
            deviceToken=1111-2222&type=&isOptedInForAdvertising=true
+ Response 400

+ Request with no isOptedInForAdvertising
    + Body
        
            deviceToken=1111-2222&type=iPhone&isOptedInForAdvertising=
+ Response 202
    + Header

            Location: http://api.paybyphone.com/public/notifications/devices/1111-2222

+ Request with missing or invalid authorization

        Authorization header field is missing or bearer token is not valid
+ Response 401

        {"message":"Authorization has been denied for this request."}


## Device [/public/notifications/devices/{deviceToken}]
An individual device resource

+ Parameters
    + deviceToken (required, string, `abcd-1234-abcd-124`) ... the `deviceToken` Unique identifier for the device

+ Model (application/json)

    JSON representation of A Device.

    + Body

            {
                "memberDeviceId": "315125",
                "wavePayMemberId": 7785523,
                "isLoggedIn": true,
                "deviceToken": "1234-5678",
                "isOptedInForAdvertising": true,
                "deviceName": "iPhone",
                "deviceLanguage": "en-CA"
            }
            
### Retrieve an individual device for a member [GET]
Member is identified by the userAccountId extracted from the access token.

Device is identified by the `deviceToken`

**Request Contract**

    Header: Authorization: Bearer <token>
    
**Mandatory Fields**

- deviceToken

**Response Contract**

        Content-Type: application/json
        {
            "memberDeviceId": ""
            "wavePayMemberId": ""
            "isLoggedIn": ""
            "deviceToken": ""
            "isOptedInForAdvertising": ""
            "deviceName": ""
            "deviceLanguage": ""
        }

**Possible Response Codes**

- 200
- 400
- 401
- 404
- 405
- 500

+ Request all fields supplied
    
        All mandatory fields supplied
+ Response 200
    [Device][]

+ Response 500
    
        Unhandled error

+ Response 404

+ Request missing deviceToken field

        /public/notifications/devices/
+ Response 405

        {"message":"The requested resource does not support http method 'GET'."}

+ Request device not found for userAccountId (extracted from token)

        /public/notifications/devices/{deviceToken}
    
+ Response 404

+ Request with missing or invalid authorization

        Authorization header field is missing or bearer token is not valid
+ Response 401

        {"message":"Authorization has been denied for this request."}


### Edit a device [PUT]
Update a member device status.

Member is identified by the userAccountId extracted from the access token.

Device is identified by the `deviceToken`.

Device language is determined by the Accept-Language header, using first value or defaults to 'en'.

**Request Contract**

    Header: Authorization: Bearer <token>
    Header: Accept-Language: en-ca,en-us;q=0.8,fr-ca;q=0.5,fr-fr;q=0.3
    Content-Type: application/x-www-form-urlencoded
    type=&isLoggedIn=&isOptedInForAdvertising=

**Mandatory Fields**

- deviceToken
- type  (possible values are: iPhone, iPad, AndroidPhone, AndroidTablet, BackBerryPhone, BlackBerryTablet, CarInDashDevice)

**Optional Paramters**

- isLoggedIn    (optional, boolean, defaults to false)
- isOptedInForAdvertising   (optional, boolean, defaults to false)

**Possible Response Codes**

- 204
- 400
- 401
- 405
- 500

+ Request with mandatory fields (application/x-www-form-urlencoded)
    + Body
    
            type=iPhone&isLoggedIn=true&isOptedInForAdvertising=true
+ Response 204 
    
+ Response 500
    
        Unhandled error

+ Response 404

+ Request no device token (application/x-www-form-urlencoded)
    + Body
    
            /public/notifications/devices/
+ Response 405

        {"message":"The requested resource does not support http method 'PUT'."}

+ Request no type (application/x-www-form-urlencoded)
    + Body
    
            type=&isLoggedIn=true&isOptedInForAdvertising=true
+ Response 400

+ Request with no isLoggedIn (application/x-www-form-urlencoded)
    + Body
    
            type=iPhone&isLoggedIn=&isOptedInForAdvertising=true
+ Response 204 

+ Request with no isOptedInForAdvertising (application/x-www-form-urlencoded)
    + Body
    
            type=iPhone&isLoggedIn=true&isOptedInForAdvertising=
+ Response 204 

+ Request with missing or invalid authorization

        Authorization header field is missing or bearer token is not valid
+ Response 401

        {"message":"Authorization has been denied for this request."}
# Group EVENTS API
## About
Events API provides event related resources
# Group Events3
Event resources

## Events [/public/events/{workflowId}/]
Get event(s) generated under a specific workflowId.

+ Parameters
    + workflowId (required, string, `8d21a912-561a-48ff-b31b-00d600a2544e`) ... `workflowId` representing a collection of related events in the form of a GUID.

+ Model (application/json)

    JSON representation of A Event.

    + Body

            [{
                "$type" : "Pbp.Contracts.Shared.Parking.Events.ParkingSessionCreated, Pbp.Contracts.Shared",
                "vehicle":{
                     "id":1234,
                     "licensePlate":"ABC123",
                     "jurisdiction":"BC",
                     "countryCode":"CA",
                     "type":"motorcycle"
                },
                "locationId" : 8990,
                "parkingSessionId" : "4f53q912-561a-48ff-b31b-00d600a2544e",
                "vendorLocationId" : "ZZ2532",
                "stall" : "",
                "startTime" : "2014-08-17T17:12:47.2834849Z",
                "expireTime" : "2014-08-17T17:42:47.2834849Z",
                "vendorId" : 504,
                "parkingAccountId" : "76662345678"
            },
            {
                "$type" : "Pbp.Contracts.Shared.Parking.Events.ParkingSessionExtended, Pbp.Contracts.Shared",
                "vehicle":{
                     "id":1234,
                     "licensePlate":"ABC123",
                     "jurisdiction":"BC",
                     "countryCode":"CA",
                     "type":"motorcycle"
                },
                "newExpiryTime" : "2014-09-17T18:42:47.2834849Z",
                "oldExpiryTime" : "2014-09-17T17:42:47.2834849Z",
                "locationId" : 8990,
                "vendorLocationId" : "RR1235",
                "stall": "",
                "vendorId" : 504,
                "parkingAccountId" : "76662345678",
                "extensionLvpId" : "B2982F39-0373-4486-A200-24FE7AC68F45"
            },
            {
                "$type": "Pbp.Contracts.Shared.Payments.Events.PaymentAccountCreated, Pbp.Contracts.Shared",
                "memberId": "B2982F39-0373-4486-A200-24FE7AC68F45",
                "paymentAccountId": "B2982F39-0373-4486-A200-24FE7AC68F45",
                "maskedCardNumber": "411111------1111",
                "expiryMonth": 12,
                "expiryYear": 2015,
                "nameOnCard": "John Smith",
                "issueNumber": 31,
                "startMonth": 1,
                "startYear": 2014
            },
            {
                "$type": "Pbp.Contracts.Shared.Payments.Events.ParkingQuoteAccepted, Pbp.Contracts.Shared",
                "vendorId": 504,
                "locationId": 8990,
                "accountId": "76662345678",
                "licencePlate": "ABC123",
                "startTime": "2015-10-16T19:10:14-0000",
                "expireTime": "2015-10-16T19:10:14-0000",
                "eligibilityType": "VIS",
                "parkingSessionId": "4f53q912-561a-48ff-b31b-00d600a2544e",
                "stall": "",
                "vehicleId": 452,
                "amount": {
                       "amount": 5.00,
                       "currency": "CAD"
                }
            },
            {
                "locationId": 3107,
                "stall": 0,
                "licensePlate": "TEST111",
                "parkingAccountId": "8c12d339-b0e4-49b2-838a-9ff4fa379951",
                "duration":{
                        "timeUnit":Hours",
                        "quantity":"9"
                    },
                "startTime": "2015-09-24T10:45:00Z",
                "cvv": "****",
                "$type": "Pbp.Contracts.Shared.Parking.Events.StartParkingRequested, Pbp.Contracts.Shared"
            },
            {
                 "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
                 "vehicleId": "12793707",
                 "countryCode": "CA",
                 "licensePlate": "GGG328",
                 "jurisdiction": "MB",
                 "vehicleType": "motorcycle",
                 "$type": "Pbp.Contracts.Shared.Parking.Events.VehicleUpdated, Pbp.Contracts.Shared"
            },
            {
                 "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
                 "vehicleId": "12793707",
                 "countryCode": "CA",
                 "licensePlate": "GGG328",
                 "jurisdiction": "MB",
                 "vehicleType": "motorcycle",
                 "$type": "Pbp.Contracts.Shared.Parking.Events.VehicleUpdatedRequested, Pbp.Contracts.Shared"
            },
            {
                 "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
                 "countryCode": "CA",
                 "licensePlate": "GGG328",
                 "jurisdiction": "MB",
                 "vehicleType": "motorcycle",
                 "$type": "Pbp.Contracts.Shared.Parking.Events.VehicleAdded, Pbp.Contracts.Shared"
            }
            {
                 "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
                 "countryCode": "CA",
                 "licensePlate": "GGG328",
                 "jurisdiction": "MB",
                 "vehicleType": "motorcycle",
                 "$type": "Pbp.Contracts.Shared.Parking.Events.VehicleAddRequested, Pbp.Contracts.Shared"
            },
            {
                 "ParkingSessionId": "4f53q912-561a-48ff-b31b-00d600a2544e"
                 "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
                 "duration":{
                        "timeUnit":Hours",
                        "quantity":"9"
                 },
                 "cvv": "****",
                 "$type": "Pbp.Contracts.Shared.Parking.Events.ExtendParkingRequested, Pbp.Contracts.Shared"
            },          
            {
                "$type": "Pbp.Contracts.Shared.UserAccounts.Events.UserAccountPasswordChanged, Pbp.Contracts.Shared",
                "userAccountId": "8d21a912-561a-48ff-b31b-00d600a2544e",
            }]
            
### Retrieve all parking events in a workflow [GET]

Workflow is identified by the workflowId.

**Request Contract**

    Header: Authorization: Bearer <token>
    
**Mandatory Fields**

- workflowId 

**Response Contract**

        Content-Type: application/json
        [{
            "$type" : "Pbp.Contracts.Shared.Parking.Events.ParkingSessionCreated, Pbp.Contracts.Shared",
            "vehicle":{
                    "id":1234,
                    "licensePlate":"ABC123",
                    "jurisdiction":"BC",
                    "countryCode":"CA",
                    "type":"motorcycle"
            },
            "locationId" : 8990,
            "parkingSessionId" : "4f53q912-561a-48ff-b31b-00d600a2544e",
            "vendorLocationId" : "ZZ2532",
            "stall" : "",
            "startTime" : "2014-09-17T17:12:47.2834849Z",
            "expireTime" : "2014-09-17T17:42:47.2834849Z",
            "vendorId" : 504,
            "parkingAccountId" : "76662345678"
        },
        {
            "$type" : "Pbp.Contracts.Shared.Parking.Events.ParkingSessionExtended, Pbp.Contracts.Shared",
            "vehicle":{
                    "id":1234,
                    "licensePlate":"ABC123",
                    "jurisdiction":"BC",
                    "countryCode":"CA",
                    "type":"motorcycle"
            },
            "newExpiryTime" : "2014-09-17T18:42:47.2834849Z",
            "oldExpiryTime" : "2014-09-17T17:42:47.2834849Z",
            "locationId" : 8990,
            "vendorLocationId" : "ZZ2532",
            "stall": "",
            "vendorId" : 504,
            "parkingAccountId" : "76662345678",
            "extensionLvpId" : "B2982F39-0373-4486-A200-24FE7AC68F45"
        },
        {
            "$type": "Pbp.Contracts.Shared.Payments.Events.PaymentAccountCreated, Pbp.Contracts.Shared",
            "memberId": "B2982F39-0373-4486-A200-24FE7AC68F45",
            "paymentAccountId": "B2982F39-0373-4486-A200-24FE7AC68F45",
            "maskedCardNumber": "411111------1111",
            "expiryMonth": 12,
            "expiryYear": 2015,
            "nameOnCard": "John Smith",
            "issueNumber": 31,
            "startMonth": 1,
            "startYear": 2014
        },
        {
            "$type": "Pbp.Contracts.Shared.Payments.Events.ParkingQuoteAccepted, Pbp.Contracts.Shared",
            "vendorId": 504,
            "locationId": 8990,
            "accountId": "76662345678",
            "licencePlate": "ABC123",
            "startTime": "2015-10-16T19:10:14-0000",
            "expireTime": "2015-10-16T19:10:14-0000",
            "eligibilityType": "VIS",
            "parkingSessionId": "4f53q912-561a-48ff-b31b-00d600a2544e",
            "stall": "",
            "vehicleId": 452,
            "amount": {
                   "amount": 5.00,
                   "currency": "CAD"
            }
        },
        {
            "locationId": 3107,
            "stall": 0,
            "licensePlate": "TEST111",
            "parkingAccountId": "8c12d339-b0e4-49b2-838a-9ff4fa379951",
            "duration":{
                    "timeUnit":Hours",
                    "quantity":"9"
                },
            "startTime": "2015-09-24T10:45:00Z",
            "cvv": "****",
            "$type": "Pbp.Contracts.Shared.Parking.Events.StartParkingRequested, Pbp.Contracts.Shared"
        },
        {
             "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
             "vehicleId": "12793707",
             "countryCode": "CA",
             "licensePlate": "GGG328",
             "jurisdiction": "MB",
             "vehicleType": "motorcycle",
             "$type": "Pbp.Contracts.Shared.Parking.Events.VehicleUpdated, Pbp.Contracts.Shared"
        },
         {
             "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
             "vehicleId": "12793707",
             "countryCode": "CA",
             "licensePlate": "GGG328",
             "jurisdiction": "MB",
             "vehicleType": "motorcycle",
             "$type": "Pbp.Contracts.Shared.Parking.Events.VehicleUpdatedRequested, Pbp.Contracts.Shared"
        },
        {
             "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
             "countryCode": "CA",
             "licensePlate": "GGG328",
             "jurisdiction": "MB",
             "vehicleType": "motorcycle",
             "$type": "Pbp.Contracts.Shared.Parking.Events.VehicleAdded, Pbp.Contracts.Shared"
        },
        {
             "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
             "countryCode": "CA",
             "licensePlate": "GGG328",
             "jurisdiction": "MB",
             "vehicleType": "motorcycle",
             "$type": "Pbp.Contracts.Shared.Parking.Events.VehicleAddRequested, Pbp.Contracts.Shared"
        },
        {
             "ParkingSessionId": "4f53q912-561a-48ff-b31b-00d600a2544e"
             "parkingAccountId": "23fccd39-70c2-446f-915c-6d2362b64e28",
             "duration":{
                    "timeUnit":Hours",
                    "quantity":"9"
             },
             "cvv": "****",
             "$type": "Pbp.Contracts.Shared.Parking.Events.ExtendParkingRequested, Pbp.Contracts.Shared"
        },
        {
            "$type": "Pbp.Contracts.Shared.UserAccounts.Events.UserAccountPasswordChanged, Pbp.Contracts.Shared",
            "userAccountId": "8d21a912-561a-48ff-b31b-00d600a2544e",
        }]

**Possible Response Codes**

- 200
- 400
- 401
- 500

+ Request all fields supplied
    
        All mandatory fields supplied
+ Response 200
    [Events][]

+ Response 500
    
        Unhandled error

+ Request missing workflowId field

        Missing {workflowId}
+ Response 400

        {"error": "The request is invalid"}

+ Request missing or invalid authorization

        Authoriazation header field is missing or bearer token is not valid
+ Response 401

        {"message":"Authorization has been denied for this request."}
# Group Events

## Get events by workflow [GET /events/workflow/{id}]

**Possible Response Codes**

- 200 - OK
- 400 - Bad Request
- 401 - Unauthorized

+ Parameters
    + id: `b942cd39-60c5-4fdd-9b4f-2c6fe8a4ea26` (required, string) - Unique identifier of the workflow.

+ Response 200 (application/json)
    [
        {
            "$type": "PaymentAccountCreated",
            "paymentAccountId": "7c3367cd-883b-47c2-bcf9-5043d6b8dea8",
            "memberId": "c61a93b7-e506-4cd4-ab74-6340e6149736",
            "maskedCardNumber": "411111------1111",
            "expiryMonth": 12,
            "expiryYear": 2015,
            "nameOnCard": "John Smith",
            "issueNumber": null,
            "startMonth": null,
            "startYear": null
        }
    ]

# HyperVerge DB Validation API Documentation

## Overview

This documentation describes the validation API. The postman collection can be found [here](https://www.getpostman.com/collections/80787f0f353b13129ced). 


- [HyperVerge PAN Validation API Documentation](#hyperverge-validation-api-documentation-beta)
	- [Overview](#overview)
	- [Schema](#schema)
	- [Parameters](#parameters)
	- [Root Endpoint](#root-endpoint)
	- [Authentication](#authentication)
	- [API Call structure](#api-call-structure)
	- [Request structure](#request-structure)
		- [Verify PAN](#verify-pan)
		- [PAN Name Fetch](#pan-name-fetch)
		- [DL Verification and Extraction](#dl-verification-and-extraction)
		- [VoterId Check](#voterId-check)
		- [Bank Account Verification](#bank-account-verification)
		- [Passport Verification](#passport-verification)
		- [Aadhaar Pan Link Status](#aadhaar-pan-link-status)
	- [Response Structure](#response-structure)
		- [Verify PAN](#verify-pan-1)
		- [PAN Name Fetch](#pan-name-fetch-1)
		- [DL Verification and Extraction](#dl-verification-and-extraction-1)
		- [VoterId Check](#voterId-check-1)
		- [Bank Account Verification](#bank-account-verification-1)
		- [Passport Verification](#passport-verification-1)
		- [Aadhaar Pan Link Status](#aadhaar-pan-link-status-1)
	- [Status Codes](#status-codes)


## Schema

It is recommended that HTTPS is used for all API calls. For HTTPS, only TLS v1.2 is supported to ensure better security. All data is received as JSON.

## Parameters
All optional and compulsory parameters are passed as part of the request body.

## Root Endpoint
A `GET` request can be issued to the root endpoint to check for successful connection. 

	 curl https://ind-verify.hyperverge.co/api

The `plain/text` reponse of `Aok` should be received.

## Authentication

Currently, an appId, appKey combination is passed in the request header. The appId and appKey are provided on request by the HyperVerge team. If you would like to try the API, please reach out to contact@hyperverge.co

	curl -X POST https://ind-verify.hyperverge.co/\
	  -H 'appid: xxx' \
	  -H 'appkey: yyy' \
	  -H 'content-type: application/json;' \
	  -d '{}' \


On failed attempt with invalid credentials or unauthorized access the HTTP response would have a status code : 401

Please do not expose the appid and appkey on browser applications. In case of a browser application, set up the API calls from the server side.

## API Call structure

1) **PAN Verification**
    * **URL**
      - /api/verifyPAN

	* **Method**
	`POST`
	
	* **Header**
		* content-type : application/json
		* appId
		* appKey

	* **Request Body**
		* *pan* : PAN Number. Should be of the format 'CCCCCDDDDC' where the C is character and D is digit.
		* *name* : Name of the PAN holder
		* *dob* : Date of Birth of the PAN holder. Should of the format dd/mm/yyyy
2) **PAN Name Fetch**
    * **URL**
      - /api/getNameFromPAN

	* **Method**
	`POST`
	
	* **Header**
		* content-type : application/json
		* appId
		* appKey

	* **Request Body**
		* *pan* : PAN Number. Should be of the format 'CCCCCDDDDC' where the C is character and D is digit.
3) **DL Verification and Extraction**
    * **URL**
      - /api/checkDL

	* **Method**
	`POST`
	
	* **Header**
		* content-type : application/json
		* appId
		* appKey

	* **Request Body**
		* *dlNumber* : DL Number
		* *dob* : Date of Birth of the DL holder. Should of the format dd-mm-yyyy
4) **VoterId  Extraction**
    * **URL**
      - /api/checkVoterId

	* **Method**
	`POST`
	
	* **Header**
		* content-type : application/json
		* appId
		* appKey

	* **Request Body**
		* *epicNumber* : EPIC Number present in VoterId

5) **Bank Account Verification**
    * **URL**
      - /api/checkBankAccount

	* **Method**
	`POST`
	
	* **Header**
		* content-type : application/json
		* appId
		* appKey

	* **Request Body**
		* *ifsc* : IFSC Code of the Account's Branch
		* *accountNumber* :  Account Number of the Bank Account

6) **Passport Verification**
    * **URL**
      - /api/verifyPassport

	* **Method**
	`POST`
	
	* **Header**
		* content-type : application/json
		* appId
		* appKey

	* **Request Body**
		* *fileNo* : The Passport Reference File Number 
		* *dob* :  Date of Birth

7) **Aadhaar Pan Link Status**
    * **URL**
      - /api/verifyAadhaarPanLink

	* **Method**
	`POST`

	* **Header**
		* content-type : application/json
		* appId
		* appKey

	* **Request Body**
		* *aadhaarNumber* : Aadhaar Number
		* *panNumber* :  Pan Number

## Request Structure

### Verify PAN
```
{
    pan: <required. String of length 10 CCCCCDDDDC>,
    name: <required. String parameter>,
    dob: <required. String of format 'DD/MM/YYYY'>
}
```

### PAN Name Fetch
```
{
    pan: <required. String of length 10 CCCCCDDDDC>
}
```
### DL Verification and Extraction
```
{
    dlNumber: <required. String of length between 9 and 20>,
    dob: <required. String of format 'DD-MM-YYYY'>
}
```
### VoterId Check
```
{
    epicNumber: <required, String of length between 6 and 13>
}
```
### Bank Account Verification
```
{
    ifsc: <required, String of length 11 characters>,
    accountNumber: <required, String>
}
```
### Passport Verification
```
{
    fileNo: <required, String>,
    dob: <required, String>
}
```
### Aadhaar Pan Link Status
```
{
    aadhaarNumber: <required, String>,
    panNumber: <required, String>
}
```
## Response Structure

### Verify PAN

* Success Response:

		* Code: 200 

		* Incase of a successful validation, the response would have the following schema.
  ```
  {
    "status": "success",
    "statusCode": "200",
    "result": {
        "status": "Active",
        "duplicate": false,
        "nameMatch": true,
        "dobMatch": true
     }
  }
  ```
  
  - `status` refers to the status of the PAN card and should be "Active"
  - `duplicate` checks if a duplicate PAN card was issued
  - `nameMatch` checks if the name of the person in API input and in the DB is matching
  - `dobMatch` checks if DOB of the person in API input and the DB is matching
* Error Responses:
	 * Incase the any input details is not correct, the following response will be sent with error code `422` with following message
	   
		   {
			   "status": "failure",
	   	       "statusCode": "422",
		       "error": "Invalid value passed for an input"
	       }
	 * Incase the PAN Number is not present in DB, the following response will be sent with error code `422` with following message
	   
		   {
			   "status": "failure",
	   	       "statusCode": "422",
		       "error": "Entered id is not found in any database"
	       }

### PAN Name Fetch

* Success Response:

	* Code: 200 
	* Incase of a successful validation, the response would have the following schema.
      ```
      {
         "status": "success",
         "statusCode": "200",
         "result": {
           "name": <Name of the PAN Holder>
         }
      }
      ```
* Error Responses:
	 * Incase the any input details is not correct, the following response will be sent with error code `422` with following message
	   
		   {
			   "status": "failure",
	   	       "statusCode": "422",
		       "error": "Invalid value passed for an input"
	       }
	 * Incase the PAN Number is not present in DB, the following response will be sent with error code `422` with following message
	   
		   {
			   "status": "failure",
	   	       "statusCode": "422",
		       "error": "Entered id is not found in any database"
	       }
  
### DL Verification and Extraction
* Success Response:

		* Code: 200 

		* Incase of a successful validation, the response would have the following schema.
  ```
  {
    "status": "success",
    "statusCode": "200",
    "result": {
        "issue_date": <Date of issue in DD-MM-YYYY format>,
        "father/husband": <Father Name/Husband Name>,
        "name": <Name of the User>,
        "img": <Base64 image of the user profile photo>,
        "blood_group": <Blood Group of the user>,
        "dob": <Date of Birth in DD-MM-YYYY format>,
        "validity": {
            "non-transport": "Date Range, DD-MM-YYYY to DD-MM-YYYY",
            "transport": ""
        },
        "cov_details": [
            {
                "issue_date": "Date of Issue/Issuing Authority",
                "cov": "Coverage type of license"
            },
        ],
        "address": "Address of the User in the DL"
     }
  }
  ```
  
* Error Responses:
	 
	 * Incase the DL Number and DOB combination is not correct, then following response will be sent with error code `422`
		```	 	
		{
			"status": "failure",
	        "statusCode": "422",
			"error": "Invalid value passed for an input"
		}
		```
    * Incase, the DL Number is not found in any Database, following response will be sent with error code `422`
		```	 	
		{
			"status": "failure",
	        "statusCode": "422",
			"error": "Entered id is not found in any database"
		}
		```

### VoterId Check
* Success Response:

	* Code: 200 
	* Incase of a successful validation, the response would have the following schema.

  ```
  {
    "status": "success",
    "statusCode": "200",
    "result": {
        "ps_lat_long": <Latitude and Longitute of Polling Station>,
        "rln_name_v1": <Relation Name 1>,
        "rln_name_v2": <Relation Name 2>,
        "rln_name_v3": <Relation Name 3>,
        "part_no": <Part Number>,
        "rln_type": <Relation Type>,
        "section_no": "Section Number",
        "id": "ID of VoterId Card",
        "epic_no": "EPIC Number",
        "rln_name": "Relation Name",
        "district": "District",
        "last_update": "Last Update Timestamp",
        "state": "State",
        "ac_no": "",
        "house_no": "House Number",
        "ps_name": "Polling Station Name",
        "pc_name": "",
        "slno_inpart": "",
        "name": "Name of the User",
        "part_name": "",
        "dob": "DOB in DD-MM-YYYY Format",
        "gender": "Gender of the User",
        "age": <Age of User, Number>,
        "ac_name": "Account Name",
        "name_v1": "",
        "st_code": "",
        "name_v3": "",
        "name_v2": ""
    },
  }
  ```
  
* Error Responses:
	 
	 * Incase the EPIC Number is not correct, then following response will be sent with error code `422`
		```	 	
		{
			"status": "failure",
	    "statusCode": "422",
			"error": "Invalid value passed for an input"
		}
		```
    * Incase, the EPIC Number is not found in any Database, following response will be sent with error code `422`
		```	 	
		{
			"status": "failure",
	    "statusCode": "422",
			"error": "Entered id is not found in any database"
		}
		```

### Bank Account Verification
* Success Response:

	* Code: **200**
	* result.bankTxnStatus: **true**
	* Incase of a successful validation, the response would have the following schema.

  ```
  {
     "status": "success",
     "statusCode": "200",
     "result": {
       "bankResponse": "Transaction Successful",
       "ifsc": <IFSC Code>,
       "accountNumber": <Account Number>,
       "accountName": <Name of the Account Holder>,
       "bankTxnStatus": true
     }
  }
  ```
  
* Error Responses:
	 
	 * Incase the Bank Account Number is not correct, then following response will be sent with status code `200` and **result.bankTxnStatus** `false`
	   ```
	   {
	       "status": "success",
           "statusCode": "200",
           "result": {
             "bankResponse": <Failure Reason>,
             "ifsc": <IFSC Code>,
             "accountNumber": <Account Number>,
             "accountName": "",
             "bankTxnStatus": false
         }
       }
       ```
 
### Passport Verification
* Success Response:

	* Code: **200**
	* Incase of a successful validation, the response would have the following schema.

```
{
    "status": "success",
    "statusCode": "200",
    "result": {
        "applicationDate": "14/05/2018",
        "dateOfIssue": {
            "dispatchedOnFromSource": "14/05/2018",
            "dateOfIssueMatch": null
        },
        "passportNumber": {
            "passportNumberFromSource": "S3733862",
            "passportNumberMatch": null
        },
        "name": {
            "nameScore": null,
            "nameMatch": null,
            "surnameFromPassport": "SHIRHATTI",
            "nameFromPassport": "OMKAR MILIND"
        },
        "typeOfApplication": "Tatkaal"
 }
}
```
  
* Error Responses:
	 
	 * Incase the  file Number or dob are incorrect, then following response will be sent with status code `422` and **result.status** `failure`
```
{
    "status": "failure",
    "statusCode": "422",
    "error": "Entered id is not found in any database"
}
```

### Aadhaar Pan Link Status

* Success Response:

	* Code: **200**
	* Incase of a successful validation, the response would have the following schema.

```
{
    "status": "success",
    "statusCode": "200",
    "result": {
        "message": "Your PAN is linked to Aadhaar Number  XXXX XXXX 2071."
	}
}
```

* Error Responses:

	 * Incase the  aadhaar number and pan number are incorrect, then following response will be sent with status code `422` and **result.status** `failure`
```
{
    "status": "failure",
    "statusCode": "422",
    "error": "Invalid Pan or Aadhaar"
}
```

#### Common Errors:
  * Incase the format of input is incorrect, the following response will be sent status code `400`
    
	```	
    {
	    "status": "failure",
	    "statusCode": "400",
	    "error": {
	        "code": 1099,
	        "message": <String, message describing the error>,
	        "path": "...",
	        "type": "...",
	        "context": "..."
	   	 }
	}	 
	```
  * In case the Data Source is not available, error with status code `503` and error `Data Source not Available` would be sent back
    ```	
    {
	    "status": "failure",
	    "statusCode": "503",
	    "error": "Data Source not Available"
	}	 
	```
	Note: Since this error occurs because of issues with various Government and non-Government Databases, we have limited control over resolution. We do try to escalate wherever feasible


## Status Codes

The following are the various error codes

|Code|Description|
|----|----|
|200|Success - Data was present in DB and the necessary details have been returned in the body|
|400|Invalid Input Format or Missing Input parameter. Please check your input to the API|
|401|Unauthorized - Please contact HyperVerge|
|404|Invalid Endpoint - Please check the API call|
|422|Not found in DB or invalid input combination|
|429|Rate Limit Exceeded - Please contact HyperVerge to increase rate limit|
|500|Internal Server Error - Please retry|
|503|Source not Available - Please retry after some time|
|504|Issue with the external service|


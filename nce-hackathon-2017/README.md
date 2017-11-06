# Welcome to hackathon

## TL;DR;:

* [Inspiration / Task / Assignment](InspirationTask.md)
* The complete documentation of the APIs, snippets, and the possibility to query the endpoints, can be found [here](https://mcb-hackathon-2017-spv.portal.azure-api.net/docs/services/), You get access to the API by either creating an account or by using an existing Facebook login.
* **TODO (link til eksempel-app, må legges til)**
* **TODO (link til Cross-origin requests and backend-seksjonen)**

## Introduction
Due to the new EU-directive named PSD2, all banks have to give access to account to third parties. This makes it possible to create applications that for a given customer aggregates information and functionality across all the banks he or she uses.

## Scenario

This hackathon presents a simplified version of PSD2. We have 20 000 customers, conveniently identified with ids: 1 - 20 000, 30 % of them are customers in only one bank, 40% has two banks and 30% are customers in three banks. 

The banks are:

* Bank A
* Bank B
* Bank C

These three finctional banks have provided their data in a standardized form (see Berlin Group) to be PSD2 compliant. Their REST-API endpoints make it possible to retrieve the accounts and transactions for their customers. Luckily, they all use the same API-specification, so we can get the (generated) data in an uniform way and create amazing things.

## API examples

You can find the documentation of the APIs, snippets, and the option to test the endpoints at the following URL: https://mcb-hackathon-2017-spv.portal.azure-api.net/docs/services/

## Account-API

We retrieve the accounts of a customer by making a GET-request to the endpoint: `/v1/{customer}/accounts`, where `customer` is a placeholder value in the range: 1 - 20 000, we get a 200-response if he has accounts, and a 404 if he has none.

### Account response example

A request to `https://mcb-hackathon-2017-spv.azure-api.net/bank-a/v1/1/accounts` gives us the following response:

```json
[
    {
        "id": "1234.0A.00028", // account number, used to query transactions
        "expected_balance": 3053,
        "booked_balance": 1992,
        "_links": {
            "transactions": "/v1/28/accounts/1234.0A.00028/transactions" // link to transaction-api
        }
    }
]
```

## Transactions-API

The accounts have an id we can use to get the transactions, by making a request to the endpoint `/v1/{customer}/accounts/{id}/transcations`.

### Transaction response example

A request to `https://mcb-hackathon-2017-spv.azure-api.net/bank-a/v1/1/accounts/1234.0A.00001/transactions` gives us the following response:

```json
[{
  "entry_date": "2017-01-03T00:00:00.0000000",
  "amount": -76.76,
  "credit_debet": "Debited",
  "category": "Bil og transport",
  "sub_category": "Tog"
}, {
  "entry_date": "2017-01-05T00:00:00.0000000",
  "amount": 2255.15,
  "credit_debet": "Credited",
  "category": "Studiestøtte",
  "sub_category": "Studiestøtte"
}, {
  "entry_date": "2017-01-05T00:00:00.0000000",
  "amount": -341.47,
  "credit_debet": "Debited",
  "category": "Bil og transport",
  "sub_category": "Tog"
}, {
  "entry_date": "2017-01-09T00:00:00.0000000",
  "amount": -11.11,
  "credit_debet": "Debited",
  "category": "Bil og transport",
  "sub_category": "Tog"
}, {
  "entry_date": "2017-01-09T00:00:00.0000000",
  "amount": -124.60,
  "credit_debet": "Debited",
  "category": "Mat og drikke",
  "sub_category": "Hurtigmat"
}]
```

The transactions are either credited (money is deposited into the account e.g salary or pension), or debited (money is withdrawn),

## Cross-origin requests and backend

It is not possible to query the API from client-side javascript, so you need a backend/server to query the APIs, the documentation has snippets for popular server-side languages, if you prefer nodejs, the following snippet will call the Account-API for bank-a:

```javascript
const request = require('request')

const customer = 'customerId' // possible values: 1 - 20000
const key = 'your-subscription-key'
const bankUrl = 'http://mcb-hackathon-2017-spv.azure-api.net/bank-a'

request({
  url: `${bankUrl}/v1/${customer}/accounts/`,
  headers: {
    'Ocp-Apim-Subscription-Key': key,
  }}, function(error, response, body) {
  if(!error && response.statusCode == 200) {
    console.log('accounts', JSON.parse(body));
  }
  if(!error && response.statusCode == 404) {
    console.log('no accounts found');
  }
})

```

## Andre API-er og åpne data
Mange offentlige datasett kan brukes gratis. Se f.eks. http://data.norge.no/. Livar Bergheim from Brreg has also published a nice introduction to open data here: https://medium.com/@livar.bergheim/opne-offentlege-data-til-hackathon-d94f4d196950

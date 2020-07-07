# Kod Örnekleri

<TOC />

## Node.js(Axios)



## CSharp(RestSharp)


## Java(Unirest)
### Token Alma
```java
  public String getAccessToken() {
        Map<String, String> headers = new HashMap<>();

        Map<String, Object> fields = new HashMap<>();
        fields.put("client_id", "37b12626-a064-44e5-bbcf-31a9e42a0774");
        fields.put("client_secret", "400ab36f-fede-488d-98ed-d46a7f751f50");
        fields.put("grant_type", "password");
        fields.put("username", "backenduser");
        fields.put("password", "123456");

        HttpResponse<JsonNode> jsonResponse
                = Unirest.post("https://${HOST}/digitanium/v1/login")
                .fields(fields)
                .asJson();
        return jsonResponse.getBody().getObject().getString("access_token");

    }
```

### Ödeme Başlatma

```java
   class PaymentContent {
        String key;
        Double value;

        public PaymentContent(String key, Double value) {
            this.key = key;
            this.value = value;
        }
    }
    public void paymentTranscation(String to) {
        Map<String, String> headers = new HashMap<>();
        headers.put("Authorization", "Bearer " + getAccessToken());
        headers.put("Accept", "application/json");
        headers.put("Content-Type", "application/json");

        Map<String, Object> fields = new HashMap<>();
        fields.put("merchantServiceUUID", "37b12626-a064-44e5-bbcf-31a9e42a0774");
        fields.put("userId", to);
        fields.put("version", 1);
        fields.put("merchantCallback","https://domain.com");
        fields.put("transactionTimeout",50);
        fields.put("merchantId","7eadbeae-85c1-43d8-aece-abc9f281dde");
        fields.put("amount",132.5);
        fields.put("currency","EUR");
        List<PaymentContent> paymentContents = new ArrayList<>();
        paymentContents.add(new PaymentContent("First Value",30.0));
        paymentContents.add(new PaymentContent("Second Value",90.0));

        fields.put("paymentContent",paymentContents);


        HttpResponse<JsonNode> jsonResponse
                = Unirest.post("https://${HOST}/digitanium/v2/mpower/payment/transaction/")
                .headers(headers).body(fields)
                .asJson();
    }

```
### Ödeme Durumu Sorgulama

```java
    public void paymentStatus(String merchantServiceUUID, String transactionID, String merchantId) {

        Map<String, String> headers = new HashMap<>();
        headers.put("Authorization", "Bearer " + getAccessToken());
        headers.put("Accept", "application/json");
        headers.put("Content-Type", "application/json");

        Map<String, Object> fields = new HashMap<>();
        fields.put("merchantServiceUUID", merchantServiceUUID);
        fields.put("transactionID", transactionID);
        fields.put("merchantId", merchantId);

        HttpResponse<JsonNode> jsonResponse
                = Unirest.post("https://${HOST}/digitanium/v2/mpower/payment/transaction/status/")
                .headers(headers).body(fields)
                .asJson();
    }
```
### Ödemeyi İptal Etme

```java
    public void cancelPayment(String merchantServiceUUID, String transactionID, String merchantId) {

        Map<String, String> headers = new HashMap<>();
        headers.put("Authorization", "Bearer " + getAccessToken());
        headers.put("Accept", "application/json");
        headers.put("Content-Type", "application/json");

        Map<String, Object> fields = new HashMap<>();
        fields.put("merchantServiceUUID", merchantServiceUUID);
        fields.put("transactionID", transactionID);
        fields.put("merchantId", merchantId);

        HttpResponse<JsonNode> jsonResponse
                = Unirest.post("https://${HOST}/digitanium/v2/mpower/payment/transaction/cancel/")
                .headers(headers).body(fields)
                .asJson();
    }
```
## Python(Requests)

### Token Alma

```python

HOST="ibb01.api.ibb-test.2ndfloor.aws-dev.kobil.com"
CLIENT_ID="a6b2423e-f621-4ddf-84f1-e102acbe5c1b"
CLIENT_SECRET="4e7108f7-192d-4389-95d1-7cf216e1d21b"
USERNAME="test-merchant"
PASSWORD="123456"
TOKEN_ENDPOINT = "https://" + HOST + "/digitanium/v1/login"

def get_token():

    data = {
        "username": USERNAME,
        "password": PASSWORD,
        "client_id": CLIENT_ID,
        "client_secret": CLIENT_SECRET,
        "grant_type": "password"
    }

    headers = {
        "Content-Type": "application/x-www-form-urlencoded"
    }
    tokenRes = requests.post(TOKEN_ENDPOINT, data=data, headers=headers)
    try:
        return tokenRes.json()["access_token"]
    except:
        return ""

```

### Ödeme Başlatma

```python

TOKEN = get_token()
CLIENT_ID="a6b2423e-f621-4ddf-84f1-e102acbe5c1b"
HOST="ibb01.api.ibb-test.2ndfloor.aws-dev.kobil.com"
TRANSACTION_ENDPOINT = "https://" + HOST + "/digitanium/v2/mpower/payment/transaction"

version="1"
idempotencyId="d60842d2-4f1f-4231-8a9d-86fa49985824"
userId="user@example.com"
merchantCallback="https://domain.com"
transactionTimeout="59"
merchantId="dfa69ef4-17e6-4652-a9d7-964b13494565"
amount="100"
currency="EUR"
paymentContent=[
    [
      {
        "key": "First Value",
        "value": "12.03 TRY"
      },
	  {
        "key": "Second Value",
        "value": "8.00 TRY"
      }
    ],
	[
      {
        "key": "Third Value",
        "value": "22 TRY"
      }
    ]
  ]
paymentType="paymentRequest"
processId="e11067af-7f74-41e5-b8c4-dd17b440f51e|9f0022ae-8b0b-4165-a164-3ded334a7fe4|d60842d2-4f1f-4231-8a9d-86fa49985824"

def paymentTransaction():

    headers = {
        "Authorization": "Bearer " + TOKEN,
        "Accept": "application/json",
        "Content-Type": "application/json"
    }

    data = {
        "version": version,
        "idempotencyId": idempotencyId,
        "userId": userId,
        "merchantServiceUUID": CLIENT_ID,
        "merchantCallback": merchantCallback,
        "transactionTimeout": transactionTimeout,
        "merchantId": merchantId,
        "amount": amount,
        "currency": currency,
        "paymentContent": paymentContent,
        "paymentType":paymentType,
        "processId":processId
    }

    res = requests.post(TRANSACTION_ENDPOINT, data=json.dumps(data), headers=headers)
    print(res.status_code, res.content)
    return res

```

### Ödeme Durumu Sorgulama

```python

TOKEN = get_token()
CLIENT_ID="a6b2423e-f621-4ddf-84f1-e102acbe5c1b"
HOST="ibb01.api.ibb-test.2ndfloor.aws-dev.kobil.com"
STATUS_ENDPOINT = "https://" + HOST + "/digitanium/v2/mpower/payment/transaction/status"

def statusTransaction():

    merchantId="dfa69ef4-17e6-4652-a9d7-964b13494565"
    transactionId="451c79d7-7787-4279-b16e-f71479416810"

    headers = {
        "Authorization": "Bearer " + TOKEN,
        "Accept": "application/json",
        "Content-Type": "application/json"
    }

    data = {
        "merchantServiceUUID": CLIENT_ID,
        "merchantId": merchantId,
        "transactionId": transactionId
    }
    res = requests.post(STATUS_ENDPOINT, data=json.dumps(data), headers=headers)
    print(res.status_code, res.content)
    return res

```

### Ödemeyi İptal Etme

```python

TOKEN = get_token()
CLIENT_ID="a6b2423e-f621-4ddf-84f1-e102acbe5c1b"
HOST="ibb01.api.ibb-test.2ndfloor.aws-dev.kobil.com"
CANCEL_ENDPOINT = "https://" + HOST + "/digitanium/v2/mpower/payment/transaction/cancel"

merchantId="dfa69ef4-17e6-4652-a9d7-964b13494565"
transactionId="451c79d7-7787-4279-b16e-f71479416810"

def cancelTransaction():

    headers = {
        "Authorization": "Bearer " + TOKEN,
        "Accept": "application/json",
        "Content-Type": "application/json"
    }

    data = {
        "merchantServiceUUID": CLIENT_ID,
        "merchantId": merchantId,
        "transactionId": transactionId
    }
    res = requests.post(CANCEL_ENDPOINT, data=json.dumps(data), headers=headers)
    print(res.status_code, res.content)
    return res

```


## Go(Native)

## PHP(Http_Requests2)

### Token Alma

```php

require_once 'HTTP/Request2.php';

$HOST = "ibb01.api.ibb-test.2ndfloor.aws-dev.kobil.com";
$CLIENT_ID = "a6b2423e-f621-4ddf-84f1-e102acbe5c1b";
$CLIENT_SECRET = "4e7108f7-192d-4389-95d1-7cf216e1d21b";
$USERNAME = "test-merchant";
$PASSWORD = "123456";

$TOKEN_ENDPOINT = "https://" + $HOST + "/digitanium/v1/login";

$request = new HTTP_Request2();
$request->setUrl($TOKEN_ENDPOINT);
$request->setMethod(HTTP_Request2::METHOD_POST);
$request->setConfig(array(
  'follow_redirects' => TRUE
));
$request->setHeader(array(
  'Content-Type' => 'application/x-www-form-urlencoded'
));
$request->addPostParameter(array(
  'username' => $USERNAME,
  'password' => $PASSWORD,
  'client_id' => $CLIENT_ID,
  'client_secret' => $CLIENT_SECRET,
  'grant_type' => 'password'
));
try {
  $response = $request->send();
  if ($response->getStatus() == 200) {
    echo $response->getBody();
  }
  else {
    echo 'Unexpected HTTP status: ' . $response->getStatus() . ' ' .
    $response->getReasonPhrase();
  }
}
catch(HTTP_Request2_Exception $e) {
  echo 'Error: ' . $e->getMessage();
}

```

### Ödeme Başlatma

```php

require_once 'HTTP/Request2.php';

$HOST = "ibb01.api.ibb-test.2ndfloor.aws-dev.kobil.com";
$TRANSACTION_ENDPOINT = "https://" + $HOST + "/digitanium/v2/mpower/payment/transaction";
$TOKEN = getToken();

$CLIENT_ID = "a6b2423e-f621-4ddf-84f1-e102acbe5c1b";
$version = 1;
$idempotencyId = "d60842d2-4f1f-4231-8a9d-86fa49985824";
$userId = "testuser01@ibb01.com";
$merchantCallback = "https://domain.com";
$transactionTimeout = 59;
$merchantId = "dfa69ef4-17e6-4652-a9d7-964b13494565";
$amount = 100;
$currency = "EUR";
$paymentContent = '[
    [
      {
        "key": "First Value",
        "value": "12.03 TRY"
      },
	  {
        "key": "Second Value",
        "value": "8.00 TRY"
      },
	  {
        "key": "Third Value",
        "value": "2.00 TRY"
      }
    ],
	[
      {
        "key": "Fourth Value",
        "value": "Any Value"
      }
    ],
	[
      {
        "key": "Sixt Value",
        "value": "Any Value"
      },
	   {
        "key": "Seventh Value",
        "value": "Any Value"
      }
    ]
  ]
';

$request = new HTTP_Request2();
$request->setUrl($TRANSACTION_ENDPOINT);
$request->setMethod(HTTP_Request2::METHOD_POST);
$request->setConfig(array(
  'follow_redirects' => TRUE
));
$request->setHeader(array(
  'Accept' => 'application/json',
  'Authorization' => 'Bearer ' + $TOKEN,
  'Content-Type' => 'application/json'
));
$request->setBody('{
      "version": ' + $version + ',
      "idempotencyId": ' + $idempotencyId + ',
      "userId": ' + $userId + ',
      "merchantServiceUUID": ' + $CLIENT_ID + ',
      "merchantCallback": ' +$merchantCallback  + ',
      "transactionTimeout": ' + $transactionTimeout + ',
      "merchantId": ' + $merchantId + ',
      "amount": ' + $amount + ',
      "currency": ' + $currency + ',
      "paymentContent": ' + $paymentContent + '
}');
try {
  $response = $request->send();
  if ($response->getStatus() == 200) {
    echo $response->getBody();
  }
  else {
    echo 'Unexpected HTTP status: ' . $response->getStatus() . ' ' .
    $response->getReasonPhrase();
  }
}
catch(HTTP_Request2_Exception $e) {
  echo 'Error: ' . $e->getMessage();
}

```

### Ödeme Durumu Sorgulama

```php

require_once 'HTTP/Request2.php';

$HOST = "ibb01.api.ibb-test.2ndfloor.aws-dev.kobil.com";
$STATUS_ENDPOINT = "https://" + $HOST + "/digitanium/v2/mpower/payment/transaction/status";
$TOKEN = getToken();

$CLIENT_ID = "a6b2423e-f621-4ddf-84f1-e102acbe5c1b";
$merchantId = "dfa69ef4-17e6-4652-a9d7-964b13494565";
$transactionId = "451c79d7-7787-4279-b16e-f71479416810";

$request = new HTTP_Request2();
$request->setUrl($STATUS_ENDPOINT);
$request->setMethod(HTTP_Request2::METHOD_POST);
$request->setConfig(array(
  'follow_redirects' => TRUE
));
$request->setHeader(array(
  'Accept' => 'application/json',
  'Authorization' => 'Bearer ' + $TOKEN,
  'Content-Type' => 'application/json'
));
$request->setBody('{
      "merchantServiceUUID": ' + $CLIENT_ID + ',
      "merchantId": ' + $merchantId + ',
      "transactionId": ' + $transactionId + '"
}');
try {
  $response = $request->send();
  if ($response->getStatus() == 200) {
    echo $response->getBody();
  }
  else {
    echo 'Unexpected HTTP status: ' . $response->getStatus() . ' ' .
    $response->getReasonPhrase();
  }
}
catch(HTTP_Request2_Exception $e) {
  echo 'Error: ' . $e->getMessage();
}

```

### Ödemeyi İptal Etme

```php

require_once 'HTTP/Request2.php';

$HOST = "ibb01.api.ibb-test.2ndfloor.aws-dev.kobil.com";
$CANCEL_ENDPOINT = "https://" + $HOST + "/digitanium/v2/mpower/payment/transaction/cancel";
$TOKEN = getToken();

$CLIENT_ID = "a6b2423e-f621-4ddf-84f1-e102acbe5c1b";
$merchantId = "dfa69ef4-17e6-4652-a9d7-964b13494565";
$transactionId = "451c79d7-7787-4279-b16e-f71479416810";

$request = new HTTP_Request2();
$request->setUrl($CANCEL_ENDPOINT);
$request->setMethod(HTTP_Request2::METHOD_POST);
$request->setConfig(array(
  'follow_redirects' => TRUE
));
$request->setHeader(array(
  'Accept' => 'application/json',
  'Authorization' => 'Bearer ' + $TOKEN,
  'Content-Type' => 'application/json'
));
$request->setBody('{
      "merchantServiceUUID": ' + $CLIENT_ID + ',
      "merchantId": ' + $merchantId + ',
      "transactionId": ' + $transactionId + '"
}');
try {
  $response = $request->send();
  if ($response->getStatus() == 200) {
    echo $response->getBody();
  }
  else {
    echo 'Unexpected HTTP status: ' . $response->getStatus() . ' ' .
    $response->getReasonPhrase();
  }
}
catch(HTTP_Request2_Exception $e) {
  echo 'Error: ' . $e->getMessage();
}

```


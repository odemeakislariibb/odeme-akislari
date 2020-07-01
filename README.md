# Ödeme API

<TOC />

## Genel Bilgiler
<br/>

### Parametreler 
<br/>

Ödeme API'yi kullanırken ihtiyacımız olacak temel parametreler aşağıdaki gibidir.

| Parametre        | Değer           | Açıklama  |
| ------------- |:-------------|:-----|
| HOST      | ibb01.api.ibb-test.2ndfloor.aws-dev.kobil.com(örnek) | Tenantınıza ait API'ye erişebileceğiniz BASE adres.  |
| CLIENT_ID     | a6b2423e-f621-4ddf-84f1-e102acbe5c1b(örnek)      |  Oluşturduğunuz Transaction Uygulamanızın Client ID değeri |
| CLIENT_SECRET | 4e7108f7-192d-4389-95d1-7cf216e1d21b(örnek)      |    Oluşturduğunuz Transaction Uygulamanızın Client Secret değeri |
| USERNAME | test-merchant(örnek)      | Yetkili kullanıcı hesabı   |
| PASSWORD | 123456(örnek)      |Yetkili kullanıcı hesabının şifresi |

### Token Alma
<br/>

* Ödeme API'sini kullanmak için token almamız gerekmektedir. Aşağıda örnek token alma işlemini görebilirsiniz.  

* **Endpoint**: `https://${HOST}/digitanium/v1/login`
* **Method**: POST
* **URL parametreleri**:
  * **HOST**: Host API'si 

**Request Headers**

| Header        | Değer     | 
| ------------- |:-------------:|
| Content-Type: | application/x-www-form-urlencoded | 

**Body Parametreleri**

| Parametre        | Değer     | Açıklama     |
| ------------- |:-------------:| :-------------:|
| username | test-merchant(Örnek) | Kullanıcı adı | 
| password | 123456(Örnek) | Kullanıcı şifresi | 
| client_id | a6b2423e-f621-4ddf-84f1-e102acbe5c1b(Örnek) | Merchant servisid'si | 
| client_secret | 4e7108f7-192d-4389-95d1-7cf216e1d21b(Örnek) | Merchant servis secret'i |
| grant_type | password | Sabit |  


```json
{
  "username": "test-merchant",
  "password": "123456",
  "client_id": "a6b2423e-f621-4ddf-84f1-e102acbe5c1b",
  "client_secret": "4e7108f7-192d-4389-95d1-7cf216e1d21b",
  "grant_type": "password"
}
```

## Fonksiyonlar
<br/>


### Ödeme Başlatma 
<br/>

* Bu endpoint kullanıcıya ödeme isteği göndermeye yarar. 

* **Endpoint**: `https://${HOST}/digitanium/v2/mpower/payment/transaction/`
* **Method**: POST
* **URL parametreleri**:
  * **HOST**: Host API'si 

**Request Headers**

| Header        | Değer     | 
| ------------- |:-------------:|
| Authorization | Bearer ${TOKEN} |   
| Content-Type: | application/json | 

**Body Parametreleri**

| Parametre        | Değer     | Açıklama     |
| ------------- |:-------------:| :-------------:|
| version | 1 | Sabit |
| idempotencyId | d60842d2-4f1f-4231-8a9d-86fa49985824(Örnek) | Unique bir UUID |  
| userId | 12345678901(Örnek) | Hangi user'a ödeme gönderileceği |  
| merchantServiceUUID | 37b12626-a064-44e5-bbcf-31a9e42a0774(Örnek) | Ödeme servisinin UUID'si | 
| merchantCallback | https://domain.com(Örnek) | Ödeme sonucunun geleceği adres |  
| transactionTimeout | 59(Örnek) | Ödeme zaman aşım süresi (dakika) |  
| merchantId | 7eadbeae-85c1-43d8-aece-abc9f281dde(Örnek) | Satıcı uygulama id'si | 
| amount | 100(Örnek) | Ödemenin ne kadar tuttuğu |  
| currency | EUR(Örnek) | Ödeme para birimi|  
| paymentContent | paymentContent Array (Örnek) | Ödeme sepetinin içeriği|  

**paymentContent**
* Bu obje sepetteki objeleri liste olarak tutar. 

**Sepet Objesi**
| Parametre        | Değer     | Açıklama     |
| ------------- |:-------------:| :-------------:|
| key | Ayakkabı(Örnek) | Alınan şeyin ismi | 
| value | 12.03 TRY(Örnek) | Alınan şeyin değeri  | 

**Örnek Gönderilecek İstek**
```json

{
  "version": 1,
  "idempotencyId": "d60842d2-4f1f-4231-8a9d-86fa49985824",
  "userId": "12345678901",
  "merchantServiceUUID": "37b12626-a064-44e5-bbcf-31a9e42a0774",
  "merchantCallback": "https://domain.com",
  "transactionTimeout": 59,
  "merchantId": "dfa69ef4-17e6-4652-a9d7-964b13494565",
  "amount": 100,
  "currency": "EUR",
  "paymentContent": [
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
  ]]
}
```
**Örnek Başarılı Cevap**
```json
{  
  "transactionId" : "3c7ebce6-e3bc-4e45-ae9b-a0eebcb1e370",
  "transactionStatus" : "new", 
  "transactionMessage" : "Transaction created"
}
```
<br/>

### Ödeme Durumu Sorgulama
<br/>
* Bu endpoint kullanıcıya gönderilen ödeme isteğinin durumunu sorgulamaya yarar. 

* **Endpoint**: `https://${HOST}/digitanium/v2/mpower/payment/transaction/status/`
* **Method**: POST
* **URL parametreleri**:
  * **HOST**: Host API'si 

**Request Headers**

| Header        | Değer     | 
| ------------- |:-------------:|
| Authorization | Bearer ${TOKEN} |   
| Content-Type: | application/json | 

**Body Parametreleri**

| Parametre        | Değer     | Açıklama     |
| ------------- |:-------------:| :-------------:|
| merchantServiceUUID | a6b2423e-f621-4ddf-84f1-e102acbe5c1b(Örnek) | Ödeme servisinin UUID'si | 
| transactionID | 3c7ebce6-e3bc-4e45-ae9b-a0eebcb1e370(Örnek) | Ödeme id'si|  
| merchantId | dfa69ef4-17e6-4652-a9d7-964b13494565(Örnek) | Ödeme uygulama id'si | 

**Örnek Gönderilecek İstek**
```json

{
  "merchantId": "dfa69ef4-17e6-4652-a9d7-964b13494565",
  "transactionId" : " 3c7ebce6-e3bc-4e45-ae9b-a0eebcb1e370",
  "merchantServiceUUID" : "a6b2423e-f621-4ddf-84f1-e102acbe5c1b",
}
```
**Örnek Başarılı Cevap**
```json
{  
  "transactionId" : "3c7ebce6-e3bc-4e45-ae9b-a0eebcb1e370",
  "transactionStatus" : "inquiring status",
  "transactionMessage" : "Inquire status in progress"
}
```
### Ödemeyi İptal Etme
<br/>
* Bu endpoint kullanıcıya atılan ödeme isteğini iptal etmeye yarar. 

* **Endpoint**: `https://${HOST}/digitanium/v2/mpower/payment/transaction/cancel/`
* **Method**: POST
* **URL parametreleri**:
  * **HOST**: Host API'si 

**Request Headers**

| Header        | Değer     | 
| ------------- |:-------------:|
| Authorization | Bearer ${TOKEN} |   
| Content-Type: | application/json | 

**Body Parametreleri**

| Parametre        | Değer     | Açıklama     |
| ------------- |:-------------:| :-------------:|
| merchantServiceUUID | a6b2423e-f621-4ddf-84f1-e102acbe5c1b(Örnek) | Ödeme servisinin UUID'si | 
| transactionID | 3c7ebce6-e3bc-4e45-ae9b-a0eebcb1e370(Örnek) | Ödeme id'si|  
| merchantId | dfa69ef4-17e6-4652-a9d7-964b13494565(Örnek) | Ödeme uygulama id'si | 

**Örnek Gönderilecek İstek**
```json

{
  "merchantId": "dfa69ef4-17e6-4652-a9d7-964b13494565",
  "transactionId" : " 3c7ebce6-e3bc-4e45-ae9b-a0eebcb1e370",
  "merchantServiceUUID" : "a6b2423e-f621-4ddf-84f1-e102acbe5c1b",
}
```
**Örnek Başarılı Cevap**
```json
{ 
  "transactionId" : "3c7ebce6-e3bc-4e45-ae9b-a0eebcb1e370",
  "transactionStatus" : "cancellation",
  "transactionMessage" : "Cancel request in progress"}
```
### Соберем приложение перед использованием rout-ов
1) Перейти в папку yalla-crm и запустить команду
	```shell
	dotnet build
	```
2) Перейдите в файле appsettings.Development.json и измените это поле на свой.
- примечание порт по умолчанию это 5432
	```shell
	"ConnectionStrings": {
 		"DefaultConnection": "Server=localhost;Port={port};User Id={userName};Password={password};Database={name database}"
 	},
	```
3) Перейти в папку Yalla.DataAccess и запустить команду
	```shell
	dotnet ef --startup-project ../Yalla.Presentation database update
	```
4) Перейти в папку Yalla.Presentation и запустить команду
	```shell
	dotnet run
	```
# 1) Консультация
#### Rout: 
```json
http://localhost:{port}/api/orders/consulting
```

#### Json для запроса.
```json
{
    "orderId": "", 
    "isContinue": false,
    "createdAt": "2024-01-13T17:42:54.703", // date format YYYY-MM-DD
    "orderNumber": 1,
    "operator": "", // max: 30 символ
    "clientPhoneNumber": "111222333", // min:9, max: 13 symbols
    "clientFullName": "", // Максимальная длина: 50 символов
    "socialUsername": "another",
    "contactType": 1, // если socialUsername не пуста то > 0
    "orderType": 1, // min: 1, max: 2  (isContinue = true)
    "comesFrom": 1, // min: 1, max: 12 (isContinue = true)
    "language": 1, // min: 1, max: 3 (isContinue = true)
    "gender": 1, // min: 1 max: 2 (isContinue = true)
    "typeOfClient": 1, // min: 1, max: 3 (isContinue = true)
    "pharmacyOrders": 
    [
        {
            "id": "", // может быть пустым
            "productsHistory": // (isContinue = true)
            [
                {
                    "id": "", // может быть пустым
                    "count": 2, // > 0
                    "product": {
                        "id": "",
                        "name": "", // min: 1, max: 50 символов
                        "dosage": "", // min: 1, max: 5000 символов
                        "countOnPackage": 1, // min: 1
			"ageType": 1, // min: 1, max: 2
                        "country": "", // min: 2, max: 100 символов
                        "releaseForm": 1, // min: 1, max: 33
                        "typeOfPackaging": 1 // min: 1, max: 5
                    }
                }
            ],
            "pharmacy": {
                "id": "" // может быть пустым
            }
        }
    ],
    "comment": "" // max: 5000 символов
}
```

# 2) В Поиске
#### Rout:
```json
http://localhost:{port}/api/orders/insearch/{orderID}
```
#### Json для запроса.
- Если Id у сущности присутствует и он есть в БД то он обновляется, в противном случае добавляется.
```json
{
    "orderId": "", // не может быть пустым
    "isContinue": false,
    "pharmacyOrders": 
    [
        {
            "id": "", // может быть пустым
            "productsHistory": // (isContinue = true)
            [
                {
		    "id": "", // может быть пустым
                    "count": 2, // > 0
                    "amountWithMarkup": 5, // > 0 
                    "amountWithoutMarkup": 3, // > 0
		    "arrivalDate":"2024-01-13T17:42:54.703", // может быть пустым если Pharmacy.IsAbroad = false
                    "comment": "another fot this product", // max: 5000
                    "product": {
			"id": "", // может быть пустым
                        "name": "", // min: 1, max: 50 символов 
                        "dosage": "", // min: 1, max: 5000 символов
                        "countOnPackage": 1, // min: 1
			"ageType": 1, // min: 1, max: 2
                        "country": "", // min: 2, max: 100 символов
                        "priceWithMarkup": 11, // > 0
                        "priceWithoutMarkup": 12, // > 0,
                        "linkProduct": "http://googleanother.com",
                        "releaseForm": 1, // min: 1, max: 33
                        "packagingUnit": 1, // min: 1, max: 6
                        "typeOfPackaging": 1 // min: 1, max: 5
                    } 
                }
            ],
            "pharmacy": {
		"id": "", // может быть пустым
                "name": "", // min: 2, max: 100 символов
                "isAbroad": true,
                "country": "asdf", // max: 5000 если isAbroad = true то не может быть пустым
		"gelolocationLink": "", max: 5000 если isAbroad = true то не может быть пустым
                "address": "", // min: 2, max: 255 символов
                "contact": "", // min: 2, max: 500 символов
                "payoutMethod": 1
            }
        }
    ],
  "comment": "" // max: 5000
}
```

# 3) В ожидании клиента
#### Rout: 
```json
http://localhost:5282/api/orders/waiting-client/{orderId}
```
#### Json для запроса.

```json
{
  "orderId": "{orderId}",
  "timeInformCustomerAboutProduct": "2024-01-13T17:19:54", // Оператор должен ввести дату и время
  "isContinue": false,
  "longSearchReason": "", // max: 5000
  "callingAt": "", // max: 5000
  "comment": "L;ASKJFAJSDFD" // max: 5000
}

```

# 4) Оформить
#### Rout: 
```json
http://localhost:{port}/api/orders/placement/{orderId}
```
#### Json для запроса.
```json
{
  "orderId": "{orderId}",
  "isContinue": true,
  "timeToObtainClientApproval": "2024-01-13T17:42:54.703", //Оператор должен ввести время (isContinue = true)
  "totalOrderAmountExcludingDelivery": 1, // > 0 (isContinue = true)
  "phoneNumber": "111222333", // min:9, max: 13 symbols
  "socialUsername": "another", // max: 150 symbols
  "address": "", // max: 500 символов 
  "landmark": "", // max: 500 символов
  "geolocationOfClientAddress": "", // max: 500
  "paymentMethod": 2, // min: 1, max: 16 (isContinue = true)
  "prepayment": 2, // >= 0
  "remainingPayment": 0, // >= 0
  "paymentStatus": 1, // min: 1, max: 4
  "callingAt": "", // not empty (isContinue = true) and max: 5000
  "individualDeliveryTime": "2024-01-13T17:42:54.703",
  "deliveryType": 3, //
  "priceForDeliveryOutsideTheCity": 0,
  "cityOrDistrict": "", // max: 50 символов
  "discount": 1, // min: 1, max: 3 (isContinue = true)
  "courier": "", // max: 50 символов
  "commentForCourier": "", // max: 5000
  "comment": "", // max: 5000
  "smsMailing": false
}
```

# 5) Доставлено
#### Rout: 
```json
http://localhost:{port}/api/orders/delivered/{orderId}
```
#### Json для запроса.
```json
{
  "deliveredTime": "2023-12-23T11:00:54.703", // оператов должен ввести дату и время
  "reasonForOrderDelay": "", // max: 5000 символов
  "comment": "" // max: 5000 символов
}
```

# 6) Отмена
#### Rout: 
```json
http://localhost:{port}/api/orders/cancellation/{orderId}
```
#### Json для запроса.
```json
{
  "reasonForOrderCancellation": "", // max: 5000
  "comment": "", // max: 5000
  "timeOfCompletionOfInquiry": "2023-12-23T15:13:01.184" // оператор должен ввести дату и время.
}
```
# 6) Отказ
#### Rout: 
```json
http://localhost:5282/api/orders/rejection/{orderId}
```
#### Json для запроса.
```json
{
  "reasonForOrderRejection": "", // max: 5000
  "comment": "", // max: 5000
  "timeOfCompletionOfInquiry": "2023-12-23T15:13:01.184Z" // оператор должен ввести дату и время
}
```
# 7) Возврат
#### Rout: 
```json
http://localhost{port}/api/orders/return-products/{orderId}
```
#### Json для запроса.
```json
{
  "orderId": "{orderId}",
  "reasonForReturningTheOrder": "", // max: 5000
  "productHistoryMinimalDtos": [
    {
      "id": "{productHistoryId}",
      "returnReason": "", // max: 5000
      "returnedCount": 0, // не может быть больше заказанного количества
      "isReturned": true
    }
  ],
  "comment": "" // max: 5000
}


```

# 8) Роуты для получения Order - ов.
#### Для получения Заказа по статусу получение в формате простого Order.
```http
http://localhost:5282/api/orders/{OrderState}
```
#### Для получения Заказа по статусу. Получение в формате своего статуса.
```http
http://localhost:5282/api/orders/{OrderId}/{OrderState}
```

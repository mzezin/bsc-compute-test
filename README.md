# Descriptions

Для запуска сервиса нужно перейти в директорию проекта и вылнить запрос в консоли/терминале:
```
java -jar service.jar 
```

Сервис запустится и вернет последней строчкой:

```
Started ComputeTestApplication in 1.88 seconds (JVM running for 2.142)
```

Что бы остановить сервис нужно в консоли нажать Ctrl+C

Сервис хранит портфели, в портфелях хранятся сделки, которые могут быть двух типов:
1) CURRENCY
2) SECURITY

Сервис принимает сделки (/deal) и обратные сделки (/deal/inverted), где тип поменяется: SELL -> BUY и BUY -> SELL
Формат хранение портфеля:
```json
{
    "account": "1213",
    "positions": [
        {
            "name": "RUB",
            "type": "currency",
            "valueTO": 0,
            "valueTN": 10
        },
        {
            "name": "XDDR",
            "type": "security",
            "valueTO": 0,
            "valueTN": 0
        }
    ]
}
```

# Задача
Протестировать HTTP запросы. Запросы изменяют внутреннее состояние сервиса.
# Endpoints

Запросить все портфели
```
GET http://localhost:8055/portfolio
```
Запросить портфель по определенному ID
```
GET http://localhost:8055/portfolio/{id}
```
Посчитать сделки
```
POST http://localhost:8055/deal
```
Посчитать сделки инвертированно, где тип сделки поменяется: SELL -> BUY и BUY -> SELL
```
POST http://localhost:8055/deal/inverted
```

# Test cases

## 1) Запрос покупки обычной сделки
```
POST http://localhost:8055/deal
```       
### Тело запроса:
```json
{
	"account": "1213",
	"type": "buy",
	"securitySum": 10,
	"currencySum": 10
}
```
### Результат (порядок позиций не важен)
```
GET http://localhost:8055/portfolio/1213
```
```json
{
    "account": "1213",
    "positions": [
        {
            "name": "RUB",
            "type": "currency",
            "valueTO": 0,
            "valueTN": 0
        },
        {
            "name": "XDDR",
            "type": "security",
            "valueTO": 0,
            "valueTN": 10
        }
    ]
}
```

## 2) Запрос покупки инвертированной сделки, которая станет продажей
```
POST http://localhost:8055/deal/inverted
```       
### Тело запроса:
```json
{
	"account": "1212",
	"type": "buy",
	"securitySum": 10,
	"currencySum": 10
}
```
### Результат (порядок позиций не важен)
```
GET http://localhost:8055/portfolio/1212
```
```json
{
    "account": "1212",
    "positions": [
        {
            "name": "XDDR",
            "type": "security",
            "valueTO": 0,
            "valueTN": 0
        },
        {
            "name": "RUB",
            "type": "currency",
            "valueTO": 0,
            "valueTN": 10
        }
    ]
}
```
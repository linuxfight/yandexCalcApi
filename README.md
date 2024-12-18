# Calculator API

API для решения математических выражений.

## Запуск
### Docker
Можно запустить приложение через [Docker](https://www.docker.com/).

1. Docker run
```shell
# образ с GitHub
docker pull ghcr.io/linuxfight/yandexcalcapi:main
docker run -p 8080:8080 -d ghcr.io/linuxfight/yandexcalcapi:main

# сборка локально
docker build -t test .
docker run -p 8080:8080 -d test
``` 
2. Docker compose
```shell
docker compose -f compose.yml up --build -d
``` 

### Обычная сборка
```shell
# unix
go mod download -x
go build -o ./server ./cmd/main.go

./server

# windows
go mod download -x
go build -o ./server.exe ./cmd/main.go

./server.exe
```

## Использование
Для использования вам нужен http клиент (к примеру [Postman](https://www.postman.com/downloads/) или [Insomnia](https://insomnia.rest/download)).
Также можно использовать любой http клиент для вашего языка программирования.

Можно использовать уже запущенную версию - https://solve.linuxfight.me/api/v1/calculate.

Отправьте запрос на ```http://localhost:8080/api/v1/calculate```

Пример на typescript
```typescript
const sendPostRequest = async () => {
  const url = 'http://localhost:8080/api/v1/calculate';
  const data = {
    expression: '2+2*2',
  };

  try {
    const response = await fetch(url, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(data),
    });

    if (!response.ok) {
      throw new Error('Network response was not ok');
    }

    const responseData = await response.json();
    console.log('Response:', responseData);
  } catch (error) {
    console.error('Error:', error);
  }
};

sendPostRequest();
```

Такой же пример с помощью curl
```shell
curl --location 'http://localhost:8080/api/v1/calculate' \
--header 'Content-Type: application/json' \
--data '{
  "expression": "2+2*2"
}'
```

## Структура проекта
- ```.github/workflows``` - CI для проверки кода на работоспособность с помощью тестов и сборка Docker образа

- ```cmd``` - пакет main, код для запуска

- ```internal``` - код веб сервера
  - ```logger``` - логирование с помощью [Zap](https://github.com/uber-go/zap)
  - ```handlers``` - функции для обработки http запросов с помощью [Fiber](https://github.com/gofiber/fiber)

- ```pkg/calc``` - код для обработки выражений

## Ответы от API
1. 200 - успешное выполнение
```json
{
    "result": "результат выражения"
}
```

2. 422 - входные данные не соответствуют требованиям приложения
```json
{
    "error": "invalid expression, etc..."
}
``` 

3. 500 - ошибка веб сервера
```json
{
    "error": "Internal server error"
}
```
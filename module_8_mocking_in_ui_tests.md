=== НАЧАЛО ФАЙЛА: module\_8\_mocking\_in\_ui\_tests.md (МАКСИМАЛЬНО ПОДРОБНО) ===
# **МОДУЛЬ 7.5: МОКИРОВАНИЕ В UI-ТЕСТИРОВАНИИ**
## **Как тестировать UI без падающих API и внешних сервисов**
Продолжительность: 3 дня\
Цель: Полностью разобраться в каждом методе мокирования

-----
## **7.5.1 Зачем мокировать в UI-тестах?**
## **Реальная проблема №1: Падающий API**
text

Понедельник 9:00 AM

Твой тест: test\_checkout.py

Запускаешь: pytest test\_checkout.py

\


Тест делает:

1\. Открывает страницу checkout

2\. Заполняет форму

3\. Нажимает "Pay"

4\. Отправляет POST /api/payment на реальный сервер

5\. Реальный сервер... не отвечает (падает)

6\. Тест зависает 30 сек

7\. FAIL: TimeoutException

\


Вывод: Когда платежный сервис падает → все UI-тесты падают

Это не баг в коде, это падение API

Результат: 100% тестов красные, хотя ничего не изменилось

\


С моками:

text

Тест делает:

1\. Открывает страницу checkout

2\. Заполняет форму

3\. Нажимает "Pay"

4\. Отправляет POST на MOCK-сервер (на твоей машине)

5\. Mock-сервер ВСЕГДА отвечает успешно

6\. Тест проходит за 2 сек

7\. PASS

\


Результат: Тесты стабильны, независимо от реальных API

\

## **Реальная проблема №2: Медленность**
text

Без моков:

test\_add\_item\_to\_cart:      2 сек (UI)

test\_checkout\_form:         3 сек (UI)

test\_payment\_processing:   15 сек (реальная платежка обрабатывает)

test\_order\_confirmation:    4 сек (UI)

test\_email\_notification:    8 сек (ждем email)

\=====================================

ВСЕГО:                      32 сек

\


С моками:

test\_add\_item\_to\_cart:      2 сек

test\_checkout\_form:         3 сек

test\_payment\_processing:    0.5 сек (мок отвечает мгновенно)

test\_order\_confirmation:    4 сек

test\_email\_notification:    0.1 сек (мок отвечает мгновенно)

\=====================================

ВСЕГО:                       9.6 сек

\


Результат: Тесты в 3.3x быстрее!

\

## **Реальная проблема №3: Edge cases (граничные случаи)**
text

Сценарий: Протестировать что происходит когда платежка вернула ошибку

\


БЕЗ моков:

\- Нужно произвольно отклонить платеж на реальном сервере

\- Платежный сервис не позволяет это делать в тестовой среде

\- Даже если позволяет - непредсказуемо

\- Невозможно потестировать за 2 сек

\


С моками:

\- Просто скажешь mock-серверу: "верни 400 + ошибку INVALID\_CARD"

\- Мок вернет ровно это

\- Можешь потестировать 100 edge cases в 2 сек

\
\-----
## **7.5.2 ПОДХОД 1: WireMock (Standalone Mock Server)**
## **Что это на человеческом языке?**
Представь:

text

Твое приложение (React на :3000) отправляет запросы на сервер

`  `↓ (отправляет HTTP запрос)

Реальный сервер https://api.production.com:443 (иногда падает, иногда медленный)

\


С WireMock:

Твое приложение (React на :3000) отправляет запросы на сервер

`  `↓ (отправляет HTTP запрос)

WireMock сервер на :8080 (всегда отвечает за 10 мс, кол-во контролируешь ты)

\


WireMock = виртуальный сервер, который слушает на порту и отвечает как ты скажешь.
## **Шаг 1: Установка WireMock**
Вариант A: Docker (РЕКОМЕНДУЕТСЯ)

bash

*# Скачиваешь образ*

docker pull rodolpheche/wiremock:3.0.1

\


*# Запускаешь контейнер*

docker run -p 8080:8080 rodolpheche/wiremock:3.0.1

\


*# Вывод:*

*# ...*

*# WireMock server started successfully on port 8080*

\


Что происходит:

text

docker run            = запускаешь контейнер

-p 8080:8080        = пробрасываешь порт (127.0.0.1:8080 → контейнер:8080)

rodolpheche/wiremock = образ с WireMock'ом

\


Проверка:

bash

curl http://localhost:8080/\_\_admin/

\


*# Вывод:*

*# {*

*#   "version": "3.0.1",*

*#   ...*

*# }*

\


Вариант B: Без Docker (если Docker недоступен)

bash

*# Идешь на https://repo1.maven.org/maven2/com/github/tomakehurst/wiremock-jre8-standalone/*

\


*# Скачиваешь последний JAR, например:*

*# wiremock-jre8-standalone-3.0.1.jar*

\


*# Запускаешь:*

java -jar wiremock-jre8-standalone-3.0.1.jar --port 8080

\


*# Вывод:*

*# ...*

*# WireMock server started successfully on port 8080*

\

## **Шаг 2: Понимание как WireMock работает**
WireMock внутри:

text

[1] Слушает на localhost:8080

[2] Когда приходит запрос → ищет в базе конфигов

[3] Если нашел совпадение → отвечает как настроено

[4] Если не нашел → ошибка 404

\


Пример:

text

Конфиги в WireMock:

\- Rule 1: POST /api/payment → 200 {success}

\- Rule 2: GET /api/users/1 → 200 {name: John}

\- Rule 3: POST /api/payment (с card=invalid) → 400 {error}

\


Запросы:

POST /api/payment                    → Подходит Rule 1 → 200 {success}

GET /api/users/1                     → Подходит Rule 2 → 200 {name: John}

POST /api/payment (card=invalid)     → Подходит Rule 3 → 400 {error}

GET /api/unknown                     → Не подходит → 404

\

## **Шаг 3: Добавление конфигов в WireMock**
Способ 1: HTTP запрос (программно)

python

*# Сохрани как mocks\_setup.py*

\


import requests

import json

\


WIREMOCK\_ADMIN\_URL = "http://localhost:8080/\_\_admin"

\


def add\_mock\_rule(name, method, url, status, response\_body):

`    `"""

`    `Добавляет правило в WireMock



`    `Args:

`        `name: название правила (для логов)

`        `method: GET, POST, PUT, DELETE

`        `url: /api/endpoint

`        `status: 200, 400, 500 и т.д.

`        `response\_body: словарь (будет конвертирован в JSON)

`    `"""



`    `*# Конструируем правило*

`    `mapping = {

`        `"request": {

`            `"method": method,      *# ← HTTP метод*

`            `"url": url             *# ← URL endpoint*

`        `},

`        `"response": {

`            `"status": status,      *# ← Статус код*

`            `"jsonBody": response\_body,  *# ← JSON в ответе*

`            `"headers": {

`                `"Content-Type": "application/json"

`            `}

`        `}

`    `}



`    `*# Отправляем на WireMock*

`    `endpoint = f"{WIREMOCK\_ADMIN\_URL}/mappings"

`    `response = requests.post(endpoint, json=mapping)



`    `print(f"✓ Добавлено правило '{name}'")

`    `print(f"  {method} {url} → {status}")



`    `return response.status\_code == 201

\
\


*# Практический пример: Платежный API*

\


def setup\_payment\_mocks():

`    `"""Настраиваем все сценарии платежей"""



`    `*# Сценарий 1: Успешный платеж*

`    `add\_mock\_rule(

`        `name="Успешный платеж",

`        `method="POST",

`        `url="/api/payment",

`        `status=200,

`        `response\_body={

`            `"status": "success",

`            `"transaction\_id": "TXN123456",

`            `"amount": 99.99,

`            `"currency": "USD",

`            `"timestamp": "2024-01-12T10:30:00Z"

`        `}

`    `)



`    `*# Сценарий 2: Неверная карта*

`    `add\_mock\_rule(

`        `name="Карта отклонена",

`        `method="POST",

`        `url="/api/payment?card=invalid",

`        `status=400,

`        `response\_body={

`            `"error": "INVALID\_CARD",

`            `"message": "Карта отклонена банком",

`            `"code": "CARD\_DECLINED"

`        `}

`    `)



`    `*# Сценарий 3: Недостаточно средств*

`    `add\_mock\_rule(

`        `name="Недостаточно средств",

`        `method="POST",

`        `url="/api/payment?card=low-balance",

`        `status=402,

`        `response\_body={

`            `"error": "INSUFFICIENT\_FUNDS",

`            `"message": "На карте недостаточно средств",

`            `"required": 99.99,

`            `"available": 50.00

`        `}

`    `)



`    `*# Сценарий 4: Ошибка сервера (500)*

`    `add\_mock\_rule(

`        `name="Сервер в стресс",

`        `method="POST",

`        `url="/api/payment?chaos=true",

`        `status=500,

`        `response\_body={

`            `"error": "INTERNAL\_SERVER\_ERROR",

`            `"message": "Сервер платежей недоступен. Повторите позже"

`        `}

`    `)



`    `*# Сценарий 5: Получить историю платежей*

`    `add\_mock\_rule(

`        `name="История платежей",

`        `method="GET",

`        `url="/api/payments",

`        `status=200,

`        `response\_body={

`            `"payments": [

`                `{

`                    `"id": "TXN001",

`                    `"amount": 50.00,

`                    `"status": "completed",

`                    `"date": "2024-01-11"

`                `},

`                `{

`                    `"id": "TXN002",

`                    `"amount": 99.99,

`                    `"status": "completed",

`                    `"date": "2024-01-12"

`                `}

`            `]

`        `}

`    `)

\
\


*# Используем:*

if \_\_name\_\_ == "\_\_main\_\_":

`    `setup\_payment\_mocks()

`    `print("\n✓ Все правила добавлены в WireMock!")

\


Способ 2: JSON конфигурация (файлы)

json

*// \_\_files\_\_/payment-success.json*

{

`  `"status": "success",

`  `"transaction\_id": "TXN123456",

`  `"amount": 99.99

}

\


json

*// mappings/payment-success-mapping.json*

{

`  `"request": {

`    `"method": "POST",

`    `"url": "/api/payment"

`  `},

`  `"response": {

`    `"status": 200,

`    `"bodyFileName": "payment-success.json",

`    `"headers": {

`      `"Content-Type": "application/json"

`    `}

`  `}

}

\


bash

*# Запускаешь WireMock с папкой конфигов*

docker run -p 8080:8080 \

`  `-v $(pwd)/mappings:/home/wiremock/mappings \

`  `-v $(pwd)/\_\_files\_\_:/home/wiremock/\_\_files\_\_ \

`  `rodolpheche/wiremock:3.0.1

\

## **Шаг 4: Docker Compose для полной интеграции**
text

\# docker-compose.yml

\


version: '3.8'

\


services:

`  `# WireMock: мокированный API

`  `wiremock:

`    `image: rodolpheche/wiremock:3.0.1

`    `container\_name: wiremock\_server

`    `ports:

`      `- "8080:8080"  # ← localhost:8080

`    `volumes:

`      `# Папка с конфигами правил

`      `- ./mocks/mappings:/home/wiremock/mappings

`      `# Папка с файлами ответов

`      `- ./mocks/\_\_files\_\_:/home/wiremock/\_\_files\_\_

`    `environment:

`      `- WIREMOCK\_DISABLE\_GZIP=true

\


`  `# Твое приложение (например, React или Node.js)

`  `app:

`    `image: my-app:latest

`    `container\_name: app\_server

`    `ports:

`      `- "3000:3000"  # ← localhost:3000

`    `depends\_on:

`      `- wiremock  # ← Ждет пока WireMock запустится

`    `environment:

`      `# Очень важно! Приложение должно использовать мок-API

`      `REACT\_APP\_API\_URL: http://wiremock:8080

`      `REACT\_APP\_ENV: test

`    `networks:

`      `- test\_network

\


`  `# Selenium/Chrome для тестирования

`  `selenium:

`    `image: selenium/standalone-chrome:latest

`    `container\_name: selenium\_chrome

`    `ports:

`      `- "4444:4444"  # ← localhost:4444

`    `depends\_on:

`      `- app

`    `networks:

`      `- test\_network

\


networks:

`  `test\_network:

`    `driver: bridge

\


bash

*# Запускаешь всё одной командой*

docker-compose up

\


*# Вывод:*

*# Creating wiremock\_server ... done*

*# Creating app\_server ... done*

*# Creating selenium\_chrome ... done*

\

## **Шаг 5: Тест с WireMock**
python

*# test\_checkout\_with\_wiremock.py*

\


import pytest

from selenium import webdriver

from selenium.webdriver.common.by import By

from selenium.webdriver.support.ui import WebDriverWait

from selenium.webdriver.support import expected\_conditions as EC

import requests

import json

\


*# URL WireMock администратора*

WIREMOCK\_ADMIN = "http://localhost:8080/\_\_admin"

\


@pytest.fixture

def setup\_mocks():

`    `"""

`    `Фикстура: настраиваем мокированные API перед тестом

`    `"""



`    `*# Успешный платеж*

`    `requests.post(

`        `f"{WIREMOCK\_ADMIN}/mappings",

`        `json={

`            `"request": {"method": "POST", "url": "/api/payment"},

`            `"response": {

`                `"status": 200,

`                `"jsonBody": {"status": "success", "order\_id": "ORD123"}

`            `}

`        `}

`    `)



`    `*# Получить товары*

`    `requests.post(

`        `f"{WIREMOCK\_ADMIN}/mappings",

`        `json={

`            `"request": {"method": "GET", "url": "/api/products"},

`            `"response": {

`                `"status": 200,

`                `"jsonBody": {

`                    `"products": [

`                        `{"id": 1, "name": "Laptop", "price": 999.99},

`                        `{"id": 2, "name": "Mouse", "price": 29.99}

`                    `]

`                `}

`            `}

`        `}

`    `)



`    `yield



`    `*# Чистка после теста*

`    `requests.delete(f"{WIREMOCK\_ADMIN}/mappings")

\
\


def test\_complete\_checkout\_flow(setup\_mocks):

`    `"""

`    `Полный тест checkout'а с мокированным API



`    `Что происходит:

`    `1. Открываем приложение (React на :3000)

`    `2. Приложение запрашивает товары у WireMock'а

`    `3. WireMock возвращает мок-данные

`    `4. Добавляем товар в корзину

`    `5. Идем в checkout

`    `6. Нажимаем "Pay"

`    `7. Приложение отправляет запрос на платеж к WireMock'у

`    `8. WireMock возвращает успешный ответ

`    `9. Проверяем что UI показывает "Order successful"

`    `"""



`    `*# ARRANGE: инициализируем браузер*

`    `driver = webdriver.Chrome()

`    `wait = WebDriverWait(driver, 10)



`    `try:

`        `*# ACT: открываем приложение*

`        `driver.get("http://localhost:3000")



`        `*# Ждем загрузки товаров (мок-данные от WireMock)*

`        `wait.until(EC.presence\_of\_element\_located((By.CLASS\_NAME, "product")))



`        `*# Добавляем первый товар в корзину*

`        `add\_button = driver.find\_element(By.ID, "add-laptop")

`        `add\_button.click()



`        `*# Идем в checkout*

`        `checkout\_button = wait.until(

`            `EC.element\_to\_be\_clickable((By.ID, "checkout"))

`        `)

`        `checkout\_button.click()



`        `*# Заполняем форму*

`        `driver.find\_element(By.ID, "full-name").send\_keys("John Doe")

`        `driver.find\_element(By.ID, "email").send\_keys("john@example.com")

`        `driver.find\_element(By.ID, "card-number").send\_keys("4111111111111111")



`        `*# Нажимаем Pay (отправляет POST к WireMock)*

`        `pay\_button = driver.find\_element(By.ID, "pay-button")

`        `pay\_button.click()



`        `*# ASSERT: проверяем что платеж прошел успешно*

`        `*# (WireMock вернул 200 + {status: success})*

`        `success\_message = wait.until(

`            `EC.presence\_of\_element\_located((By.CLASS\_NAME, "success-message"))

`        `)



`        `assert "Order successful" in success\_message.text

`        `assert "ORD123" in driver.page\_source  *# Order ID из мока*



`    `finally:

`        `driver.quit()

\
\


def test\_payment\_error\_scenario(setup\_mocks):

`    `"""

`    `Тест ошибки платежа: подменяем мок на ошибку

`    `"""



`    `*# Перенастраиваем мок: вместо успеха возвращаем ошибку*

`    `requests.post(

`        `f"{WIREMOCK\_ADMIN}/mappings",

`        `json={

`            `"request": {"method": "POST", "url": "/api/payment"},

`            `"response": {

`                `"status": 400,

`                `"jsonBody": {"error": "INVALID\_CARD", "message": "Card declined"}

`            `}

`        `}

`    `)



`    `driver = webdriver.Chrome()

`    `wait = WebDriverWait(driver, 10)



`    `try:

`        `driver.get("http://localhost:3000/checkout")



`        `*# Заполняем форму с невалидной картой*

`        `driver.find\_element(By.ID, "card-number").send\_keys("4111111111111111")

`        `driver.find\_element(By.ID, "pay-button").click()



`        `*# ASSERT: проверяем ошибку*

`        `error\_msg = wait.until(

`            `EC.presence\_of\_element\_located((By.CLASS\_NAME, "error-message"))

`        `)

`        `assert "Card declined" in error\_msg.text



`    `finally:

`        `driver.quit()

\
\-----
## **7.5.3 ПОДХОД 2: Selenium 4 Перехват запросов**
## **Что это?**
Chrome DevTools Protocol (CDP) позволяет перехватывать сетевые запросы внутри браузера ДО того как они уйдут на сервер.

text

Нормальный поток:

[Браузер] → (запрос идет на интернет) → [Сервер]

\


С перехватом:

[Браузер] → (мы перехватываем ЗДЕСЬ) → [Вместо сервера подменяем ответ]

\

## **Шаг 1: Активация перехвата**
python

from selenium import webdriver

from selenium.webdriver.chrome.options import Options

\


def test\_with\_selenium\_interception():

`    `*# Включаем CDP логирование*

`    `options = Options()

`    `options.set\_capability("goog:loggingPrefs", {

`        `"performance": "ALL"  *# ← Логируем все сетевые события*

`    `})



`    `driver = webdriver.Chrome(options=options)



`    `*# Активируем перехват на уровне CDP*

`    `driver.execute\_cdp\_cmd("Network.enable", {})



`    `*# Настраиваем какие запросы перехватывать*

`    `driver.execute\_cdp\_cmd("Network.setRequestInterception", {

`        `"patterns": [

`            `{

`                `"urlPattern": "\*\*/api/\*"  *# ← Перехватываем все /api/ запросы*

`            `}

`        `]

`    `})

\

## **Шаг 2: Перехват и подмена ответа**
python

import json

from selenium import webdriver

from selenium.webdriver.chrome.options import Options

\


def test\_intercept\_payment\_api():

`    `options = Options()

`    `options.set\_capability("goog:loggingPrefs", {"performance": "ALL"})



`    `driver = webdriver.Chrome(options=options)

`    `driver.execute\_cdp\_cmd("Network.enable", {})



`    `*# Включаем перехват*

`    `driver.execute\_cdp\_cmd("Network.setRequestInterception", {

`        `"patterns": [{"urlPattern": "\*\*/api/payment"}]

`    `})



`    `*# Настраиваем обработчик перехваченных запросов*

`    `def intercept\_request\_handler(request):

`        `"""

`        `Эта функция вызывается когда браузер хочет отправить запрос

`        `Мы можем его изменить, заменить или заблокировать

`        `"""



`        `if request["request"]["url"].endswith("/api/payment"):

`            `*# Заменяем ответ вместо отправки реального запроса*

`            `driver.execute\_cdp\_cmd("Network.continueInterceptedRequest", {

`                `"interceptionId": request["interceptionId"],

`                `"rawResponse": to\_base64\_string(

`                    `"HTTP/1.1 200 OK\r\nContent-Type: application/json\r\n\r\n" +

`                    `json.dumps({"status": "success", "order\_id": "ORD123"})

`                `)

`            `})

`        `else:

`            `*# Для остальных запросов отправляем как обычно*

`            `driver.execute\_cdp\_cmd("Network.continueInterceptedRequest", {

`                `"interceptionId": request["interceptionId"]

`            `})



`    `*# К сожалению, Selenium 4 не имеет встроенного способа настроить callback*

`    `*# Это требует более низкоуровневый доступ к Chrome DevTools*

\

## **Шаг 3: Более простой способ через Playwright**
Selenium 4 очень сложный для перехвата. Лучше использовать Playwright:

python

import asyncio

from playwright.async\_api import async\_playwright

\


async def test\_intercept\_with\_playwright():

`    `"""

`    `Playwright имеет более простой API для перехвата

`    `"""



`    `async with async\_playwright() as p:

`        `browser = await p.chromium.launch()

`        `page = await browser.new\_page()



`        `*# ARRANGE: настраиваем перехват для платежей*

`        `async def handle\_route(route):

`            `request = route.request



`            `if "api/payment" in request.url:

`                `*# Перехватываем платеж и возвращаем мок-ответ*

`                `await route.fulfill(

`                    `status=200,

`                    `content\_type="application/json",

`                    `body='{"status": "success", "order\_id": "ORD123"}'

`                `)

`            `elif "api/products" in request.url:

`                `*# Перехватываем запрос товаров*

`                `await route.fulfill(

`                    `status=200,

`                    `content\_type="application/json",

`                    `body='''{

`                        `"products": [

`                            `{"id": 1, "name": "Laptop", "price": 999.99},

`                            `{"id": 2, "name": "Mouse", "price": 29.99}

`                        `]

`                    `}'''

`                `)

`            `else:

`                `*# Остальные запросы пускаем дальше*

`                `await route.continue\_()



`        `*# Активируем перехват*

`        `await page.route("\*\*/\*", handle\_route)



`        `*# ACT: открываем страницу*

`        `await page.goto("http://localhost:3000/checkout")



`        `*# Нажимаем Pay (отправляет запрос к API)*

`        `await page.click("id=pay-button")



`        `*# ASSERT: проверяем успешный платеж*

`        `success\_msg = await page.text\_content(".success-message")

`        `assert "successful" in success\_msg



`        `await browser.close()

\
\


*# Запуск асинхронного теста*

asyncio.run(test\_intercept\_with\_playwright())

\

## **Шаг 4: Сложные сценарии с Playwright**
python

async def test\_payment\_error\_with\_delay():

`    `"""Платеж с ошибкой и задержкой"""



`    `async with async\_playwright() as p:

`        `browser = await p.chromium.launch()

`        `page = await browser.new\_page()



`        `async def handle\_payment\_route(route):

`            `request = route.request



`            `*# Получаем параметры из запроса*

`            `post\_data = request.post\_data\_json if request.post\_data else {}



`            `*# В зависимости от карты возвращаем разные ошибки*

`            `if post\_data.get("card") == "invalid":

`                `await asyncio.sleep(0.5)  *# Имитируем задержку*

`                `await route.fulfill(

`                    `status=400,

`                    `body='{"error": "INVALID\_CARD"}'

`                `)

`            `elif post\_data.get("card") == "timeout":

`                `await asyncio.sleep(5)  *# Имитируем таймаут*

`                `await route.abort(error\_code="timedout")

`            `else:

`                `await route.fulfill(

`                    `status=200,

`                    `body='{"status": "success"}'

`                `)



`        `await page.route("\*\*/api/payment", handle\_payment\_route)

`        `await page.goto("http://localhost:3000/checkout")



`        `*# Тест с неверной картой*

`        `await page.fill("id=card", "invalid")

`        `await page.click("id=pay")



`        `error = await page.text\_content(".error-message")

`        `assert "INVALID\_CARD" in error

\
\-----
## **7.5.4 ПОДХОД 3: Python requests + responses**
## **Что это?**
responses — библиотека для мокирования HTTP запросов в Python. Работает на уровне Python, не браузера.

python

*# Без responses:*

response = requests.get("https://api.example.com/users")

*# Идет РЕАЛЬНЫЙ запрос в интернет → реальный ответ*

\


*# С responses:*

@responses.activate

def test():

`    `responses.add(responses.GET, "https://api.example.com/users",

`                  `json=[{"id": 1}])

`    `response = requests.get("https://api.example.com/users")

`    `*# Не идет в интернет! Возвращает мок-ответ*

\

## **Шаг 1: Установка**
bash

pip install responses

\

## **Шаг 2: Базовое использование**
python

import responses

import requests

\


@responses.activate  *# ← Декоратор включает мокирование*

def test\_get\_user():

`    `*# ARRANGE: добавляем правило в responses*

`    `responses.add(

`        `responses.GET,  *# ← Метод (GET, POST, PUT, DELETE)*

`        `"https://api.example.com/users/1",  *# ← URL*

`        `json={"id": 1, "name": "John", "email": "john@example.com"},  *# ← JSON ответ*

`        `status=200  *# ← HTTP статус*

`    `)



`    `*# ACT: делаем запрос*

`    `response = requests.get("https://api.example.com/users/1")



`    `*# ASSERT: проверяем что пришло*

`    `assert response.status\_code == 200

`    `assert response.json()["name"] == "John"

\

## **Шаг 3: Множественные правила**
python

@responses.activate

def test\_checkout\_flow():

`    `*# Правило 1: Получить товары*

`    `responses.add(

`        `responses.GET,

`        `"https://api.example.com/products",

`        `json={

`            `"products": [

`                `{"id": 1, "name": "Laptop", "price": 999.99},

`                `{"id": 2, "name": "Mouse", "price": 29.99}

`            `]

`        `},

`        `status=200

`    `)



`    `*# Правило 2: Получить цену доставки*

`    `responses.add(

`        `responses.POST,

`        `"https://api.example.com/shipping",

`        `json={"cost": 10.00, "days": 3},

`        `status=200

`    `)



`    `*# Правило 3: Обработать платеж*

`    `responses.add(

`        `responses.POST,

`        `"https://api.example.com/payment",

`        `json={"status": "success", "order\_id": "ORD123"},

`        `status=200

`    `)



`    `*# Теперь все эти запросы вернут мок-ответы*



`    `products = requests.get("https://api.example.com/products").json()

`    `assert len(products["products"]) == 2



`    `shipping = requests.post("https://api.example.com/shipping").json()

`    `assert shipping["cost"] == 10.00



`    `payment = requests.post("https://api.example.com/payment").json()

`    `assert payment["status"] == "success"

\

## **Шаг 4: Разные сценарии для одного URL**
python

@responses.activate

def test\_payment\_success():

`    `responses.add(

`        `responses.POST,

`        `"https://api.example.com/payment",

`        `json={"status": "success"},

`        `status=200

`    `)

`    `resp = requests.post("https://api.example.com/payment")

`    `assert resp.json()["status"] == "success"

\


@responses.activate

def test\_payment\_error\_invalid\_card():

`    `responses.add(

`        `responses.POST,

`        `"https://api.example.com/payment",

`        `json={"error": "INVALID\_CARD"},

`        `status=400

`    `)

`    `resp = requests.post("https://api.example.com/payment")

`    `assert resp.status\_code == 400

\


@responses.activate

def test\_payment\_server\_error():

`    `responses.add(

`        `responses.POST,

`        `"https://api.example.com/payment",

`        `json={"error": "Internal server error"},

`        `status=500

`    `)

`    `resp = requests.post("https://api.example.com/payment")

`    `assert resp.status\_code == 500

\

## **Шаг 5: Проверка параметров запроса**
python

@responses.activate

def test\_payment\_with\_validation():

`    `responses.add(

`        `responses.POST,

`        `"https://api.example.com/payment",

`        `json={"status": "success"},

`        `status=200

`    `)



`    `*# ACT: отправляем платеж с параметрами*

`    `requests.post(

`        `"https://api.example.com/payment",

`        `json={

`            `"amount": 99.99,

`            `"currency": "USD",

`            `"card": "4111111111111111"

`        `}

`    `)



`    `*# ASSERT: проверяем что запрос был сделан*

`    `assert len(responses.calls) == 1  *# ← Один запрос сделан*



`    `*# Проверяем содержимое запроса*

`    `request = responses.calls[0].request

`    `assert request.method == "POST"

`    `assert request.url == "https://api.example.com/payment"



`    `*# Проверяем что отправили правильные параметры*

`    `import json

`    `body = json.loads(request.body)

`    `assert body["amount"] == 99.99

`    `assert body["card"] == "4111111111111111"

\

## **Шаг 6: Callback функции (динамические ответы)**
python

@responses.activate

def test\_payment\_dynamic\_response():

`    `def payment\_callback(request):

`        `"""

`        `Функция которая вызывается при запросе

`        `Мы можем вернуть разный ответ в зависимости от параметров

`        `"""



`        `import json

`        `body = json.loads(request.body)



`        `*# Если карта заканчивается на 1111 → успех*

`        `if body["card"].endswith("1111"):

`            `return (200, {}, json.dumps({"status": "success"}))

`        `*# Если заканчивается на 2222 → ошибка*

`        `elif body["card"].endswith("2222"):

`            `return (400, {}, json.dumps({"error": "INVALID\_CARD"}))

`        `*# Остальные → неизвестная ошибка*

`        `else:

`            `return (500, {}, json.dumps({"error": "UNKNOWN\_ERROR"}))



`    `*# Регистрируем callback вместо статического ответа*

`    `responses.add\_callback(

`        `responses.POST,

`        `"https://api.example.com/payment",

`        `callback=payment\_callback

`    `)



`    `*# Тест 1: валидная карта*

`    `resp1 = requests.post("https://api.example.com/payment",

`                         `json={"card": "4111111111111111"})

`    `assert resp1.json()["status"] == "success"



`    `*# Тест 2: невалидная карта*

`    `resp2 = requests.post("https://api.example.com/payment",

`                         `json={"card": "4111111111112222"})

`    `assert resp2.json()["error"] == "INVALID\_CARD"

\
\-----
## **7.5.5 ПОДХОД 4: Mock Service Worker (MSW)**
## **Что это?**
MSW — перехватчик запросов на уровне браузера. Работает с JavaScript приложениями (React, Vue, Angular).

text

Без MSW:

[React приложение] → (fetch/axios) → [Реальный API сервер]

\


С MSW:

[React приложение] → (fetch/axios) → [Service Worker перехватывает] → [MSW возвращает мок]

\

## **Шаг 1: Установка**
bash

npm install msw

\


*# или yarn*

yarn add msw

\

## **Шаг 2: Создание handlers (правила)**
javascript

*// src/mocks/handlers.js*

\


import { rest } from 'msw'

\


*// Это правила: "когда приходит ЭТОТ запрос → верни ЭТО"*

export const handlers = [

`  `*// Правило 1: Получить товары*

`  `rest.get('https://api.example.com/products', (req, res, ctx) => {

`    `return res(

`      `ctx.status(200),  *// ← Статус*

`      `ctx.json({  *// ← JSON ответ*

`        `products: [

`          `{ id: 1, name: 'Laptop', price: 999.99 },

`          `{ id: 2, name: 'Mouse', price: 29.99 }

`        `]

`      `})

`    `)

`  `}),



`  `*// Правило 2: Обработать платеж (успех)*

`  `rest.post('https://api.example.com/payment', (req, res, ctx) => {

`    `return res(

`      `ctx.status(200),

`      `ctx.json({

`        `status: 'success',

`        `order\_id: 'ORD123',

`        `transaction\_id: 'TXN456'

`      `})

`    `)

`  `}),



`  `*// Правило 3: Ошибка доставки*

`  `rest.post('https://api.example.com/shipping', (req, res, ctx) => {

`    `return res(

`      `ctx.status(400),

`      `ctx.json({

`        `error: 'INVALID\_ADDRESS',

`        `message: 'Адрес не найден в базе'

`      `})

`    `)

`  `})

]

\

## **Шаг 3: Инициализация сервера**
javascript

*// src/mocks/server.js*

\


import { setupServer } from 'msw/node'

import { handlers } from './handlers'

\


*// Создаем MSW сервер с нашими handlers*

export const server = setupServer(...handlers)

\

## **Шаг 4: Включение в тесты (Jest)**
javascript

*// src/setupTests.js*

\


import { server } from './mocks/server'

\


*// Запускаем MSW перед всеми тестами*

beforeAll(() => server.listen())

\


*// Очищаем перехватчики после каждого теста*

afterEach(() => server.resetHandlers())

\


*// Останавливаем сервер после всех тестов*

afterAll(() => server.close())

\

## **Шаг 5: Написание тестов**
javascript

*// src/\_\_tests\_\_/Checkout.test.js*

\


import { render, screen, fireEvent, waitFor } from '@testing-library/react'

import Checkout from '../pages/Checkout'

\


test('user can complete checkout successfully', async () => {

`  `*// ARRANGE: отрендериваем компонент*

`  `render(<Checkout />)



`  `*// ACT: пользователь нажимает pay*

`  `const payButton = screen.getByRole('button', { name: /pay/i })

`  `fireEvent.click(payButton)



`  `*// ASSERT: проверяем что платеж успешен*

`  `*// (MSW вернул success ответ)*

`  `await waitFor(() => {

`    `expect(screen.getByText(/Order successful/i)).toBeInTheDocument()

`    `expect(screen.getByText(/ORD123/i)).toBeInTheDocument()

`  `})

})

\


test('user sees error on invalid address', async () => {

`  `render(<Checkout />)



`  `*// Вводим неверный адрес*

`  `const addressInput = screen.getByLabelText(/address/i)

`  `fireEvent.change(addressInput, { target: { value: 'invalid' } })



`  `*// Нажимаем continue*

`  `const continueBtn = screen.getByRole('button', { name: /continue/i })

`  `fireEvent.click(continueBtn)



`  `*// ASSERT: видим ошибку (MSW вернул 400)*

`  `await waitFor(() => {

`    `expect(screen.getByText(/Address not found/i)).toBeInTheDocument()

`  `})

})

\

## **Шаг 6: Переопределение handlers в отдельных тестах**
javascript

*// Для конкретного теста изменяем ответ*

\


test('payment fails with server error', async () => {

`  `*// Переопределяем обработчик платежа*

`  `server.use(

`    `rest.post('https://api.example.com/payment', (req, res, ctx) => {

`      `return res(

`        `ctx.status(500),

`        `ctx.json({ error: 'Internal server error' })

`      `)

`    `})

`  `)



`  `render(<Checkout />)

`  `const payButton = screen.getByRole('button', { name: /pay/i })

`  `fireEvent.click(payButton)



`  `*// Проверяем что видим ошибку сервера*

`  `await waitFor(() => {

`    `expect(screen.getByText(/server error/i)).toBeInTheDocument()

`  `})



`  `*// После теста handlers автоматически восстановятся*

})

\
\-----
## **7.5.6 СРАВНЕНИЕ ПОДХОДОВ (С ПРИМЕРАМИ)**

|**Подход**|**Когда использовать**|**Пример**|**Сложность**|
| :- | :- | :- | :- |
|WireMock|Полная интеграция (UI + Backend)|Docker Compose с полным стеком|⭐⭐⭐|
|Selenium Intercept|Старые проекты на Selenium 3|Перехват внутри браузера|⭐⭐⭐|
|Playwright|Новые проекты, лучший DX|Async/await, просто и понятно|⭐⭐|
|responses|Unit-тесты Python кода|Мокирование requests в Python|⭐|
|MSW|React/Vue/Angular приложения|setupTests + handlers|⭐⭐|

-----
## **7.5.7 АНТИПАТТЕРН: Over-mocking**
## **ПЛОХО: Мокируешь всё подряд**
python

@responses.activate

def test\_checkout():

`    `*# Мокируем 20+ endpoints*

`    `responses.add(...)  *# Products*

`    `responses.add(...)  *# User*

`    `responses.add(...)  *# Cart*

`    `responses.add(...)  *# Shipping*

`    `responses.add(...)  *# Payment*

`    `responses.add(...)  *# Email*

`    `responses.add(...)  *# Analytics*

`    `*# ... еще 13 эндпоинтов*



`    `*# Результат: мок-код больше чем реальный код*

`    `*# Невозможно поддерживать*

`    `*# Не ясно что тестируется*

\

## **ХОРОШО: Мокируешь только самое нестабильное**
python

@responses.activate

def test\_checkout():

`    `*# Мокируем только:*

`    `*# 1. Платежный API (внешний, нестабильный)*

`    `*# 2. Email service (медленный)*



`    `*# Остальное (товары, доставка) - реальная БД*



`    `responses.add(

`        `responses.POST,

`        `"https://payment-gateway.external.com/pay",

`        `json={"status": "success"}

`    `)



`    `responses.add(

`        `responses.POST,

`        `"https://email-service.external.com/send",

`        `json={"queued": True}

`    `)



`    `*# Результат: Читабельный, быстрый, поддерживаемый тест*

\
\-----
## **🧪 ПРАКТИЧЕСКИЕ ЗАДАНИЯ**
## **7.5.1 Выбор подхода (45 мин)**
Для каждого проекта выбери подходящий подход мокирования:

|**Проект**|**Выбранный подход**|**Почему**|
| :- | :- | :- |
|React + Node.js Backend (на одной машине)|||
|Старая Selenium 3 система|||
|Только Unit-тесты Python API|||
|Angular SPA с внешним API|||
|Microservices архитектура|||
## **7.5.2 WireMock детальный разбор (60 мин)**
Для сценария Checkout:

1. Нарисуй диаграмму: где WireMock, где приложение, где браузер
1. Перечисли все endpoints которые нужно смокировать
1. Напиши JSON конфиг для каждого (успех, ошибка, таймаут)
## **7.5.3 responses библиотека (30 мин)**
Напиши в таблице для каждого сценария:

|**Сценарий**|**HTTP метод**|**URL**|**Status**|**JSON body**|
| :- | :- | :- | :- | :- |
|Получить товары|GET||||
|Создать заказ|||||
|Ошибка платежа|||400||
-----
## **✅ Чек-лист "Что я усвоил"**
- ` `WireMock: как запустить, как настроить, как использовать в тесте
- ` `Selenium Intercept: когда использовать, почему это сложно
- ` `Playwright: почему проще чем Selenium
- ` `responses: мокирование Python requests
- ` `MSW: мокирование для браузерных приложений
- ` `Антипаттерн over-mocking и как его избежать

Теперь ты эксперт по мокированию!

=== КОНЕЦ ФАЙЛА: module\_8\_mocking\_in\_ui\_tests.md ===
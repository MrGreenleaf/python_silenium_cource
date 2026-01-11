=== НАЧАЛО ФАЙЛА: appendix\_materials.md ===
# **📚 APPENDIX MATERIALS - СПРАВОЧНЫЕ МАТЕРИАЛЫ И ПОДГОТОВКА**
## **Практическая информация, примеры заданий и чек-листы**
Версия: 2.0\
Статус: ✅ Полный набор справочных материалов

-----
## **📋 ЧАСТЬ 1: ЧЕК-ЛИСТЫ ПО УРОВНЯМ**
## **🟢 JUNIOR QA AUTOMATION ENGINEER (40+ пунктов)**
Что должен знать и уметь:
## **Python базы**
- ` `Типы данных (int, str, list, dict, tuple, set)
- ` `Условные операторы (if/elif/else)
- ` `Циклы (for, while, break, continue)
- ` `Функции (def, параметры, return)
- ` `Списки (list comprehension, методы)
- ` `Работа со строками (format, split, join)
- ` `Базовые исключения (try-except)
- ` `Классы (init, self, методы)
## **Веб-основы**
- ` `HTML структура и теги
- ` `CSS селекторы (class, id, type, descendant)
- ` `XPath селекторы (базовые)
- ` `DOM (Document Object Model)
- ` `Браузер DevTools (F12)
## **Selenium базы**
- ` `Установить и настроить Selenium
- ` `Открыть браузер (webdriver.Chrome())
- ` `Закрыть браузер (quit())
- ` `Навигация (get, refresh, back, forward)
- ` `Поиск элемента (find\_element)
- ` `Клик (click)
- ` `Ввод текста (send\_keys)
- ` `Очистка поля (clear)
- ` `Получение текста (text)
- ` `Получение атрибута (get\_attribute)
- ` `Implicit wait (implicitly\_wait)
- ` `Работа с alert (accept, dismiss)
## **Pytest базы**
- ` `Писать функцию теста (test\_\*)
- ` `Использовать assert
- ` `Запустить тесты (pytest)
- ` `Запустить конкретный тест
- ` `Фикстуры (fixture, yield)
- ` `conftest.py
## **Page Object Model**
- ` `Понимать что такое Page Object
- ` `Создать BasePage с методами
- ` `Создать Page Object для страницы
- ` `Написать тест используя Page Object
## **Практика**
- ` `Написал 10+ простых тестов
- ` `Создал 1-2 Page Objects
- ` `Используюсь фикстуры в тестах
- ` `Тесты работают и проходят
- ` `Код понимает мой коллега

Примерный уровень опыта: 1-3 месяца работы\
Средняя зарплата (2024): $40-50k/год (в разных странах варьируется)

-----
## **🟡 MIDDLE QA AUTOMATION ENGINEER (50+ пунктов)**
Все из Junior + дополнительно:
## **Advanced Python**
- ` `Generators и yield
- ` `Decorators
- ` `Context managers (with statement)
- ` `\*args и \*\*kwargs
- ` `Lambda функции
- ` `Map, filter, reduce
- ` `Наследование (inheritance)
- ` `Полиморфизм
- ` `Магические методы (str, repr)
## **Advanced Selenium**
- ` `Explicit wait (WebDriverWait)
- ` `Expected Conditions (EC)
- ` `Работа с iframe
- ` `Drag and Drop
- ` `Загрузка файлов
- ` `Выполнение JavaScript (execute\_script)
- ` `Работа с cookies
- ` `Переключение между окнами
- ` `Обработка ошибок (try-except)
- ` `Selenium 4 новшества (Relative Locators)
## **Advanced Pytest**
- ` `Параметризация (@pytest.mark.parametrize)
- ` `Маркеры (@pytest.mark.smoke, @pytest.mark.skip)
- ` `Fixtures с параметрами
- ` `Фикстуры с разными scope (function, class, module, session)
- ` `Генерация отчетов (--html, --junit-xml)
- ` `Логирование
## **Архитектура**
- ` `Advanced Page Object Model
- ` `BasePage с общими методами
- ` `Иерархия Page Objects
- ` `Переиспользование методов
- ` `Кэширование элементов
- ` `Локальная конфигурация (settings.json)
## **Инфраструктура**
- ` `ConfigManager для параметров
- ` `DriverFactory для браузеров
- ` `Скриншоты при ошибках (pytest hooks)
- ` `Логирование (logging module)
- ` `conftest.py структура
- ` `requirements.txt
## **Тестирование**
- ` `Написал 50+ тестов
- ` `Используюсь параметризацию
- ` `Создал 5+ Page Objects
- ` `Тесты покрывают основные сценарии
- ` `Коллега может поддерживать мой код
- ` `Выполняю 10-20 тестов в день
- ` `Тесты проходят стабильно (не flaky)
## **Документация**
- ` `Пишу комментарии к сложному коду
- ` `Документирую Page Objects
- ` `Пишу README для проекта

Примерный уровень опыта: 1-2 года работы\
Средняя зарплата (2024): $60-80k/год

-----
## **🔴 SENIOR QA AUTOMATION ENGINEER (60+ пунктов)**
Все из Middle + дополнительно:
## **Software Design Patterns**
- ` `Page Object Model (advanced)
- ` `Factory Pattern
- ` `Singleton Pattern
- ` `Builder Pattern
- ` `Observer Pattern
## **Advanced Architecture**
- ` `Многоуровневая архитектура (UI, API, Database)
- ` `Dependency Injection
- ` `Mockito и mocking
- ` `TDD (Test-Driven Development)
- ` `BDD (Behavior-Driven Development)
## **Инструменты и фреймворки**
- ` `Allure отчеты (advanced)
- ` `GitHub Actions (CI/CD)
- ` `Jenkins pipeline
- ` `Docker и контейнеризация
- ` `Параллельное выполнение (pytest-xdist)
- ` `BrowserStack или Sauce Labs
## **Performance и Optimization**
- ` `Профилирование тестов
- ` `Оптимизация скорости
- ` `Масштабирование (100+ тестов)
- ` `Параллельное выполнение на 10+ потоках
## **Mobile Testing**
- ` `Эмуляция мобильных устройств
- ` `Реальные мобильные устройства
- ` `Appium (для native приложений)
- ` `PWA тестирование
## **Advanced Debugging**
- ` `Chrome DevTools Protocol
- ` `BrowserMob Proxy (сетевые запросы)
- ` `Логирование (разные уровни)
- ` `Анализ браузер логов
- ` `Чтение Stack Trace
- ` `Профилирование
## **Управление и менторство**
- ` `Ревью кода других
- ` `Менторство Junior/Middle специалистов
- ` `Архитектура проекта
- ` `Процессы и best practices
- ` `Автоматизация CI/CD
- ` `Улучшение процессов
## **Написано тестов**
- ` `200+ стабильных тестов
- ` `Код на 80%+ покрытием
- ` `Полностью автоматизированные регрессионные тесты
- ` `Тесты выполняются параллельно
- ` `Интегрированы в CI/CD
- ` `Есть документация
## **Лидерство**
- ` `Вел проект с 2-3 разработчиками
- ` `Реализовал стратегию тестирования
- ` `Улучшил процессы на 30%+
- ` `Обучил 2-3 разработчиков

Примерный уровень опыта: 3-5+ лет работы\
Средняя зарплата (2024): $90-130k+/год

-----
## **💼 ЧАСТЬ 2: ПРИМЕРЫ ЗАДАНИЙ СО СОБЕСЕДОВАНИЙ**
## **Задание 1: BASIC SELENIUM TEST (Easy)**
Уровень: Junior\
Время: 30 минут\
Требование: Напиши тест на Selenium
## **Задача:**
text

Тестовый сайт: https://www.saucedemo.com

\


Тест должен:

1\. Открыть сайт

2\. Заполнить username: "standard\_user"

3\. Заполнить password: "secret\_sauce"

4\. Кликнуть кнопку Login

5\. Проверить, что появилась страница товаров

6\. Проверить, что видны товары

\

## **Решение:**
python

from selenium import webdriver

from selenium.webdriver.common.by import By

from selenium.webdriver.support.ui import WebDriverWait

from selenium.webdriver.support import expected\_conditions as EC

\


def test\_login():

`    `*# 1. Открыть сайт*

`    `driver = webdriver.Chrome()

`    `driver.get("https://www.saucedemo.com")



`    `*# 2-3. Заполнить данные*

`    `username = driver.find\_element(By.ID, "user-name")

`    `password = driver.find\_element(By.ID, "password")



`    `username.send\_keys("standard\_user")

`    `password.send\_keys("secret\_sauce")



`    `*# 4. Кликнуть Login*

`    `login\_button = driver.find\_element(By.ID, "login-button")

`    `login\_button.click()



`    `*# 5. Проверить URL*

`    `wait = WebDriverWait(driver, 10)

`    `wait.until(EC.url\_contains("inventory"))



`    `*# 6. Проверить товары*

`    `products = driver.find\_elements(By.CLASS\_NAME, "inventory\_item")

`    `assert len(products) > 0, "Товары не найдены"



`    `driver.quit()

`    `print("✅ Тест прошел успешно")

\


if \_\_name\_\_ == "\_\_main\_\_":

`    `test\_login()

\

## **Что оценивают:**
- ✅ Базовое использование Selenium
- ✅ Правильное ожидание (WebDriverWait)
- ✅ Правильные селекторы
- ✅ Логичный код
-----
## **Задание 2: PAGE OBJECT MODEL (Medium)**
Уровень: Middle\
Время: 1-1.5 часа\
Требование: Напиши тесты используя Page Object Model
## **Задача:**
text

Сайт: https://www.saucedemo.com

\


Создай:

1\. BasePage класс с общими методами

2\. LoginPage класс

3\. ProductsPage класс

4\. Напиши 2-3 теста (login, add to cart)

\


Требования:

\- Использовать Page Object Model

\- Явные ожидания (WebDriverWait)

\- conftest.py с фикстурой браузера

\- Pytest для запуска

\

## **Решение:**
python

*# pages/base\_page.py*

from selenium.webdriver.support.ui import WebDriverWait

from selenium.webdriver.support import expected\_conditions as EC

\


class BasePage:

`    `def \_\_init\_\_(self, driver, timeout=10):

`        `self.driver = driver

`        `self.wait = WebDriverWait(driver, timeout)



`    `def click(self, locator):

`        `element = self.wait.until(EC.element\_to\_be\_clickable(locator))

`        `element.click()



`    `def input\_text(self, locator, text):

`        `element = self.find\_element(locator)

`        `element.clear()

`        `element.send\_keys(text)



`    `def find\_element(self, locator):

`        `return self.driver.find\_element(\*locator)

\


*# pages/login\_page.py*

from selenium.webdriver.common.by import By

from pages.base\_page import BasePage

\


class LoginPage(BasePage):

`    `USERNAME = (By.ID, "user-name")

`    `PASSWORD = (By.ID, "password")

`    `LOGIN\_BUTTON = (By.ID, "login-button")



`    `def login(self, username, password):

`        `self.input\_text(self.USERNAME, username)

`        `self.input\_text(self.PASSWORD, password)

`        `self.click(self.LOGIN\_BUTTON)

\


*# pages/products\_page.py*

from selenium.webdriver.common.by import By

from pages.base\_page import BasePage

\


class ProductsPage(BasePage):

`    `PRODUCTS = (By.CLASS\_NAME, "inventory\_item")



`    `def get\_products\_count(self):

`        `products = self.find\_elements(self.PRODUCTS)

`        `return len(products)

\


*# tests/conftest.py*

import pytest

from selenium import webdriver

\


@pytest.fixture

def driver():

`    `browser = webdriver.Chrome()

`    `browser.get("https://www.saucedemo.com")

`    `yield browser

`    `browser.quit()

\


*# tests/test\_login.py*

from pages.login\_page import LoginPage

from pages.products\_page import ProductsPage

\


def test\_login(driver):

`    `login\_page = LoginPage(driver)

`    `login\_page.login("standard\_user", "secret\_sauce")



`    `products\_page = ProductsPage(driver)

`    `assert products\_page.get\_products\_count() > 0

\


def test\_add\_to\_cart(driver):

`    `login\_page = LoginPage(driver)

`    `login\_page.login("standard\_user", "secret\_sauce")



`    `products\_page = ProductsPage(driver)

`    `*# Добавить товар...*

`    `assert products\_page.get\_products\_count() > 0

\

## **Что оценивают:**
- ✅ Правильная архитектура (Page Object)
- ✅ BasePage с переиспользуемыми методами
- ✅ conftest.py с фикстурой
- ✅ Правильное использование Pytest
- ✅ Явные ожидания
- ✅ Читаемый код
-----
## **Задание 3: CI/CD PIPELINE (Hard)**
Уровень: Senior\
Время: 2-3 часа\
Требование: Настрой GitHub Actions для запуска тестов
## **Задача:**
text

Требования:

1\. Создать GitHub Actions workflow

2\. Запускать тесты при push/PR

3\. Использовать параллельное выполнение

4\. Генерировать отчеты (HTML, Allure)

5\. Сохранять скриншоты при ошибках

6\. Отправлять результаты в PR

\

## **Решение:**
text

\# .github/workflows/tests.yml

name: UI Tests

\


on:

`  `push:

`    `branches: [ main, develop ]

`  `pull\_request:

`    `branches: [ main, develop ]

\


jobs:

`  `test:

`    `runs-on: ubuntu-latest

`    `strategy:

`      `matrix:

`        `python-version: [3.9, '3.10']

`        `test-group: [smoke, regression]



`    `steps:

`    `- uses: actions/checkout@v3



`    `- name: Set up Python

`      `uses: actions/setup-python@v4

`      `with:

`        `python-version: ${{ matrix.python-version }}



`    `- name: Install dependencies

`      `run: |

`        `python -m pip install --upgrade pip

`        `pip install -r requirements.txt



`    `- name: Run tests

`      `run: |

`        `pytest tests/ -m ${{ matrix.test-group }} \

`          `-n 4 \

`          `-v \

`          `--html=report.html \

`          `--alluredir=allure-results



`    `- name: Upload HTML report

`      `if: always()

`      `uses: actions/upload-artifact@v3

`      `with:

`        `name: html-report-${{ matrix.python-version }}-${{ matrix.test-group }}

`        `path: report.html



`    `- name: Upload screenshots

`      `if: failure()

`      `uses: actions/upload-artifact@v3

`      `with:

`        `name: screenshots-${{ matrix.python-version }}-${{ matrix.test-group }}

`        `path: screenshots/



`    `- name: Comment PR

`      `if: always()

`      `uses: actions/github-script@v6

`      `with:

`        `script: |

`          `github.rest.issues.createComment({

`            `issue\_number: context.issue.number,

`            `owner: context.repo.owner,

`            `repo: context.repo.repo,

`            `body: '✅ Tests completed. See artifacts for reports.'

`          `})

\

## **Что оценивают:**
- ✅ GitHub Actions синтаксис
- ✅ Параллельное выполнение (matrix)
- ✅ Артифакты (отчеты, скриншоты)
- ✅ Обработка ошибок
- ✅ Оптимизация (параллели, кэш)
- ✅ Коммент в PR
-----
## **Задание 4: MOBILE TESTING FRAMEWORK (Hard)**
Уровень: Senior\
Время: 2-3 часа\
Требование: Создай фреймворк для мобильного тестирования
## **Задача:**
text

Требования:

1\. Создать MobileDriverFactory для разных устройств

2\. Написать Page Objects для мобильной версии

3\. Параметризировать тесты на разные устройства

4\. Запускать параллельно

5\. Генерировать отчет по устройствам

\


Устройства: iPhone 12, Samsung Galaxy S21, iPad

\

## **Решение:**
python

*# utils/mobile\_driver.py*

from selenium import webdriver

from selenium.webdriver.chrome.options import Options

\


class MobileDriverFactory:

`    `DEVICES = {

`        `"iPhone 12": {

`            `"width": 390,

`            `"height": 844,

`            `"userAgent": "Mozilla/5.0 (iPhone; CPU iPhone OS 14\_6 like Mac OS X)"

`        `},

`        `"Samsung Galaxy S21": {

`            `"width": 360,

`            `"height": 800,

`            `"userAgent": "Mozilla/5.0 (Linux; Android 11)"

`        `},

`        `"iPad": {

`            `"width": 1024,

`            `"height": 1366,

`            `"userAgent": "Mozilla/5.0 (iPad; CPU OS 14\_6 like Mac OS X)"

`        `}

`    `}



`    `@staticmethod

`    `def create\_driver(device="iPhone 12"):

`        `device\_config = MobileDriverFactory.DEVICES[device]

`        `options = Options()



`        `mobile\_emulation = {

`            `"deviceName": device,

`            `"userAgent": device\_config["userAgent"],

`            `"deviceScaleFactor": 3,

`            `"mobile": True,

`            `"hasTouch": True

`        `}



`        `options.add\_experimental\_option("mobileEmulation", mobile\_emulation)

`        `driver = webdriver.Chrome(options=options)

`        `driver.set\_window\_size(device\_config["width"], device\_config["height"])

`        `return driver

\


*# tests/conftest.py*

import pytest

from utils.mobile\_driver import MobileDriverFactory

\


@pytest.fixture(params=["iPhone 12", "Samsung Galaxy S21", "iPad"])

def mobile\_driver(request):

`    `device = request.param

`    `driver = MobileDriverFactory.create\_driver(device)

`    `yield driver

`    `driver.quit()

\


*# tests/test\_mobile.py*

def test\_responsive\_design(mobile\_driver):

`    `mobile\_driver.get("https://www.example.com")



`    `*# Проверить, что элементы видны на мобильном*

`    `assert mobile\_driver.get\_window\_size()["width"] < 1024



`    `*# Проверить основные элементы*

`    `products = mobile\_driver.find\_elements(By.CLASS\_NAME, "product")

`    `assert len(products) > 0

\

## **Что оценивают:**
- ✅ MobileDriverFactory архитектура
- ✅ Параметризация по устройствам
- ✅ Правильная конфигурация мобильных браузеров
- ✅ Page Objects для мобильной версии
- ✅ Параллельное выполнение
- ✅ Отчеты по устройствам
-----
## **🎁 ЧАСТЬ 3: ШАБЛОН ПРОЕКТА ДЛЯ ПОРТФОЛИО**
## **Структура проекта**
text

selenium-automation-project/

├── config/

│   └── settings.json

├── pages/

│   ├── base\_page.py

│   ├── login\_page.py

│   └── products\_page.py

├── tests/

│   ├── conftest.py

│   ├── test\_login.py

│   └── test\_products.py

├── utils/

│   ├── config\_manager.py

│   ├── driver\_factory.py

│   └── logger.py

├── .github/workflows/

│   └── tests.yml

├── README.md

├── requirements.txt

└── pytest.ini

\

## **README для GitHub**
text

\# Selenium Automation Project

\


Автоматизация тестирования веб-приложений с использованием Selenium и Pytest.

\


\## Установка

\


\```bash

pip install -r requirements.txt

\

## **Запуск тестов**
bash

pytest

\

## **Структура**
- pages/ - Page Object Models
- tests/ - Тесты
- config/ - Конфигурация
- utils/ - Вспомогательные классы
## **CI/CD**
GitHub Actions запускает тесты автоматически при push.
## **Автор**
[Твое имя]

text

\


\*\*\*

\


\## ❓ ЧАСТЬ 4: FAQ И ОТВЕТЫ

\


\### Q: Какой версии Selenium я должен использовать?

\*\*A:\*\* Версия 4.0+ (Selenium 4.x). Это последняя стабильная версия с лучшей поддержкой и новыми фичами.

\


\### Q: CSS или XPath селекторы?

\*\*A:\*\* Приоритет: data-testid > CSS > Relative Locators > XPath.

\- data-testid - для элементов, которые нужно тестировать

\- CSS - для быстрых селекторов

\- Relative Locators - для элементов без уникальных атрибутов

\- XPath - только если нет других вариантов

\


\### Q: Implicit vs Explicit wait?

\*\*A:\*\* Используй explicit wait (WebDriverWait). Implicit wait - старый подход, менее надежный.

\


\### Q: Page Object для каждой страницы?

\*\*A:\*\* Не обязательно для ВСЕХ. Создавай Page Object для основных страниц, где много взаимодействия.

\


\### Q: Как избежать flaky тестов?

\*\*A:\*\* 

\- Используй explicit wait

\- Избегай sleep() 

\- Используй правильные условия (visibility, clickability)

\- Изолируй тесты (clean state между тестами)

\


\### Q: GitHub Actions или Jenkins?

\*\*A:\*\* GitHub Actions - если используешь GitHub. Jenkins - для более сложных pipeline.

\


\### Q: Сколько тестов писать?

\*\*A:\*\* 

\- Junior: 10-20 тестов

\- Middle: 50-100 тестов

\- Senior: 200+

\


\### Q: Как получить первую работу?

\*\*A:\*\*

1\. Выучи все 8 модулей этого курса

2\. Создай свой проект (GitHub)

3\. Реши примеры заданий (appendix)

4\. Собеседуй отвечай на вопросы из чек-листов

5\. Начни с Junior позиции

\


\*\*\*

\


\## 📚 ЧАСТЬ 5: ПОЛЕЗНЫЕ РЕСУРСЫ

\


\### Документация

\- [Selenium Official Docs](https://www.selenium.dev/documentation/)

\- [Pytest Docs](https://docs.pytest.org/)

\- [MDN Web Docs (HTML/CSS/JS)](https://developer.mozilla.org/)

\


\### Сайты для практики

\- [SauceDemo](https://www.saucedemo.com/) - идеален для начинающих

\- [Automationexercise.com](https://www.automationexercise.com/)

\- [The Internet](https://the-internet.herokuapp.com/)

\


\### YouTube каналы

\- [Selenium WebDriver Tutorial](https://www.youtube.com/watch?v=kHF\_Hfd1Z4k)

\- [Pytest Tutorial](https://www.youtube.com/watch?v=IYE6n5N5Yd0)

\


\### GitHub репозитории

\- [Selenium GitHub](https://github.com/SeleniumHQ/selenium)

\- [Awesome Selenium](https://github.com/christian-bromann/awesome-selenium)

\


\### Статьи

\- Best Practices для Selenium

\- Page Object Model паттерн

\- CI/CD для тестов

\


\*\*\*

\


\## 🎓 ИТОГО

\


Используй этот appendix как справочник для:

\- ✅ Проверки своего уровня (чек-листы)

\- ✅ Подготовки к собеседованию (примеры заданий)

\- ✅ Создания портфолио (шаблон проекта)

\- ✅ Быстрого поиска информации (FAQ)

\- ✅ Поиска ресурсов для обучения

\


=== КОНЕЦ ФАЙЛА: appendix\_materials.md ===
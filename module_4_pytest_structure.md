=== НАЧАЛО ФАЙЛА: module\_4\_pytest\_structure.md ===
# **МОДУЛЬ 4: PYTEST И СТРУКТУРА ТЕСТОВ (3–4 дня)**
## **Полное объяснение с теорией и примерами**
-----
## **ВВЕДЕНИЕ: ЗАЧЕМ НУЖЕН ФРЕЙМВОРК ДЛЯ ТЕСТОВ?**
## **Проблема: хаос в тестах**
Представь, ты пишешь Selenium тесты просто в Python скриптах:

python

*# test1.py*

**from** selenium **import** webdriver

driver = webdriver.Chrome()

driver.get("https://example.com")

*# Тест 1: авторизация*

username = driver.find\_element("id", "username")

username.send\_keys("admin")

*# ... много кода ...*

**print**("Тест 1 пройден")

driver.quit()

python

*# test2.py*

**from** selenium **import** webdriver

driver = webdriver.Chrome()

driver.get("https://example.com")

*# Тест 2: добавление товара*

*# ... много кода ...*

**print**("Тест 2 пройден")

driver.quit()

Проблемы:

1. Нет стандартного способа запускать тесты
1. Нет отчетов о падениях
1. Сложно переиспользовать код
1. Нет способа запустить конкретный тест
1. Нет способа запустить тесты параллельно
1. Если один тест упадет, как узнаешь, почему?
   ## **Решение: фреймворк для тестирования**
   Pytest — это фреймворк, который:

- ✅ Находит и запускает тесты
- ✅ Показывает результаты понятным способом
- ✅ Позволяет переиспользовать код (фикстуры)
- ✅ Позволяет параметризировать тесты
- ✅ Генерирует отчеты
- ✅ Интегрируется с CI/CD
  -----
  ## **БЛОК 4.1: ЧТО ТАКОЕ PYTEST?**
  ## **Pytest — фреймворк для тестирования**
  Pytest — это библиотека Python, которая упрощает написание и запуск тестов.
  ## **Установка**
  bash

  pip install pytest
  ## **Основной концепт: тест как функция**
  В pytest тест — это просто функция, которая начинается с test\_:

  python

  *# test\_example.py*

  **def** test\_addition():

  `    `result = 2 + 2

  `    `**assert** result == 4

  **def** test\_subtraction():

  `    `result = 5 - 2

  `    `**assert** result == 3

  Как это работает:

1. Pytest ищет файлы с именем test\_\*.py или \*\_test.py
1. В этих файлах ищет функции, которые начинаются с test\_
1. Запускает каждую функцию
1. Если функция завершилась без ошибок — тест прошел
1. Если выкидывается исключение — тест упал
   ## **Запуск тестов**
   bash

   *# Запустить все тесты в текущей папке и подпапках*

   pytest

   *# Запустить конкретный файл*

   pytest test\_example.py

   *# Запустить конкретный тест*

   pytest test\_example.py::test\_addition

   *# Запустить с подробным выводом*

   pytest -v

   *# Запустить с выводом print()*

   pytest -s

   *# Остановиться на первом падении*

   pytest -x

   *# Показать 10 самых медленных тестов*

   pytest --durations=10

   -----
   ## **БЛОК 4.2: ASSERT — КАК ПРОВЕРЯТЬ РЕЗУЛЬТАТЫ**
   ## **Что такое assert?**
   Assert — это проверка условия. Если условие верно — продолжить. Если ложь — выкинуть исключение.

   python

   **assert** condition, "Сообщение если условие ложь"

   Примеры:

   python

   *# Проверка равенства*

   **assert** 2 + 2 == 4

   **assert** "hello" == "hello"

   *# Проверка неравенства*

   **assert** 5 != 3

   **assert** "a" != "b"

   *# Проверка булевых значений*

   **assert** True

   **assert** **not** False

   *# Проверка с сообщением об ошибке*

   **assert** 2 + 2 == 4, "Ошибка: математика не работает!"

   **assert** len([1, 2, 3]) == 3, "Список должен содержать 3 элемента"
   ## **Assert для разных типов**
   python

   *# Строки*

   **assert** "text" **in** "some text"

   **assert** "hello".upper() == "HELLO"

   *# Списки*

   **assert** 1 **in** [1, 2, 3]

   **assert** len([1, 2, 3]) == 3

   **assert** [1, 2] == [1, 2]

   *# Словари*

   **assert** "key" **in** {"key": "value"}

   **assert** data.get("name") == "John"

   *# None*

   **assert** value **is** None

   **assert** value **is** **not** None

   *# Типы*

   **assert** isinstance(5, int)

   **assert** isinstance("text", str)

   *# Исключения (можно проверить, что функция выкидывает исключение)*

   **import** pytest

   **def** test\_division\_by\_zero():

   `    `**with** pytest.raises(ZeroDivisionError):

   `        `result = 1 / 0
   ## **Pytest выводит подробную информацию об ошибке**
   Если assert падает, pytest показывает:

   python

   **def** test\_example():

   `    `a = 5

   `    `b = 3

   `    `**assert** a == b

   *# Вывод:*

   *# AssertionError: assert 5 == 3*

   *#   where 5 = a*

   *#   and   3 = b*

   -----
   ## **БЛОК 4.3: ТЕСТ-КЕЙСЫ И СТРУКТУРА ТЕСТА**
   ## **Структура теста: Arrange-Act-Assert (AAA)**
   Каждый хороший тест следует структуре Arrange-Act-Assert:

1. Arrange (подготовка) — подготовь данные, откройи браузер, создай объекты
1. Act (действие) — выполни тестируемое действие
1. Assert (проверка) — проверь результат

   Пример:

   python

   **def** test\_login():

   `    `*# ARRANGE — подготовка*

   `    `username = "admin"

   `    `password = "secret"

    

   `    `*# ACT — действие*

   `    `result = authenticate(username, password)

    

   `    `*# ASSERT — проверка*

   `    `**assert** result == True

   `    `**assert** result **is** **not** None

   С Selenium:

   python

   **from** selenium **import** webdriver

   **from** selenium.webdriver.common.by **import** By

   **def** test\_user\_can\_login():

   `    `*# ARRANGE — подготовка*

   `    `driver = webdriver.Chrome()

   `    `driver.get("https://example.com/login")

    

   `    `*# ACT — действие*

   `    `username\_field = driver.find\_element(By.ID, "username")

   `    `password\_field = driver.find\_element(By.ID, "password")

   `    `login\_button = driver.find\_element(By.ID, "login-button")

    

   `    `username\_field.send\_keys("admin")

   `    `password\_field.send\_keys("secret")

   `    `login\_button.click()

    

   `    `*# ASSERT — проверка*

   `    `**assert** "dashboard" **in** driver.current\_url

    

   `    `driver.quit()

   -----
   ## **БЛОК 4.4: ФИКСТУРЫ (FIXTURES)**
   ## **Проблема: повторение кода**
   Представь, что у тебя есть 10 тестов, и каждый начинается с:

   python

   **def** test\_1():

   `    `driver = webdriver.Chrome()

   `    `driver.get("https://example.com")

   `    `*# тест*

   `    `driver.quit()

   **def** test\_2():

   `    `driver = webdriver.Chrome()

   `    `driver.get("https://example.com")

   `    `*# тест*

   `    `driver.quit()

   *# И так 10 раз...*

   Много дублирования!
   ## **Решение: фикстуры**
   Фикстура — это функция, которая подготавливает данные или объекты для тестов. Она выполняется автоматически перед тестом.

   python

   **import** pytest

   **from** selenium **import** webdriver

   @pytest.fixture

   **def** driver():

   `    `"""Фикстура, которая создает и закрывает браузер"""

   `    `*# SETUP — подготовка*

   `    `browser = webdriver.Chrome()

   `    `browser.get("https://example.com")

    

   `    `**yield** browser  *# Передаем браузер тесту*

    

   `    `*# TEARDOWN — очистка*

   `    `browser.quit()


   **def** test\_login(driver):

   `    `"""Тест получает браузер через фикстуру"""

   `    `*# driver уже открыт и на сайте*

   `    `username\_field = driver.find\_element("id", "username")

   `    `username\_field.send\_keys("admin")

   `    `*# ...*


   **def** test\_search(driver):

   `    `"""Еще один тест с тем же браузером"""

   `    `search\_field = driver.find\_element("id", "search")

   `    `search\_field.send\_keys("laptop")

   `    `*# ...*

   Как это работает:

1. Pytest видит параметр driver в функции теста
1. Pytest ищет фикстуру с именем driver
1. Pytest запускает фикстуру (SETUP)
1. Pytest передает результат (yield значение) в тест
1. Pytest запускает тест
1. Pytest продолжает фикстуру после yield (TEARDOWN)
   ## **Фикстуры с параметрами**
   python

   @pytest.fixture

   **def** test\_data():

   `    `"""Возвращает данные для тестов"""

   `    `**return** {

   `        `"username": "admin",

   `        `"password": "secret",

   `        `"email": "admin@example.com"

   `    `}

   **def** test\_with\_data(test\_data):

   `    `**assert** test\_data["username"] == "admin"

   `    `**assert** test\_data["password"] == "secret"
   ## **Фикстуры для области видимости (scope)**
   По умолчанию фикстура создается для каждого теста. Но можно изменить:

   python

   @pytest.fixture(scope="session")

   **def** database():

   `    `"""Создается один раз для всей сессии тестов"""

   `    `db = Database()

   `    `db.connect()

   `    `**yield** db

   `    `db.close()

   @pytest.fixture(scope="module")

   **def** api\_client():

   `    `"""Создается один раз для каждого модуля (файла)"""

   `    `**return** APIClient()

   @pytest.fixture(scope="function")  *# По умолчанию*

   **def** temp\_file():

   `    `"""Создается для каждого теста"""

   `    `**return** create\_temp\_file()
   ## **conftest.py — общие фикстуры**
   Если создать файл conftest.py, фикстуры в нем будут доступны всем тестам в папке и подпапках:

   python

   *# conftest.py*

   **import** pytest

   **from** selenium **import** webdriver

   **from** webdriver\_manager.chrome **import** ChromeDriverManager

   **from** selenium.webdriver.service **import** Service

   @pytest.fixture

   **def** driver():

   `    `"""Фикстура доступна всем тестам"""

   `    `browser = webdriver.Chrome(service=Service(ChromeDriverManager().install()))

   `    `browser.implicitly\_wait(10)

   `    `**yield** browser

   `    `browser.quit()

   @pytest.fixture

   **def** logged\_in\_driver(driver):

   `    `"""Фикстура использует другую фикстуру"""

   `    `driver.get("https://www.saucedemo.com")

    

   `    `username = driver.find\_element("id", "user-name")

   `    `password = driver.find\_element("id", "password")

   `    `login\_button = driver.find\_element("id", "login-button")

    

   `    `username.send\_keys("standard\_user")

   `    `password.send\_keys("secret\_sauce")

   `    `login\_button.click()

    

   `    `**yield** driver

   Теперь все тесты в папке могут использовать эти фикстуры.

   -----
   ## **БЛОК 4.5: ПАРАМЕТРИЗАЦИЯ ТЕСТОВ**
   ## **Проблема: один тест, много данных**
   Представь, нужно протестировать авторизацию с разными пользователями:

   python

   **def** test\_login\_user1():

   `    `result = authenticate("user1", "pass1")

   `    `**assert** result == True

   **def** test\_login\_user2():

   `    `result = authenticate("user2", "pass2")

   `    `**assert** result == True

   **def** test\_login\_user3():

   `    `result = authenticate("user3", "pass3")

   `    `**assert** result == True

   Много дублирования!
   ## **Решение: параметризация**
   python

   **import** pytest

   @pytest.mark.parametrize("username,password,expected", [

   `    `("user1", "pass1", True),

   `    `("user2", "pass2", True),

   `    `("wrong\_user", "wrong\_pass", False),

   ])

   **def** test\_login(username, password, expected):

   `    `result = authenticate(username, password)

   `    `**assert** result == expected

   Pytest автоматически запустит тест 3 раза с разными параметрами!

   bash

   *# Запуск*

   pytest -v

   *# Результат:*

   *# test\_login[user1-pass1-True] PASSED*

   *# test\_login[user2-pass2-True] PASSED*

   *# test\_login[wrong\_user-wrong\_pass-False] PASSED*
   ## **Параметризация с разными типами данных**
   python

   @pytest.mark.parametrize("value,expected", [

   `    `(2, 4),

   `    `(3, 9),

   `    `(-1, 1),

   `    `(0, 0),

   ])

   **def** test\_square(value, expected):

   `    `**assert** value \*\* 2 == expected
   ## **Комбинирование параметризации с фикстурой**
   python

   @pytest.mark.parametrize("browser", ["chrome", "firefox"])

   **def** test\_login(driver, browser):

   `    `*# driver приходит из фикстуры*

   `    `*# browser приходит из параметризации*

   `    `**pass**

   -----
   ## **БЛОК 4.6: МАРКЕРЫ ТЕСТОВ**
   ## **Что такое маркер?**
   Маркер — это метка для группировки тестов. Можно запустить только тесты с определенной меткой.

   python

   **import** pytest

   @pytest.mark.smoke

   **def** test\_login():

   `    `"""Критичный тест (smoke-тест)"""

   `    `**pass**

   @pytest.mark.slow

   **def** test\_complex\_calculation():

   `    `"""Медленный тест"""

   `    `**pass**

   @pytest.mark.skip

   **def** test\_not\_ready():

   `    `"""Этот тест пропустится"""

   `    `**pass**

   @pytest.mark.xfail

   **def** test\_known\_bug():

   `    `"""Этот тест, вероятно, упадет, но это ожидается"""

   `    `**pass**
   ## **Запуск тестов по маркерам**
   bash

   *# Запустить только smoke-тесты*

   pytest -m smoke

   *# Запустить все кроме slow-тестов*

   pytest -m "not slow"

   *# Запустить smoke ИЛИ regression*

   pytest -m "smoke or regression"

   *# Запустить smoke И НЕ slow*

   pytest -m "smoke and not slow"
   ## **Определение своих маркеров**
   Создай файл pytest.ini:

   text

   [pytest]

   markers =

   `    `smoke: критичные тесты

   `    `slow: медленные тесты

   `    `regression: регрессионные тесты

   `    `ui: UI-тесты

   `    `api: API-тесты

   -----
   ## **БЛОК 4.7: КЛАССЫ ТЕСТОВ**
   Тесты можно группировать в классы:

   python

   **import** pytest

   **from** selenium **import** webdriver

   **from** selenium.webdriver.common.by **import** By

   **class** TestProductSearch:

   `    `"""Тесты для поиска товаров"""

    

   `    `**def** test\_search\_by\_name(self):

   `        `results = search("laptop")

   `        `**assert** len(results) > 0

    

   `    `**def** test\_search\_empty\_query(self):

   `        `results = search("")

   `        `**assert** len(results) == 0

   **class** TestCheckout:

   `    `"""Тесты для оформления заказа"""

    

   `    `**def** test\_add\_item(self):

   `        `cart = create\_cart()

   `        `cart.add\_product("laptop")

   `        `**assert** "laptop" **in** cart.items

    

   `    `**def** test\_checkout(self):

   `        `cart = create\_cart()

   `        `cart.add\_product("mouse")

   `        `total = cart.checkout()

   `        `**assert** total > 0
   ## **Фикстуры для класса**
   python

   **import** pytest

   **class** TestCheckout:

   `    `@pytest.fixture

   `    `**def** cart(self):

   `        `"""Фикстура для класса"""

   `        `**return** create\_cart()

    

   `    `**def** test\_add\_item(self, cart):

   `        `cart.add\_product("laptop")

   `        `**assert** "laptop" **in** cart.items

    

   `    `**def** test\_checkout(self, cart):

   `        `cart.add\_product("mouse")

   `        `total = cart.checkout()

   `        `**assert** total > 0

   -----
   ## **БЛОК 4.8: ЗАПУСК И ОТЧЕТЫ**
   ## **Запуск тестов**
   bash

   *# Все тесты*

   pytest

   *# С подробным выводом*

   pytest -v

   *# С выводом print()*

   pytest -s

   *# Остановиться на первой ошибке*

   pytest -x

   *# Остановиться после N ошибок*

   pytest --maxfail=3

   *# Показать самые медленные тесты*

   pytest --durations=5

   *# Запустить последний упавший тест*

   pytest --lf

   *# Запустить упавшие тесты, потом остальные*

   pytest --ff
   ## **Генерация отчетов**
   bash

   *# HTML отчет*

   pip install pytest-html

   pytest --html=report.html

   *# JUnit XML (для CI/CD)*

   pytest --junit-xml=report.xml

   *# Coverage (какой % кода покрыт тестами)*

   pip install pytest-cov

   pytest --cov=myapp

   -----
   ## **ПРАКТИЧЕСКИЙ ПРИМЕР: ПОЛНЫЙ НАБОР ТЕСТОВ**
   conftest.py:

   python

   **import** pytest

   **from** selenium **import** webdriver

   **from** webdriver\_manager.chrome **import** ChromeDriverManager

   **from** selenium.webdriver.service **import** Service

   @pytest.fixture

   **def** driver():

   `    `"""Браузер для каждого теста"""

   `    `browser = webdriver.Chrome(service=Service(ChromeDriverManager().install()))

   `    `browser.implicitly\_wait(10)

   `    `**yield** browser

   `    `browser.quit()

   @pytest.fixture

   **def** logged\_in\_driver(driver):

   `    `"""Браузер с залогированным пользователем"""

   `    `driver.get("https://www.saucedemo.com")

    

   `    `username = driver.find\_element("id", "user-name")

   `    `password = driver.find\_element("id", "password")

   `    `login\_button = driver.find\_element("id", "login-button")

    

   `    `username.send\_keys("standard\_user")

   `    `password.send\_keys("secret\_sauce")

   `    `login\_button.click()

    

   `    `**yield** driver

   test\_login.py:

   python

   **import** pytest

   **from** selenium.webdriver.common.by **import** By

   @pytest.mark.smoke

   **class** TestLogin:

   `    `**def** test\_successful\_login(self, driver):

   `        `"""Успешная авторизация"""

   `        `driver.get("https://www.saucedemo.com")

        

   `        `username = driver.find\_element(By.ID, "user-name")

   `        `password = driver.find\_element(By.ID, "password")

   `        `login\_button = driver.find\_element(By.ID, "login-button")

        

   `        `username.send\_keys("standard\_user")

   `        `password.send\_keys("secret\_sauce")

   `        `login\_button.click()

        

   `        `**assert** "inventory" **in** driver.current\_url

    

   `    `@pytest.mark.parametrize("username,password", [

   `        `("", "secret\_sauce"),

   `        `("standard\_user", ""),

   `        `("wrong\_user", "secret\_sauce"),

   `    `])

   `    `**def** test\_failed\_login(self, driver, username, password):

   `        `"""Неудачная авторизация с разными параметрами"""

   `        `driver.get("https://www.saucedemo.com")

        

   `        `username\_field = driver.find\_element(By.ID, "user-name")

   `        `password\_field = driver.find\_element(By.ID, "password")

   `        `login\_button = driver.find\_element(By.ID, "login-button")

        

   `        `username\_field.send\_keys(username)

   `        `password\_field.send\_keys(password)

   `        `login\_button.click()

        

   `        `error\_message = driver.find\_element(By.CLASS\_NAME, "error-message-container")

   `        `**assert** error\_message.is\_displayed()

   test\_products.py:

   python

   **import** pytest

   **from** selenium.webdriver.common.by **import** By

   @pytest.mark.regression

   **class** TestProducts:

   `    `**def** test\_products\_are\_displayed(self, logged\_in\_driver):

   `        `"""Товары отображаются после авторизации"""

   `        `driver = logged\_in\_driver

        

   `        `products = driver.find\_elements(By.CLASS\_NAME, "inventory\_item")

   `        `**assert** len(products) > 0

    

   `    `@pytest.mark.slow

   `    `**def** test\_add\_product\_to\_cart(self, logged\_in\_driver):

   `        `"""Добавление товара в корзину"""

   `        `driver = logged\_in\_driver

        

   `        `add\_to\_cart\_button = driver.find\_element(By.ID, "add-to-cart-sauce-labs-backpack")

   `        `add\_to\_cart\_button.click()

        

   `        `cart\_badge = driver.find\_element(By.CLASS\_NAME, "shopping\_cart\_badge")

   `        `**assert** cart\_badge.text == "1"

   Запуск:

   bash

   *# Все тесты*

   pytest

   *# Только smoke-тесты*

   pytest -m smoke

   *# Все кроме slow-тестов*

   pytest -m "not slow"

   *# С подробным выводом*

   pytest -v

   *# С отчетом*

   pytest --html=report.html

   -----
   ## **ИТОГО: ЧТО НУЖНО ЗНАТЬ**
1. Pytest — фреймворк для автоматизации тестов
1. Тест — функция, которая начинается с test\_
1. Assert — проверка условия, основа каждого теста
1. AAA структура — Arrange, Act, Assert
1. Фикстуры — функции для подготовки данных/объектов
1. Параметризация — запуск теста с разными параметрами
1. Маркеры — метки для группировки тестов
1. Классы — группировка связанных тестов
1. Отчеты — результаты в понятном виде
   -----
   ## **ПРАКТИЧЕСКИЕ ЗАДАНИЯ**
   ## **Задание 4.1: Первый тест**
   Напиши тест, который проверяет:

- Функция add(a, b) возвращает правильную сумму
- Используй assert для проверки разных случаев (положительные, отрицательные, ноль)
  ## **Задание 4.2: Фикстура**
  Создай фикстуру, которая:

- Открывает браузер
- Переходит на [**https://www.example.com**](https://www.example.com/)
- Возвращает браузер
- Закрывает браузер после теста

  Напиши тест, который использует эту фикстуру.
  ## **Задание 4.3: Параметризация**
  Напиши параметризованный тест для функции is\_even(n):

- Проверь несколько четных чисел
- Проверь несколько нечетных чисел
- Используй @pytest.mark.parametrize
  ## **Задание 4.4: Классы тестов**
  Создай два класса тестов:

- TestMath — тесты для математических функций
- TestStrings — тесты для строковых функций

  Каждый класс должен содержать по 3 теста.
  ## **Задание 4.5: Маркеры**
  Напиши тесты и маркируй их:

- @pytest.mark.smoke — критичные тесты
- @pytest.mark.slow — медленные тесты

  Запусти только smoke-тесты.
  ## **Задание 4.6: Конфигурация**
  Создай conftest.py с общей фикстурой для браузера.\
  Создай несколько тестовых файлов, которые используют эту фикстуру.

  === КОНЕЦ ФАЙЛА: module\_4\_pytest\_structure.md ===

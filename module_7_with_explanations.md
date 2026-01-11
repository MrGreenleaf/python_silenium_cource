=== НАЧАЛО ФАЙЛА: module\_7\_with\_explanations.md ===
# **МОДУЛЬ 7: ПОЛНЫЙ ПЕТ-ПРОЕКТ С ПОДРОБНЫМИ ОБЪЯСНЕНИЯМИ**
## **От нуля к работающему фреймворку**
-----
## **ВВЕДЕНИЕ: СИНТЕЗ ВСЕГО**
В предыдущих модулях ты узнал все части. Теперь соберем полный проект на реальном сайте.

Проект: Автоматизация тестирования [**https://www.saucedemo.com**](https://www.saucedemo.com/)

-----
## **ФАЗА 1: СТРУКТУРА ПРОЕКТА (День 1)**
## **Зачем нужна правильная структура?**
Плохая структура:

text

my\_tests.py  ← все в одном файле (500+ строк, невозможно найти ничего)

Хорошая структура:

text

project/

├── pages/         ← логика работы со страницами

├── tests/         ← сами тесты

├── utils/         ← вспомогательный код

├── config/        ← конфигурация (параметры)

├── screenshots/   ← скриншоты при ошибках

└── logs/          ← логи для отладки
## **Создание структуры**
bash

mkdir selenium\_automation\_project

cd selenium\_automation\_project

*# Папки*

mkdir tests pages utils config screenshots logs allure\_results

mkdir .github/workflows

*# Файлы*

touch .gitignore requirements.txt pytest.ini README.md

touch utils/\_\_init\_\_.py utils/config.py utils/logger.py utils/driver\_factory.py

touch pages/\_\_init\_\_.py pages/base\_page.py pages/login\_page.py pages/products\_page.py pages/checkout\_page.py

touch tests/\_\_init\_\_.py tests/conftest.py tests/test\_login.py tests/test\_products.py tests/test\_checkout.py

touch config/settings.json

-----
## **ФАЗА 2: КОНФИГУРАЦИЯ (День 1-2)**
## **📄 config/settings.json**
json

{

`  `"base\_url": "https://www.saucedemo.com",



`  `"browser": {

`    `"type": "chrome",

`    `"headless": false,

`    `"window\_size": "1920x1080"

`  `},



`  `"timeouts": {

`    `"implicit\_wait": 10,

`    `"explicit\_wait": 15

`  `},



`  `"test\_users": {

`    `"valid\_user": {

`      `"username": "standard\_user",

`      `"password": "secret\_sauce"

`    `},

`    `"locked\_user": {

`      `"username": "locked\_out\_user",

`      `"password": "secret\_sauce"

`    `}

`  `},



`  `"products": [

`    `{

`      `"name": "Sauce Labs Backpack",

`      `"id": "sauce-labs-backpack"

`    `},

`    `{

`      `"name": "Sauce Labs Bike Light",

`      `"id": "sauce-labs-bike-light"

`    `}

`  `]

}
## **🔧 utils/config.py**
python

**import** json

**from** pathlib **import** Path

**class** Config:

`    `"""Менеджер конфигурации"""



`    `\_config = None



`    `@classmethod

`    `**def** load(cls):

`        `"""Загрузить конфиг один раз"""

`        `**if** cls.\_config **is** None:

`            `config\_path = Path(\_\_file\_\_).parent.parent / "config" / "settings.json"

`            `**with** open(config\_path, 'r', encoding='utf-8') **as** f:

`                `cls.\_config = json.load(f)

`        `**return** cls.\_config



`    `@classmethod

`    `**def** get(cls, key, default=None):

`        `"""Получить значение по ключу"""

`        `config = cls.load()

`        `**return** config.get(key, default)



`    `@classmethod

`    `**def** get\_base\_url(cls):

`        `**return** cls.get("base\_url")



`    `@classmethod

`    `**def** get\_user(cls, user\_type="valid\_user"):

`        `**return** cls.get("test\_users", {}).get(user\_type)
## **📝 utils/logger.py**
python

**import** logging

**import** os

**from** datetime **import** datetime

**class** Logger:

`    `"""Система логирования"""



`    `\_logger = None



`    `@classmethod

`    `**def** setup(cls):

`        `"""Настроить логирование"""

`        `**if** cls.\_logger **is** **not** None:

`            `**return** cls.\_logger



`        `*# Создать папку для логов*

`        `os.makedirs("logs", exist\_ok=True)



`        `*# Имя файла*

`        `timestamp = datetime.now().strftime("%Y%m%d\_%H%M%S")

`        `log\_file = f"logs/test\_run\_{timestamp}.log"



`        `*# Создать логгер*

`        `logger = logging.getLogger("automation")

`        `logger.setLevel(logging.DEBUG)



`        `*# Формат*

`        `formatter = logging.Formatter(

`            `'%(asctime)s - %(levelname)s - %(message)s',

`            `datefmt='%Y-%m-%d %H:%M:%S'

`        `)



`        `*# Файловый обработчик*

`        `file\_handler = logging.FileHandler(log\_file, encoding='utf-8')

`        `file\_handler.setLevel(logging.DEBUG)

`        `file\_handler.setFormatter(formatter)

`        `logger.addHandler(file\_handler)



`        `*# Консольный обработчик*

`        `console\_handler = logging.StreamHandler()

`        `console\_handler.setLevel(logging.INFO)

`        `console\_handler.setFormatter(formatter)

`        `logger.addHandler(console\_handler)



`        `cls.\_logger = logger

`        `**return** logger

*# Глобальный логгер*

logger = Logger.setup()
## **🚗 utils/driver\_factory.py**
python

**from** selenium **import** webdriver

**from** webdriver\_manager.chrome **import** ChromeDriverManager

**from** selenium.webdriver.service **import** Service

**from** selenium.webdriver.chrome.options **import** Options

**from** utils.config **import** Config

**from** utils.logger **import** logger

**class** DriverFactory:

`    `"""Фабрика для создания браузера"""



`    `@staticmethod

`    `**def** create\_driver():

`        `"""Создать WebDriver"""

`        `config = Config.load()

`        `browser\_config = config.get("browser", {})



`        `options = Options()



`        `*# Headless режим*

`        `**if** browser\_config.get("headless", False):

`            `options.add\_argument("--headless")



`        `*# Размер окна*

`        `window\_size = browser\_config.get("window\_size", "1920x1080")

`        `options.add\_argument(f"--window-size={window\_size}")



`        `*# Отключить уведомления*

`        `options.add\_argument("--disable-notifications")



`        `driver = webdriver.Chrome(

`            `service=Service(ChromeDriverManager().install()),

`            `options=options

`        `)



`        `*# Таймауты*

`        `timeouts = config.get("timeouts", {})

`        `driver.implicitly\_wait(timeouts.get("implicit\_wait", 10))



`        `logger.info("✅ Браузер запущен")

`        `**return** driver

-----
## **ФАЗА 3: PAGE OBJECTS (День 2-3)**
## **📄 pages/base\_page.py**
python

**from** selenium.webdriver.support.ui **import** WebDriverWait

**from** selenium.webdriver.support **import** expected\_conditions **as** EC

**from** utils.logger **import** logger

**class** BasePage:

`    `"""Базовая страница со вспомогательными методами"""



`    `**def** \_\_init\_\_(self, driver, timeout=15):

`        `self.driver = driver

`        `self.timeout = timeout

`        `self.wait = WebDriverWait(driver, timeout)



`    `**def** find\_element(self, locator):

`        `"""Найти элемент"""

`        `**return** self.driver.find\_element(\*locator)



`    `**def** find\_elements(self, locator):

`        `"""Найти все элементы"""

`        `**return** self.driver.find\_elements(\*locator)



`    `**def** click(self, locator):

`        `"""Кликнуть с ожиданием"""

`        `element = self.wait.until(EC.element\_to\_be\_clickable(locator))

`        `element.click()

`        `logger.debug(f"Клик по элементу: {locator}")



`    `**def** input\_text(self, locator, text):

`        `"""Ввести текст"""

`        `element = self.find\_element(locator)

`        `element.clear()

`        `element.send\_keys(text)

`        `logger.debug(f"Введен текст в {locator}: {text}")



`    `**def** get\_text(self, locator):

`        `"""Получить текст"""

`        `**return** self.find\_element(locator).text



`    `**def** wait\_for\_element(self, locator):

`        `"""Ждать видимого элемента"""

`        `**return** self.wait.until(EC.visibility\_of\_element\_located(locator))



`    `**def** is\_displayed(self, locator):

`        `"""Проверить видимость"""

`        `**try**:

`            `**return** self.find\_element(locator).is\_displayed()

`        `**except**:

`            `**return** False
## **📄 pages/login\_page.py**
python

**from** selenium.webdriver.common.by **import** By

**from** pages.base\_page **import** BasePage

**from** utils.logger **import** logger

**class** LoginPage(BasePage):

`    `"""Страница авторизации"""



`    `USERNAME = (By.ID, "user-name")

`    `PASSWORD = (By.ID, "password")

`    `LOGIN\_BUTTON = (By.ID, "login-button")

`    `ERROR\_MESSAGE = (By.CLASS\_NAME, "error-message-container")



`    `**def** login(self, username, password):

`        `"""Авторизоваться"""

`        `logger.info(f"Авторизация с пользователем: {username}")



`        `self.input\_text(self.USERNAME, username)

`        `self.input\_text(self.PASSWORD, password)

`        `self.click(self.LOGIN\_BUTTON)



`        `logger.info("✅ Авторизация выполнена")



`    `**def** is\_error\_displayed(self):

`        `"""Видна ли ошибка"""

`        `**return** self.is\_displayed(self.ERROR\_MESSAGE)



`    `**def** get\_error\_text(self):

`        `"""Получить текст ошибки"""

`        `**return** self.get\_text(self.ERROR\_MESSAGE)
## **📄 pages/products\_page.py**
python

**from** selenium.webdriver.common.by **import** By

**from** pages.base\_page **import** BasePage

**from** utils.logger **import** logger

**class** ProductsPage(BasePage):

`    `"""Страница товаров"""



`    `PRODUCTS = (By.CLASS\_NAME, "inventory\_item")

`    `PRODUCT\_NAMES = (By.CLASS\_NAME, "inventory\_item\_name")

`    `CART\_BADGE = (By.CLASS\_NAME, "shopping\_cart\_badge")

`    `CART\_LINK = (By.CLASS\_NAME, "shopping\_cart\_link")



`    `**def** get\_products\_count(self):

`        `"""Получить количество товаров"""

`        `products = self.find\_elements(self.PRODUCTS)

`        `count = len(products)

`        `logger.info(f"На странице {count} товаров")

`        `**return** count



`    `**def** get\_product\_names(self):

`        `"""Получить имена всех товаров"""

`        `elements = self.find\_elements(self.PRODUCT\_NAMES)

`        `names = [el.text **for** el **in** elements]

`        `logger.debug(f"Найденные товары: {names}")

`        `**return** names



`    `**def** add\_to\_cart\_by\_name(self, product\_name):

`        `"""Добавить товар в корзину"""

`        `logger.info(f"Добавляем товар в корзину: {product\_name}")



`        `*# Найти товар и кликнуть кнопку add to cart*

`        `button\_id = product\_name.lower().replace(" ", "-").replace("labs-", "labs-")

`        `add\_button = (By.ID, f"add-to-cart-{button\_id}")



`        `self.click(add\_button)

`        `logger.info(f"✅ Товар добавлен: {product\_name}")



`    `**def** get\_cart\_count(self):

`        `"""Получить количество товаров в корзине"""

`        `**try**:

`            `badge = self.find\_element(self.CART\_BADGE)

`            `**return** int(badge.text)

`        `**except**:

`            `**return** 0



`    `**def** go\_to\_cart(self):

`        `"""Перейти в корзину"""

`        `self.click(self.CART\_LINK)

`        `logger.info("✅ Перейдено в корзину")
## **📄 pages/checkout\_page.py**
python

**from** selenium.webdriver.common.by **import** By

**from** pages.base\_page **import** BasePage

**from** utils.logger **import** logger

**class** CheckoutPage(BasePage):

`    `"""Страница оформления заказа"""



`    `CHECKOUT\_BUTTON = (By.ID, "checkout")

`    `FIRST\_NAME = (By.ID, "first-name")

`    `LAST\_NAME = (By.ID, "last-name")

`    `POSTAL\_CODE = (By.ID, "postal-code")

`    `CONTINUE\_BUTTON = (By.ID, "continue")

`    `FINISH\_BUTTON = (By.ID, "finish")

`    `COMPLETE\_MESSAGE = (By.CLASS\_NAME, "complete-header")



`    `**def** click\_checkout(self):

`        `"""Нажать Checkout"""

`        `self.click(self.CHECKOUT\_BUTTON)

`        `logger.info("✅ Нажата кнопка Checkout")



`    `**def** fill\_info(self, first\_name, last\_name, postal\_code):

`        `"""Заполнить информацию"""

`        `logger.info(f"Заполняем информацию: {first\_name} {last\_name}")



`        `self.input\_text(self.FIRST\_NAME, first\_name)

`        `self.input\_text(self.LAST\_NAME, last\_name)

`        `self.input\_text(self.POSTAL\_CODE, postal\_code)



`        `self.click(self.CONTINUE\_BUTTON)

`        `logger.info("✅ Информация заполнена и подтверждена")



`    `**def** finish(self):

`        `"""Завершить заказ"""

`        `self.click(self.FINISH\_BUTTON)

`        `logger.info("✅ Заказ завершен")



`    `**def** is\_complete(self):

`        `"""Завершен ли заказ"""

`        `**return** self.is\_displayed(self.COMPLETE\_MESSAGE)

-----
## **ФАЗА 4: ТЕСТЫ (День 3-4)**
## **📄 tests/conftest.py**
python

**import** pytest

**from** pathlib **import** Path

**from** datetime **import** datetime

**from** selenium.webdriver.common.by **import** By

**from** utils.driver\_factory **import** DriverFactory

**from** utils.config **import** Config

**from** utils.logger **import** logger

**from** pages.login\_page **import** LoginPage

**from** pages.products\_page **import** ProductsPage

@pytest.fixture

**def** driver():

`    `"""Браузер для теста"""

`    `browser = DriverFactory.create\_driver()

`    `**yield** browser

`    `browser.quit()

`    `logger.info("✅ Браузер закрыт")

@pytest.fixture

**def** login\_page(driver):

`    `"""Страница авторизации"""

`    `driver.get(Config.get\_base\_url())

`    `**return** LoginPage(driver)

@pytest.fixture

**def** logged\_in\_user(driver):

`    `"""Авторизованный пользователь"""

`    `user = Config.get\_user("valid\_user")



`    `driver.get(Config.get\_base\_url())

`    `login\_page = LoginPage(driver)

`    `login\_page.login(user["username"], user["password"])



`    `**return** driver

@pytest.hookimpl(tryfirst=True, hookwrapper=True)

**def** pytest\_runtest\_makereport(item, call):

`    `"""Сохранить скриншот при ошибке"""

`    `outcome = **yield**

`    `report = outcome.get\_result()



`    `**if** report.failed **and** call.when == "call":

`        `driver = item.funcargs.get('driver')

`        `**if** driver:

`            `Path("screenshots").mkdir(exist\_ok=True)

`            `timestamp = datetime.now().strftime("%Y%m%d\_%H%M%S\_%f")[:-3]

`            `path = f"screenshots/{item.name}\_{timestamp}.png"

`            `driver.save\_screenshot(path)

`            `logger.error(f"📸 Скриншот сохранен: {path}")
## **📄 tests/test\_login.py**
python

**import** pytest

**from** utils.logger **import** logger

**class** TestLogin:

`    `"""Тесты авторизации"""



`    `@pytest.mark.smoke

`    `**def** test\_successful\_login(self, login\_page):

`        `"""Успешная авторизация"""

`        `**from** utils.config **import** Config



`        `user = Config.get\_user("valid\_user")



`        `login\_page.login(user["username"], user["password"])



`        `**assert** "inventory" **in** login\_page.driver.current\_url

`        `logger.info("✅ Тест успешной авторизации пройден")



`    `@pytest.mark.parametrize("username,password", [

`        `("", "secret\_sauce"),

`        `("standard\_user", ""),

`        `("wrong\_user", "secret\_sauce"),

`    `])

`    `**def** test\_login\_with\_invalid\_data(self, login\_page, username, password):

`        `"""Авторизация с неправильными данными"""

`        `login\_page.login(username, password)



`        `**assert** login\_page.is\_error\_displayed()

`        `logger.info(f"✅ Ошибка авторизации для {username}/{password}")
## **📄 tests/test\_products.py**
python

**import** pytest

**from** pages.products\_page **import** ProductsPage

**from** utils.logger **import** logger

**class** TestProducts:

`    `"""Тесты товаров"""



`    `@pytest.mark.smoke

`    `**def** test\_products\_displayed(self, logged\_in\_user):

`        `"""Товары отображаются"""

`        `page = ProductsPage(logged\_in\_user)



`        `**assert** page.get\_products\_count() > 0

`        `logger.info("✅ Товары отображаются")



`    `**def** test\_add\_to\_cart(self, logged\_in\_user):

`        `"""Добавление в корзину"""

`        `page = ProductsPage(logged\_in\_user)



`        `initial\_count = page.get\_cart\_count()

`        `page.add\_to\_cart\_by\_name("Sauce Labs Backpack")



`        `**assert** page.get\_cart\_count() == initial\_count + 1

`        `logger.info("✅ Товар добавлен в корзину")
## **📄 tests/test\_checkout.py**
python

**import** pytest

**from** pages.products\_page **import** ProductsPage

**from** pages.checkout\_page **import** CheckoutPage

**from** utils.logger **import** logger

**class** TestCheckout:

`    `"""Тесты оформления заказа"""



`    `**def** test\_full\_checkout(self, logged\_in\_user):

`        `"""Полный процесс оформления"""

`        `*# Страница товаров*

`        `products = ProductsPage(logged\_in\_user)

`        `products.add\_to\_cart\_by\_name("Sauce Labs Backpack")

`        `products.go\_to\_cart()



`        `*# Страница оформления*

`        `checkout = CheckoutPage(logged\_in\_user)

`        `checkout.click\_checkout()

`        `checkout.fill\_info("John", "Doe", "12345")

`        `checkout.finish()



`        `*# Проверка*

`        `**assert** checkout.is\_complete()

`        `logger.info("✅ Полный флоу оформления пройден")

-----
## **ФАЗА 5: КОНФИГУРАЦИЯ И ДОКУМЕНТАЦИЯ (День 4)**
## **📄 requirements.txt**
text

selenium>=4.0.0

pytest>=7.0.0

pytest-html>=3.1.1

pytest-xdist>=2.5.0

webdriver-manager>=3.8.0

allure-pytest>=2.9.45

python-dotenv>=0.19.0
## **📄 pytest.ini**
text

[pytest]

testpaths = tests

python\_files = test\_\*.py

python\_functions = test\_\*

python\_classes = Test\*

markers =

`    `smoke: Критичные тесты

`    `regression: Регрессионные тесты

`    `slow: Медленные тесты

addopts = 

`    `-v

`    `--tb=short

`    `--html=report.html

`    `--self-contained-html
## **📄 .gitignore**
text

\_\_pycache\_\_/

\*.py[cod]

\*$py.class

\*.so

.env

.venv

venv/

ENV/

\# Тесты

.pytest\_cache/

.coverage

htmlcov/

report.html

\# IDE

.vscode/

.idea/

\*.swp

\*.swo

\# Логи и скриншоты

logs/

screenshots/

allure-results/
## **📄 README.md**
text

\# Selenium Automation Project

Автоматизация тестирования для https://www.saucedemo.com

\## Установка

\```bash

pip install -r requirements.txt
## **Запуск тестов**
bash

*# Все тесты*

pytest

*# Только smoke-тесты*

pytest -m smoke

*# С Allure отчетом*

pytest --alluredir=allure-results
## **Структура проекта**
- config/ - Конфигурация
- pages/ - Page Objects
- tests/ - Тесты
- utils/ - Утилиты
- logs/ - Логи
- screenshots/ - Скриншоты при ошибках

  text

  ### 📄 .github/workflows/tests.yml

  ```yaml

  name: Tests

  on: [push, pull\_request]

  jobs:

  `  `test:

  `    `runs-on: ubuntu-latest

  `    `strategy:

  `      `matrix:

  `        `python-version: [3.9, '3.10']

    

  `    `steps:

  `    `- uses: actions/checkout@v3

    

  `    `- name: Set up Python

  `      `uses: actions/setup-python@v4

  `      `with:

  `        `python-version: ${{ matrix.python-version }}

    

  `    `- name: Install dependencies

  `      `run: |

  `        `python -m pip install --upgrade pip

  `        `pip install -r requirements.txt

    

  `    `- name: Run tests

  `      `run: pytest -v --html=report.html

    

  `    `- name: Upload report

  `      `if: always()

  `      `uses: actions/upload-artifact@v3

  `      `with:

  `        `name: report-${{ matrix.python-version }}

  `        `path: report.html

    

  `    `- name: Upload screenshots

  `      `if: failure()

  `      `uses: actions/upload-artifact@v3

  `      `with:

  `        `name: screenshots-${{ matrix.python-version }}

  `        `path: screenshots/

  -----
  ## **БЫСТРЫЙ СТАРТ**
  ## **1. Установка**
  bash

  pip install -r requirements.txt
  ## **2. Запуск**
  bash

  *# Все тесты*

  pytest

  *# Только smoke тесты*

  pytest -m smoke

  *# С подробным выводом*

  pytest -v -s

  *# На 4 потоках параллельно*

  pytest -n 4
  ## **3. Результаты**
- 📊 HTML отчет: report.html (откроется в браузере)
- 📸 Скриншоты: папка screenshots/
- 📝 Логи: папка logs/
  -----
  ## **ИТОГО: ФАЙЛЫ И ИХ НАЗНАЧЕНИЕ**

  |**Файл**|**Зачем**|
  | :- | :- |
  |config/settings.json|Все параметры (URL, браузер, пользователи)|
  |utils/config.py|Загрузка и чтение конфига|
  |utils/logger.py|Логирование событий|
  |utils/driver\_factory.py|Создание браузера с нужными опциями|
  |pages/base\_page.py|Базовые методы для всех страниц|
  |pages/login\_page.py|Методы для страницы авторизации|
  |pages/products\_page.py|Методы для страницы товаров|
  |pages/checkout\_page.py|Методы для оформления заказа|
  |tests/conftest.py|Фикстуры для всех тестов|
  |tests/test\_login.py|Тесты для авторизации|
  |tests/test\_products.py|Тесты для товаров|
  |tests/test\_checkout.py|Тесты для оформления|
  |pytest.ini|Конфигурация pytest|
  |.github/workflows/tests.yml|Автоматизация в GitHub|
  |requirements.txt|Список зависимостей|
  |README.md|Документация проекта|

  -----
  ## **СТРУКТУРА ПРОЕКТА**
  text

  selenium\_automation\_project/

  │

  ├── 📁 config/                      ← Конфигурация

  │   └── settings.json              ← Параметры (URL, браузер, пользователи)

  │

  ├── 📁 utils/                       ← Вспомогательный код

  │   ├── config.py                  ← Загрузка конфига

  │   ├── logger.py                  ← Логирование

  │   └── driver\_factory.py           ← Создание браузера

  │

  ├── 📁 pages/                       ← Page Objects (логика работы со страницами)

  │   ├── base\_page.py               ← Базовые методы

  │   ├── login\_page.py              ← Авторизация

  │   ├── products\_page.py           ← Товары

  │   └── checkout\_page.py           ← Оформление заказа

  │

  ├── 📁 tests/                       ← Сами тесты

  │   ├── conftest.py                ← Фикстуры (подготовка для тестов)

  │   ├── test\_login.py              ← Тесты авторизации

  │   ├── test\_products.py           ← Тесты товаров

  │   └── test\_checkout.py           ← Тесты оформления

  │

  ├── 📁 .github/

  │   └── workflows/

  │       └── tests.yml              ← Автоматизация в GitHub Actions

  │

  ├── 📁 screenshots/                ← Скриншоты при ошибках

  ├── 📁 logs/                       ← Логи тестов

  │

  ├── requirements.txt               ← Список пакетов Python

  ├── pytest.ini                     ← Конфигурация pytest

  ├── README.md                      ← Документация

  └── .gitignore                     ← Файлы для игнорирования в Git

  -----
  ## **КЛЮЧЕВЫЕ КОНЦЕПЦИИ ЭТОГО ПРОЕКТА**
  ## **1. Разделение ответственности**
- Config управляет конфигурацией
- DriverFactory создает браузер
- Logger логирует события
- BasePage предоставляет общие методы
- Page Objects содержат логику работы со страницами
- Тесты используют Page Objects, не касаясь селекторов
  ## **2. Переиспользование кода**
- Если нужно изменить селектор, меняешь в одном месте (в Page Object)
- Если нужно добавить новую страницу, создаешь новый класс, наследующий BasePage
- Все тесты автоматически используют новый функционал
  ## **3. Чистота и читаемость**
  Плохо (смешанный код):

  python

  **def** test\_login():

  `    `driver = webdriver.Chrome()

  `    `username = driver.find\_element(By.ID, "user-name")

  `    `username.send\_keys("admin")

  `    `*# ... 50 строк кода ...*

  Хорошо (с Page Objects):

  python

  **def** test\_login(login\_page):

  `    `login\_page.login("admin", "secret")

  `    `**assert** "inventory" **in** login\_page.driver.current\_url

  -----
  ## **ПОЗДРАВЛЯЕМ! 🎉**
  Теперь ты знаешь, как:

  ✅ Установить и настроить Selenium\
  ✅ Найти и взаимодействовать с элементами\
  ✅ Писать Pytest тесты\
  ✅ Использовать Page Object Model\
  ✅ Управлять конфигурацией\
  ✅ Логировать события\
  ✅ Сохранять скриншоты при ошибках\
  ✅ Запускать тесты параллельно\
  ✅ Интегрировать с CI/CD (GitHub Actions)\
  ✅ Генерировать отчеты

  Это действительно полный фреймворк для UI автоматизации!

  === КОНЕЦ ФАЙЛА: module\_7\_with\_explanations.md ===

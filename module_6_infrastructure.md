=== НАЧАЛО ФАЙЛА: module\_6\_infrastructure.md ===
# **МОДУЛЬ 6: ИНФРАСТРУКТУРА, КОНФИГУРАЦИЯ И CI/CD (3–4 дня)**
## **Полное объяснение с теорией и примерами**
-----
## **ВВЕДЕНИЕ: ЗАЧЕМ НУЖНА ИНФРАСТРУКТУРА?**
## **Проблема: как управлять конфигурацией?**
Когда растет проект, появляются вопросы:

- Как хранить параметры (URL сайта, логины, браузеры)?
- Как запустить тесты на разных окружениях (dev, test, prod)?
- Как запустить тесты параллельно?
- Как автоматизировать запуск тестов (CI/CD)?
- Как видеть отчеты и логи?
- Как сохранять скриншоты при ошибках?
  ## **Решение: правильная инфраструктура**
  В этом модуле ты научишься:

1. Конфигурация — хранение параметров в JSON/YAML
1. Логирование — отслеживание всех событий
1. Скриншоты и видео — сохранение при ошибках
1. Отчеты — Allure, HTML, JUnit
1. CI/CD — GitHub Actions, GitLab CI
1. Параллельное выполнение — pytest-xdist
   -----
   ## **БЛОК 6.1: КОНФИГУРАЦИЯ ПРОЕКТА**
   ## **Структура проекта с конфигурацией**
   text

   selenium\_project/

   ├── config/

   │   ├── \_\_init\_\_.py

   │   ├── settings.json          # Основные параметры

   │   ├── browsers.yaml          # Конфигурация браузеров

   │   └── environments.json       # Параметры разных окружений

   │

   ├── utils/

   │   ├── \_\_init\_\_.py

   │   ├── config\_manager.py      # Загрузка конфига

   │   └── driver\_factory.py       # Создание браузера

   │

   ├── tests/

   │   ├── conftest.py

   │   └── test\_login.py

   │

   ├── requirements.txt

   └── pytest.ini
   ## **config/settings.json**
   json

   {

   `  `"app": {

   `    `"base\_url": "https://www.saucedemo.com",

   `    `"name": "Sauce Labs Demo App"

   `  `},

  

   `  `"browser": {

   `    `"type": "chrome",

   `    `"headless": false,

   `    `"window\_size": "1920x1080",

   `    `"timeout": 10,

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

  

   `  `"logging": {

   `    `"level": "INFO",

   `    `"format": "%(asctime)s - %(name)s - %(levelname)s - %(message)s"

   `  `},

  

   `  `"reports": {

   `    `"html": true,

   `    `"allure": true,

   `    `"screenshots\_on\_failure": true,

   `    `"video\_on\_failure": false

   `  `}

   }
   ## **utils/config\_manager.py**
   python

   **import** json

   **import** os

   **from** pathlib **import** Path

   **class** ConfigManager:

   `    `"""Менеджер для загрузки и управления конфигурацией"""

    

   `    `\_instance = None

   `    `\_config = None

    

   `    `**def** \_\_new\_\_(cls):

   `        `"""Синглтон - только один экземпляр класса"""

   `        `**if** cls.\_instance **is** None:

   `            `cls.\_instance = super().\_\_new\_\_(cls)

   `        `**return** cls.\_instance

    

   `    `**def** \_\_init\_\_(self):

   `        `"""Загрузить конфигурацию при инициализации"""

   `        `**if** self.\_config **is** None:

   `            `self.\_load\_config()

    

   `    `@classmethod

   `    `**def** \_load\_config(cls):

   `        `"""Загрузить JSON конфиг"""

   `        `config\_path = Path(\_\_file\_\_).parent.parent / "config" / "settings.json"

        

   `        `**with** open(config\_path, 'r', encoding='utf-8') **as** f:

   `            `cls.\_config = json.load(f)

    

   `    `**def** get(self, key\_path, default=None):

   `        `"""

   `        `Получить значение по пути (например: "app.base\_url")

        

   `        `Args:

   `            `key\_path: путь к ключу, разделенный точками

   `            `default: значение по умолчанию

        

   `        `Returns:

   `            `Значение или default

   `        `"""

   `        `keys = key\_path.split('.')

   `        `value = self.\_config

        

   `        `**for** key **in** keys:

   `            `**if** isinstance(value, dict):

   `                `value = value.get(key)

   `                `**if** value **is** None:

   `                    `**return** default

   `            `**else**:

   `                `**return** default

        

   `        `**return** value

    

   `    `**def** get\_base\_url(self):

   `        `"""Получить базовый URL сайта"""

   `        `**return** self.get("app.base\_url")

    

   `    `**def** get\_browser\_type(self):

   `        `"""Получить тип браузера"""

   `        `**return** self.get("browser.type", "chrome")

    

   `    `**def** is\_headless(self):

   `        `"""Запускать ли браузер в headless режиме"""

   `        `**return** self.get("browser.headless", False)

    

   `    `**def** get\_timeout(self):

   `        `"""Получить таймаут для ожиданий"""

   `        `**return** self.get("browser.explicit\_wait", 15)

    

   `    `**def** get\_user(self, user\_type="valid\_user"):

   `        `"""

   `        `Получить учетные данные пользователя

        

   `        `Args:

   `            `user\_type: тип пользователя (valid\_user, locked\_user и т.д.)

        

   `        `Returns:

   `            `Словарь с username и password

   `        `"""

   `        `**return** self.get(f"test\_users.{user\_type}")

    

   `    `**def** get\_all\_config(self):

   `        `"""Получить всю конфигурацию"""

   `        `**return** self.\_config
   ## **utils/driver\_factory.py**
   python

   **from** selenium **import** webdriver

   **from** selenium.webdriver.chrome.options **import** Options

   **from** webdriver\_manager.chrome **import** ChromeDriverManager

   **from** selenium.webdriver.service **import** Service

   **from** utils.config\_manager **import** ConfigManager

   **class** DriverFactory:

   `    `"""Фабрика для создания WebDriver с разными конфигурациями"""

    

   `    `@staticmethod

   `    `**def** create\_driver():

   `        `"""

   `        `Создать WebDriver согласно конфигурации

        

   `        `Returns:

   `            `WebDriver браузера

   `        `"""

   `        `config = ConfigManager()

   `        `browser\_type = config.get\_browser\_type()

        

   `        `**if** browser\_type.lower() == "chrome":

   `            `**return** DriverFactory.\_create\_chrome\_driver()

   `        `**elif** browser\_type.lower() == "firefox":

   `            `**return** DriverFactory.\_create\_firefox\_driver()

   `        `**else**:

   `            `**raise** ValueError(f"Неподдерживаемый браузер: {browser\_type}")

    

   `    `@staticmethod

   `    `**def** \_create\_chrome\_driver():

   `        `"""Создать Chrome браузер"""

   `        `config = ConfigManager()

        

   `        `options = Options()

        

   `        `*# Headless режим*

   `        `**if** config.is\_headless():

   `            `options.add\_argument("--headless")

        

   `        `*# Размер окна*

   `        `window\_size = config.get("browser.window\_size", "1920x1080")

   `        `options.add\_argument(f"--window-size={window\_size}")

        

   `        `*# Отключить уведомления*

   `        `options.add\_argument("--disable-notifications")

        

   `        `*# Отключить блокировщик попапов*

   `        `options.add\_argument("--disable-popup-blocking")

        

   `        `*# Отключить расширения*

   `        `options.add\_argument("--disable-extensions")

        

   `        `driver = webdriver.Chrome(

   `            `service=Service(ChromeDriverManager().install()),

   `            `options=options

   `        `)

        

   `        `*# Установить таймауты*

   `        `driver.implicitly\_wait(config.get("browser.implicit\_wait", 10))

        

   `        `**return** driver

    

   `    `@staticmethod

   `    `**def** \_create\_firefox\_driver():

   `        `"""Создать Firefox браузер"""

   `        `config = ConfigManager()

        

   `        `options = webdriver.FirefoxOptions()

        

   `        `**if** config.is\_headless():

   `            `options.add\_argument("--headless")

        

   `        `driver = webdriver.Firefox(options=options)

   `        `driver.implicitly\_wait(config.get("browser.implicit\_wait", 10))

        

   `        `**return** driver

   -----
   ## **БЛОК 6.2: ЛОГИРОВАНИЕ**
   ## **utils/logger.py**
   python

   **import** logging

   **import** os

   **from** datetime **import** datetime

   **from** pathlib **import** Path

   **class** TestLogger:

   `    `"""Система логирования для тестов"""

    

   `    `\_logger = None

    

   `    `@classmethod

   `    `**def** setup\_logger(cls, name="automation"):

   `        `"""

   `        `Настроить логирование (один раз)

        

   `        `Args:

   `            `name: имя логгера

        

   `        `Returns:

   `            `Logger объект

   `        `"""

   `        `**if** cls.\_logger **is** **not** None:

   `            `**return** cls.\_logger

        

   `        `*# Создать папку для логов если её нет*

   `        `log\_dir = Path("logs")

   `        `log\_dir.mkdir(exist\_ok=True)

        

   `        `*# Имя файла с датой и временем*

   `        `timestamp = datetime.now().strftime("%Y%m%d\_%H%M%S")

   `        `log\_file = log\_dir / f"test\_run\_{timestamp}.log"

        

   `        `*# Создать логгер*

   `        `logger = logging.getLogger(name)

   `        `logger.setLevel(logging.DEBUG)

        

   `        `*# Формат логов*

   `        `formatter = logging.Formatter(

   `            `'%(asctime)s - %(name)s - %(levelname)s - %(message)s',

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

   logger = TestLogger.setup\_logger()
   ## **Использование в тестах**
   python

   **from** utils.logger **import** logger

   **def** test\_login(driver):

   `    `logger.info("Начинаем тест авторизации")

    

   `    `**try**:

   `        `logger.debug(f"Открываем URL: {driver.current\_url}")

        

   `        `*# ... тест ...*

        

   `        `logger.info("✅ Тест авторизации пройден")

   `    `**except** Exception **as** e:

   `        `logger.error(f"❌ Ошибка в тесте: {e}", exc\_info=True)

   `        `**raise**

   -----
   ## **БЛОК 6.3: СКРИНШОТЫ И ВИДЕО**
   ## **Скриншоты при ошибках**
   conftest.py:

   python

   **import** pytest

   **from** pathlib **import** Path

   **from** datetime **import** datetime

   **from** utils.logger **import** logger

   @pytest.fixture

   **def** driver():

   `    `"""WebDriver с сохранением скриншотов при ошибке"""

   `    `**from** utils.driver\_factory **import** DriverFactory

    

   `    `browser = DriverFactory.create\_driver()

    

   `    `**yield** browser

    

   `    `browser.quit()

   @pytest.hookimpl(tryfirst=True, hookwrapper=True)

   **def** pytest\_runtest\_makereport(item, call):

   `    `"""

   `    `Hook для сохранения скриншота при падении теста

    

   `    `https://docs.pytest.org/en/stable/how\_to/skipping.html#condition-booleans

   `    `"""

   `    `outcome = **yield**

   `    `report = outcome.get\_result()

    

   `    `*# Если тест упал*

   `    `**if** report.failed **and** call.when == "call":

   `        `*# Получить driver из фикстуры*

   `        `driver = item.funcargs.get('driver')

        

   `        `**if** driver:

   `            `*# Создать папку для скриншотов*

   `            `screenshot\_dir = Path("screenshots")

   `            `screenshot\_dir.mkdir(exist\_ok=True)

            

   `            `*# Имя файла с датой и временем*

   `            `timestamp = datetime.now().strftime("%Y%m%d\_%H%M%S\_%f")[:-3]

   `            `test\_name = item.name

   `            `screenshot\_path = screenshot\_dir / f"{test\_name}\_{timestamp}.png"

            

   `            `*# Сохранить скриншот*

   `            `driver.save\_screenshot(str(screenshot\_path))

   `            `logger.error(f"📸 Скриншот сохранен: {screenshot\_path}")

   -----
   ## **БЛОК 6.4: PYTEST.INI И КОНФИГУРАЦИЯ**
   ## **pytest.ini**
   text

   [pytest]

   # Директория с тестами

   testpaths = tests

   # Файлы с тестами

   python\_files = test\_\*.py

   # Функции с тестами

   python\_functions = test\_\*

   # Классы с тестами

   python\_classes = Test\*

   # Маркеры

   markers =

   `    `smoke: Критичные тесты

   `    `regression: Регрессионные тесты

   `    `slow: Медленные тесты

   `    `ui: UI-тесты

   `    `api: API-тесты

   # Опции по умолчанию

   addopts = 

   `    `-v

   `    `--tb=short

   `    `--strict-markers

   `    `--disable-warnings

   # Минимальная версия

   minversion = 6.0

   -----
   ## **БЛОК 6.5: ALLURE ОТЧЕТЫ**
   ## **Установка**
   bash

   pip install allure-pytest

   *# На Windows нужен Java для Allure*
   ## **Использование в тестах**
   python

   **import** allure

   **from** utils.logger **import** logger

   @allure.feature("Authentication")

   @allure.story("Login")

   @allure.severity(allure.severity\_level.CRITICAL)

   **def** test\_successful\_login(login\_page):

   `    `"""Успешная авторизация"""

    

   `    `**with** allure.step("Заполняем username"):

   `        `login\_page.input\_text(login\_page.USERNAME\_INPUT, "standard\_user")

   `        `logger.info("Username заполнен")

    

   `    `**with** allure.step("Заполняем password"):

   `        `login\_page.input\_text(login\_page.PASSWORD\_INPUT, "secret\_sauce")

   `        `logger.info("Password заполнен")

    

   `    `**with** allure.step("Кликаем кнопку Login"):

   `        `login\_page.click(login\_page.LOGIN\_BUTTON)

   `        `logger.info("Кнопка Login нажата")

    

   `    `**with** allure.step("Проверяем URL"):

   `        `**assert** "inventory" **in** login\_page.get\_current\_url()

   `        `logger.info("✅ Авторизация успешна")

   @allure.description("Этот тест проверяет процесс авторизации с неправильными учетными данными")

   @allure.link("https://github.com/project/issues/123", name="Issue #123")

   @pytest.mark.parametrize("username,password", [

   `    `("", "secret\_sauce"),

   `    `("standard\_user", ""),

   ])

   **def** test\_login\_with\_empty\_fields(login\_page, username, password):

   `    `"""Авторизация с пустыми полями"""

   `    `login\_page.login(username, password)

   `    `**assert** login\_page.is\_error\_message\_displayed()
   ## **Запуск с Allure**
   bash

   *# Запустить тесты и сгенерировать Allure отчет*

   pytest --alluredir=allure-results

   *# Открыть отчет в браузере*

   allure serve allure-results

   -----
   ## **БЛОК 6.6: ПАРАЛЛЕЛЬНОЕ ВЫПОЛНЕНИЕ**
   ## **Установка pytest-xdist**
   bash

   pip install pytest-xdist
   ## **Запуск параллельно**
   bash

   *# Запустить на 4 потоках*

   pytest -n 4

   *# Автоматически использовать количество потоков = количество ядер ЦП*

   pytest -n auto

   *# С распределением по тестам (каждый тест на отдельном потоке)*

   pytest -n auto -dist loadscope
   ## **Конфигурация в pytest.ini**
   text

   [pytest]

   addopts = 

   `    `-n auto

   `    `--dist loadscope

   -----
   ## **БЛОК 6.7: CI/CD - GITHUB ACTIONS**
   ## **.github/workflows/tests.yml**
   text

   name: UI Tests

   on:

   `  `push:

   `    `branches: [ main, develop ]

   `  `pull\_request:

   `    `branches: [ main, develop ]

   jobs:

   `  `test:

   `    `runs-on: ubuntu-latest

    

   `    `strategy:

   `      `matrix:

   `        `python-version: [3.9, '3.10', 3.11]

    

   `    `steps:

   `    `- uses: actions/checkout@v3

    

   `    `- name: Set up Python ${{ matrix.python-version }}

   `      `uses: actions/setup-python@v4

   `      `with:

   `        `python-version: ${{ matrix.python-version }}

    

   `    `- name: Install dependencies

   `      `run: |

   `        `python -m pip install --upgrade pip

   `        `pip install -r requirements.txt

    

   `    `- name: Run tests

   `      `run: |

   `        `pytest -v --html=report.html --alluredir=allure-results

    

   `    `- name: Upload HTML report

   `      `if: always()

   `      `uses: actions/upload-artifact@v3

   `      `with:

   `        `name: html-report-${{ matrix.python-version }}

   `        `path: report.html

    

   `    `- name: Upload screenshots

   `      `if: failure()

   `      `uses: actions/upload-artifact@v3

   `      `with:

   `        `name: screenshots-${{ matrix.python-version }}

   `        `path: screenshots/

    

   `    `- name: Upload Allure results

   `      `if: always()

   `      `uses: actions/upload-artifact@v3

   `      `with:

   `        `name: allure-results-${{ matrix.python-version }}

   `        `path: allure-results/

   -----
   ## **БЛОК 6.8: REQUIREMENTS.TXT**
   text

   # Основное

   selenium>=4.0.0

   pytest>=7.0.0

   pytest-html>=3.0.0

   pytest-xdist>=2.0.0

   webdriver-manager>=3.0.0

   # Логирование и отчеты

   allure-pytest>=2.0.0

   # Утилиты

   requests>=2.0.0

   python-dotenv>=0.10.0

   # Форматирование

   black>=22.0.0

   flake8>=4.0.0

   pylint>=2.0.0

   # Для работы с различными форматами

   pyyaml>=5.0.0

   -----
   ## **НОВОЕ: БЛОК 6.9 - ПРОФЕССИОНАЛЬНАЯ ОТЛАДКА 🆕**
   ## **6.9.1: Чтение Stack Trace**
   Когда тест упадет, увидишь Stack Trace. Как его читать?

   text

   FAILED tests/test\_login.py::TestLogin::test\_login - TimeoutException: Message: 

   `   `no such element: Unable to locate element: {"method":"css selector","selector":".inventory\_item"}

   Как читать:

1. FAILED — тест упал
1. tests/test\_login.py::TestLogin::test\_login — какой тест упал
1. TimeoutException — какая ошибка
1. no such element — элемент не найден
1. css selector: ".inventory\_item" — какой селектор ты использовал
   ## **6.9.2: Анализ скриншотов**
   Когда сохранять скриншоты:

   python

   @pytest.hookimpl(tryfirst=True, hookwrapper=True)

   **def** pytest\_runtest\_makereport(item, call):

   `    `"""Сохранить скриншот при ошибке"""

   `    `outcome = **yield**

   `    `report = outcome.get\_result()

    

   `    `**if** report.failed:

   `        `driver = item.funcargs.get('driver')

   `        `**if** driver:

   `            `timestamp = datetime.now().strftime("%Y%m%d\_%H%M%S")

   `            `path = f"screenshots/{item.name}\_{timestamp}.png"

   `            `driver.save\_screenshot(path)

   `            `logger.error(f"Screenshot: {path}")
   ## **6.9.3: BrowserMob Proxy для отладки сетевых запросов**
   bash

   pip install browsermob-proxy

   python

   **from** browsermobproxy **import** Server

   @pytest.fixture

   **def** proxy():

   `    `"""Запустить proxy для отладки"""

   `    `server = Server("./browsermob-proxy/bin/browsermob-proxy")

   `    `server.start()

    

   `    `proxy = server.create\_proxy()

    

   `    `**yield** proxy

    

   `    `proxy.close()

   `    `server.stop()

   **def** test\_with\_proxy(proxy, driver):

   `    `"""Тест с отладкой сетевых запросов"""

   `    `proxy.new\_har("test")

    

   `    `driver.get("https://example.com")

    

   `    `*# Получить все запросы*

   `    `har = proxy.har

   `    `**for** entry **in** har['log']['entries']:

   `        `url = entry['request']['url']

   `        `status = entry['response']['status']

   `        `logger.info(f"Request: {url} - Status: {status}")
   ## **6.9.4: Allure с видео и diff-скриншотами**
   python

   @pytest.fixture

   **def** driver\_with\_video():

   `    `"""WebDriver с записью видео"""

   `    `**from** pyvirtualdisplay **import** Display

    

   `    `*# Запустить виртуальный дисплей*

   `    `display = Display(visible=0, size=(1920, 1080))

   `    `display.start()

    

   `    `driver = create\_driver()

    

   `    `**yield** driver

    

   `    `driver.quit()

   `    `display.stop()

   **def** test\_with\_allure\_attachment(driver):

   `    `"""Тест с Allure вложениями"""

   `    `**import** allure

    

   `    `driver.get("https://example.com")

    

   `    `*# Скриншот*

   `    `screenshot = driver.get\_screenshot\_as\_png()

   `    `allure.attach(

   `        `screenshot,

   `        `name="page\_screenshot",

   `        `attachment\_type=allure.attachment\_type.PNG

   `    `)

    

   `    `*# Логи*

   `    `logs = driver.get\_log('browser')

   `    `allure.attach(

   `        `str(logs),

   `        `name="browser\_logs",

   `        `attachment\_type=allure.attachment\_type.TEXT

   `    `)

    

   `    `*# HTML страницы*

   `    `page\_source = driver.page\_source

   `    `allure.attach(

   `        `page\_source,

   `        `name="page\_source",

   `        `attachment\_type=allure.attachment\_type.HTML

   `    `)
   ## **6.9.5: Логирование разных уровней**
   python

   **from** utils.logger **import** logger

   **def** test\_with\_detailed\_logging(driver):

   `    `"""Пример теста с логированием"""

    

   `    `logger.debug("DEBUG: Подробная отладка")

   `    `logger.info("INFO: Основная информация о ходе теста")

   `    `logger.warning("WARNING: Предупреждение о потенциальной проблеме")

   `    `logger.error("ERROR: Ошибка в тесте")

    

   `    `*# Получить значения и залогировать*

   `    `**try**:

   `        `element = driver.find\_element(By.ID, "some-element")

   `        `logger.debug(f"Element found: {element.tag\_name}")

   `        `logger.debug(f"Element text: {element.text}")

   `        `logger.debug(f"Element attributes: {element.get\_attribute('class')}")

   `    `**except** Exception **as** e:

   `        `logger.error(f"Failed to find element: {e}", exc\_info=True)

   `        `**raise**

    

   `    `logger.info("✅ Test passed")

   -----
   ## **ИТОГО: КАК ОРГАНИЗОВАТЬ ПРОЕКТ**
   text

   project/

   ├── config/

   │   └── settings.json           # Параметры

   │

   ├── utils/

   │   ├── config\_manager.py       # Загрузка конфига

   │   ├── driver\_factory.py        # Создание браузера

   │   └── logger.py               # Логирование

   │

   ├── pages/

   │   ├── base\_page.py            # Базовые методы

   │   ├── login\_page.py           # Страницы

   │   └── ...

   │

   ├── tests/

   │   ├── conftest.py             # Фикстуры

   │   ├── test\_login.py           # Тесты

   │   └── ...

   │

   ├── .github/workflows/

   │   └── tests.yml               # CI/CD

   │

   ├── logs/                       # Логи тестов

   ├── screenshots/                # Скриншоты при ошибках

   ├── allure-results/             # Allure отчеты

   │

   ├── pytest.ini                  # Конфигурация pytest

   ├── requirements.txt            # Зависимости

   └── README.md                   # Документация

   -----
   ## **ПРАКТИЧЕСКИЕ ЗАДАНИЯ**
   ## **Задание 6.1: Конфигурация**
   Создай файл config/settings.json с параметрами для твоего проекта.
   ## **Задание 6.2: ConfigManager**
   Напиши класс ConfigManager для загрузки конфига из JSON.
   ## **Задание 6.3: DriverFactory**
   Напиши класс DriverFactory для создания WebDriver с разными конфигурациями.
   ## **Задание 6.4: Логирование**
   Добавь логирование в тесты с разными уровнями (DEBUG, INFO, ERROR).
   ## **Задание 6.5: Скриншоты**
   Напиши код для сохранения скриншотов при падении тестов.
   ## **Задание 6.6: CI/CD**
   Создай GitHub Actions workflow для запуска тестов.
   ## **Задание 6.7: Параллельное выполнение**
   Запусти тесты параллельно с pytest-xdist.
   ## **Задание 6.8: Allure отчеты**
   Добавь Allure аннотации в тесты и сгенерируй отчет.

   === КОНЕЦ ФАЙЛА: module\_6\_infrastructure.md ===
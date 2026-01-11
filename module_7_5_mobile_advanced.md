=== НАЧАЛО ФАЙЛА: module\_7\_5\_mobile\_advanced.md ===
# **МОДУЛЬ 7.5: МОБИЛЬНОЕ ТЕСТИРОВАНИЕ И ПРОДВИНУТЫЕ ТЕХНИКИ 🆕**
## **Расширение фреймворка для мобильных и PWA приложений**
-----
## **ВВЕДЕНИЕ: ЗАЧЕМ НУЖНО МОБИЛЬНОЕ ТЕСТИРОВАНИЕ?**
## **Статистика трафика**
По состоянию на 2024 год:

- 60-70% трафика идет с мобильных устройств
- 40-50% продаж происходит через мобильные приложения
- 95% пользователей сначала проверяют сайт на мобильном

  Если не тестировать на мобильных — теряешь большую часть пользователей!

  -----
  ## **БЛОК 7.5.1: МОБИЛЬНОЕ WEB-ТЕСТИРОВАНИЕ**
  ## **Способ 1: Эмуляция в Chrome**
  Selenium может эмулировать мобильные устройства через Chrome DevTools.
  ## **📄 utils/mobile\_driver\_factory.py**
  python

  **from** selenium **import** webdriver

  **from** selenium.webdriver.chrome.options **import** Options

  **from** webdriver\_manager.chrome **import** ChromeDriverManager

  **from** selenium.webdriver.service **import** Service

  **class** MobileDriverFactory:

  `    `"""Фабрика для создания мобильного браузера"""

    

  `    `*# Популярные мобильные устройства*

  `    `DEVICES = {

  `        `"iPhone 12": {

  `            `"deviceName": "iPhone 12",

  `            `"userAgent": "Mozilla/5.0 (iPhone; CPU iPhone OS 14\_6 like Mac OS X)",

  `            `"width": 390,

  `            `"height": 844,

  `            `"deviceScaleFactor": 3,

  `            `"mobile": True,

  `            `"hasTouch": True

  `        `},

  `        `"iPhone 14": {

  `            `"deviceName": "iPhone 14",

  `            `"userAgent": "Mozilla/5.0 (iPhone; CPU iPhone OS 16\_0 like Mac OS X)",

  `            `"width": 390,

  `            `"height": 844,

  `            `"deviceScaleFactor": 3,

  `            `"mobile": True,

  `            `"hasTouch": True

  `        `},

  `        `"Samsung Galaxy S21": {

  `            `"deviceName": "Samsung Galaxy S21",

  `            `"userAgent": "Mozilla/5.0 (Linux; Android 11; Samsung Galaxy S21)",

  `            `"width": 360,

  `            `"height": 800,

  `            `"deviceScaleFactor": 2,

  `            `"mobile": True,

  `            `"hasTouch": True

  `        `},

  `        `"iPad": {

  `            `"deviceName": "iPad",

  `            `"userAgent": "Mozilla/5.0 (iPad; CPU OS 14\_6 like Mac OS X)",

  `            `"width": 1024,

  `            `"height": 1366,

  `            `"deviceScaleFactor": 2,

  `            `"mobile": True,

  `            `"hasTouch": True

  `        `}

  `    `}

    

  `    `@staticmethod

  `    `**def** create\_mobile\_driver(device="iPhone 12"):

  `        `"""

  `        `Создать мобильный браузер

        

  `        `Args:

  `            `device: имя устройства из DEVICES

        

  `        `Returns:

  `            `WebDriver мобильного браузера

  `        `"""

  `        `**if** device **not** **in** MobileDriverFactory.DEVICES:

  `            `**raise** ValueError(f"Неизвестное устройство: {device}")

        

  `        `device\_config = MobileDriverFactory.DEVICES[device]

        

  `        `options = Options()

        

  `        `*# Эмулировать мобильное устройство*

  `        `mobile\_emulation = {

  `            `"deviceName": device\_config["deviceName"],

  `            `"userAgent": device\_config["userAgent"],

  `            `"hardwareConcurrency": 4,

  `            `"deviceScaleFactor": device\_config["deviceScaleFactor"],

  `            `"mobile": device\_config["mobile"],

  `            `"hasTouch": device\_config["hasTouch"]

  `        `}

        

  `        `options.add\_experimental\_option("mobileEmulation", mobile\_emulation)

        

  `        `*# Отключить расширения*

  `        `options.add\_argument("--disable-extensions")

        

  `        `driver = webdriver.Chrome(

  `            `service=Service(ChromeDriverManager().install()),

  `            `options=options

  `        `)

        

  `        `driver.set\_window\_size(

  `            `device\_config["width"],

  `            `device\_config["height"]

  `        `)

        

  `        `**return** driver
  ## **Использование в тестах**
  python

  **import** pytest

  **from** utils.mobile\_driver\_factory **import** MobileDriverFactory

  @pytest.fixture(params=["iPhone 12", "Samsung Galaxy S21", "iPad"])

  **def** mobile\_driver(request):

  `    `"""Параметризованная фикстура для тестирования на разных устройствах"""

  `    `device = request.param

  `    `driver = MobileDriverFactory.create\_mobile\_driver(device)

  `    `**yield** driver

  `    `driver.quit()

  **def** test\_mobile\_responsive(mobile\_driver):

  `    `"""Тест на разных мобильных устройствах"""

  `    `mobile\_driver.get("https://www.saucedemo.com")

    

  `    `*# На мобильном браузер должен быть узким*

  `    `**assert** mobile\_driver.get\_window\_size()["width"] < 1024

    

  `    `*# Элементы должны быть видны*

  `    `products = mobile\_driver.find\_elements(By.CLASS\_NAME, "inventory\_item")

  `    `**assert** len(products) > 0
  ## **Способ 2: BrowserStack для реальных мобильных устройств**
  Если нужно тестировать на реальных устройствах, используй BrowserStack.

  bash

  pip install browserstack-local

  python

  **from** browserstack.local **import** Local

  **from** selenium **import** webdriver

  **class** BrowserStackMobileDriver:

  `    `"""Мобильный драйвер через BrowserStack"""

    

  `    `@staticmethod

  `    `**def** create\_driver(username, access\_key, device\_name="iPhone 14"):

  `        `"""

  `        `Создать браузер на реальном мобильном устройстве через BrowserStack

        

  `        `Args:

  `            `username: BrowserStack username

  `            `access\_key: BrowserStack access key

  `            `device\_name: имя устройства (iPhone 14, Samsung Galaxy S21 и т.д.)

        

  `        `Returns:

  `            `WebDriver реального устройства

  `        `"""

        

  `        `capabilities = {

  `            `"browserName": "Chrome",

  `            `"browserVersion": "latest",

  `            `"platformName": "Android" **if** "Samsung" **in** device\_name **else** "iOS",

  `            `"deviceName": device\_name,

  `            `"realMobile": True,

  `            `"os\_version": "latest",

  `            `"build": "Test Build",

  `            `"name": "Mobile Test"

  `        `}

        

  `        `*# BrowserStack URL*

  `        `url = f"https://{username}:{access\_key}@hub.browserstack.com/wd/hub"

        

  `        `driver = webdriver.Remote(

  `            `command\_executor=url,

  `            `desired\_capabilities=capabilities

  `        `)

        

  `        `**return** driver

  -----
  ## **БЛОК 7.5.2: RESPONSIVE-ТЕСТИРОВАНИЕ**
  ## **Что такое responsive?**
  Responsive — это когда сайт выглядит хорошо на всех размерах экрана.
  ## **Breakpoints (точки разрыва)**
  python

  **class** ResponsiveBreakpoints:

  `    `"""Стандартные breakpoints для responsive тестирования"""

    

  `    `BREAKPOINTS = {

  `        `"mobile\_small": {"width": 320, "height": 568},      *# iPhone SE*

  `        `"mobile\_medium": {"width": 375, "height": 667},     *# iPhone 8*

  `        `"mobile\_large": {"width": 414, "height": 896},      *# iPhone 11*

  `        `"tablet": {"width": 768, "height": 1024},           *# iPad*

  `        `"tablet\_large": {"width": 1024, "height": 1366},    *# iPad Pro*

  `        `"desktop": {"width": 1920, "height": 1080},         *# Desktop*

  `        `"desktop\_large": {"width": 2560, "height": 1440},   *# 2K*

  `    `}
  ## **Параметризованное responsive тестирование**
  python

  **import** pytest

  **from** selenium **import** webdriver

  @pytest.mark.parametrize("breakpoint\_name,width,height", [

  `    `("mobile\_small", 320, 568),

  `    `("mobile\_medium", 375, 667),

  `    `("tablet", 768, 1024),

  `    `("desktop", 1920, 1080),

  ])

  **def** test\_responsive\_design(breakpoint\_name, width, height):

  `    `"""Тест responsive дизайна на разных разрешениях"""

  `    `**from** utils.driver\_factory **import** DriverFactory

    

  `    `driver = DriverFactory.create\_driver()

  `    `driver.set\_window\_size(width, height)

    

  `    `**try**:

  `        `driver.get("https://www.saucedemo.com")

        

  `        `*# Проверить видимость элементов*

  `        `products = driver.find\_elements(By.CLASS\_NAME, "inventory\_item")

  `        `**assert** len(products) > 0, f"Товары не видны на {breakpoint\_name}"

        

  `        `*# Проверить видимость меню*

  `        `menu = driver.find\_element(By.CLASS\_NAME, "bm-burger-button")

  `        `**assert** menu.is\_displayed(), f"Меню не видно на {breakpoint\_name}"

        

  `    `**finally**:

  `        `driver.quit()

  -----
  ## **БЛОК 7.5.3: ТЕСТИРОВАНИЕ PWA (PROGRESSIVE WEB APPS)**
  ## **Что такое PWA?**
  PWA — веб-приложение, которое работает как нативное:

- Работает без интернета (offline)
- Добавляется на домашний экран
- Имеет свой значок
- Быстрое и отзывчивое
  ## **Проверка Service Worker**
  python

  **def** test\_service\_worker\_installed(driver):

  `    `"""Проверить, установлен ли Service Worker"""

    

  `    `*# Открыть сайт*

  `    `driver.get("https://www.example-pwa.com")

    

  `    `*# Проверить наличие Service Worker*

  `    `result = driver.execute\_script("""

  `        `return navigator.serviceWorker.getRegistrations().then(function(regs) {

  `            `return regs.length > 0 ? 'installed' : 'not\_installed';

  `        `});

  `    `""")

    

  `    `**assert** result == "installed", "Service Worker не установлен"
  ## **Работа offline**
  python

  **def** test\_pwa\_offline(driver):

  `    `"""Проверить работу PWA без интернета"""

    

  `    `*# Включить offline режим*

  `    `driver.execute\_cdp\_cmd('Network.emulateNetworkConditions', {

  `        `"offline": True,

  `        `"downloadThroughput": 0,

  `        `"uploadThroughput": 0,

  `        `"latency": 0

  `    `})

    

  `    `*# Загрузить страницу*

  `    `driver.get("https://www.example-pwa.com")

    

  `    `*# Проверить, что страница все еще загружается (благодаря cache)*

  `    `**assert** "offline" **not** **in** driver.page\_source.lower() **or** \

  `           `len(driver.find\_elements(By.TAG\_NAME, "h1")) > 0

    

  `    `*# Выключить offline режим*

  `    `driver.execute\_cdp\_cmd('Network.emulateNetworkConditions', {

  `        `"offline": False,

  `        `"downloadThroughput": -1,

  `        `"uploadThroughput": -1,

  `        `"latency": 0

  `    `})

  -----
  ## **БЛОК 7.5.4: РАБОТА С ГЕОЛОКАЦИЕЙ**
  ## **Эмуляция геолокации**
  python

  **def** test\_location\_based\_feature(driver):

  `    `"""Тестирование функции, использующей геолокацию"""

    

  `    `*# Задать координаты (Москва: 55.7558° N, 37.6173° E)*

  `    `driver.execute\_cdp\_cmd('Emulation.setGeolocationOverride', {

  `        `"latitude": 55.7558,

  `        `"longitude": 37.6173,

  `        `"accuracy": 100

  `    `})

    

  `    `driver.get("https://www.example-location-app.com")

    

  `    `*# Проверить, что приложение получило правильную локацию*

  `    `location\_text = driver.find\_element(By.ID, "location-text").text

  `    `**assert** "Москва" **in** location\_text **or** "Moscow" **in** location\_text
  ## **Отключение геолокации**
  python

  **def** test\_without\_location\_permission(driver):

  `    `"""Тестирование без разрешения на доступ к геолокации"""

    

  `    `*# Отклонить запрос на доступ к геолокации*

  `    `driver.execute\_cdp\_cmd('Emulation.setEmulatedMedia', {

  `        `"features": [{"name": "geolocation", "value": "denied"}]

  `    `})

    

  `    `driver.get("https://www.example-location-app.com")

    

  `    `*# Приложение должно показать дефолтную локацию*

  `    `**assert** "default location" **in** driver.page\_source.lower()

  -----
  ## **БЛОК 7.5.5: КАМЕРА И СЕНСОРЫ**
  ## **Эмуляция ориентации экрана**
  python

  **def** test\_portrait\_orientation(driver):

  `    `"""Тестирование в портретной ориентации"""

    

  `    `driver.set\_window\_size(375, 667)  *# iPhone*

  `    `driver.execute\_script("window.orientationchange = 0;")  *# Portrait*

    

  `    `driver.get("https://www.example.com")

    

  `    `*# Проверить макет для портретной ориентации*

  `    `main\_content = driver.find\_element(By.ID, "main")

  `    `**assert** main\_content.size["width"] < 400

  **def** test\_landscape\_orientation(driver):

  `    `"""Тестирование в ландшафтной ориентации"""

    

  `    `driver.set\_window\_size(667, 375)  *# Landscape*

  `    `driver.execute\_script("window.orientationchange = 90;")  *# Landscape*

    

  `    `driver.get("https://www.example.com")

    

  `    `*# Проверить макет для ландшафтной ориентации*

  `    `main\_content = driver.find\_element(By.ID, "main")

  `    `**assert** main\_content.size["width"] > 600
  ## **Эмуляция ускорения (accelerometer)**
  python

  **def** test\_device\_motion(driver):

  `    `"""Тестирование датчика движения"""

    

  `    `driver.get("https://www.example-motion-app.com")

    

  `    `*# Эмулировать встряску устройства*

  `    `driver.execute\_script("""

  `        `const event = new DeviceMotionEvent('devicemotion', {

  `            `acceleration: {x: 10, y: 20, z: 30},

  `            `accelerationIncludingGravity: {x: 10, y: 20, z: 30},

  `            `rotationRate: {alpha: 0, beta: 0, gamma: 0},

  `            `interval: 16

  `        `});

  `        `window.dispatchEvent(event);

  `    `""")

    

  `    `*# Проверить, что приложение обработало событие*

  `    `result = driver.find\_element(By.ID, "motion-result").text

  `    `**assert** "motion detected" **in** result.lower()

  -----
  ## **БЛОК 7.5.6: ПАРАЛЛЕЛЬНОЕ ВЫПОЛНЕНИЕ С PYTEST-XDIST**
  ## **Установка**
  bash

  pip install pytest-xdist
  ## **Распределение тестов**
  python

  *# Запустить на N потоках*

  *# pytest -n auto  # Использует количество ядер ЦП*

  *# Параметры распределения:*

  *# -n auto          # Использует все ядра ЦП*

  *# -n 4             # Использует 4 потока*

  *# --dist loadscope # Распределяет по классам (группирует похожие тесты)*

  *# --dist loadfile  # Распределяет по файлам*
  ## **Конфигурация в pytest.ini**
  text

  [pytest]

  addopts = 

  `    `-n auto

  `    `--dist loadscope

  `    `-v
  ## **Пример параллельного выполнения**
  python

  *# Все эти тесты будут запускаться параллельно:*

  **class** TestLogin:

  `    `**def** test\_login\_1(self): **pass**

  `    `**def** test\_login\_2(self): **pass**

  **class** TestProducts:

  `    `**def** test\_products\_1(self): **pass**

  `    `**def** test\_products\_2(self): **pass**

  **class** TestCheckout:

  `    `**def** test\_checkout\_1(self): **pass**

  `    `**def** test\_checkout\_2(self): **pass**

  *# Запуск: pytest -n 3*

  *# Тесты распределятся по 3 потокам*

  -----
  ## **БЛОК 7.5.7: GITHUB ACTIONS MATRIX**
  ## **Множественные конфигурации в CI/CD**
  .github/workflows/mobile-tests.yml:

  text

  name: Mobile Tests Matrix

  on: [push, pull\_request]

  jobs:

  `  `mobile-tests:

  `    `runs-on: ubuntu-latest

    

  `    `strategy:

  `      `matrix:

  `        `device: [iPhone 12, Samsung Galaxy S21, iPad]

  `        `python-version: [3.9, '3.10']

    

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

    

  `    `- name: Run mobile tests

  `      `run: |

  `        `DEVICE=${{ matrix.device }} pytest tests/test\_mobile.py -v

    

  `    `- name: Upload report

  `      `if: always()

  `      `uses: actions/upload-artifact@v3

  `      `with:

  `        `name: report-${{ matrix.device }}-${{ matrix.python-version }}

  `        `path: report.html
  ## **Параллельные браузеры**
  .github/workflows/parallel-browsers.yml:

  text

  name: Tests on Multiple Browsers

  on: [push, pull\_request]

  jobs:

  `  `test:

  `    `runs-on: ubuntu-latest

  `    `strategy:

  `      `matrix:

  `        `browser: [chrome, firefox, edge]

    

  `    `steps:

  `    `- uses: actions/checkout@v3

    

  `    `- name: Set up Python

  `      `uses: actions/setup-python@v4

  `      `with:

  `        `python-version: '3.10'

    

  `    `- name: Install dependencies

  `      `run: pip install -r requirements.txt

    

  `    `- name: Run tests on ${{ matrix.browser }}

  `      `run: BROWSER=${{ matrix.browser }} pytest -v

  -----
  ## **БЛОК 7.5.8: ПРОДВИНУТЫЕ СЦЕНАРИИ**
  ## **Touch события**
  python

  **def** test\_touch\_swipe(mobile\_driver):

  `    `"""Тестирование свайпа (touch события)"""

  `    `**from** selenium.webdriver.common.action\_chains **import** ActionChains

  `    `**from** selenium.webdriver.common.touch\_actions **import** TouchActions

    

  `    `mobile\_driver.get("https://www.saucedemo.com")

    

  `    `*# Найти элемент*

  `    `element = mobile\_driver.find\_element(By.CLASS\_NAME, "inventory\_item")

    

  `    `*# Выполнить свайп*

  `    `actions = ActionChains(mobile\_driver)

  `    `actions.move\_to\_element(element).perform()

    

  `    `*# TouchActions для мобильного*

  `    `touch\_actions = TouchActions(mobile\_driver)

  `    `touch\_actions.swipe(element, 0, -100)  *# Свайп вверх*

  `    `touch\_actions.perform()
  ## **Двойной тап**
  python

  **def** test\_double\_tap(mobile\_driver):

  `    `"""Тестирование двойного тапа"""

  `    `**from** selenium.webdriver.common.action\_chains **import** ActionChains

    

  `    `mobile\_driver.get("https://www.example.com")

    

  `    `element = mobile\_driver.find\_element(By.ID, "double-tap-element")

    

  `    `*# Двойной тап*

  `    `actions = ActionChains(mobile\_driver)

  `    `actions.double\_click(element).perform()

    

  `    `*# Проверить результат*

  `    `**assert** "activated" **in** element.get\_attribute("class")
  ## **Долгий нажим (long press)**
  python

  **def** test\_long\_press(mobile\_driver):

  `    `"""Тестирование долгого нажима"""

  `    `**from** selenium.webdriver.common.action\_chains **import** ActionChains

  `    `**import** time

    

  `    `mobile\_driver.get("https://www.example.com")

    

  `    `element = mobile\_driver.find\_element(By.ID, "long-press-element")

    

  `    `*# Долгий нажим (600ms)*

  `    `actions = ActionChains(mobile\_driver)

  `    `actions.click\_and\_hold(element)

  `    `time.sleep(0.6)

  `    `actions.release()

  `    `actions.perform()

    

  `    `*# Проверить контекстное меню*

  `    `context\_menu = mobile\_driver.find\_element(By.CLASS\_NAME, "context-menu")

  `    `**assert** context\_menu.is\_displayed()
  ## **Скролл на мобильном**
  python

  **def** test\_mobile\_scroll(mobile\_driver):

  `    `"""Тестирование скролла на мобильном"""

    

  `    `mobile\_driver.get("https://www.saucedemo.com")

    

  `    `*# Скролл вниз*

  `    `mobile\_driver.execute\_script("window.scrollBy(0, 500);")

    

  `    `*# Проверить, что элементы загружены*

  `    `products = mobile\_driver.find\_elements(By.CLASS\_NAME, "inventory\_item")

  `    `**assert** len(products) > 0
  ## **Переключение между вкладками**
  python

  **def** test\_multiple\_tabs\_mobile(mobile\_driver):

  `    `"""Тестирование работы с несколькими вкладками"""

    

  `    `mobile\_driver.get("https://www.example.com")

    

  `    `*# Открыть новую вкладку*

  `    `mobile\_driver.execute\_script("window.open('https://www.google.com');")

    

  `    `*# Получить все вкладки*

  `    `all\_tabs = mobile\_driver.window\_handles

    

  `    `*# Переключиться на новую вкладку*

  `    `mobile\_driver.switch\_to.window(all\_tabs[-1])

    

  `    `**assert** "google" **in** mobile\_driver.current\_url.lower()

    

  `    `*# Вернуться на первую вкладку*

  `    `mobile\_driver.switch\_to.window(all\_tabs[0])

  -----
  ## **ИТОГО: МОБИЛЬНОЕ ТЕСТИРОВАНИЕ**
  ✅ Эмуляция в Chrome — быстро и просто\
  ✅ BrowserStack — реальные устройства\
  ✅ Responsive тестирование — разные разрешения\
  ✅ PWA тестирование — offline, Service Worker\
  ✅ Геолокация и сенсоры — эмуляция устройства\
  ✅ Touch события — свайпы, тапы, долгий нажим\
  ✅ Параллельное выполнение — быстрый запуск\
  ✅ CI/CD Matrix — автоматизация

  -----
  ## **ПРАКТИЧЕСКИЕ ЗАДАНИЯ**
  ## **Задание 7.5.1: Мобильная эмуляция**
  Напиши тест, который запускается на 3 разных мобильных устройствах (параметризация).
  ## **Задание 7.5.2: Responsive тестирование**
  Создай параметризованный тест, проверяющий сайт на разных разрешениях экрана.
  ## **Задание 7.5.3: PWA Service Worker**
  Напиши тест, проверяющий установку Service Worker на PWA сайте.
  ## **Задание 7.5.4: Геолокация**
  Напиши тест, эмулирующий геолокацию и проверяющий результат.
  ## **Задание 7.5.5: Touch события**
  Напиши тест свайпа на мобильном устройстве.
  ## **Задание 7.5.6: Параллельное выполнение**
  Запусти все тесты параллельно с pytest-xdist.
  ## **Задание 7.5.7: GitHub Actions Matrix**
  Создай workflow для запуска тестов на разных устройствах и браузерах.
  ## **Задание 7.5.8: Ориентация экрана**
  Напиши тесты для портретной и ландшафтной ориентации.

  === КОНЕЦ ФАЙЛА: module\_7\_5\_mobile\_advanced.md ===

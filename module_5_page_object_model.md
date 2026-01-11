=== НАЧАЛО ФАЙЛА: module\_5\_page\_object\_model.md ===
# **МОДУЛЬ 5: SELENIUM + PYTEST ВМЕСТЕ. PAGE OBJECT MODEL (4–5 дней)**
## **Полное объяснение с теорией и примерами**
-----
## **ВВЕДЕНИЕ: ОБЪЕДИНЕНИЕ SELENIUM И PYTEST**
## **Проблема: смешанный код**
Когда пишешь Selenium тесты без структуры, получается грязный код:

python

*# Плохо: все смешано в одном месте*

**def** test\_user\_login():

`    `driver = webdriver.Chrome()

`    `driver.get("https://example.com")



`    `*# Поиск элементов*

`    `username\_field = driver.find\_element(By.ID, "username")

`    `password\_field = driver.find\_element(By.ID, "password")

`    `login\_button = driver.find\_element(By.ID, "login-button")



`    `*# Действия*

`    `username\_field.send\_keys("admin")

`    `password\_field.send\_keys("secret")

`    `login\_button.click()



`    `*# Ожидание*

`    `wait = WebDriverWait(driver, 10)

`    `wait.until(EC.url\_contains("dashboard"))



`    `*# Проверка*

`    `**assert** "dashboard" **in** driver.current\_url



`    `driver.quit()

Проблемы:

1. Много кода в одной функции (сложно читать)
1. Повторение поиска элементов в разных тестах
1. Селекторы жестко закодированы в тестах
1. Сложно изменять селекторы (нужно менять везде)
1. Нет отделения деталей реализации от логики теста
   ## **Решение: Page Object Model (POM)**
   Page Object Model — это паттерн проектирования, который:

- Отделяет детали страницы (селекторы, элементы) от логики теста
- Каждая страница представлена классом
- Класс содержит селекторы и методы для взаимодействия
- Тесты используют методы класса, не зная о селекторах

  python

  *# Хорошо: отделение логики от деталей*

  **class** LoginPage:

  `    `**def** \_\_init\_\_(self, driver):

  `        `self.driver = driver

    

  `    `**def** login(self, username, password):

  `        `*# Поиск элементов*

  `        `username\_field = self.driver.find\_element(By.ID, "username")

  `        `password\_field = self.driver.find\_element(By.ID, "password")

  `        `login\_button = self.driver.find\_element(By.ID, "login-button")

        

  `        `*# Действия*

  `        `username\_field.send\_keys(username)

  `        `password\_field.send\_keys(password)

  `        `login\_button.click()

        

  `        `*# Ожидание*

  `        `wait = WebDriverWait(self.driver, 10)

  `        `wait.until(EC.url\_contains("dashboard"))

  **def** test\_user\_login(driver):

  `    `page = LoginPage(driver)

  `    `page.login("admin", "secret")

    

  `    `**assert** "dashboard" **in** driver.current\_url

  Видишь разницу? Тест стал чище и проще!

  -----
  ## **БЛОК 5.1: БАЗОВАЯ АРХИТЕКТУРА ПРОЕКТА**
  ## **Структура проекта**
  text

  ui\_automation\_project/

  ├── tests/

  │   ├── \_\_init\_\_.py

  │   ├── conftest.py                 # Общие фикстуры

  │   ├── test\_login.py

  │   ├── test\_products.py

  │   └── test\_checkout.py

  │

  ├── pages/

  │   ├── \_\_init\_\_.py

  │   ├── base\_page.py               # Базовые методы для всех страниц

  │   ├── login\_page.py              # Страница авторизации

  │   ├── products\_page.py           # Страница товаров

  │   └── checkout\_page.py           # Страница оформления заказа

  │

  ├── pytest.ini                      # Конфигурация pytest

  ├── requirements.txt               # Зависимости

  └── README.md                      # Документация

  -----
  ## **БЛОК 5.2: BASE PAGE — БАЗОВЫЕ МЕТОДЫ**
  ## **Зачем нужна базовая страница?**
  У всех страниц есть общие действия:

- Поиск элемента
- Ожидание элемента
- Клик
- Ввод текста
- И т.д.

  Вместо того чтобы повторять код в каждом классе страницы, создадим базовый класс с общими методами.
  ## **Реализация BasePage**
  pages/base\_page.py:

  python

  **from** selenium.webdriver.support.ui **import** WebDriverWait

  **from** selenium.webdriver.support **import** expected\_conditions **as** EC

  **from** selenium.webdriver.common.action\_chains **import** ActionChains

  **class** BasePage:

  `    `"""

  `    `Базовый класс для всех страниц.

  `    `Содержит общие методы для работы с элементами.

  `    `"""

    

  `    `**def** \_\_init\_\_(self, driver, timeout=10):

  `        `"""

  `        `Инициализация страницы

        

  `        `Args:

  `            `driver: WebDriver браузера

  `            `timeout: таймаут для ожидания элементов (по умолчанию 10 сек)

  `        `"""

  `        `self.driver = driver

  `        `self.timeout = timeout

  `        `self.wait = WebDriverWait(driver, timeout)

    

  `    `*# ПОИСК ЭЛЕМЕНТОВ*

    

  `    `**def** find\_element(self, locator):

  `        `"""

  `        `Найти один элемент

        

  `        `Args:

  `            `locator: кортеж (By.ID, "element\_id") или (By.CSS\_SELECTOR, ".class")

        

  `        `Returns:

  `            `WebElement

  `        `"""

  `        `**return** self.driver.find\_element(\*locator)

    

  `    `**def** find\_elements(self, locator):

  `        `"""

  `        `Найти все элементы

        

  `        `Args:

  `            `locator: кортеж селектора

        

  `        `Returns:

  `            `Список WebElement

  `        `"""

  `        `**return** self.driver.find\_elements(\*locator)

    

  `    `**def** wait\_for\_element(self, locator):

  `        `"""

  `        `Ждать, пока элемент станет видимым

        

  `        `Args:

  `            `locator: кортеж селектора

        

  `        `Returns:

  `            `WebElement

  `        `"""

  `        `**return** self.wait.until(EC.visibility\_of\_element\_located(locator))

    

  `    `**def** wait\_for\_element\_clickable(self, locator):

  `        `"""

  `        `Ждать, пока элемент станет кликабельным

        

  `        `Args:

  `            `locator: кортеж селектора

        

  `        `Returns:

  `            `WebElement

  `        `"""

  `        `**return** self.wait.until(EC.element\_to\_be\_clickable(locator))

    

  `    `**def** wait\_for\_element\_to\_disappear(self, locator):

  `        `"""

  `        `Ждать, пока элемент исчезнет

        

  `        `Args:

  `            `locator: кортеж селектора

        

  `        `Returns:

  `            `True если элемент исчез

  `        `"""

  `        `**return** self.wait.until(EC.invisibility\_of\_element\_located(locator))

    

  `    `*# ВЗАИМОДЕЙСТВИЕ С ЭЛЕМЕНТАМИ*

    

  `    `**def** click(self, locator):

  `        `"""

  `        `Кликнуть по элементу (с ожиданием)

        

  `        `Args:

  `            `locator: кортеж селектора

  `        `"""

  `        `element = self.wait\_for\_element\_clickable(locator)

  `        `element.click()

    

  `    `**def** input\_text(self, locator, text):

  `        `"""

  `        `Ввести текст в элемент

        

  `        `Args:

  `            `locator: кортеж селектора

  `            `text: текст для ввода

  `        `"""

  `        `element = self.find\_element(locator)

  `        `element.clear()

  `        `element.send\_keys(text)

    

  `    `**def** get\_text(self, locator):

  `        `"""

  `        `Получить текст элемента

        

  `        `Args:

  `            `locator: кортеж селектора

        

  `        `Returns:

  `            `Текст элемента

  `        `"""

  `        `element = self.find\_element(locator)

  `        `**return** element.text

    

  `    `**def** get\_attribute(self, locator, attribute\_name):

  `        `"""

  `        `Получить значение атрибута элемента

        

  `        `Args:

  `            `locator: кортеж селектора

  `            `attribute\_name: имя атрибута

        

  `        `Returns:

  `            `Значение атрибута

  `        `"""

  `        `element = self.find\_element(locator)

  `        `**return** element.get\_attribute(attribute\_name)

    

  `    `**def** is\_element\_displayed(self, locator):

  `        `"""

  `        `Проверить, видим ли элемент

        

  `        `Args:

  `            `locator: кортеж селектора

        

  `        `Returns:

  `            `True если видим, False иначе

  `        `"""

  `        `**try**:

  `            `element = self.find\_element(locator)

  `            `**return** element.is\_displayed()

  `        `**except**:

  `            `**return** False

    

  `    `**def** is\_element\_enabled(self, locator):

  `        `"""

  `        `Проверить, активен ли элемент

        

  `        `Args:

  `            `locator: кортеж селектора

        

  `        `Returns:

  `            `True если активен, False иначе

  `        `"""

  `        `element = self.find\_element(locator)

  `        `**return** element.is\_enabled()

    

  `    `*# НАВИГАЦИЯ*

    

  `    `**def** go\_to(self, url):

  `        `"""

  `        `Перейти на URL

        

  `        `Args:

  `            `url: полный URL или путь

  `        `"""

  `        `self.driver.get(url)

    

  `    `**def** get\_current\_url(self):

  `        `"""

  `        `Получить текущий URL

        

  `        `Returns:

  `            `Текущий URL

  `        `"""

  `        `**return** self.driver.current\_url

    

  `    `**def** get\_page\_title(self):

  `        `"""

  `        `Получить заголовок страницы

        

  `        `Returns:

  `            `Заголовок страницы

  `        `"""

  `        `**return** self.driver.title

    

  `    `*# СКРОЛЛ*

    

  `    `**def** scroll\_to\_element(self, locator):

  `        `"""

  `        `Прокрутить страницу к элементу

        

  `        `Args:

  `            `locator: кортеж селектора

  `        `"""

  `        `element = self.find\_element(locator)

  `        `self.driver.execute\_script("arguments[0].scrollIntoView();", element)

    

  `    `**def** scroll\_to\_bottom(self):

  `        `"""Прокрутить до конца страницы"""

  `        `self.driver.execute\_script("window.scrollTo(0, document.body.scrollHeight);")

    

  `    `*# JAVASCRIPT*

    

  `    `**def** execute\_script(self, script, \*args):

  `        `"""

  `        `Выполнить JavaScript код

        

  `        `Args:

  `            `script: JavaScript код

  `            `\*args: аргументы для скрипта

        

  `        `Returns:

  `            `Результат выполнения скрипта

  `        `"""

  `        `**return** self.driver.execute\_script(script, \*args)

  -----
  ## **БЛОК 5.3: PAGE OBJECTS — КОНКРЕТНЫЕ СТРАНИЦЫ**
  ## **Страница авторизации (LoginPage)**
  pages/login\_page.py:

  python

  **from** selenium.webdriver.common.by **import** By

  **from** pages.base\_page **import** BasePage

  **class** LoginPage(BasePage):

  `    `"""

  `    `Page Object для страницы авторизации

  `    `https://www.saucedemo.com

  `    `"""

    

  `    `*# СЕЛЕКТОРЫ*

  `    `USERNAME\_INPUT = (By.ID, "user-name")

  `    `PASSWORD\_INPUT = (By.ID, "password")

  `    `LOGIN\_BUTTON = (By.ID, "login-button")

  `    `ERROR\_MESSAGE = (By.CLASS\_NAME, "error-message-container")

    

  `    `*# МЕТОДЫ ВЫСОКОГО УРОВНЯ*

    

  `    `**def** login(self, username, password):

  `        `"""

  `        `Выполнить авторизацию

        

  `        `Args:

  `            `username: имя пользователя

  `            `password: пароль

  `        `"""

  `        `self.input\_text(self.USERNAME\_INPUT, username)

  `        `self.input\_text(self.PASSWORD\_INPUT, password)

  `        `self.click(self.LOGIN\_BUTTON)

    

  `    `**def** is\_error\_message\_displayed(self):

  `        `"""

  `        `Проверить, отображается ли сообщение об ошибке

        

  `        `Returns:

  `            `True если ошибка видна, False иначе

  `        `"""

  `        `**return** self.is\_element\_displayed(self.ERROR\_MESSAGE)

    

  `    `**def** get\_error\_message\_text(self):

  `        `"""

  `        `Получить текст сообщения об ошибке

        

  `        `Returns:

  `            `Текст ошибки

  `        `"""

  `        `**return** self.get\_text(self.ERROR\_MESSAGE)
  ## **Страница товаров (ProductsPage)**
  pages/products\_page.py:

  python

  **from** selenium.webdriver.common.by **import** By

  **from** pages.base\_page **import** BasePage

  **class** ProductsPage(BasePage):

  `    `"""

  `    `Page Object для страницы товаров

  `    `"""

    

  `    `*# СЕЛЕКТОРЫ*

  `    `PRODUCT\_ITEM = (By.CLASS\_NAME, "inventory\_item")

  `    `PRODUCT\_NAME = (By.CLASS\_NAME, "inventory\_item\_name")

  `    `ADD\_TO\_CART\_BUTTON = (By.CSS\_SELECTOR, "button[id\*='add-to-cart']")

  `    `CART\_BADGE = (By.CLASS\_NAME, "shopping\_cart\_badge")

  `    `CART\_LINK = (By.CLASS\_NAME, "shopping\_cart\_link")

    

  `    `*# МЕТОДЫ*

    

  `    `**def** get\_products\_count(self):

  `        `"""

  `        `Получить количество товаров на странице

        

  `        `Returns:

  `            `Количество товаров

  `        `"""

  `        `products = self.find\_elements(self.PRODUCT\_ITEM)

  `        `**return** len(products)

    

  `    `**def** get\_product\_names(self):

  `        `"""

  `        `Получить список имен товаров

        

  `        `Returns:

  `            `Список имен товаров

  `        `"""

  `        `names = []

  `        `products = self.find\_elements(self.PRODUCT\_NAME)

  `        `**for** product **in** products:

  `            `names.append(product.text)

  `        `**return** names

    

  `    `**def** add\_product\_to\_cart\_by\_name(self, product\_name):

  `        `"""

  `        `Добавить товар в корзину по имени

        

  `        `Args:

  `            `product\_name: имя товара (например, "Sauce Labs Backpack")

  `        `"""

  `        `*# Найти товар по имени*

  `        `product\_xpath = f"//div[@class='inventory\_item']//\*[text()='{product\_name}']"

  `        `self.scroll\_to\_element((By.XPATH, product\_xpath))

        

  `        `*# Найти кнопку "Add to cart" для этого товара*

  `        `*# (обычно находится в том же родительском контейнере)*

  `        `add\_button\_xpath = f"//div[@class='inventory\_item']//\*[text()='{product\_name}']/..//button[contains(@id, 'add-to-cart')]"

  `        `self.click((By.XPATH, add\_button\_xpath))

    

  `    `**def** get\_cart\_items\_count(self):

  `        `"""

  `        `Получить количество товаров в корзине (из badge)

        

  `        `Returns:

  `            `Количество товаров в корзине (или 0 если корзина пуста)

  `        `"""

  `        `**try**:

  `            `badge\_text = self.get\_text(self.CART\_BADGE)

  `            `**return** int(badge\_text)

  `        `**except**:

  `            `**return** 0  *# Если нет badge, корзина пуста*

    

  `    `**def** go\_to\_cart(self):

  `        `"""Перейти в корзину"""

  `        `self.click(self.CART\_LINK)
  ## **Страница оформления заказа (CheckoutPage)**
  pages/checkout\_page.py:

  python

  **from** selenium.webdriver.common.by **import** By

  **from** pages.base\_page **import** BasePage

  **class** CheckoutPage(BasePage):

  `    `"""

  `    `Page Object для страницы оформления заказа

  `    `"""

    

  `    `*# СЕЛЕКТОРЫ*

  `    `CHECKOUT\_BUTTON = (By.ID, "checkout")

  `    `FIRST\_NAME\_INPUT = (By.ID, "first-name")

  `    `LAST\_NAME\_INPUT = (By.ID, "last-name")

  `    `POSTAL\_CODE\_INPUT = (By.ID, "postal-code")

  `    `CONTINUE\_BUTTON = (By.ID, "continue")

  `    `FINISH\_BUTTON = (By.ID, "finish")

  `    `COMPLETE\_HEADER = (By.CLASS\_NAME, "complete-header")

  `    `CART\_ITEMS = (By.CLASS\_NAME, "cart\_item")

    

  `    `*# МЕТОДЫ*

    

  `    `**def** click\_checkout(self):

  `        `"""Нажать кнопку Checkout"""

  `        `self.click(self.CHECKOUT\_BUTTON)

    

  `    `**def** fill\_shipping\_info(self, first\_name, last\_name, postal\_code):

  `        `"""

  `        `Заполнить информацию доставки

        

  `        `Args:

  `            `first\_name: имя

  `            `last\_name: фамилия

  `            `postal\_code: почтовый код

  `        `"""

  `        `self.input\_text(self.FIRST\_NAME\_INPUT, first\_name)

  `        `self.input\_text(self.LAST\_NAME\_INPUT, last\_name)

  `        `self.input\_text(self.POSTAL\_CODE\_INPUT, postal\_code)

  `        `self.click(self.CONTINUE\_BUTTON)

    

  `    `**def** finish\_checkout(self):

  `        `"""Завершить оформление заказа"""

  `        `self.click(self.FINISH\_BUTTON)

    

  `    `**def** is\_checkout\_complete(self):

  `        `"""

  `        `Проверить, завершен ли заказ

        

  `        `Returns:

  `            `True если заказ завершен, False иначе

  `        `"""

  `        `**return** self.is\_element\_displayed(self.COMPLETE\_HEADER)

    

  `    `**def** get\_cart\_items\_count(self):

  `        `"""

  `        `Получить количество товаров в корзине

        

  `        `Returns:

  `            `Количество товаров

  `        `"""

  `        `items = self.find\_elements(self.CART\_ITEMS)

  `        `**return** len(items)

  -----
  ## **БЛОК 5.4: НАПИСАНИЕ ТЕСТОВ С PAGE OBJECTS**
  ## **conftest.py — общие фикстуры**
  python

  **import** pytest

  **from** selenium **import** webdriver

  **from** webdriver\_manager.chrome **import** ChromeDriverManager

  **from** selenium.webdriver.service **import** Service

  **from** pages.login\_page **import** LoginPage

  @pytest.fixture

  **def** driver():

  `    `"""Создать браузер для теста"""

  `    `browser = webdriver.Chrome(service=Service(ChromeDriverManager().install()))

  `    `browser.implicitly\_wait(10)

  `    `**yield** browser

  `    `browser.quit()

  @pytest.fixture

  **def** login\_page(driver):

  `    `"""Открыть страницу авторизации"""

  `    `driver.get("https://www.saucedemo.com")

  `    `**return** LoginPage(driver)

  @pytest.fixture

  **def** logged\_in\_user(login\_page):

  `    `"""Авторизованный пользователь"""

  `    `login\_page.login("standard\_user", "secret\_sauce")

  `    `**return** login\_page.driver
  ## **test\_login.py**
  python

  **import** pytest

  **from** pages.login\_page **import** LoginPage

  **class** TestLogin:

  `    `"""Тесты для страницы авторизации"""

    

  `    `**def** test\_successful\_login(self, login\_page):

  `        `"""Успешная авторизация"""

  `        `*# ARRANGE - страница уже открыта в фикстуре*

        

  `        `*# ACT*

  `        `login\_page.login("standard\_user", "secret\_sauce")

        

  `        `*# ASSERT*

  `        `**assert** "inventory" **in** login\_page.get\_current\_url()

    

  `    `@pytest.mark.parametrize("username,password", [

  `        `("", "secret\_sauce"),

  `        `("standard\_user", ""),

  `        `("wrong\_user", "secret\_sauce"),

  `    `])

  `    `**def** test\_login\_with\_invalid\_credentials(self, login\_page, username, password):

  `        `"""Авторизация с неправильными учетными данными"""

  `        `*# ACT*

  `        `login\_page.login(username, password)

        

  `        `*# ASSERT*

  `        `**assert** login\_page.is\_error\_message\_displayed()

  `        `**assert** "Epic sadface" **in** login\_page.get\_error\_message\_text()
  ## **test\_products.py**
  python

  **import** pytest

  **from** pages.products\_page **import** ProductsPage

  **from** pages.checkout\_page **import** CheckoutPage

  **class** TestProducts:

  `    `"""Тесты для страницы товаров"""

    

  `    `**def** test\_products\_are\_displayed(self, logged\_in\_user):

  `        `"""Товары отображаются на странице"""

  `        `products\_page = ProductsPage(logged\_in\_user)

        

  `        `*# ASSERT*

  `        `**assert** products\_page.get\_products\_count() > 0

    

  `    `**def** test\_add\_product\_to\_cart(self, logged\_in\_user):

  `        `"""Добавление товара в корзину"""

  `        `products\_page = ProductsPage(logged\_in\_user)

        

  `        `*# ACT*

  `        `initial\_count = products\_page.get\_cart\_items\_count()

  `        `products\_page.add\_product\_to\_cart\_by\_name("Sauce Labs Backpack")

        

  `        `*# ASSERT*

  `        `**assert** products\_page.get\_cart\_items\_count() == initial\_count + 1

    

  `    `**def** test\_full\_checkout\_flow(self, logged\_in\_user):

  `        `"""Полный процесс оформления заказа"""

  `        `products\_page = ProductsPage(logged\_in\_user)

        

  `        `*# Добавить товар*

  `        `products\_page.add\_product\_to\_cart\_by\_name("Sauce Labs Backpack")

        

  `        `*# Перейти в корзину*

  `        `products\_page.go\_to\_cart()

        

  `        `*# Оформить заказ*

  `        `checkout\_page = CheckoutPage(logged\_in\_user)

  `        `checkout\_page.click\_checkout()

  `        `checkout\_page.fill\_shipping\_info("John", "Doe", "12345")

  `        `checkout\_page.finish\_checkout()

        

  `        `*# ASSERT*

  `        `**assert** checkout\_page.is\_checkout\_complete()

  -----
  ## **БЛОК 5.5: ЛУЧШИЕ ПРАКТИКИ POM**
  ## **1. Разделение ответственности**
  Плохо:

  python

  **class** LoginPage:

  `    `**def** login\_and\_verify(self, username, password):

  `        `"""Делает две вещи: логинит И проверяет"""

  `        `self.input\_text(self.USERNAME\_INPUT, username)

  `        `self.input\_text(self.PASSWORD\_INPUT, password)

  `        `self.click(self.LOGIN\_BUTTON)

        

  `        `*# Проверка (это не задача Page Object!)*

  `        `**assert** "inventory" **in** self.driver.current\_url

  Хорошо:

  python

  **class** LoginPage:

  `    `**def** login(self, username, password):

  `        `"""Только логинит, не проверяет"""

  `        `self.input\_text(self.USERNAME\_INPUT, username)

  `        `self.input\_text(self.PASSWORD\_INPUT, password)

  `        `self.click(self.LOGIN\_BUTTON)

  *# Проверка делается в тесте*

  **def** test\_login(login\_page):

  `    `login\_page.login("admin", "secret")

  `    `**assert** "inventory" **in** login\_page.get\_current\_url()
  ## **2. Возвращение Page Objects**
  Если метод переходит на другую страницу, верни новый Page Object:

  python

  **class** LoginPage(BasePage):

  `    `**def** login(self, username, password):

  `        `"""Вход и переход на страницу товаров"""

  `        `self.input\_text(self.USERNAME\_INPUT, username)

  `        `self.input\_text(self.PASSWORD\_INPUT, password)

  `        `self.click(self.LOGIN\_BUTTON)

        

  `        `*# Возвращаем новую страницу*

  `        `**from** pages.products\_page **import** ProductsPage

  `        `**return** ProductsPage(self.driver)

  *# Использование*

  **def** test\_login(login\_page):

  `    `products\_page = login\_page.login("admin", "secret")

  `    `**assert** products\_page.get\_products\_count() > 0
  ## **3. Селекторы в начале класса**
  python

  **class** LoginPage(BasePage):

  `    `*# Все селекторы в начале*

  `    `USERNAME\_INPUT = (By.ID, "user-name")

  `    `PASSWORD\_INPUT = (By.ID, "password")

  `    `LOGIN\_BUTTON = (By.ID, "login-button")

    

  `    `*# Методы ниже*

  `    `**def** login(self, username, password):

  `        `**pass**
  ## **4. Документирование методов**
  python

  **def** fill\_shipping\_info(self, first\_name, last\_name, postal\_code):

  `    `"""

  `    `Заполнить информацию доставки

    

  `    `Args:

  `        `first\_name: имя пользователя

  `        `last\_name: фамилия пользователя

  `        `postal\_code: почтовый код доставки

    

  `    `Returns:

  `        `CheckoutPage (следующая страница)

    

  `    `Raises:

  `        `TimeoutException: если форма не загрузилась

  `    `"""

  `    `self.input\_text(self.FIRST\_NAME\_INPUT, first\_name)

  `    `self.input\_text(self.LAST\_NAME\_INPUT, last\_name)

  `    `self.input\_text(self.POSTAL\_CODE\_INPUT, postal\_code)

  `    `self.click(self.CONTINUE\_BUTTON)

  `    `**return** CheckoutPage(self.driver)

  -----
  ## **БЛОК 5.6: ПРОДВИНУТЫЕ ТЕХНИКИ**
  ## **Использование явных ожиданий в Page Objects**
  python

  **class** ProductsPage(BasePage):

  `    `LOADING\_SPINNER = (By.CLASS\_NAME, "loading-spinner")

  `    `PRODUCT\_LIST = (By.CLASS\_NAME, "inventory\_list")

    

  `    `**def** wait\_for\_products\_to\_load(self):

  `        `"""Ждать загрузки товаров"""

  `        `*# Ждать исчезновения спиннера*

  `        `self.wait\_for\_element\_to\_disappear(self.LOADING\_SPINNER)

        

  `        `*# Ждать появления списка товаров*

  `        `self.wait\_for\_element(self.PRODUCT\_LIST)
  ## **Работа с динамическими селекторами**
  python

  **class** ProductsPage(BasePage):

  `    `**def** get\_product\_by\_name(self, product\_name):

  `        `"""Получить товар по имени"""

  `        `xpath = f"//div[@class='inventory\_item']//\*[contains(text(), '{product\_name}')]"

  `        `**return** self.find\_element((By.XPATH, xpath))

    

  `    `**def** add\_to\_cart\_by\_name(self, product\_name):

  `        `"""Добавить товар в корзину по имени"""

  `        `product = self.get\_product\_by\_name(product\_name)

        

  `        `*# Найти кнопку в том же контейнере*

  `        `button = product.find\_element(By.XPATH, ".//button")

  `        `button.click()
  ## **Кэширование элементов**
  python

  **class** BasePage:

  `    `**def** \_\_init\_\_(self, driver, timeout=10):

  `        `self.driver = driver

  `        `self.timeout = timeout

  `        `self.\_element\_cache = {}

    

  `    `**def** find\_element(self, locator):

  `        `"""Найти элемент с кэшированием"""

  `        `locator\_key = str(locator)

        

  `        `**if** locator\_key **not** **in** self.\_element\_cache:

  `            `self.\_element\_cache[locator\_key] = self.driver.find\_element(\*locator)

        

  `        `**return** self.\_element\_cache[locator\_key]

  -----
  ## **ИТОГО: КАК СТРОИТСЯ ПРОЕКТ**
1. BasePage — базовые методы для всех страниц
1. Page Objects — классы для каждой страницы (LoginPage, ProductsPage и т.д.)
1. conftest.py — фикстуры для тестов
1. Тесты — они используют Page Objects, не селекторы напрямую

   Преимущества:

- ✅ Легко менять селекторы (меняешь в одном месте)
- ✅ Тесты читаемы (нет технических деталей)
- ✅ Легко переиспользовать методы
- ✅ Легко добавлять новые страницы
  -----
  ## **ПРАКТИЧЕСКИЕ ЗАДАНИЯ**
  ## **Задание 5.1: BasePage**
  Создай класс BasePage с методами:

- find\_element(locator)
- click(locator)
- input\_text(locator, text)
- get\_text(locator)
  ## **Задание 5.2: LoginPage**
  Создай класс LoginPage, наследующий BasePage:

- Селекторы для username, password, login button
- Метод login(username, password)
- Метод для проверки ошибки авторизации
  ## **Задание 5.3: ProductsPage**
  Создай класс ProductsPage:

- Селекторы для товаров, корзины
- Метод получить количество товаров
- Метод добавить товар в корзину
  ## **Задание 5.4: Тесты с Page Objects**
  Напиши тесты, которые используют созданные Page Objects:

- Тест успешной авторизации
- Тест неудачной авторизации
- Тест добавления товара в корзину
  ## **Задание 5.5: Возврат Page Objects**
  Измени метод login() чтобы он возвращал ProductsPage:

  python

  **def** test\_login(login\_page):

  `    `products\_page = login\_page.login("admin", "secret")

  `    `**assert** products\_page.get\_products\_count() > 0
  ## **Задание 5.6: Полный флоу**
  Напиши тест для полного флоу:

- Авторизация
- Добавление товара
- Переход в корзину
- Оформление заказа

  === КОНЕЦ ФАЙЛА: module\_5\_page\_object\_model.md ===
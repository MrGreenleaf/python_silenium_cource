=== НАЧАЛО ФАЙЛА: module\_3\_selenium\_basics.md ===
# **МОДУЛЬ 3: ОСНОВЫ SELENIUM (4–5 дней)**
## **Полное объяснение с теорией и примерами**
-----
## **ВВЕДЕНИЕ: ЧТО ТАКОЕ SELENIUM И ЗАЧЕМ ОНО НУЖНО**
## **Проблема: как тестировать веб-приложение?**
Представь, что у тебя есть веб-сайт. Ты должен проверить:

- Может ли пользователь заполнить форму авторизации?
- Появляется ли сообщение об ошибке при неправильном пароле?
- Загружается ли таблица товаров?
- Работает ли фильтр?

  Ты можешь проверять вручную каждый раз, но это:

1. Медленно — одна ошибка проверяется 30 секунд
1. Скучно — одни и те же действия тысячи раз
1. Ненадежно — ты можешь забыть проверить что-то
1. Дорого — тестировщик стоит денег
   ## **Решение: автоматизировать тесты**
   Selenium — это инструмент, который управляет браузером из кода Python. Ты пишешь программу, которая:

   python

   *# Откроет браузер*

   *# Перейдет на сайт*

   *# Заполнит форму*

   *# Кликнет кнопку*

   *# Проверит результат*

   *# Закроет браузер*

   Все это выполняется автоматически и быстро.
   ## **Архитектура Selenium**
   text

   ┌─────────────────────────────────────────┐

   │  Твой код на Python (тест)             │

   │  from selenium import webdriver         │

   │  driver = webdriver.Chrome()            │

   │  driver.get("https://example.com")      │

   └────────────────┬────────────────────────┘

   `                 `│ (команды)

   `         `┌───────▼──────────┐

   `         `│  Selenium Client │ (JSON Wire Protocol)

   `         `└───────┬──────────┘

   `                 `│

   `         `┌───────▼──────────────┐

   `         `│  ChromeDriver        │ (executable)

   `         `│  (управляет Chrome)  │

   `         `└───────┬──────────────┘

   `                 `│ (команды браузеру)

   `         `┌───────▼──────────────┐

   `         `│  Chrome Browser      │

   `         `│  (открывается сам)   │

   `         `└──────────────────────┘

   Как это работает:

1. Ты пишешь код на Python
1. Python отправляет команды Selenium
1. Selenium отправляет команды ChromeDriver (управляет браузером)
1. ChromeDriver управляет Chrome браузером
1. Chrome выполняет действия (открывает сайт, кликает, вводит текст)
   -----
   ## **БЛОК 3.1: УСТАНОВКА И НАСТРОЙКА**
   ## **Шаг 1: Установи Python**
   Если еще не установлен, скачай с [**https://www.python.org**](https://www.python.org/)
   ## **Шаг 2: Создай папку проекта**
   bash

   mkdir my\_selenium\_project

   cd my\_selenium\_project
   ## **Шаг 3: Создай виртуальное окружение**
   Виртуальное окружение — это изолированное место, где живут твои пакеты Python. Это нужно, чтобы не загрязнять глобальную систему.

   bash

   *# Windows*

   python -m venv venv

   venv\Scripts\activate

   *# Mac/Linux*

   python3 -m venv venv

   source venv/bin/activate

   После активации видишь (venv) в терминале.
   ## **Шаг 4: Установи Selenium**
   bash

   pip install selenium
   ## **Шаг 5: Установи ChromeDriver**
   ChromeDriver — это программа, которая управляет Chrome браузером.

   Способ 1: Автоматический (рекомендуется)

   bash

   pip install webdriver-manager

   Теперь не нужно скачивать драйвер вручную, webdriver-manager сделает это за тебя.

   Способ 2: Ручной

1. Узнай версию своего Chrome: нажми на три точки → Settings → About Chrome
1. Скачай ChromeDriver этой версии с [**https://chromedriver.chromium.org**](https://chromedriver.chromium.org/)
1. Положи chromedriver.exe в папку проекта или добавь в PATH
   ## **Шаг 6: Проверь установку**
   Создай файл test\_setup.py:

   python

   **from** selenium **import** webdriver

   **from** webdriver\_manager.chrome **import** ChromeDriverManager

   **from** selenium.webdriver.service **import** Service

   *# Способ 1: С webdriver-manager (автоматический)*

   driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))

   *# Способ 2: Если ChromeDriver в одной папке*

   *# driver = webdriver.Chrome('./chromedriver.exe')*

   *# Открой Google*

   driver.get("https://www.google.com")

   *# Подожди 3 секунды, чтобы увидеть браузер*

   **import** time

   time.sleep(3)

   *# Закрой браузер*

   driver.quit()

   **print**("Selenium работает!")

   Запусти:

   bash

   python test\_setup.py

   Если браузер открылся и закрылся — поздравляю, Selenium установлен правильно!

   -----
   ## **БЛОК 3.2: ОСНОВНЫЕ КОНЦЕПЦИИ**
   ## **1. WebDriver**
   WebDriver — это объект, который представляет браузер. Через него ты управляешь браузером.

   python

   **from** selenium **import** webdriver

   **from** webdriver\_manager.chrome **import** ChromeDriverManager

   **from** selenium.webdriver.service **import** Service

   *# Создай WebDriver для Chrome*

   driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))

   *# Теперь driver — твой браузер*

   Важные методы WebDriver:

   python

   *# Открыть сайт*

   driver.get("https://example.com")

   *# Получить текущий URL*

   current\_url = driver.current\_url

   *# Получить заголовок страницы (то, что в <title>)*

   page\_title = driver.title

   *# Вернуться на предыдущую страницу*

   driver.back()

   *# Перейти на следующую страницу*

   driver.forward()

   *# Обновить страницу*

   driver.refresh()

   *# Получить исходный HTML код страницы*

   page\_source = driver.page\_source

   *# Закрыть текущую вкладку*

   driver.close()

   *# Закрыть браузер полностью*

   driver.quit()
   ## **2. Поиск элементов (Locators)**
   Перед тем как взаимодействовать с элементом, нужно его найти.

   python

   **from** selenium.webdriver.common.by **import** By

   *# Найти ОДН элемент по id*

   element = driver.find\_element(By.ID, "submit-button")

   *# Найти один элемент по class*

   element = driver.find\_element(By.CLASS\_NAME, "btn-primary")

   *# Найти один элемент по селектору CSS*

   element = driver.find\_element(By.CSS\_SELECTOR, ".btn.btn-primary")

   *# Найти один элемент по XPath*

   element = driver.find\_element(By.XPATH, "//button[@id='submit-button']")

   *# Найти один элемент по имени тага*

   element = driver.find\_element(By.TAG\_NAME, "button")

   *# Найти один элемент по атрибуту name*

   element = driver.find\_element(By.NAME, "username")

   *# Найти один элемент по полному тексту ссылки*

   element = driver.find\_element(By.LINK\_TEXT, "Нажми на меня")

   *# Найти один элемент по частичному тексту ссылки*

   element = driver.find\_element(By.PARTIAL\_LINK\_TEXT, "Нажми")

   Важно: find\_element() находит первый найденный элемент. Если элемента нет, выкидывает исключение NoSuchElementException.
   ## **3. Поиск нескольких элементов**
   python

   *# Найти ВСЕ элементы по селектору*

   elements = driver.find\_elements(By.CLASS\_NAME, "item")

   *# elements — это список (list)*

   *# Можешь итерировать по нему*

   **for** element **in** elements:

   `    `**print**(element.text)

   *# Или обратиться к конкретному элементу*

   first\_element = elements[0]

   second\_element = elements[1]

   Важно: если элементы не найдены, возвращает пустой список, а не исключение.
   ## **4. Элемент (WebElement)**
   WebElement — это объект, который представляет конкретный элемент на странице.

   python

   **from** selenium.webdriver.common.by **import** By

   element = driver.find\_element(By.ID, "submit-button")

   *# Теперь element — это WebElement*

   *# Можешь с ним взаимодействовать*

   Важные методы WebElement:

   python

   *# Клик по элементу*

   element.click()

   *# Ввести текст (для input)*

   element.send\_keys("Иван")

   *# Очистить поле (удалить текст)*

   element.clear()

   *# Отправить форму (нажать Enter)*

   element.submit()

   *# Получить текст элемента*

   text = element.text

   **print**(text)

   *# Получить значение атрибута*

   id\_value = element.get\_attribute("id")

   class\_value = element.get\_attribute("class")

   *# Получить значение value (для input)*

   input\_value = element.get\_attribute("value")

   *# Проверить, видим ли элемент*

   is\_displayed = element.is\_displayed()  *# True/False*

   *# Проверить, активен ли элемент*

   is\_enabled = element.is\_enabled()  *# True/False*

   *# Проверить, выбран ли элемент (для checkbox, radio)*

   is\_selected = element.is\_selected()  *# True/False*

   *# Получить тег элемента*

   tag = element.tag\_name  *# "button", "input", "p" и т.д.*

   *# Получить размер элемента*

   size = element.size  *# {'width': 100, 'height': 50}*

   *# Получить позицию элемента*

   location = element.location  *# {'x': 10, 'y': 20}*

   *# Получить CSS свойство элемента*

   color = element.value\_of\_css\_property("color")

   -----
   ## **БЛОК 3.3: ВЗАИМОДЕЙСТВИЕ С ЭЛЕМЕНТАМИ**
   ## **1. Клик**
   python

   **from** selenium.webdriver.common.by **import** By

   *# Найди кнопку*

   button = driver.find\_element(By.ID, "submit-button")

   *# Кликни по ней*

   button.click()
   ## **2. Ввод текста**
   python

   **from** selenium.webdriver.common.by **import** By

   *# Найди поле для ввода email*

   email\_input = driver.find\_element(By.ID, "email")

   *# Введи текст*

   email\_input.send\_keys("user@example.com")

   *# Можешь ввести несколько раз*

   email\_input.send\_keys(" дополнительный текст")

   Важно: send\_keys() добавляет текст, не заменяет. Если нужно заменить, сначала очисти:

   python

   *# Очисти поле*

   email\_input.clear()

   *# Теперь введи новый текст*

   email\_input.send\_keys("new\_email@example.com")
   ## **3. Работа со специальными клавишами**
   python

   **from** selenium.webdriver.common.by **import** By

   **from** selenium.webdriver.common.keys **import** Keys

   username\_input = driver.find\_element(By.ID, "username")

   *# Введи текст*

   username\_input.send\_keys("admin")

   *# Нажми Tab*

   username\_input.send\_keys(Keys.TAB)

   *# Нажми Enter*

   username\_input.send\_keys(Keys.ENTER)

   *# Другие клавиши*

   Keys.SHIFT

   Keys.CONTROL

   Keys.ALT

   Keys.DELETE

   Keys.BACKSPACE

   Keys.SPACE

   Keys.ARROW\_DOWN

   Keys.ARROW\_UP

   Keys.HOME

   Keys.END

   Keys.PAGE\_DOWN

   Keys.PAGE\_UP
   ## **4. Отправка формы**
   python

   **from** selenium.webdriver.common.by **import** By

   *# Способ 1: Найти и кликнуть на кнопку*

   submit\_button = driver.find\_element(By.ID, "submit-button")

   submit\_button.click()

   *# Способ 2: Нажать Enter в любом поле формы*

   input\_field = driver.find\_element(By.ID, "email")

   input\_field.submit()
   ## **5. Получение информации из элемента**
   python

   **from** selenium.webdriver.common.by **import** By

   *# Получить видимый текст*

   heading = driver.find\_element(By.TAG\_NAME, "h1")

   title = heading.text  *# "Добро пожаловать"*

   *# Получить значение атрибута*

   link = driver.find\_element(By.TAG\_NAME, "a")

   href = link.get\_attribute("href")  *# "https://google.com"*

   *# Получить value (для input)*

   input\_field = driver.find\_element(By.ID, "search")

   current\_value = input\_field.get\_attribute("value")

   *# Получить HTML код элемента*

   html = link.get\_attribute("outerHTML")  *# Весь элемент с тегами*

   *# Получить HTML внутри элемента*

   inner\_html = link.get\_attribute("innerHTML")  *# Без самого тега*
   ## **6. Проверка состояния элемента**
   python

   **from** selenium.webdriver.common.by **import** By

   button = driver.find\_element(By.ID, "submit-button")

   *# Видим ли элемент на экране?*

   **if** button.is\_displayed():

   `    `**print**("Кнопка видима")

   **else**:

   `    `**print**("Кнопка скрыта (CSS: display: none, visibility: hidden и т.д.)")

   *# Активна ли кнопка?*

   **if** button.is\_enabled():

   `    `**print**("Кнопка активна, можно кликать")

   **else**:

   `    `**print**("Кнопка отключена (disabled)")

   *# Выбран ли checkbox/radio?*

   checkbox = driver.find\_element(By.ID, "terms")

   **if** checkbox.is\_selected():

   `    `**print**("Checkbox отмечен")

   **else**:

   `    `**print**("Checkbox не отмечен")

   -----
   ## **БЛОК 3.4: ОЖИДАНИЯ (WAITS)**
   ## **Проблема: динамически загружаемые элементы**
   Представь, что ты открываешь сайт и нажимаешь "Load More". Сайт отправляет запрос к серверу, сервер обрабатывает, потом отправляет данные, браузер рисует новые элементы. Это занимает время.

   Если ты сразу попытаешься найти новые элементы, они еще не будут на странице:

   python

   **from** selenium.webdriver.common.by **import** By

   button = driver.find\_element(By.ID, "load-more")

   button.click()

   *# Новые товары еще не загружены!*

   new\_items = driver.find\_elements(By.CLASS\_NAME, "item")

   **print**(len(new\_items))  *# Может быть 0 или старое количество*

   Нужно подождать, пока элементы загрузятся. Для этого используются ожидания.
   ## **1. Неявные ожидания (Implicit Waits)**
   Неявное ожидание — это когда Selenium ждет несколько секунд, прежде чем выкинуть исключение "элемент не найден".

   python

   **from** selenium **import** webdriver

   **from** webdriver\_manager.chrome **import** ChromeDriverManager

   **from** selenium.webdriver.service **import** Service

   driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))

   *# Установи неявное ожидание на 10 секунд*

   driver.implicitly\_wait(10)

   *# Если элемент не найдется сразу, Selenium будет искать 10 секунд*

   *# Если найдет раньше, вернет сразу*

   **from** selenium.webdriver.common.by **import** By

   new\_item = driver.find\_element(By.CLASS\_NAME, "new-item")

   Плюсы:

- Просто установить один раз

  Минусы:

- Применяется ко всем элементам
- Если элемента нет, будешь ждать весь таймаут
- Не очень гибко
  ## **2. Явные ожидания (Explicit Waits) — РЕКОМЕНДУЕТСЯ**
  Явное ожидание — это когда ты точно говоришь Selenium, что ждать.

  python

  **from** selenium **import** webdriver

  **from** webdriver\_manager.chrome **import** ChromeDriverManager

  **from** selenium.webdriver.service **import** Service

  **from** selenium.webdriver.support.ui **import** WebDriverWait

  **from** selenium.webdriver.support **import** expected\_conditions **as** EC

  **from** selenium.webdriver.common.by **import** By

  driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))

  driver.get("https://example.com")

  *# Ожидай, что элемент станет видимым (максимум 10 секунд)*

  wait = WebDriverWait(driver, 10)

  element = wait.until(EC.presence\_of\_element\_located((By.CLASS\_NAME, "new-item")))

  *# Теперь element точно найден и видим*

  element.click()

  Частые условия ожидания:

  python

  **from** selenium.webdriver.support **import** expected\_conditions **as** EC

  **from** selenium.webdriver.common.by **import** By

  *# Элемент присутствует в DOM (но может быть невидимым)*

  EC.presence\_of\_element\_located((By.ID, "element"))

  *# Элемент видимый на экране*

  EC.visibility\_of\_element\_located((By.ID, "element"))

  *# Элемент кликабельный (видимый и активный)*

  EC.element\_to\_be\_clickable((By.ID, "button"))

  *# Элемент больше не видимый*

  EC.invisibility\_of\_element\_located((By.ID, "loading"))

  *# Заголовок страницы содержит текст*

  EC.title\_contains("Привет")

  *# Текст есть в элементе*

  EC.text\_to\_be\_present\_in\_element((By.TAG\_NAME, "h1"), "Добро пожаловать")

  *# Атрибут имеет значение*

  EC.text\_to\_be\_present\_in\_element\_value((By.ID, "input"), "ожидаемое значение")

  *# Количество окон браузера*

  EC.number\_of\_windows\_to\_be(2)

  *# Элемент выбран (для checkbox, radio)*

  EC.element\_to\_be\_selected((By.ID, "checkbox"))

  *# Alert появился*

  EC.alert\_is\_present()

  Как использовать:

  python

  **from** selenium.webdriver.support.ui **import** WebDriverWait

  **from** selenium.webdriver.support **import** expected\_conditions **as** EC

  **from** selenium.webdriver.common.by **import** By

  wait = WebDriverWait(driver, 10)  *# Максимум 10 секунд ждем*

  *# Вариант 1: Дождись, пока элемент кликабельный*

  button = wait.until(EC.element\_to\_be\_clickable((By.ID, "submit-button")))

  button.click()

  *# Вариант 2: Дождись текста в элементе*

  message = wait.until(EC.text\_to\_be\_present\_in\_element((By.CLASS\_NAME, "message"), "Успешно"))

  *# Вариант 3: Дождись, пока элемент исчезнет (загрузка закончилась)*

  wait.until(EC.invisibility\_of\_element\_located((By.CLASS\_NAME, "loading-spinner")))
  ## **3. Обработка таймаутов**
  Если ожидание истекло (элемент так и не появился), выкидывается исключение TimeoutException:

  python

  **from** selenium.webdriver.support.ui **import** WebDriverWait

  **from** selenium.webdriver.support **import** expected\_conditions **as** EC

  **from** selenium.webdriver.common.by **import** By

  **from** selenium.common.exceptions **import** TimeoutException

  wait = WebDriverWait(driver, 5)

  **try**:

  `    `*# Ждем элемент максимум 5 секунд*

  `    `element = wait.until(EC.presence\_of\_element\_located((By.ID, "nonexistent")))

  **except** TimeoutException:

  `    `**print**("Элемент не появился за 5 секунд!")

  `    `**print**(f"Текущий URL: {driver.current\_url}")

  `    `**print**(f"Заголовок: {driver.title}")

  -----
  ## **БЛОК 3.5: ОБРАБОТКА ИСКЛЮЧЕНИЙ**
  Selenium выкидывает разные исключения в разных ситуациях. Нужно их ловить и обрабатывать.

  Основные исключения:

  python

  **from** selenium.common.exceptions **import** (

  `    `NoSuchElementException,      *# Элемент не найден*

  `    `TimeoutException,            *# Ожидание истекло*

  `    `StaleElementReferenceException,  *# Элемент больше не в DOM*

  `    `NoSuchFrameException,        *# Frame не найден*

  `    `NoAlertPresentException,     *# Alert не показан*

  `    `InvalidSelectorException,    *# Неправильный селектор*

  )

  **from** selenium.webdriver.common.by **import** By

  **try**:

  `    `element = driver.find\_element(By.ID, "nonexistent")

  **except** NoSuchElementException:

  `    `**print**("Элемент не найден")

  **try**:

  `    `wait = WebDriverWait(driver, 5)

  `    `wait.until(EC.presence\_of\_element\_located((By.ID, "slow-element")))

  **except** TimeoutException:

  `    `**print**("Элемент не появился вовремя")

  -----
  ## **НОВОЕ: БЛОК 3.6 - SELENIUM 4 ЧТО НОВОГО 🆕**
  Selenium 4 вышел в 2021 году и принес много улучшений. Вот главные:
  ## **1. Улучшенные локаторы**
  Selenium 3 (старый способ):

  python

  **from** selenium.webdriver.common.by **import** By

  element = driver.find\_element(By.ID, "button")

  Selenium 4 (еще одна опция - сокращенная):

  python

  *# Работает, но длинный способ все еще актуален*

  element = driver.find\_element(By.ID, "button")
  ## **2. Relative Locators (новое!)**
  Проблема: иногда элемент не имеет id или class, но ты знаешь его позицию относительно другого элемента.

  Решение: Relative Locators!

  python

  **from** selenium.webdriver.common.by **import** By

  **from** selenium.webdriver.common.relative\_locator **import** locate\_with

  *# Найти submit кнопку*

  submit\_button = driver.find\_element(By.ID, "submit")

  *# Найти password input, который находится ВЫШЕ кнопки*

  password\_field = driver.find\_element(

  `    `locate\_with(By.TAG\_NAME, "input")

      .above(submit\_button)

  )

  *# Другие позиции:*

  *# .above()     — выше*

  *# .below()     — ниже*

  *# .to\_left\_of() — левее*

  *# .to\_right\_of() — правее*

  *# .near()      — рядом (в пределах 50px)*

  *# Пример:*

  username = driver.find\_element(

  `    `locate\_with(By.TAG\_NAME, "input")

      .above(password\_field)

  )

  email = driver.find\_element(

  `    `locate\_with(By.TAG\_NAME, "input")

      .below(username)

      .to\_left\_of(submit\_button)

  )
  ## **3. BiDi API (Chrome DevTools Protocol)**
  Selenium 4 может напрямую взаимодействовать с Chrome DevTools Protocol для отладки и получения информации.

  python

  **from** selenium **import** webdriver

  **from** selenium.webdriver.chrome.service **import** Service

  **from** webdriver\_manager.chrome **import** ChromeDriverManager

  options = webdriver.ChromeOptions()

  *# Включи CDP*

  options.add\_experimental\_option("w3c", True)

  driver = webdriver.Chrome(

  `    `service=Service(ChromeDriverManager().install()),

  `    `options=options

  )

  *# Теперь можно получить информацию из DevTools*
  ## **4. WebDriver Bidi (обмен данными)**
  Можно слушать события браузера:

  python

  *# Получить ошибки JavaScript*

  *# Получить сетевые запросы*

  *# Получить логи консоли*

  -----
  ## **НОВОЕ: БЛОК 3.7 - ПРАКТИЧЕСКИЕ СЦЕНАРИИ 🆕**
  ## **Работа с iframe**
  Проблема: iframe — это окно внутри страницы, которое имеет отдельный DOM. Ты не можешь найти элементы внутри iframe обычным способом.

  xml

  *<!-- HTML -->*

  <div>

  `  `<h1>Основная страница</h1>

  `  `<iframe id="payment-frame" src="payment.html"></iframe>

  </div>

  Неправильно:

  python

  *# Это НЕ найдет элемент, потому что он в iframe!*

  button = driver.find\_element(By.ID, "pay-button")

  Правильно:

  python

  **from** selenium.webdriver.common.by **import** By

  *# Сначала перейди в iframe*

  iframe = driver.find\_element(By.ID, "payment-frame")

  driver.switch\_to.frame(iframe)

  *# Теперь можешь найти элемент в iframe*

  button = driver.find\_element(By.ID, "pay-button")

  button.click()

  *# Верни фокус на основную страницу*

  driver.switch\_to.default\_content()
  ## **Drag and Drop (перетаскивание)**
  python

  **from** selenium.webdriver.common.action\_chains **import** ActionChains

  **from** selenium.webdriver.common.by **import** By

  *# Найди элементы*

  source = driver.find\_element(By.ID, "draggable")

  target = driver.find\_element(By.ID, "droppable")

  *# Создай цепочку действий*

  actions = ActionChains(driver)

  actions.drag\_and\_drop(source, target).perform()
  ## **Загрузка файлов**
  python

  **from** selenium.webdriver.common.by **import** By

  **import** os

  *# Найди input для загрузки файла*

  file\_input = driver.find\_element(By.ID, "file-upload")

  *# Укажи путь к файлу (абсолютный путь!)*

  file\_path = os.path.abspath("path/to/file.txt")

  *# Отправь путь (это НЕ клик и отправка данных, это прямая загрузка)*

  file\_input.send\_keys(file\_path)

  *# Если есть кнопка "Upload", кликни её*

  upload\_button = driver.find\_element(By.ID, "upload-btn")

  upload\_button.click()
  ## **Работа с Alert (всплывающие окна)**
  python

  **from** selenium.webdriver.common.alert **import** Alert

  **from** selenium.webdriver.support.ui **import** WebDriverWait

  **from** selenium.webdriver.support **import** expected\_conditions **as** EC

  *# Дождись alert*

  wait = WebDriverWait(driver, 10)

  alert\_element = wait.until(EC.alert\_is\_present())

  *# Получи текст alert*

  alert\_text = alert\_element.text

  **print**(alert\_text)

  *# Нажми OK*

  alert\_element.accept()

  *# Или нажми Cancel*

  *# alert\_element.dismiss()*

  *# Или введи текст (для prompt)*

  *# alert\_element.send\_keys("Мой текст")*

  *# alert\_element.accept()*
  ## **Работа с JavaScript**
  python

  *# Выполни JavaScript код*

  result = driver.execute\_script("return 2 + 2")

  **print**(result)  *# 4*

  *# Получи переменную со страницы*

  value = driver.execute\_script("return window.someVariable")

  *# Прокрути страницу вниз*

  driver.execute\_script("window.scrollBy(0, 1000)")

  *# Установи значение input через JavaScript (обход Selenium)*

  driver.execute\_script("""

  `    `var input = document.getElementById('email');

  `    `input.value = 'test@example.com';

  """)

  *# Получи элемент и работай с ним*

  script = """

  `    `var elements = document.querySelectorAll('.item');

  `    `return elements.length;

  """

  count = driver.execute\_script(script)

  **print**(f"Найдено элементов: {count}")

  -----
  ## **ПРАКТИЧЕСКИЙ ПРИМЕР: ПОЛНЫЙ ТЕСТ**
  python

  **from** selenium **import** webdriver

  **from** selenium.webdriver.common.by **import** By

  **from** selenium.webdriver.support.ui **import** WebDriverWait

  **from** selenium.webdriver.support **import** expected\_conditions **as** EC

  **from** webdriver\_manager.chrome **import** ChromeDriverManager

  **from** selenium.webdriver.service **import** Service

  **import** time

  *# SETUP*

  driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))

  driver.implicitly\_wait(10)

  **try**:

  `    `*# ARRANGE - Подготовка*

  `    `**print**("Открываем сайт...")

  `    `driver.get("https://www.saucedemo.com")

    

  `    `*# Проверяем заголовок*

  `    `**assert** "Swag Labs" **in** driver.title

  `    `**print**(f"✅ Заголовок правильный: {driver.title}")

    

  `    `*# ACT - Действие 1: Авторизация*

  `    `**print**("\nВводим учетные данные...")

    

  `    `username\_field = driver.find\_element(By.ID, "user-name")

  `    `password\_field = driver.find\_element(By.ID, "password")

  `    `login\_button = driver.find\_element(By.ID, "login-button")

    

  `    `username\_field.send\_keys("standard\_user")

  `    `password\_field.send\_keys("secret\_sauce")

  `    `login\_button.click()

    

  `    `*# Ожидаем загрузку главной страницы*

  `    `wait = WebDriverWait(driver, 10)

  `    `wait.until(EC.presence\_of\_element\_located((By.CLASS\_NAME, "inventory\_item")))

    

  `    `**print**("✅ Авторизация успешна")

    

  `    `*# ACT - Действие 2: Добавляем товар в корзину*

  `    `**print**("\nДобавляем товар...")

    

  `    `add\_button = driver.find\_element(By.ID, "add-to-cart-sauce-labs-backpack")

  `    `add\_button.click()

    

  `    `*# Проверяем, что товар добавлен*

  `    `cart\_badge = driver.find\_element(By.CLASS\_NAME, "shopping\_cart\_badge")

  `    `**assert** cart\_badge.text == "1"

    

  `    `**print**("✅ Товар добавлен в корзину")

    

  `    `*# ACT - Действие 3: Переходим в корзину*

  `    `**print**("\nПереходим в корзину...")

    

  `    `cart\_link = driver.find\_element(By.CLASS\_NAME, "shopping\_cart\_link")

  `    `cart\_link.click()

    

  `    `*# Ожидаем загрузку страницы корзины*

  `    `wait.until(EC.url\_contains("cart.html"))

    

  `    `*# ASSERT - Проверка*

  `    `**assert** "cart" **in** driver.current\_url

    

  `    `items = driver.find\_elements(By.CLASS\_NAME, "inventory\_item\_name")

  `    `**assert** len(items) == 1

  `    `**assert** "Backpack" **in** items[0].text

    

  `    `**print**("✅ Корзина содержит правильный товар")

  `    `**print**(f"📍 Текущий URL: {driver.current\_url}")

    

  `    `**print**("\n✅ ВСЕ ТЕСТЫ ПРОЙДЕНЫ!")

    

  **finally**:

  `    `*# TEARDOWN - Очистка*

  `    `**print**("\nЗакрываем браузер...")

  `    `driver.quit()

  `    `**print**("✅ Браузер закрыт")

  -----
  ## **ИТОГО: ЧТО НУЖНО ЗНАТЬ**
1. WebDriver — объект браузера
1. find\_element() — поиск одного элемента
1. find\_elements() — поиск всех элементов
1. Взаимодействие — click(), send\_keys(), clear(), submit()
1. Информация — text, get\_attribute(), is\_displayed()
1. Ожидания — implicit и explicit waits
1. Исключения — try-except для обработки ошибок
1. Selenium 4 — Relative Locators, BiDi API
1. Практические сценарии — iframe, drag-drop, файлы, alert, JavaScript
   -----
   ## **ПРАКТИЧЕСКИЕ ЗАДАНИЯ**
   ## **Задание 3.1: Открытие и закрытие браузера**
   Напиши код, который:

- Открывает Chrome браузер
- Переходит на [**https://www.google.com**](https://www.google.com/)
- Ждет 3 секунды
- Закрывает браузер
  ## **Задание 3.2: Поиск элементов**
  На [**https://www.saucedemo.com**](https://www.saucedemo.com/) найди и выведи в консоль:

- Текст заголовка (h1, h2 или h3)
- Все элементы с классом "inventory\_item"
- Кнопку "Login" по разным селекторам (id, css, xpath)
  ## **Задание 3.3: Взаимодействие**
  Напиши тест, который:

- Открывает [**https://www.saucedemo.com**](https://www.saucedemo.com/)
- Вводит username (standard\_user)
- Вводит password (secret\_sauce)
- Кликает Login
- Проверяет, что появились товары
- Закрывает браузер
  ## **Задание 3.4: Ожидания**
  Напиши код, который:

- Открывает сайт
- Нажимает кнопку, которая загружает данные
- Ждет, пока появится элемент с текстом "Loaded"
- Проверяет, что текст есть
  ## **Задание 3.5: Selenium 4**
  Используя Relative Locators, найди элементы относительно друг друга на [**https://www.saucedemo.com**](https://www.saucedemo.com/)
  ## **Задание 3.6: Обработка ошибок**
  Напиши код, который:

- Пытается найти несуществующий элемент
- Ловит исключение NoSuchElementException
- Выводит сообщение об ошибке

  === КОНЕЦ ФАЙЛА: module\_3\_selenium\_basics.md ===
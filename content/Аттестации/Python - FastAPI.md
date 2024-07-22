---
tags:
  - Аттестация
date: 2024-06-10
author: Проскурин Д.А
---
Список вопросов:

1. Создание приложения
2. Добавление эндпоинтов
3. Добавление редиректов
4. использование серверных cookie
5. использование логирования
6. Получение параметров запроса и тела запроса
7. модуль pydantic
8. Валидация параметров запроса и ответа
9. Модели таблиц базы данных
10. ошибок через exception_handler
11. Обработка ошибок в эндпоинтах
12. Передача статичных файлов
13. Передача шаблонов
14. Преимущества и недостатки асинхронных фреймворков
15. Запуск приложения на uvicorn


---

## **Основы FastAPI**
- Что такое FastAPI и какова его основная цель? (Основная цель FastAPI — обеспечение высокой производительности, сопоставимой с NodeJS и Go);
- Какие преимущества FastAPI перед другими фреймворками?
- На каких технологиях реализован FastAPI (инструменты Starlette и Pydantic);


## **Установка и начало работы**
- Как установить FastAPI и необходимые зависимости?
- Как создать новый проект на FastAPI?
      
>[!question] 
>pip install  `fastapi` `uvicorn`
>

```python
from fastapi import FastAPI, HTTPException, status

app = FastAPI()
items = []
items_id = 1

@app.get('/')
def root():
    return {'message': 'Hello World'}

@app.get('/items')
def get_item(item_id: int):
    try:
        return items[item_id - 1]
    except IndexError:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail='item does not exists')
```


## **Роутинг и обработчики запросов**
- Как определить маршруты в приложении FastAPI?
- Как обрабатывать GET, POST, PUT, DELETE запросы?
- Как сделать более сложный структурный роутинг? (Класс APIRouter, как и когда его использовать)?
- Как использовать параметры пути (path parameters) и параметры запроса (query parameters). Лучшие практик работы с path parameters, query_params.?


В FastAPI роутинг и обработка запросов осуществляются с помощью декораторов, таких как @app.get(), @app.post(), @app.put(), @app.delete() и других HTTP методов. Эти декораторы определяют тип HTTP-запроса, который должен быть обработан функцией, аргументы функции указывают на параметры запроса или пути. Вот пример базового роутинга и обработки запросов:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def read_root():
    return {"Hello": "World"}

@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}
```

Для более сложных приложений, где код может быть разделен на несколько файлов, FastAPI предлагает класс `APIRouter`. Этот класс позволяет группировать пути и зависимости вместе, что упрощает структуризацию больших приложений. Вы можете создать экземпляр `APIRouter` и определить пути внутри него, а затем включить этот роутер в главное приложение с помощью метода `.include_router()`. Класс `APIRouter` также позволяет задать префиксы путей, теги, зависимости и другие параметры для группы путей, делая управление большими приложениями более удобным и организованным.

```python
from fastapi import APIRouter, FastAPI

app = FastAPI()
router = APIRouter(prefix="/items", tags=["Items"])

@router.get("/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}

app.include_router(router)
```

Параметры пути (Path Parameters)

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/users/{user_id}/items/{item_id}")
async def read_user_item(user_id: int, item_id: str):
    return {"user_id": user_id, "item_id": item_id}
```

Параметры запроса (Query Parameters)
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/items/")
async def read_items(skip: int = 0, limit: int = 100):
    items = [{"item_id": f"{skip + i}"} for i in range(limit)]
    return items

```

**Лучшие практики**

При работе с параметрами запроса в FastAPI важно соблюдать следующие рекомендации:

- Выбирайте значимые имена параметров, чтобы улучшить читаемость и понимание API.
- Укажите тип каждого параметра запроса, чтобы избежать ошибок и сделать API более надежным.
- Для необязательных параметров запроса установите значения по умолчанию, чтобы минимизировать ошибки и повысить общую доступность API



## Добавление редиректов

- Как создавать редиректы?
```python
from fastapi import FastAPI
from fastapi.responses import RedirectResponse

app = FastAPI()

@app.get("/")
async def read_root():
    return RedirectResponse(url="http://www.example.com")
```

```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from fastapi.responses import RedirectResponse

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

@app.post("/login")
async def login(username: str, password: str, token: str = Depends(oauth2_scheme)):
    # Здесь должна быть логика проверки учетных данных
    if username == "admin" and password == "secret":
        return RedirectResponse(url="/profile", status_code=status.HTTP_302_FOUND)
    else:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Incorrect username or password")

@app.get("/profile")
async def profile():
    return {"message": "Welcome to your profile!"}

```
- Как комбинировать шаблоны и редиректы?
Если ваше приложение FastAPI также использует шаблоны HTML для представлений, вы можете комбинировать использование `TemplateResponse` и `RedirectResponse` для отображения страницы и последующего перенаправления пользователя. Например, после успешной аутентификации вы можете отобразить страницу успеха и затем перенаправить пользователя:
```python
from fastapi import FastAPI, Request, Form
from fastapi.responses import RedirectResponse, HTMLResponse
from starlette.templating import Jinja2Templates
from fastapi.status import HTTP_302_FOUND

app = FastAPI()
templates = Jinja2Templates(directory="templates")

@app.post("/", response_class=HTMLResponse)
async def validate(request: Request, email: str = Form(...), password: str = Form(...)):
    valid_credentials = email == "admin@example.com" and password == "password123"
    if valid_credentials:
        return templates.TemplateResponse("success.html", context={"request": request})
    else:
        redirect_url = request.url_for('validate') + "?error=Invalid+credentials"
        return RedirectResponse(redirect_url, status_code=HTTP_302_FOUND)

```


## Использование серверных cookie

- Что такое куки? Для чего нужны?
- Как установить куки? Как получить куки?
- Как установить куки в header? зачем это делать?

В FastAPI есть два основных способа работы с серверными cookie: установка cookie и получение cookie из запроса.

Установка cookie
Для установки cookie в ответе на запрос используется объект Response. Вы можете объявить параметр типа Response в вашем пути операции и затем установить cookie в этом временном объекте ответа. Например:

```python
from fastapi import FastAPI, Response

app = FastAPI()

@app.post("/cookie-and-object/")
def create_cookie(response: Response):
    response.set_cookie(key="fakesession", value="fake-cookie-session-value")
    return {"message": "Come to the dark side, we have cookies"}
```
Здесь мы устанавливаем cookie с ключом fakesession и значением fake-cookie-session-value в ответе на POST запрос к /cookie-and-object/ 1.

Получение cookie из запроса
Для получения cookie из запроса вы можете использовать параметр Cookie в декораторе функции пути. Это позволяет извлекать значение cookie, отправленное клиентом в запросе. Например:

```python
from fastapi import FastAPI, Cookie

app = FastAPI()

@app.get("/items/")
async def read_items(ads_id: str | None = Cookie(default=None)):
    return {"ads_id": ads_id}
```
В этом примере мы ожидаем, что клиент отправит cookie с ключом ads_id, и возвращаем его значение в ответе 2.

Дополнительные способы работы с cookie
Использование Request для получения cookie: Вы можете использовать объект Request для доступа к всем cookie, отправленным в запросе, напрямую:
```python
from fastapi import Request

@app.get('/')
async def root(request: Request):
    return request.cookies.get('sessionKey')
```
Этот метод позволяет получить доступ ко всем cookie, отправленным в запросе, и может быть полезен, когда вам нужно работать с несколькими cookie одновременно 3.

Использование Header для получения cookie: Также можно использовать параметр Header для получения cookie, хотя это менее распространенный подход:
```Python
from fastapi import Cookie, Header

@app.get("/")
async def root(text: str, sessionKey: str = Header(None), cookie_param: int | None = Cookie(None)):
    print(cookie_param)
    return {"message": f"{text} returned"}
```
В этом примере sessionKey и cookie_param используются для получения cookie из запроса 3.

Эти методы предоставляют гибкость в работе с cookie в FastAPI, позволяя как устанавливать, так и получать cookie в зависимости от потребностей вашего приложения.


## Логгирование

- Как логгировать в Fast API? (loguru, senry-python);
- Как можно логгировать все ошибки? (в мидлваре)
Журналирование ошибок может помочь в диагностике проблем. FastAPI не предоставляет прямой поддержки журналирования, но вы можете использовать сторонние библиотеки, такие как `loguru` или `sentry-python`, для логирования ошибок.


## **Обработка ошибок**
    - Как обрабатывать исключения и ошибки в FastAPI?
    - Использование HTTPException для возврата специфических кодов состояния.
    - Что такое middlewere?


Обработка исключений и ошибок в FastAPI важна для создания надежных и устойчивых веб-приложений. FastAPI предоставляет несколько способов для управления ошибками, включая использование HTTP статусных кодов, встроенных обработчиков исключений, пользовательских обработчиков исключений, промежуточного программного обеспечения (middleware) и журналирования.

Использование HTTP статусных кодов
FastAPI позволяет явно указывать HTTP статусные коды в ответах на запросы. Это может быть сделано путем возврата объекта Response с нужным статусным кодом. Например, для возврата ошибки 404 (Not Found):

```python
from fastapi import FastAPI, Response

app = FastAPI()

@app.get("/items/{item_id}")
async def read_item(item_id: str, response: Response):
    if item_id not in items:
        response.status_code = 404
        return {"detail": "Item not found"}
    return {"item_id": item_id}
```
Использование встроенных обработчиков исключений
FastAPI предоставляет встроенные обработчики для некоторых стандартных исключений, таких как HTTPException. Это позволяет легко возвращать соответствующие HTTP статусные коды и детали ошибки:

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()

@app.get("/items/{item_id}")
async def read_item(item_id: str):
    if item_id not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    return {"item_id": item_id}
```
Использование пользовательских обработчиков исключений
Вы можете создать собственные обработчики исключений, чтобы управлять поведением при возникновении ошибок. Это может быть полезно для централизованного управления ошибками или для добавления дополнительной логики обработки:

```python
from fastapi import FastAPI, Request, HTTPException

app = FastAPI()

@app.exception_handler(HTTPException)
async def http_exception_handler(request: Request, exc: HTTPException):
    return {"custom_message": "An HTTP error occurred", "status_code": exc.status_code}

@app.get("/items/{item_id}")
async def read_item(item_id: str):
    if item_id not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    return {"item_id": item_id}
```
Использование промежуточного программного обеспечения (Middleware)
Middleware позволяет выполнять дополнительную логику обработки запросов и ответов, что может быть полезно для обработки ошибок. Например, вы можете использовать middleware для логирования всех ошибок:

```python
from fastapi import FastAPI, Request, HTTPException
from starlette.middleware.base import BaseHTTPMiddleware
from starlette.requests import Request

class ErrorHandlerMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request: Request, call_next):
        try:
            response = await call_next(request)
            return response
        except Exception as e:
            return {"detail": str(e)}

app = FastAPI()

app.add_middleware(ErrorHandlerMiddleware)
```


## модуль pydantic
- Основные возможности pydantic.
- Как повысить производительность FastAPI при помощи pydantic? (ленивая валидация)
- 

Модуль Pydantic играет ключевую роль в FastAPI, обеспечивая валидацию данных, сериализацию и документацию схем. Он позволяет создавать модели данных, которые автоматически проверяют входные данные на соответствие определенным типам и ограничениям, что значительно упрощает работу с данными в веб-приложениях.

 Основные возможности Pydantic:

- **Валидация данных**: Pydantic использует аннотации типов Python для валидации входных данных во время выполнения. Это означает, что данные автоматически проверяются на соответствие определенным типам и ограничениям, например, длине строки или диапазону чисел [4](https://www.tutorialspoint.com/fastapi/fastapi_pydantic.htm).
    
- **Сериализация и десериализация**: Модели Pydantic могут автоматически преобразовывать данные между словарями Python и JSON, а также поддерживают глубоко вложенные структуры благодаря использованию рекурсивных моделей [2](https://fastapi.tiangolo.com/tutorial/body-nested-models/).
    

- **Генерация документации**: Pydantic модели автоматически генерируют документацию OpenAPI (ранее известную как Swagger), которая может быть использована для автоматической генерации интерактивных API документаций [2](https://fastapi.tiangolo.com/tutorial/body-nested-models/).

- **Поддержка JSON Schema**: Модели Pydantic могут эмитировать JSON Schema, что позволяет легко интегрировать их с другими инструментами и библиотеками [3](https://docs.pydantic.dev/latest/).

- **Ленивая валидация**: Pydantic поддерживает ленивую валидацию, что означает, что данные могут быть валидированы только тогда, когда это действительно необходимо, что повышает производительность приложения
```python
from pydantic import BaseModel
from typing import List

class Item(BaseModel):
    name: str
    description: str = None
    price: float
    tax: float = None

@app.post("/items/", response_model=Item)
async def create_item(item: Item):
    return item

```

## Валидация параметров запроса и ответа


Валидация параметров запроса и ответа в FastAPI может быть реализована с помощью Pydantic моделей и декоратора `Query` для параметров запроса, а также параметра `response_model` для ответов. Вот как это работает на практике:

Для валидации параметров запроса можно использовать декоратор `Query` из FastAPI, который позволяет задать различные ограничения и параметры для параметров запроса. Например, можно ограничить максимальную длину строки или установить значение по умолчанию:

```python
from fastapi import FastAPI, Query

app = FastAPI()

@app.get("/items/")
async def read_items(name: str = Query(None, min_length=3, max_length=50)):
    return {"name": name}

```

Валидация ответа

Для валидации ответа можно использовать параметр `response_model` в декораторе пути. Это позволяет FastAPI автоматически сериализовать возвращаемые данные в соответствии с определенной моделью Pydantic, а также применять валидацию и документацию:

```python
from typing import List
from fastapi import FastAPI
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: str = None
    price: float
    tax: float = None

app = FastAPI()

@app.post("/items/", response_model=Item)
async def create_item(item: Item):
    return item

@app.get("/items/", response_model=List[Item])
async def read_items():
    return [Item(name="Foo"), Item(name="Bar")]

```

## Валидация ошибок через exception_handler

```python
from fastapi import FastAPI, Request
from pydantic import BaseModel, ValidationError
from fastapi.responses import JSONResponse

app = FastAPI()

class Item(BaseModel):
    name: str
    description: str = None
    price: float
    tax: float = None

@app.exception_handler(ValidationError)
async def validation_exception_handler(request: Request, exc: ValidationError):
    return JSONResponse(status_code=400, content={"detail": exc.errors()})

```


## Модели таблиц базы данных.Интеграция с базами данных**
- Как интегрировать FastAPI с различными базами данных?
- Примеры использования SQLAlchemy или Tortoise ORM.

SQLAlchemy,SQLModel

```python
from typing import Optional

from sqlmodel import Field, SQLModel


class Hero(SQLModel, table=True):
    id: Optional[int] = Field(default=None, primary_key=True)
    name: str
    secret_name: str
    age: Optional[int] = None
```
- Что такое миграции проекта? Как настроить миграции?

## **Работа с формами и JSON**
    - Как обрабатывать данные формы в FastAPI?
    - Как работать с JSON данными? Преобразование данных из JSON в модели Pydantic.

## **Маршрутизация и подмаршрутизация**
- Как организовать подмаршрутизацию в FastAPI?
- Использование PathOperationDecorator для документирования маршрутов.

## **Безопасность**
- Как обеспечить безопасность в приложениях FastAPI?
- Работа с OAuth2 и JWT токенами.

## **Тестирование**
- Как тестировать приложения FastAPI?
- Использование FastAPI TestClient для автоматического тестирования.


`TestClient` является удобным инструментом для тестирования FastAPI приложений, поскольку он позволяет отправлять запросы к вашему приложению без необходимости запускать реальный сервер. `TestClient` использует асинхронную библиотеку HTTPX под капотом, что делает его идеальным для тестирования асинхронных приложений FastAPI.

Пример использования `TestClient` для тестирования простого эндпоинта:

## **Асинхронность**
- Как FastAPI поддерживает асинхронное программирование?
- Примеры асинхронных операций и их использования.
(asyncpg, pcycopg).


## **Деплоймент и производительность**
- Как оптимизировать производительность приложений FastAPI?
- Рекомендации по деплою приложений FastAPI.

1. **Использование асинхронности**: FastAPI поддерживает асинхронное программирование, что позволяет обрабатывать множество запросов параллельно, улучшая общую производительность приложения. Используйте ключевое слово `async` для определения асинхронных функций и `await` для ожидания завершения асинхронных операций, таких как обращения к базе данных или вызовы внешних API.
2. **Разделение маршрутов на разные файлы**: С помощью модулей `APIRouter` можно организовать маршруты в отдельные файлы, что упрощает управление кодом и повышает его читаемость. Это особенно полезно для больших приложений, где маршруты становятся сложными для управления в одном месте.
3. **Масштабирование и оптимизация для продакшена**: При развертывании приложения FastAPI на продакшн-платформах, таких как Heroku, AWS, или GCP, важно учитывать рекомендации по масштабированию и оптимизации. Это может включать выбор подходящего типа экземпляра сервера, настройку балансировщика нагрузки и использование кэширования для часто запрашиваемых данных.

4. **Использование кэширования**: Кэширование может существенно улучшить производительность за счет сокращения количества обращений к базе данных или внешним API. FastAPI поддерживает различные стратегии кэширования, включая кэширование результатов запросов и кэширование ответов на клиентские запросы.

5. **Оптимизация конфигурации**: Использование централизованной системы конфигурации, подобной той, что используется в Flask, может помочь избежать повторения кода и упростить изменение настроек приложения.

6. **Мониторинг и логирование**: Регулярный мониторинг производительности и логирование ошибок помогают выявлять узкие места и



## **Работа с файлами и медиафайлами**
- Как обрабатывать загрузку файлов в FastAPI?
- Работа с медиафайлами через FormData.

Работа с файлами и медиафайлами в FastAPI включает в себя несколько аспектов, начиная от загрузки файлов до их обработки и хранения. Вот основные шаги и примеры кода, демонстрирующие, как это можно сделать:

Загрузка Файлов
Для загрузки файлов в FastAPI используются классы File и UploadFile, которые предоставляются библиотекой FastAPI. Эти классы позволяют обрабатывать файлы, загруженные через HTTP POST запросы.

Простая Загрузка Файла
```python
from fastapi import FastAPI, File, UploadFile

app = FastAPI()

@app.post("/uploadfile/")
async def upload_file(file: UploadFile = File(...)):
    return {"filename": file.filename}
```
Множественная Загрузка Файлов
Для загрузки нескольких файлов одновременно можно использовать список UploadFile.

```python
from fastapi import FastAPI, File, UploadFile

app = FastAPI()

@app.post("/uploadfiles/")
async def upload_files(files: list[UploadFile] = File(...)):
    filenames = [file.filename for file in files]
    return {"filenames": filenames}
```
Обработка Файлов
После загрузки файлов вы можете обрабатывать их содержимое. Например, вы можете сохранять файлы на диск, обрабатывать их содержимое или передавать дальше для дальнейшей обработки.

Сохранение Файла
```python
import os
from fastapi import FastAPI, File, UploadFile

app = FastAPI()

@app.post("/savefile/")
async def save_file(file: UploadFile = File(...)):
    filename = f"{file.filename}"
    with open(filename, "wb") as buffer:
        contents = await file.read()  # async read
        buffer.write(contents)
    return {"filename": filename}
```
Возвращение Файлов
Вы также можете возвращать файлы в ответ на запрос. FastAPI позволяет возвращать файлы напрямую в ответе.

```python
from fastapi import FastAPI, File, UploadFile
from fastapi.responses import FileResponse

app = FastAPI()

@app.post("/downloadfile/")
async def download_file(file: UploadFile = File(...)):
    return FileResponse(file.file, media_type=file.content_type, filename=file.filename)
```
Эти примеры демонстрируют базовые возможности работы с файлами и медиафайлами в FastAPI. Вы можете расширять эти примеры, добавляя дополнительную логику обработки файлов, валидацию входящих файлов, а также интеграцию с системами хранения файлов, такими как Amazon S3 или Google Cloud Storage.


## Передача шаблонов
- Как производиться передача шаблонов? Рендеринг. Когда это нужно использовать?

## Преимущества и недостатки асинхронных фреймворков

Асинхронные фреймворки предлагают ряд преимуществ и недостатков по сравнению со синхронными фреймворками. Вот краткое резюме основных моментов:

### Преимущества асинхронных фреймворков:

- **Улучшенное производство и использование ресурсов** для задач, ограниченных вводом-выводом (I/O-bound), благодаря тому, что асинхронные операции освобождают потоки для выполнения других задач во время ожидания I/O [1](https://sunscrapers.com/blog/programming-async-vs-sync-best-approach/).
- **Высокая конкуренция и масштабируемость**, что позволяет обрабатывать больше пользователей с теми же аппаратными ресурсами, предотвращая потери времени ЦПУ на ожидание I/O [1](https://sunscrapers.com/blog/programming-async-vs-sync-best-approach/)[2](https://www.ramotion.com/blog/synchronous-vs-asynchronous-programming/).
- **Отзывчивый интерфейс пользователя (UI)**, поскольку асинхронные операции предотвращают блокировку потока UI, что позволяет оставить UI интерактивным во время ожидания данных [2](https://www.ramotion.com/blog/synchronous-vs-asynchronous-programming/).

### Недостатки асинхронных фреймворков:

- **Сложность реализации и отладки**, поскольку асинхронный код может быть сложнее понять и отлаживать из-за того, что выполнение происходит вне порядка [1](https://sunscrapers.com/blog/programming-async-vs-sync-best-approach/).
- **Возможность "адской пещеры обратных вызовов" или цепочек промисов**, что может усложнить понимание и управление потоком выполнения кода [1](https://sunscrapers.com/blog/programming-async-vs-sync-best-approach/).
- **Не всегда превосходит синхронное программирование для задач, ограниченных процессором (CPU-bound)**, поскольку асинхронные операции лучше всего подходят для задач, ожидающих ввода-вывода [1](https://sunscrapers.com/blog/programming-async-vs-sync-best-approach/).

### Преимущества синхронных фреймворков:

- **Простота и легкость реализации**, что делает синхронное программирование более доступным для разработчиков, особенно для небольших проектов с низкой конкуренцией [1](https://sunscrapers.com/blog/programming-async-vs-sync-best-approach/).
- **Прогнозируемое выполнение кода**, что упрощает отладку и тестирование, поскольку выполнение кода происходит последовательно [1](https://sunscrapers.com/blog/programming-async-vs-sync-best-approach/).

### Недостатки синхронных фреймворков:

- **Нерациональное использование ресурсов** для задач, ограниченных вводом-выводом, поскольку синхронные операции блокируют потоки, что приводит к снижению эффективности использования ресурсов [1](https://sunscrapers.com/blog/programming-async-vs-sync-best-approach/)[2](https://www.ramotion.com/blog/synchronous-vs-asynchronous-programming/).
- **Замедленная производительность** из-за блокировки выполнения, что может привести к замедлению общей производительности приложения, особенно если некоторые операции занимают много времени [2](https://www.ramotion.com/blog/synchronous-vs-asynchronous-programming/).

В целом, выбор между асинхронным и синхронным программированием зависит от специфики проекта, требуемых к нему задач и предпочтений команды разработки. Асинхронные фреймворки лучше подходят для высококонкурентных приложений и задач, связанных с сетью, тогда как синхронные фреймворки могут быть проще в использовании для небольших проектов или задач, ограниченных процессором.
## Запуск приложения на uvicorn
- Пример запуска на uvicorn

# Источники

https://habr.com/ru/articles/774032/
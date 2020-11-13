
# Fastapi-mail

The fastapi-mail simple lightweight mail system, sending emails and attachments(individual && bulk)


[![MIT licensed](https://img.shields.io/github/license/marlin-dev/fastapi-mail)](https://raw.githubusercontent.com/marlin-dev/fastapi-mail/master/LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/marlin-dev/fastapi-mail.svg)](https://github.com/marlin-dev/fastapi-mail/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/marlin-dev/fastapi-mail.svg)](https://github.com/marlin-dev/fastapi-mail/network)
[![GitHub issues](https://img.shields.io/github/issues-raw/marlin-dev/fastapi-mail)](https://github.com/marlin-dev/fastapi-mail/issues)
[![Downloads](https://pepy.tech/badge/fastapi-mail)](https://pepy.tech/project/fastapi-mail)


###  🔨  Installation ###

```sh
 $ pip install fastapi-mail
```


### In order to run the application use command below ####

```sh
uvicorn examples.main:app --reload  --port 8001

```

### Guide


```python

from fastapi import FastAPI, BackgroundTasks, UploadFile, File, Form
from starlette.responses import JSONResponse
from starlette.requests import Request
from fastapi_mail import FastMail, MessageSchema,ConnectionConfig
from pydantic import EmailStr
from pydantic import EmailStr, BaseModel
from typing import List



class EmailSchema(BaseModel):
    email: List[EmailStr]


conf = ConnectionConfig(
    MAIL_USERNAME = "YourUsername",
    MAIL_PASSWORD = "strong_password",
    MAIL_FROM = "your@email.com",
    MAIL_PORT = 587,
    MAIL_SERVER = "your mail server",
    MAIL_TLS = True,
    MAIL_SSL = False
)

app = FastAPI()


html = """
<p>Hi this test mail, thanks for using Fastapi-mail</p> 
"""

template = """
<p>Hi this test mail using BackgroundTasks, thanks for using Fastapi-mail</p> 
"""


@app.post("/email")
async def simple_send(email: EmailSchema) -> JSONResponse:

    message = MessageSchema(
        subject="Fastapi-Mail module",
        recipients=email.dict().get("email"),  # List of recipients, as many as you can pass 
        body=html,
        subtype="html"
        )

    fm = FastMail(conf)
    await fm.send_message(message)
    return JSONResponse(status_code=200, content={"message": "email has been sent"})
        

```

### Sending email as background task

```python
   
@app.post("/emailbackground")
async def send_in_background(background_tasks: BackgroundTasks,email: EmailSchema) -> JSONResponse:

    message = MessageSchema(
        subject="Fastapi mail module",
        recipients=email.dict().get("email"),
        body="Simple background task ",
        )

    fm = FastMail(conf)
    
    background_tasks.add_task(fm.send_message,message)

    return JSONResponse(status_code=200, content={"message": "email has been sent"})


```


### Sending files


```python

@app.post("/file")
async def send_file(background_tasks: BackgroundTasks,file: UploadFile = File(...),email:EmailStr = Form(...)) -> JSONResponse:

    message = MessageSchema(
            subject="Fastapi mail module",
            recipients=[email],
            body="Simple background task ",
            attachments=[file]
            )

    fm = FastMail(conf)
        
    background_tasks.add_task(fm.send_message,message)

    return JSONResponse(status_code=200, content={"message": "email has been sent"})



```

###  Using Jinja2 HTML Templates

The email folder must be present within your applications working directory.

In sending HTML emails, the CSS expected by mail servers -outlook, google, etc- must be inline CSS. Fastapi mail passes _"body"_ to the rendered template. In creating the template for emails the dynamic objects should be used with the assumption that the variable is named "_body_" and that it is a python dict.

check out jinja2 for more details 
https://jinja.palletsprojects.com/en/2.11.x/


```python


class EmailSchema(BaseModel):
    email: List[EmailStr]
    body: Dict[str, Any]

conf = ConnectionConfig(
    MAIL_USERNAME = "YourUsername",
    MAIL_PASSWORD = "strong_password",
    MAIL_FROM = "your@email.com",
    MAIL_PORT = 587,
    MAIL_SERVER = "your mail server",
    MAIL_TLS = True,
    MAIL_SSL = False,
    TEMPLATE_FOLDER='./email templates folder'
)


@app.post("/email")
async def send_with_template(email: EmailSchema) -> JSONResponse:

    message = MessageSchema(
        subject="Fastapi-Mail module",
        recipients=email.dict().get("email"),  # List of recipients, as many as you can pass 
        body=email.dict().get("body"),
        subtype="html"
        )

    fm = FastMail(conf)
    await fm.send_message(message, template_name="email_template.html") ##optional field template_name is the name of the html file(jinja template) to use from the email template folder
    return JSONResponse(status_code=200, content={"message": "email has been sent"})


```


# Contributing
Fell free to open issue and send pull request.


## Contributors ✨

Thanks goes to these wonderful people ([🚧](https://allcontributors.org/docs/en/maintenance)):


<table>
  <tr>
    <td align="center"><a href="https://github.com/marlin-dev"><img src="https://avatars.githubusercontent.com/u/46589585?v=3" width="100px;" alt=""/><br /><sub><b>Sabuhi Shukurov</b></sub></a><br /><a href="#maintenance-tbenning" title="Answering Questions">💬</a> <a href="https://github.com/marlin-dev/fastapi-mail/" title="Reviewed Pull Requests">👀</a> <a href="#maintenance-jakebolam" title="Maintenance">🚧</a></td>
    <td align="center"><a href="https://github.com/Turall"><img src="https://avatars.githubusercontent.com/u/32899328?v=3" width="100px;" alt=""/><br /><sub><b>Tural Muradov</b></sub></a><br /><a href="https://github.com/marlin-dev/fastapi-mail/" title="Documentation">📖</a> <a href="https://github.com/marlin-dev/fastapi-mail/" title="Reviewed Pull Requests">👀</a> <a href="#tool-jfmengels" title="Tools">🔧</a></td>
    <td align="center"><a href="https://github.com/AliyevH"><img src="https://avatars.githubusercontent.com/u/5507950?v=3" width="100px;" alt=""/><br /><sub><b>Hasan Aliyev</b></sub></a><br /><a href="https://github.com/marlin-dev/fastapi-mail/" title="Documentation">📖</a> <a href="#maintenance-jakebolam" title="Maintenance">🚧</a> <a href="https://github.com/marlin-dev/fastapi-mail/" title="Reviewed Pull Requests">👀</a></td>
    <td align="center"><a href="https://github.com/imaskm"><img src="https://avatars.githubusercontent.com/u/20543833?v=3" width="100px;" alt=""/><br /><sub><b>Ashwani</b></sub></a><br /><a href="#maintenance-tbenning" title="Maintenance">🚧</a></td>
    <td align="center"><a href="https://github.com/LLYX"><img src="https://avatars1.githubusercontent.com/u/10430633" width="100px;" alt=""/><br /><sub><b>Leon Xu</b></sub></a><br /><a href="#maintenance-tbenning" title="Maintenance">🚧</a></td>
    <td align="center"><a href="https://github.com/gabrielponto"><img src="https://avatars.githubusercontent.com/u/7227328" width="100px;" alt=""/><br /><sub><b>Gabriel Oliveira</b></sub></a><br /><a href="https://github.com/marlin-dev/fastapi-mail/" title="Documentation">📖</a> <a href="#maintenance-jakebolam" title="Maintenance">🚧</a></td>

  </tr>
  


</table>


This project follows the [all-contributors](https://allcontributors.org) specification.
Contributions of any kind are welcome!


## LICENSE

[MIT](LICENSE)
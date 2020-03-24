---
title: "Making a telegram bot for coronavirus stats"
tags: [python, bots]
style: 
color: 
image: "/assets/images/telegram_bot_corona_virus/cover.jpg"
description: "Creating a Telegram bot which can give you stats related to COVID-19"
permalink: making-a-telegram-bot-for-coronavirus-stats
---

![covid19]({{site.baseurl}}/assets/images/telegram_bot_corona_virus/cover.jpg)

> **_“Numbers never lie, after all: they simply tell different stories depending on the math of the tellers.”_**

> ― Luis Alberto Urrea,

The world is going through a war—a virus that developed in the Chinese city **Wuhan** has made everyone go for hiding. Everyone is scared of everyone. This situation has made every person very vulnerable. 

These days people are always using the internet to see the stats. These stats show them the seriousness of this pandemic. These stats tell them how much they should panic. Being quarantined at home gives me so much time to create something valuable.

I decided to make a bot that updates us with stats. At first, I wanted to make a **WhatsApp bot**, but there is no official support for that. There was one implementation using Twilio, but it was asking me for some bucks. So, I ended up making Telegram bot.

### Approach

The approach was pretty simple. For getting the stats, I scraped a website using `beautiful soup`, which was having all the data needed. 

Steps:

1. Scrape the website to get all the data. 
2. Prettify it to make it presentable.
3. Generating Token for Telegram bot.
4. Attach it to python-telegram-bot library.
5. Deploy it on Heroku.

Install `bs4` and `python-telegram-bot` using pip before moving forward.
```
pip install bs4
pip install python-telegram-bot
```

{% include elements/highlight.html text="Note:- I just want to make it clear that the code you are going to see is a complete mess because it was written within an hour." %}

#### 1. Scraping the website

I used [worldinfo](https://www.worldometers.info/coronavirus/) website to get all the data. I noticed that all the data is kept here **inside the table**. So I fetched all the data inside the <tr> tag and from each <tr> tag, I fetched data from <td> tag.

code:

```python
from urllib.request import urlopen
from bs4 import BeautifulSoup

def get_country_data(country):
    url = "https://www.worldometers.info/coronavirus/"
    contents = BeautifulSoup(urlopen(url).read(), \
        features="html.parser")

    table_data = [[cell.text.strip().strip(':').lower() \
        for cell in row("td")] for row in contents("tr")]
    table_data = table_data[1:]
    for i,countries in enumerate(table_data):
        if len(countries) == 0:
            return None
        if country.lower() == countries[0]:
            return table_data[i]
    return None


def get_state_data(state):
    url = "https://www.mohfw.gov.in"
    contents = BeautifulSoup(urlopen(url).read(), \
        features="html.parser")

    table_data = [[cell.text.strip().strip(':').lower() \
        for cell in row("td")] for row in contents("tr")]
    final = []
    i = len(table_data) - 1
    while i >= 0:
        if len(table_data[i]) == 0:
            break
        final.append(table_data[i])
        i -= 1
    final.pop(0)

    [j.pop(0) for j in final]

    for i,s in enumerate(final):
        if s[0].lower() == state:
            return final[i]
    return None
```

#### 2. Prettifying the data

After getting the country data, it should be formatted in a way to make it look presentable. There is nothing fancy here, and it is working as a **wrapper** to the previous function.

code:

```python
def pritify_country(country):
    columns = ["Country", "Total Cases", "New Cases", \
        "Total Deaths", "New Deaths", "Total Recovered", \
        "Active Cases", "Serious Cases", "Tot Cases/1M pop"]
    data = get_country_data(country)
    if not data:
        return "NOT FOUND"
    final = ""
    for first, second in zip(columns, data):
        final += f"{first}: {second}\n"
    final += "\n\nsource - https://www.worldometers.info/coronavirus/"
    return final

def pritify_india(state):
    columns = ["State","Confirmed cases(Indian)",\
        "Confirmed cases(Foreign)", "Cured", "Death"]
    data = get_state_data(state)
    if not data:
        return "Not found"
    final = ""
    for first, second in zip(columns, data):
        final += f"{first}: {second}\n"
    final += "\n\nsource - https://www.mohfw.gov.in"
    return final
```

#### 3. Generating Token

Before moving forward and making it a full-fledged bot, we need to generate a token. This token is a secret key and should not be shared.

For getting the token, go to telegram and search for [BotFather](https://t.me/BotFather) and send the message `/newbot`. After that, follow the instructions. The token will get generated, and we will use it afterward.

#### 4. Attaching it to python-telegram-bot

I used a [boilerplate](https://github.com/python-telegram-bot/python-telegram-bot/blob/master/examples/echobot2.py) template which was already there in the example and made the changes according to my need.

So the complete code was something like this - 

```python
from urllib.request import urlopen
from bs4 import BeautifulSoup

# Helper function for parsing data
def get_country_data(country):
    url = "https://www.worldometers.info/coronavirus/"
    contents = BeautifulSoup(urlopen(url).read(), \
        features="html.parser")

    table_data = [[cell.text.strip().strip(':').lower() \
        for cell in row("td")] for row in contents("tr")]
    table_data = table_data[1:]
    for i,countries in enumerate(table_data):
        if len(countries) == 0:
            return None
        if country.lower() == countries[0]:
            return table_data[i]
    return None

def pritify_country(country):
    columns = ["Country", "Total Cases", "New Cases", \
        "Total Deaths", "New Deaths", "Total Recovered", \
        "Active Cases", "Serious Cases", "Tot Cases/1M pop"]
    data = get_country_data(country)
    if not data:
        return "NOT FOUND"
    final = ""
    for first, second in zip(columns, data):
        final += f"{first}: {second}\n"
    final += "\n\nsource - https://www.worldometers.info/coronavirus/"
    return final

def get_state_data(state):
    url = "https://www.mohfw.gov.in"
    contents = BeautifulSoup(urlopen(url).read(), \
        features="html.parser")

    table_data = [[cell.text.strip().strip(':').lower() \
        for cell in row("td")] for row in contents("tr")]
    final = []
    i = len(table_data) - 1
    while i >= 0:
        if len(table_data[i]) == 0:
            break
        final.append(table_data[i])
        i -= 1
    final.pop(0)

    [j.pop(0) for j in final]

    for i,s in enumerate(final):
        if s[0].lower() == state:
            return final[i]
    return None

def pritify_india(state):
    columns = ["State","Confirmed cases(Indian)",\
        "Confirmed cases(Foreign)", "Cured", "Death"]
    data = get_state_data(state)
    if not data:
        return "Not found"
    final = ""
    for first, second in zip(columns, data):
        final += f"{first}: {second}\n"
    final += "\n\nsource - https://www.mohfw.gov.in"
    return final


# Telegram bot configs
import logging, os
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
                    level=logging.INFO)
logger = logging.getLogger(__name__)

def start(update, context):
    update.message.reply_text('Hi! \nThis bot can give you live '\
        'stats of corona virus cases.\n\nType /help\n\nAny issue? ' \
        'admin@amanroy.me')

def help(update, context):
    update.message.reply_text('Commands\n\n/help - shows this ' \
        'menu\n/links - shows important links related to corona ' \
        'virus\n/get country_name - shows corona case stats of ' \
        'that country\n/get total - shows total cases across ' \
        'countries\n/india state_name - Show stats related to that state')

def links(update, context):
    update.message.reply_text('Here are some important links ' \
        '-\n\nWHO Myth Busters: http://tiny.cc/coronamyth\nCorona ' \
        'information(India): http://tiny.cc/mohfw\nQ&A on ' \
        'coronavirus: http://tiny.cc/qandawho')

def get(update, context):
    user_says = " ".join(context.args)
    update.message.reply_text(pritify_country(user_says.lower()))

def india(update, context):
    user_says = " ".join(context.args)
    update.message.reply_text(pritify_india(user_says.lower()))

def error(update, context):
    logger.warning('Update "%s" caused error "%s"', update, context.error)


def main():
    updater = Updater(os.getenv('TOKEN'), use_context=True)
    dp = updater.dispatcher

    dp.add_handler(CommandHandler("start", start))
    dp.add_handler(CommandHandler("help", help))
    dp.add_handler(CommandHandler("links", links))
    dp.add_handler(CommandHandler("india", india))
    dp.add_handler(CommandHandler("get", get))

    dp.add_error_handler(error)

    # Start the Bot
    updater.start_polling()

    updater.idle()


if __name__ == '__main__':
    main()
```

You can run the above code like this - 
```
export TOKEN="<your_token>"
python app.py
```

#### 5. Deploying it to Heroku

You can't keep your computer running. You need to deploy it somewhere so that it can run even if your system is down. I generally prefer Heroku. You can choose any cloud service.

Before deploying it to Heroku, You need three files there with the python script.
- Procfile
- requirements.txt
- runtime.txt

In Procfile, write - 
```
worker: python app.py
```

In requirements.txt, write - 
```
bs4
python-telegram-bot
```

In runtime.txt, write - 
```
python-3.8.2
```

Make sure that you have [Heroku cli](https://devcenter.heroku.com/articles/heroku-cli) and Git installed.

Type these commands to make them up and running - 
```
heroku login
git init
git add .
git commit -m "initial commit"
heroku create your-app-name
git push heroku master
heroku config:set TOKEN=<your-app-token>
heroku ps:scale web=1
```

For checking if it is working fine or not - 
```
heroku logs --tail
```

Wolla, It's done!

I have deployed it and you can use it here - [Corona Telegram Bot](https://t.me/chinacoronacovid19amanbot)

See code - [GitHub Repo](https://github.com/aman-roy/corona-stats-telegram-bot)


### Conclusion

Making this bot was just a way to spread awareness about the COVID-19. Be at home, make the best out of this time, stay away from fake news. Getting infected with the virus is as easy as making this bot. So, it's better to treat wellness than treating illness.
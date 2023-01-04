# What is this project about?

This project is an internal tool of the ELVA AI company, which has the function of streamlining the control of tasks carried out and tasks to be carried out by employees. It consists of writing a report of what they did yesterday and what they plan to do. Today, this streamlines the process of administration and assignment of tasks.

## Table of Contents

1.  [What is this project about?](#what-is-this-project-about)
2.  [In what technologies is the system developed?](#in-what-technologies-is-the-system-developed)
    1. [What was appsmith used for?](#what-was-appsmith-used-for)
    2. [What was Strapi used for?](#what-was-strapi-used-for)
    3. [What was Python used for?](#what-was-python-used-for)
    4. [What was AWS used for](#what-was-aws-used-for)
3.  [Endpoints](#endpoints)
4.  [YesterdayReports Endpoint](#yesterdayreports-endpoint)
5.  [TodayReports](#todayreports)
6.  [Users](#users)
7.  [Appsmith Structure](#appsmith-structure)
    1. [How does the login work?](#how-does-the-login-work)
    2. [How does the Dashboard work?](#how-does-the-dashboard-work)
       1. [JsObjects](#jsobjects)
       2. [API Calls](#api-calls)
    3. [How does the logout function work?](#how-does-the-logout-function-work)

### In what technologies is the system developed?

This project was developed in 3 technologies and uses an aws server

### What was appsmith used for?

We use appsmith to build the frontend part and connect it with the strapi api to send the reports to the database

### What was Strapi used for?

Strapi was used as the NoSQL database where we created the collections and the users in Strapi stored the reports, the endpoints were also built from Strapi, basically it was used as the project database.

### What was Python used for?

Python was used to consume the strapi api and the slack api
got the reports from strapi and wrote them with the slack api in a slack channel.

### What was AWS used for

Aws was used to host strapi

Technologies:

1. Appsmith
2. Strapi
3. Python

Apis:

1. Strapi
2. Slack

Servers

1. AWS

## Endpoints

Next I deliver the endpoints with which this project works

Strapi endpoints

API URL: `http://100.26.148.198:443`

### YesterdayReports Endpoint

Endpoint: `/yesterdays`
Curl

```bash
curl -X GET http://100.26.148.198:443/yesterdays
```

Python

```py
def getyesterday():
   url = "http://100.26.148.198:443/yesterdays"

   response  = requests.get(url)

   if response.status_code == 200:
     return response.text
   else:
     print("Error: Status code", response.status_code)
```

### TodayReports

Endpoint: `/todays`

Curl

```bash
curl -X GET http://100.26.148.198:443/todays
```

Python

```py
def gettoday():
 url = "http://100.26.148.198:443/todays"

 response = requests.get(url)

 if response.status_code == 200:
     return response.text
 else:
   print("Error: Status code", response.status_code)
```

### Users

Endpoint: `/logins`

Curl

```bash
curl -X GET http://100.26.148.198:443/logins
```

Python

```py
def gettoday():
 url = "http://100.26.148.198:443/logins"

 response = requests.get(url)

 if response.status_code == 200:
     return response.text
 else:
   print("Error: Status code", response.status_code)
```

## Appsmith Structure

The part by appsmith consists of two pages
Login and Dashboard

### How does the login work?

The login page consists of a get request for existing users in Strapi and a JS object that validates and stores the data of the temporarily logged in user to local storage if the login succeeds and is redirected to the dashboard. Login does not require passwords, just email.

JSobject

```js
export default {
  loginval: () => {
    LoginAPI.run();
    let logindata = LoginAPI.data;
    console.clear();
    for (let data of logindata) {
      if (data.mail == Mail.text) {
        storeValue("data", data);
        navigateTo("Dashboard", {}, "SAME_WINDOW");
      }
    }
  },
};
```

### How does the Dashboard work?

The dashboard consists of 4 JS objects and 3 API calls. I will briefly explain each of them.

### JsObjects

1. FilterReports
   - This object filters the reports that belong to the logged in user and displays them in the table widget
2. Logout
   - This object clears the local storage and redirects to the login page
3. Postdata
   - This object is responsible for executing two queries, the PostToday and PostYesterday query, these two queries are responsible for writing the reports of the previous day and today's plan in the database.
4. Validation
   - This object is in charge of validating that a user is logged in otherwise it will redirect him to the login page

### API'S

1. GetYesterdayReports
   - This query is responsible for obtaining the reports of yesterday and displaying them in a table
2. PostToday
   - This query takes care of writing to the database that you plan to do today
3. PostYesterday
   - This query is responsible for writing to the database what you did yesterday

## Python slack bot.

The python slack bot is the python program that controls the slack bot that writes the reports at the end of the day in slack. It consists of 4 python files that I will describe below.

### Python

1. GetData
   - This file is in charge of obtaining the reports from strapi
2. FormatData
   - This file is responsible for iterating each existing report, extracting the user and the pod and the report and giving it a format to later call `PostMessage` and pass it the information of the already formatted message.
   - The `message` variable that is inside the for is where we can modify the format of the message that will be written in slack
3. PostMessage
   - This file is responsible for grabbing the message from `FormatData` and writing it to the slack channel via a POST request.
4. TimerToActiveSYS
   - This file is in charge of verifying what time it is of the day and at 6pm it will send the accumulated reports of the day.

### AWS

El programa de python y la base de datos de strapi se encuentran en este servidor de AWS

In the Status Update folder I wrote a file in bash that you have to run it to start the project use the following command

Run program: `./start`

# Final group project

# Contents
1. [Overview](https://github.com/geoffdibb/Beholder-Deployment#final-group-project)
2. [Project Architecture](https://github.com/geoffdibb/Beholder-Deployment#project-architecture)
3. [Deployment](https://github.com/geoffdibb/Beholder-Deployment#deployment)
4. [Logging in](https://github.com/geoffdibb/Beholder-Deployment#logging-in)
5. [Using the search function](https://github.com/geoffdibb/Beholder-Deployment#using-the-search-function)
6. [Testing](https://github.com/geoffdibb/Beholder-Deployment#testing)


# Overview
This application allows you to search a database for potential information on a number of factors with multiple search categories. It is also secured with Passport and Bcrypted behind a login wall.

# Project Architecture
Our project uses a mixture of different softwares to create a working application. We have several springboot API’s that handle the Audit and Search functionality. We also make use of a react frontend and a node backend to cope with a secure login making use of Bcrypt and Passport. This is then linked with nginx to allow the various services to communicate with each other. Additionally, this links to several databases. The main database is kept separate to preserve the integrity of the data and add an extra level of security.
One final component worth mentioning is the Queue which handles the requests from the Core API to the Audit API. This is implemented using ActiveMQ and serves as a handler to the audit api preventing simultaneous requests from slowing down the application.

![project architecture](https://raw.githubusercontent.com/geoffdibb/Beholder-Deployment/master/Documents/architecture.png)

# Deployment

**Requirements**
*	cloud server (e.g. GCP or Azure)
*	Docker/Docker-compose installed on the vm
*	Mongodb container running on a seperate vm

You will also need to clone down these projects into the root folder.

```
git clone https://github.com/geoffdibb/Beholder-Audit.git
git clone https://github.com/geoffdibb/Beholder-Core.git
git clone https://github.com/geoffdibb/Beholder-User.git
git clone https://github.com/geoffdibb/Beholder-Frontend.git
git clone https://github.com/geoffdibb/Beholder-Search.git
```
Beholder-Audit will need an application.properties file in src/main/resources

```
server.port=8081
spring.activemq.broker-url=tcp://localhost:61616?jms.redeliveryPolicy.maximumRedeliveries=1
spring.activemq.user=[USERNAME]
spring.activemq.password=[PASSWORD]
spring.data.mongodb.uri=mongodb://[USERNAME]:[PASSWORD]@mongoaudit:27018/userLogs

path.getSearchLogs=/getSearchLogs
path.getAuditRequestLogs=/getAuditRequestLogs
path.searchLog=/searchLogs
path.accessLogs=/accessLogs
path.getAuditUserAccessLogs=/getAuditUserAccessLogs
path.requestLogs=/requestLogs
```

Beholder-Search will need an application.properties file in src/main/resources

```
server.port=8082

spring.data.mongodb.uri=mongodb://[USERNAME]:[PASSWORD]@[MONGODB_IP]:27017/beholder

spring.main.allow-bean-definition-overriding=true

path.search=/search
path.searchCategory=/{category}/{searchTerm}
```

Beholder-user will need a config folder with 3 files: keys.js, jwtConfig.js & passport.js.
**keys.js**

```
module.exports = { mongoURI: "mongodb://[USERNAME]:[PASSWORD]@mongoUser/UserDatabase"}
```

**jwtConfig.js**

```
module.exports = {
    secret: "[SECRET]",
};
```
**passport.js**

*Passport implements a local strategy for login and api requests use a jwt strategy. It also implements bcyrpt for password storage*

**Run the docker compose.yaml file**

```
docker-compose up
```

# Logging in 
Upon navigating to the hosted url you will need to enter a valid username and password. These cannot be made from the frontend and will be provided if you have the relevant permissions. On entering these details in the relevant fields and checking the bot checkbox you will be able to successfully login and navigate to the landing page.

# Using the search function
Select a category from the dropdown list and enter a searchterm in the textbox before clicking the search button. This will return a list of profiles that match the searchterm. Clicking on a profile will lead to a detailed page depicting more information on them. This will also display the known associates with a dropdown with more detailed info on them.
Use of the inbuilt back button instead of the browser provided button is recommended if you want the data to persist.


# Testing
Testing was completed with Sonarqube, jacoco and manual reviews. Test coverage is over 70% and code smells are at acceptable levels with 10 over all three springboot API with no current bugs recorded. Manual testing was also performed to a destructive degree and the site held up under these conditions.

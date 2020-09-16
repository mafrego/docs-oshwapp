# OSHW Web App

[![version](https://img.shields.io/badge/version-1.0.0-blue)](https://github.com/mafrego/docs-oshwapp/blob/master/CHANGELOG.md)
[![License: AGPL v3](https://img.shields.io/badge/License-AGPL%20v3-blue.svg)](https://www.gnu.org/licenses/agpl-3.0)

Web application for searching open source hardware products and managing their versions.

## Table of Contents

- [Introduction](#introduction)
- [Features](#features)
- [Stack](#stack)
- [Architecture](#architecture)
- [Installation](#installation)
- [Run](#run)
- [Tests](#tests)
- [Status](#status)
- [Contributing](#contributing)
- [Credits](#credits)
- [License](#license)
- [Contacts](#contacts)

## Introduction

The first aim of this application is to provide a common online platform for searching open source hardware products and for managing their versions.  
The second aim is to empower the Maker and OSHW community, the Fab Lab network and any DIY hobbyst to collaborate easily on common projects and to share their knowledges effectively.  
This application has been being developed as bachelor's thesis project at the university of Innsbruck.  

## Features

Users of this platform will be able to:

- retrieve rapidly the OSHW object they are looking for or any part of it,
- find all the relevant data relative to it,
- visualize its tree structure,
- upload an object BOM and the relative images,
- assemble the object from the BOM and
- manage all its possible next versions.

## Stack

NEVN stack is listed below along with all relevant technologies (i.e. libraries, frameworks, plugins, middleware, etc. in parenthesis):

- Neo4j (APOC)
- Express.js (Neode, jsonwebtoken, multer)
- Vue.js (Vuex, Vuetify, Vue Router, axios)
- Node.js

## Architecture

The application has the following architecture:

- Node.js Express exports REST APIs and interacts with Neo4j database using Neode OGM.
- Vue Client sends HTTP Requests and retrieves HTTP Responses using axios, consume data on the components. Vue Router is used for navigating to pages and Vuex serves as centralized store for all the Vue components.

## Installation

### Preliminary remark

The following instructions for installing the application locally in dev mode are tested on Linux OS, namely Ubuntu 18.04 and 20.04. We can't assure that they will be appropriate for other Operating Systems.

### Prerequisites

We assume that Neo4j and Node.js are already installed and properly working on your system, if not, follow the relative link to install them:

- Neo4j Enterprise Edition 4.0 [donwload](https://neo4j.com/download-center/#enterprise) or, preferably, run Neo4j in Docker [how-to](https://neo4j.com/developer/docker-run-neo4j/)
- Node.js latest LTS version (includes npm) [download](https://nodejs.org/it/download/)

We also assume that you have your own AWS account and you are able to manage S3 Buckets in order to upload images and [`yourProject-BOM.csv`](https://github.com/mafrego/docs-oshwapp/blob/master/OSHWBOMprotocol.md) files.

### Clone repository

- Clone the two git repositories of our application(oshwapp) in two separate directories: client-oshwapp and server-oshwapp
- Clone the repo with SSH or using HTTPS:  
`$ git clone <repo>`

### Install node packages with npm

- go inside the `server/` directory and then install the npm packages:  
`$ cd oshwapp/server/`  
`$ npm i`
- then go inside the `client/` directory and then install the npm packages:  
`$ cd ../client/`  
`$ npm i`  

### Adding .env file for Neo4j credentials

- in the `oshwapp/server/` folder add a file named `.env` with the following content (lines starting with # are comments):  
```
NEO4J_PROTOCOL=bolt
NEO4J_HOST=localhost
NEO4J_USERNAME=your_neo4j_username
NEO4J_PASSWORD=your_neo4j_admin_password
#bolt port should be 7687 by default
NEO4J_PORT=7687
#used in AuthJWT.js
JWT_SECRET=your_jwt_secret_word
```
- You need to substitute `your_neo4j_username` and `your_neo4j_admin_password`  with the username and password that you have chosen before during the Neo4j data base set up and `your_jwt_secret_word` with any string you like for the encryption of json web tokens.  
- The `.env` file has to be in the same folder from where you are going to run the Express.js server later with the command `npm run start`. 

### Adding AWS S3 Bucket credentials (not necessary)

- in the same `.env` file of server add the following lines:
```
#AWS-S3 bucket
BUCKET_NAME=your_S3_bucket_name
AWSAccessKeyId=your_access_key_id
AWSSecretKey=your_secret_key
```
- and substitute `your_S3_bucket_name` with a name you choose and the other two values with the credentials provided by your AWS account and created in the AWS website following:  
`username -> My Security Credentials -> Access keys (access key ID and secret access key) -> Create New Access Key`.

### Adding client url in the whitelist of CORS options

- in the `server/src/app.js` file of server add 'http://localhost:8080' into the `whitelist` array or the ip of the machine running the client(Vue.js) if this is on a different device (with Linux you can find this ip using the command `ifconfig`). 

### Adding an .env file for the Vue environment variables

- go inside `oshwapp/client/` folder:   
- add a file named `.env` with the following content:  
```
VUE_APP_BASE_URL=url_of_your_local_express_server
```
- substitute `url_of_your_local_express_server` with `localhost:8081` or with the ip of the machine running the server, something like `http://192.168.XXX.XXX:8081`, if you want to get access to the application from a different device (with Linux you can find this ip using the command `ifconfig`). 

## Run

In order to run the application in development mode you have to start Neo4j first and then both the backend server (Express.js) and the frontend server, that is the client (Vue.js), from two distinct shell instances.  
Let's see now all the single steps.

### Start Express and Vue
  
- Start Neo4j database and make sure that is using the same bolt port as stated in the `.env` file previously created. 
- Neo4j has also a gui accessible with `http://localhost:7474/` from  your browser.  
- Open the first shell tab and go to the `server/` directory (i.e. the root folder of Express.js)  
- then start the server with the following command:  
`$ npm run dev-start`
- then open the second shell tab and go to the `client/` directory (i.e. the root folder of Vue.js)
- then start the client with the folowing command:  
`$ npm run serve`
- now you should be able to access the web application from your browser with the URL `http://localhost:8080/`. 
- if you want to use the second URL showed by the Vue.js console, don't forget to add this URL to the whitelist array in the `server/src/app.js` file.   

### Add the administrator role

Since by default all users are registered with the "assembler" role, if you want to register yourself as "admin" you have to change the role manually.

There are two ways to do that, first option:
- Go to the file `oshwapp/server/src/controllers/AuthenticationController.js`.
- At line 12 substitute the line `const roles = ["assembler"]` with the line `const roles = ["admin"]`. 
- Save and then register yourself in the application. 
- Once you get your account registered with "admin" role (you can check it in the application profile or directly in the Neo4j browser), change again the role in `AuthenticationController.js` file to `const roles = ["assembler"]`.

Second option:
- Go to the Neo4j gui on the browser `http://localhost:7474/` or access the Cypher shell on the terminal
- and then run the following two Cypher queries, first:
```
CREATE (r:Role { name: 'admin'})
```
- then run the following query after substituting `UserName` with the username of the user you want to promote as the app administrator:
```
MATCH (a:User),(b:Role)
WHERE a.username = 'UserName' AND b.name = 'admin'
CREATE (a)-[r:HAS_ROLE]->(b)
RETURN type(r)
```

## Tests

### Tests on client

Test types:
- unit tests: Jest
- integration tests: Jest
- end to end tests: Cypress

Test environment set but no tests implemented: collaboration on this point would be very appreciated.

### Tests on server

Test type:
- unit test: Jest

Test environment set but no tests implemented: collaboration on this point would be very appreciated.

## Status

The project is in progress.

## Contributing

Open to contributions:
- first of all contribute on how to contribute: contact Marco.Fregonese@student.uibk.ac.at

## Credits

Here a list of tutorials I followed to solve specific problems:

* [tab-traker](https://github.com/codyseibert/tab-tracker) by Cody Seibert for the basic set up of Express and Vue.   
* [vue-tree-browser](https://github.com/codyseibert/vue-tree-browser) by Cody Seibert for the recursion of Vue components.
* [vuex_todo_manager](https://github.com/bradtraversy/vuex_todo_manager) by Brad Traversy for the set up of Vuex.
* [vue-vuex-jwt-auth](https://github.com/bezkoder/vue-vuex-jwt-auth) by bezcoder for the set up of jwt authentication and autorization in Vue.
* [neode-example](https://github.com/adam-cowley/neode-example) by Adam Cowley for the set up of neode.
* [upload](https://www.youtube.com/watch?v=GXe_JpBQLTQ) by Rem Zolotykh for uploading files with Vue and Express.

## Contacts

Student: Marco.Fregonese@student.uibk.ac.at  
Supervisor: Michael.Breu@uibk.ac.at  

[![Foo](https://www2.uibk.ac.at/stylesheets/15/images/350/logo-uibk.svg)](https://qe-informatik.uibk.ac.at/)

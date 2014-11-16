---
layout: page
title: Historical Document Management System
permalink: /projects/prcc/
---

This project was to create a website where the Chicago Puerto Rican Cultural Center (PRCC) could scan, tag and host digital versions of their historical documents. 

#### Note
*The project is currently being set up on a new server and is offline for the moment. Once the project goes live I will post a link here.*

<br>  

### Features
- Remotely scan in documents
- Document permissions
- User roles
- Ability to tag documents
- Search documents
  
  

### Technology Stack
- PHP 5.5
- MySQL
- Javascript
- JQuery/JQuery UI
- HTML/CSS
  
  
### Architecture
- MVC with custom router
	- Router supports controller/action/parameters url
	- Router also supports subcontrollers like controller/subcontroller/action/parameters
- Custom caching system for application settings
- Custom asset management with cache busting feature
- Dependency Injection
- Dispatcher Ajax system
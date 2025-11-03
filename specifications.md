# Cookbook Website Specifications

---

## Purpose

To provide a common interface to post and find recipes to fit all types of cooking needs, including cooking meals, desserts, breads, and baking.

Technical purpose is to help develop full-stack skills and fluency with specific technologies. Those technologies include React with Typescript, Java using Javalin, and MySQL.

---

## Diagrams

[Wireframes in Figma](https://www.figma.com/design/Hx3vtKCXFi5g6MMalYhWxy/CookBook-Wireframes?node-id=2-2&t=raKiuMf55VLQ5qZJ-0)

[UML Diagrams in LucidChart](https://lucid.app/lucidchart/07936146-58ea-48e8-a4fc-107e084cbcad/edit?beaconFlowId=2009C4B2BFB40410&page=0_0&invitationId=inv_63e03314-02dd-492b-8ab9-c7e369dcdf79#)

---

## Endpoints

---

### Register a new user

path: /api/user/create  
method: POST  

**Request:**  

Headers: None  
Body (JSON):  
{  
"username": username,  
"email": email,  
"password": plainTextPassword,  
}  

Username and email are unique.

**Response:**  

Status: 201 (Created)  
Body (JSON):  
{  
"username": username,  
"authToken": authToken  
}

Status: 400 (Bad Request)  
Body (JSON):
{  
"message": errorMessage  
}  

---

### Login a user

path: /api/user/login  
method: POST  

**Request:**  

Headers: None  
Body (JSON):  
{  
"identifier": usernameOrEmail,  
"password": plainTextPassword,  
}  

Since both username and email are unique, you can login with either.

**Response:**  

Status: 200  
Body (JSON):  
{  
"username": username,  
"authToken": authToken,  
}  

Status: 

---

### Logout a user

path: /api/user/logout
method: DELETE

**Request:**  

Headers: Authentication  
Body:  
None
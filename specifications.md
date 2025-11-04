# Cookbook Website Specifications

---

## Purpose

To provide a common interface to post and find recipes to fit all types of cooking needs, including cooking meals,
desserts, breads, and baking.

Technical purpose is to help develop full-stack skills and fluency with specific technologies. Those technologies
include React with Typescript, Java using Javalin, and MySQL.

---

## Diagrams

[Wireframes in Figma](https://www.figma.com/design/Hx3vtKCXFi5g6MMalYhWxy/CookBook-Wireframes?node-id=2-2&t=raKiuMf55VLQ5qZJ-0)

[UML Diagrams in LucidChart](https://lucid.app/lucidchart/07936146-58ea-48e8-a4fc-107e084cbcad/edit?beaconFlowId=2009C4B2BFB40410&page=0_0&invitationId=inv_63e03314-02dd-492b-8ab9-c7e369dcdf79#)

---

## Endpoints

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

Username and email are unique, all fields must be provided.

**Response:**

Success:  
Status: 201 (Created)  
Body (JSON):  
{  
"username": username,  
"authToken": authToken  
}

Failures:  
Status: 400 (Bad Request - malformed attempt), 409 (Conflict - existing user), 500 (Server Error)  
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

Since both username and email are unique, you can log in with either. All fields must be provided.

**Response:**

Success:  
Status: 200 (OK)  
Body (JSON):  
{  
"username": username,  
"authToken": authToken,  
}

Failures:  
Status: 400 (Bad request - malformed attempt), 401 (Unauthorized - bad pass or user), 500 (Server Error)  
Body (JSON):  
{  
"message": errorMessage  
}

---

### Logout a user

path: /api/user/logout  
method: DELETE

**Request:**

Headers: Authentication  
Body:  
None

**Response:**

Success:  
Status: 200 (OK)  
Body:  
None

Failures:  
Status: 401 (Unauthorized - no authentication), 500 (Server Error)  
Body (JSON):  
{  
"message": errorMessage  
}

---

### Get User for Display

path: /api/user/{username}  
method: GET

**Request:**

Headers: Authentication  
Body:  
None

**Response:**

Success:  
Status: 200 (OK)  
Body (JSON):  
{  
"username": username,  
"email": email  
}

Failures:  
Status: 400 (bad request - malformed), 401 (Unauthorized - bad auth), 404 (Not found - username doesn't exist), 500 (
Server error)  
Body (JSON):  
{  
"message": errorMessage  
}

---

### Change Authentication Credentials

path: /api/user/change  
method: PUT

**Request:**

Headers: Authentication  
Body (JSON):  
{  
"username": currentUsername,  
"password": currentPassword,  
"field": "username"/"email"/"password",  
"value": newValue  
}

Username must be provided as the old username and password must be provided as the old password, used for verifying
proper credentials before change. Field must be one of the three options listed (which one to change) and the value
contains the new value to replace the existing. New usernames and emails must remain unique.

**Response:**

Success:  
Status: 200 (OK)  
Body (JSON):  
{  
"username": username,  
"email": email,  
"authToken": authToken  
}

Failures:  
Status: 400 (Bad request - malformed), 401 (Unauthorized - bad auth or incorrect password), 409 (Conflict - existing
user or email), 500 (Server Error)  
Body (JSON):  
{  
"message": errorMessage  
}

---

## Search Engine

- List of all recipies by a certain user can be obtained just with SQL search
- Build a map of keywords, with the key being the keyword and the value being a list of all recipeIDs containing the
  keyword
- Save this map into a SQL table for persistence across reloads
- Update map each time a new recipe is added
- Have a ScheduledExecutorService to update the map each day?
- Build the map in one function, then replace the map being used after the new map is built
- A user types in the query on the frontend, it sends the query to the backend that will search the map and respond with
  an ordered list, top of the list is the closest match
- If more than 50% of the query's characters match a key, the key is included in the result to account for misspelling?
  these are given lower priority
- exact matches at the top
- Phrase matches have higher priority
- tokenize into individual words, somehow ignore filler words that don't matter - use a predefined list of stopwords,
  include it as a set for the tokenizer to ignore
-

## Java Data Models

UserData:

| Field    | Type         |
|----------|--------------|
| username | String (255) |
| email    | String (255) |
| password | String (255) |

AuthData:

| Field       | Type          |
|-------------|---------------|
| username    | String (255)  |
| authToken   | String (255)  |
| dateCreated | LocalDateTime |

RecipeData:

| Field           | Type                    | Description                                                         |
|-----------------|-------------------------|---------------------------------------------------------------------|
| recipeID        | int                     | id of recipe, generated by SQL                                      |
| recipeName      | String (255)            | Name of the recipe                                                  |
| postedBy        | String (255)            | Username of poster                                                  |
| ingredientsList | Collection\<Ingredient> | List of ingredients for the recipe                                  |
| cookTime        | String (255)            | How long to cook, example: 30 minutes, 1.5 hours, 2-3 minutes       |
| directions      | Collection\<String>     | List of instructions, each instruction is a String                  |
| categoryByType  | TypeCategory            | Which category the recipe fits in, defined using an enum            |
| amount          | int                     | Either how many items or how many people it serves                  |
| otherRecipes    | Collection\<RecipeData> | List of other recipes that are necessary for this one (sauces, etc) |
| temperature     | int                     | Temperature the recipe cooks at, if applicable                      |

Ingredient:

| Field      | Type         | Description                           |
|------------|--------------|---------------------------------------|
| amount     | String (255) | how much is needed                    |
| amountType | String (255) | what measurement is used (cups, tbps) |
| name       | String (255) | ingredient name                       |

ENUM - TypeCategory:

| Type      | Description                          |
|-----------|--------------------------------------|
| Breakfast | Breakfast foods                      |
| Entree    | Main dishes                          |
| Side      | Sides to accompany entrees           |
| Salad     | Salads (duh)                         |
| Soup      | Soups                                |
| Dessert   | Desserts                             |
| Bread     | Any type of bread                    |
| Snack     | Snacks, appetizers, dips, etc        |
| Sauce     | Something that classifies as a sauce |
| Beverage  | Any kind of drink                    |

## SQL Data Tables

users:

| Field    | Type         | Additional                            |
|----------|--------------|---------------------------------------|
| id       | int          | NOT NULL, AUTO_INCREMENT, PRIMARY_KEY |
| username | VARCHAR(255) | NOT NULL, INDEX                       |
| email    | VARCHAR(255) | NOT NULL, INDEX                       |
| password | VARCHAR(255) | NOT NULL                              |

auth:

| Field     | Type         | Additional                            |
|-----------|--------------|---------------------------------------|
| id        | int          | NOT_NULL, AUTO_INCREMENT, PRIMARY_KEY |
| userID    | int          | NOT_NULL, FOREIGN_KEY                 |
| authToken | VARCHAR(255) | NOT_NULL, UNIQUE, INDEX               |

recipies:

| Field          | Type                   | Additional                            |
|----------------|------------------------|---------------------------------------|
| id             | int                    | NOT_NULL, AUTO_INCREMENT, PRIMARY_KEY |
| name           | VARCHAR(255)           | NOT_NULL, INDEX                       |
| userID         | int                    | NOT_NULL, FOREIGN_KEY                 |
| ingredients    | TEXT                   | DEFAULT NULL                          |
| cookTime       | VARCHAR(255)           | DEFAULT NULL                          |
| directions     | TEXT                   | DEFAULT NULL                          |
| categoryByType | ENUM(see TypeCategory) | DEFAULT NULL, INDEX                   |
| amount         | int                    | DEFAULT NULL, INDEX                   |
| otherRecipes   | TEXT                   | DEFAULT NULL                          |
| temperature    | int                    | DEFAULT NULL                          |

searchEngine:

| Field | Type         | Additional                    | Description                            |
|-------|--------------|-------------------------------|----------------------------------------|
| key   | VARCHAR(255) | NOT_NULL, UNIQUE, PRIMARY_KEY | unique search token                    |
| value | TEXT         | DEFAULT NULL                  | list of recipeIDs that have that token |
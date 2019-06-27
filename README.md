# Express Authentication

## Setup

Initialize and run the app: `npm install` && `npm start`.

The app is using `nodemon`. Any changes made (and saved) will cause the server to restart.

Navigate to the `sql/connections.js` file and alter the following fields to reflect your database setup:

```
  host: 'localhost',
  user: 'root',
  password: 'password',
  database: 'admin'
```

These will be the same credentials we used to set up a connection in MySQL Workbench.

Finally, in MySQL Workbench, run the `initialize.sql` script (on the "admin" database) that is included in this project.

## Overview

The routes/controllers, SQL statements and basic setup has been done for us. Our job is now to complete the functions in the middleware folder and then use them in our routes. 

Keep in mind that your port (4001) may be different.

## Signup and Login

Take a look at the auth routes and auth controller. There is code in these that helps users signup and login to their accounts. We've created a separate table for this in our database called `usersCredentials`. Try making requests to `/auth/signup` and `/auth/login` with a similar request body:

```
{
  "username": "testuser",
  "password": "password"
}
```

Can you signup and login? Notice the `token` that comes back on login.

## Middleware functions

### logger

Create a function called `logger` in the `middleware/index.js` file. It's purpose will be to log the route and date/time that each request happened. The outline of the function will look like this:

```
const logger = (req, res, next) => {

}
```

Inside of this function we will put a `console.log` statement with three arguments separated by a comma:

1. The string, 'Logging route:'
2. The request path ex. /users
3. The date/time in ISO format. Ex. new Date().toISOString()

Remember to call the `next()` function in order to continue. Otherwise, the API call will get hung up in this middleware function.

Import this logger function into the main `index.js` file: `const { logger } = require('./middleware')`

Between the bodyParser and the users router add the following: `app.use(logger)`

This is an example of application specific middleware. Every route will now pass through our logger function and log the path and the date/time that the request was made. This would be useful for determining our most popular routes.


### authenticate

Create a function called `authenticate` in the `middleware/index.js` file. It's purpose will be to check for the existence of an _Authorization header_ and parse/decode/verify the header. This header contains a Bearer token and if that token is valid we will allow the user to access the given resource.

Use the /signup route to signup with a username/password combination. Then call the /login function to retrieve a token. Save that token for later use. We will include it in our Authorization header. 

The outline of the authenticate function should look like this:
```
const authenticate = (req, res, next) => {

}
```

Inside of this function we will do three things:

1. Retrieve the token from the Authorization header: req.headers['authorization']
2. Split the result to get just the token. `header.split(' ')` should return `['Bearer', <TOKEN>]`
3. [Verify the token](https://www.npmjs.com/package/jsonwebtoken#jwtverifytoken-secretorpublickey-options-callback)
4. If successful, add the decoded token to the request object: `req.user = decoded`
5. Call next()

Think about what to do if the token is not verified or the Authorization header does not exist. We probably want to `res.sendStatus(401)` to let the user know that they are not authorized. 

Import `{ authenticate }` into the users router and add it as middleware on all of the users routes. Ex:

`router.get('/', authenticate, usersController.getAllUsers)`

Attempt to call the /users endpoints to GET and POST user data. If you don't included an Authorization header in your request from Postman, you should see an error 401 status. If you did include a valid token you should see a 200 status code and the data should be returned to you.

The Authorization header should look like this: `Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwidXNlcm5hbWUiOiJ0ZXN0dXNlciIsInBhc3N3b3JkIjoiUkVEQUNURUQiLCJpYXQiOjE1NjE1ODM3MDR9.oTiDpNWJ3-JZ1k47nG-xQyi1fk_zlNjRgAbxRrYTlJU`

You are responsible for putting this in Postman manually. 

## Summary

If all went according to plan we now have an API that is locked down with authentication and we have also added middleware on all of our routes that logs the current request and the associated date/time.
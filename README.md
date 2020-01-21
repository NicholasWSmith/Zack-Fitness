# Synctier #

A step by step guide on how to get the Synctier modules running locally.

[Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

# Table of contents
1. [Set Up](#setup)
2. [Social Account Creation](#social)
3. [API Endpoints](#api)
    1. [Facebook](#facebook)
    2. [Email/Password](#email)
    3. [Google](#google)
4. [Docker](#docker)



## Setting up the API locally <a name="setup"></a>

### How do I set up Synctier?  

 1. Get your virtualenv set up using `Python 3.7.5`

    - Install the `requirements.txt` using pip.

    * `pip install -r requirements.txt`

    - Create a `.env` file based off the `.env.example` file found at `social_app/env_settings/.env.example`.
  
    * For secret keys reach out to Jennie Jia or Nick Smith.
    * The database section will depend on which database you want to use. After setting the database variables,
    you will need to make and run migrations.
    
    * `python manage.py makemigrations`
    * `python manage.py migrate`
    
    * Connect to your local database to ensure all the proper tables have been created.
    
 2. Once all of the environment values are set up, you can run your server.

 3. Activate the virtualenv, and run the server. `python manage.py runserver`.
 
 4. Create a superuser for admin related tasks.
    1. `python manage.py createsuperuser `
    2. Create a username, email (can be anything), and a password.


### Social Account Creation <a name="social"></a>

1. To register your social accounts (facebook, google) with the Django application, 
you will need to add them in the admin portal.

    1. With the django project running, go to `http://localhost:8000/admin/`
    
    2. Login with the super user created above.
    
    3. Under `Social Applications`, click `Add`.
    
    4. Select the intended provider, and enter the client ID/Secret from the application.
    
    5. Voila! Your users can now register/login with this application/provider. 
    
### API Endpoints <a name="api"></a>

##### **facebook_login** <a name="facebook"></a>

Usage:

1. The facebook register/login endpoint takes 1 parameter as a POST with data, the users `code`.
This token is retrieved from the login button. 
    * You can test using this curl command. `curl -X POST   http://localhost:8000/api/v1/rest-auth/facebook/ -d '{
"code": "<your-user-token"
}'`
    * Response from API is status code 200 for success, or 400 for "Bad Requeest"
2. For [testing purposes](https://developers.facebook.com/tools/accesstoken/) you can find your
own userToken.

3. In your local database, you will see quite a few new entries. 

    1. `auth_user` - Basic user information.
    2. `account_emailaddress` - Email address for users and verified status.
    3. `authtoken_token` - Each users authorization token. 
    4. `django_session` - Each users current session. 
    5. `socialaccount` - The provider specific data for your user.
    6. `socialtoken` - The user token used to register/login. 

4. Using the `auth_token` on all future API calls will distinguish/authorize the user.

##### **user_login** <a name="email"></a>

Endpoints:

1. User Registration - `/api/v1/rest-auth/registration/`

    Which takes the following parameters:
    
    1. `email` as a String
    2. `first_name` as a String
    3. `last_name` as a String
    4. `password1` as a String
    5. `password2` as a String.
    
    If successful, a 201 response with a dictionary will be returned.
    
    Response Data
    
    1. `{ "key": "<users_auth_token>" }`
    
    If non-successful, a 400 error for `Bad Request` will be returned. 
    Will need to investigate stack trace for specific error.
    
2. Login - `/api/v1/rest-auth/login/`

    Which takes the following parameters:
    
    1. `email` as String
    2. `password` as String
    
    If a user exists, a 200 response with a dictionary will be returned. 
    
    Response Data
    
    1. `{ "key": "<users_auth_token>" }`
    
3. Logout - `/api/v1/rest-auth/logout/`
    

##### **google_login** <a name="google"></a>

In development.

### Who do I talk to? ###

* Jennie Jia, Bo Yuan and Nick Smith for access.

## Docker Build Locally <a name="docker"></a> 

Note: This docker image is using MongoDB.

#### Prerequisite ####
1. Install Docker (i.e. For Mac OS,  install Docker Desktop App)
2. Clone the Repository
3. Create a `.env` file based off the `.env.example` file found at `social_app/env_settings/.env.example`.  Make sure the DB host name same as the DB service name defined in docker-compose.yml.  i.e.
   DB_HOST=mongodb:27017
4.  Go to  your project root folder, run the following command

#### Build docker image ####
   docker-compose build
#### Run docker Images  ####
   docker-compose up -d
#### Check the log ####
   docker compose logs -f   

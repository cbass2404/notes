# Python Flask App Heroku Setup
- Head over to heroku and make an account
- Make sure you have the Heroku CLI installed
  - https://devcenter.heroku.com/articles/heroku-cli#download-and-install
​
## Install Dependencies
> The psycopg2 could have the following errors:
  - Windows most likely needs psycopg2
  - Mac most likely needs psycopg2-binary
- flask-heroku
```
$ pipenv install flask-heroku
```
- psycopg2-binary
```
$ pipenv install psycopg2-binary
```
- gunicorn
```
$ pipenv install gunicorn
```
​
## Add your Procfile to the root
```
web: gunicorn app:app
```
​
## Commit the changes
```
$ git add .
$ git commit -m "added dependencies for heroku push"
```
​
## Create A New App On Heroku
- Create a new app
- Call it df-flask-todo-api
- Head over to the deploy section and copy the code they have for existing repo's
- Head back to your terminal and paste in the code you just copied
  - The folder has to be a git repository.
  - Make sure everything is commited
  - You might have to login after running this command.  Follow the prompts if it asks you to login
```
$ git status
$ heroku git:remote -a df-flask-todo-api
```
- Make sure heroku has been added as a remote
```
$ git remote -v
```
​
## Create A Postgres DB on Heroku
- Click configure addons
- Search for postgres
- After it adds postgress, click on the icon to go to the database
- Go to settings
- View credentials
- Copy the URI
​
## Setup project for environment variables
- You don't really want to send your database url out to github and the rest of the world, so we will want to hide it.
- First create a new file at the root of the project called .env and add the following
```
DATABASE_URL = YOUR_POSTGRESS_URL_GOES_HERE
```
- Update your .gitignore to hide that new file
```
.env
```
- You will also want to install a new dependency called python-dotenv
```
$ pipenv install python-dotenv
```
- You will also want to check heroku's config variables to make sure its setup correctly.  If you remember we're hiding our .env.  After we publish our code to Heroku, it will need to process an environment variable and they call them config variables.
- Head over to your Heroku projects settings tab, click on reveal config vars.  You will see a variable called DATABASE_URL and a value of your postgress database.  In the future if you need to have any other environment variables this is the location to set those up
​
## Change App To Work With Heroku And Postgres
- Update your app.py file
```python
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy
from flask_marshmallow import Marshmallow
from flask_cors import CORS
from flask_heroku import Heroku
​
app = Flask(__name__)
heroku = Heroku(app)
CORS(app)
​
app.config['SQLALCHEMY_DATABASE_URI'] = os.environ.get('DATABASE_URL')
​
# The rest of the code doesn't change
```
- Create the tables in the Database
  - After running the next commands it should create new tables on heroku's postgress database.  You won't see any new files locally like we saw previously with the app.sqlite file before.
```
$ pipenv shell
$ python
>>> from app import db
>>> db.create_all()
>>> 
```
​
## Deploy your application
- Push the code up to heroku and your app should be working
- Don't forget if you didn't setup a '/' route you won't see anything when you go to that route.  You would have to go to say '/todos', and you would see an empty array since you have a newly created database
```
$ git add .
$ git commit -m "added postgress database"
$ git push heroku master
```
​
## Refactor The React Client To Work With Postgres App
- Change any localhost:5000's with your heroku url
- Test it locally
- Commit and push your code
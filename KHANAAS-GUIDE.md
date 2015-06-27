# Guide for implementing Khan AAS

### Create a Django Project
 - `vagrant ssh -c 'django-admin startproject khanaas ./khanaas'`
   - creates a new django environment in your shared directory
   - show that the changes are available from the host environment (mac, windows)
 - settings.py options (psql, maybe more?)
 - Create some minimal view/template/url that we can demo with runtestserver

### Demo runtestserver
 - `vagrant ssh -c './khanaas/manage.py runserver 0.0.0.0:8000'`
 - The server should be running at localhost:8000

### Configure Database Settings
- Create local_settings.py file under `./khanaas/khanaas/` folder. 
- Place database setting in `./khanaas/khanaas/local_settings.py` file

```
DATABASES = { 'default': { 'ENGINE': 'django.db.backends.postgresql_psycopg2', 'NAME': 'khanaas', 'USER': 'postgres', 'PASSWORD': '', } }
```
- Make sure application can connect to database
`vagrant ssh -c './khanaas/manage.py dbshell'`


### Create simple API views
 - create urls for khan/$name and spock/$name, view/template that display $name on the page

### Walk through process for adding an image for the background (khan and spock)

### Walk through the process of styling the input text
 - Probably should just do inline CSS in the template for simplicity, unless you think there is value is demoing the static file functionality (which is a decent argument)

### IDEA investigate if we can have some customizable django admin field for modifying the input text
 - i.e. mike -> mikeeee or miiikkkeee based on some regex in a django-admin text field

### IDEA create a model for the API pages (i.e. khan and spock) so we can log hit counts or something
 - Create a view/template for displaying the metrics of all endpoints (i.e. a table of person and visit count, etc.)


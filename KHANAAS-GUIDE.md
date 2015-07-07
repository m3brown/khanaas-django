# Guide for implementing Khan AAS

### Initialize the virtual machine
 - clone this repo
 - run `vagrant up`

### Create a Django Project
 - Create a Django project.  The root of this repository will be our Django project root.
 - `vagrant ssh -c 'django-admin startproject khanaas .'`
   - creates a new django environment in your shared directory
   - show that the changes are available from the host environment (mac, windows)

### Configure the Django App
- Create `./khanaas/local_settings.py` and paste the following database settings: 
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'khanaas',
        'USER': 'khan',
        'PASSWORD': '', # no password needed for dev
    }
}
```
- Place the following at the bottom of khanaas/settings.py
```python
from khanaas.local_settings import *
```
- Initialize the Django database tables
```shell
vagrant ssh -c './manage.py migrate'
```
- Create a superuser account for yourself
```shell
# You will be prompted for a username, email, and password
vagrant ssh -c './manage.py createsuperuser'
```
- Check out the database via postgresql shell
  - Activate the shell with `vagrant ssh -c './manage.py dbshell'`
    - `manage.py dbshell` is effectively shorthand for `psql -U khan -d khanaas`
  - Show a list of the tables in the database: `\dt`
  - Show the list of users in the database: `select * from auth_user;`
  - Type 'Control-D' to exit the postgresql shell

### Demo runtestserver
 - Open up a new terminal window and run `vagrant ssh -c './manage.py runserver 0.0.0.0:8000'`
   - Leave this running in the background for the remainder of the project
 - You can test the app on your local computer by visiting http://localhost:8000
 - You can view the admin interface by visiting http://localhost:8000/admin
   - Notice you can view **and edit** records in the database
   - Try adding your first and last name to your user account: http://localhost:8000/admin/auth/user/1
     - Note: The save button is annoying in the bottom right corner of the page.

### Create an API App
 - Create a new django app called 'api': `vagrant ssh -c './manage.py startapp api'`
 - Enable the api app by adjusting a couple files:
   - Add `'api'` to the INSTALLED_APPS array in `khanaas/settings.py`:
   - Forward requests to the api by adding the following to the urlpatterns array in `khanaas/urls.py`:

       ```python
       url(r'^', include('api.urls', namespace='api')),
       ```
 - Create a 'hello world' view by adding the following to `api/views.py`:

    ```python
    from django.http import HttpResponse # Put this at the top of the file
    def hello_world_view(request):
       return HttpResponse("Hello, world, this is khan aas")
    ```
 - Create a url path to the new view by creating `api/urls.py` with the following content:

    ```python
    from django.conf.urls import patterns, url

    urlpatterns = patterns('api.views',
                           url(r'^hello$', 'hello_world_view'),
                          )
    ```
 - View the page at http://localhost:8000/hello

#### Create simple API views
 - Create a html template, view and url that display $name on the page
   - Create a html template by creating `api/templates/kirk.html` and add the following code:

      ```
      <html>
        <head>
          <title>Khan As A Service (KHANAAS)</title>
          <meta charset="utf-8">
          <style> span#message { font-family:"Helvetica Neue",Helvetica,Arial,sans-serif; padding-left: .2em; font-weight: bold; color: white; font-size: 10em; display: inline-block; white-space: nowrap; } </style>
        </head>
        <body background="{{ img_url }}" style="background-size: 100%; margin-top:40px;">
          <span id="message">{{ phrase }} </span>
        </body>
      </html>
      ```
     * NOTE: We cannot put this template directly into the `api/templates/` directory because of how the template loader            does namespacing. Django will choose the first template it finds whose name matches, and if you had a template               with the same name in a different application, Django would be unable to distinguish between them.
           
   - Add the following code to `api/views.py` to render the template with a name:

      ```python
      def kirk_view(request, phrase):
      last_char = phrase[-1]
      context = { 'img_url': 'http://www.khanaas.com/images/kirk.jpg', 
                  'phrase': phrase + (last_char * 5) 
                }
      return render(request, 'kirk.html', context)
      ```

   - Add the following url path to `api/urls.py` under urlpatterns
     
      ```
      url(r'^kirk/(?P<phrase>[\w]+)$', 'kirk_view'),
      ```

   - Follow the above instructions to create a view for the spock image located  at `http://www.khanaas.com/images/spock.jpg`

### Walk through process for adding an image for the background (khan and spock)

### Walk through the process of styling the input text
 - Probably should just do inline CSS in the template for simplicity, unless you think there is value is demoing the static file functionality (which is a decent argument)

### IDEA investigate if we can have some customizable django admin field for modifying the input text
 - i.e. mike -> mikeeee or miiikkkeee based on some regex in a django-admin text field

### IDEA create a model for the API pages (i.e. khan and spock) so we can log hit counts or something
 - Create a view/template for displaying the metrics of all endpoints (i.e. a table of person and visit count, etc.)
 - Which character do people like the most? (Spock vs. Khanaas vs. Kirk)

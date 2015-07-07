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
Create an html template, view and url that display a phrase on the page
 - Create an html template by creating `api/templates/api/khanaas_template.html` and add the following code:

    ```html
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
    * NOTE: Django combines the templates directories from all apps into one namespace.  As a result, it's considered good practice to create an `<appname>` directory inside your templates directory to ensure there is not a naming conflict with templates in another Django app.
           
 - Add the following code to `api/views.py` to render the template in the previous step:

    ```python
    def kirk_view(request, input_phrase):
        last_char = input_phrase[-1]
        # The context defines the variables used in the template. Our template
        # expects 'img_url' and 'phrase' variables to be provided.
        context = { 'img_url': 'http://www.khanaas.com/images/kirk.jpg',
                    'phrase': input_phrase + (last_char * 5)
                  }

        # render() creates an HTTP Response using the template (2nd argument) and the
        # context (3rd argument) providing variables for the template
        return render(request, 'api/khanaas_template.html', context)
    ```

 - Add the following url path to the urlpatterns in `api/urls.py`:
     
    ```
    url(r'^kirk/(?P<input_phrase>[\w]+)$', 'kirk_view'),
    ```
    - kirk_view is the name of the function to call in views.py
    - <input_phrase> is the name of the parameter passed to the kirk_view function
    - [\w]+ is regex for input_phrase, requiring that the value be one or more 'word' (i.e. alphanumeric) characters

 - Congratulations, you've implemented KhanAAS! Try out the api endpoint with your name: http://localhost:8000/kirk/<yournamehere>

### IDEA investigate if we can have some customizable django admin field for modifying the input text
 - i.e. mike -> mikeeee or miiikkkeee based on some regex in a django-admin text field

### Intro to Django's Database ORM
Django has a very easy to use ORM for interfacing with the database.  Let's use Django's ORM to manage our pages and background images.

- Create a database table for managing name and image URL for a page:
  - Add the following to `api/models.py`:
      ```python
      class Character(models.Model):
      name = models.CharField(max_length=50)
      hits = models.IntegerField(default=0, editable=False) # TODO we should add this in a later exercise
      img_url = models.CharField(max_length=100)

      def __unicode__(self):
          return self.name
      ```
  - Create a definition for the database changes, called a '[migration](https://docs.djangoproject.com/en/1.8/topics/migrations/)':

      ```shell
      vagrant ssh -c './manage.py makemigrations api'
      ```

  - Apply the migration on the database:

      ```shell
      vagrant ssh -c './manage.py migrate'
      ```
      - If you login to the database, you'll notice there is an api_character table:
          - Login to the dbshell: `vagrant ssh -c './manage.py dbshell'`
          - In the psql prompt, run `\dt` to list the tables, notice `api_character` is listed

  - You'll notice that the Character table is NOT listed in http://localhost:8000/admin
    - Add the following code to 'api/admin.py' to activate our Character model in the Django admin console:
      ```python
      admin.site.register(Character)
      ```
    - Reload the admin page, and note that 'Character' is now available for viewing and editing
      - Create a 'kirk' character, and set the 'img_url' to 'http://khanaas.com/images/kirk.jpg'

- Replace `kirk_view()` in 'api/views.py' with a more generic implementation:
    ```python
    def get_character_view(request, input_character, input_phrase):
    # Get the character by 'name', if it exists (case insensitive)
    # If a match is not found, return a 404 page
    character_obj = get_object_or_404(Character, name__iexact=input_character)

    last_char = input_phrase[-1]
    context = {
        # Tweak the input phrase by repeating the last character
        'phrase' : input_phrase + (last_char * 5),
        'img_url': character_obj.img_url
    }
    return render(request, 'khanaas.html', context)
     ```
- Lastly, let's adjust the URL path in 'api/urls.py' to reflect the new function and its **two** arguments:

    ```python
    url(r'^(?P<input_character>[\w]+)/(?P<input_phrase>[\w]+)$', 'get_character_view'),
    ```
- The kirk URL should work the same as it did before, test it out: http://localhost:8000/kirk/yourphrasehere
- Once you get kirk working, using the Django admin console to add a page for Spock (http://localhost:8000/spock/yourphrasehere)
  - Image URL for Spock: http://khanaas.com/images/spock.jpg

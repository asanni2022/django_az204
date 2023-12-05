Day 7 Plan:
Django REST
AppService with PostgreSQL DB
Install Docker
Containerize Django
Follow-up on Django Tutorials: https://docs.djangoproject.com/en/5.0/
REST
Python OOP:
Class: contain different methods/functions. A Class can be public or private.

Interface: Interfaces exposes a set of methods/functions of a private class for an outside class to use

API: Application Programming Interface with an EndPoint (/post/1/) where CRUD operations are possible http://127.0.0.1:8000/post/1/ http://127.0.0.1:8000/images/2341234/ http://127.0.0.1:8000/videos/5523/

APIs exchange JSON Objects (similar to Python Dictionary Data Structures)

JSON:
Reference: https://www.w3schools.com/js/js_json_intro.asp

JSON stands for JavaScript Object Notation
JSON contains {"Key" : "Value"} pairs. {"name":"John", "age":30, "car":null}
JSON is Nested
JSON is a lightweight data-interchange format
JSON is plain text written in JavaScript object notation
JSON is used to send data between computers
JSON is language independent *
{
    // Comments in JSON
    "glossary": {
        "title": "example glossary",
        "GlossDiv": {
            "title": "S",
            "GlossList": {
                "GlossEntry": {
                    "ID": "SGML",
                    "SortAs": "SGML",
                    "GlossTerm": "Standard Generalized Markup Language",
                    "Acronym": "SGML",
                    "Abbrev": "ISO 8879:1986",
                    "GlossDef": {
                        "para": "A meta-markup language, used to create markup languages such as DocBook.",
                        "GlossSeeAlso": ["GML", "XML"]
                    },
                    "GlossSee": "markup"
                }
            }
        }
    }
}
Python Dictionary Data Structure
Python Reference: https://www.w3schools.com/python/python_dictionaries.asp

# Python Dictionary Example:
thisdict = {
  "brand": "Ford",
  "model": "Mustang",
  "year": 1964
}
URLs:
https://python.org/about/ has three potential parts:

• a scheme - https

• a hostname - www.python.org

• and an (optional) path - /about/

Endpoints:
https://www.mysite.com/api/users # GET returns all users
https://www.mysite.com/api/users/<id> # GET returns a single user

HTTP Verbs for CRUD Operations:
CRUD HTTP Verbs

Create <--------------------> POST Read <--------------------> GET Update <--------------------> PUT Delete <--------------------> DELETE

HTTP Message:
Every http message consists of request/status line, headers, and optional body data

Example of a sample HTTP Message that a Browser might send to request Google homepage: https://wwww.google.com

GET / HTTP/1.1 Host: google.com Accept_Language: en-US

HTTP Status Codes:
Once your web browser has executed an HTTP REquiest on a URL > Status Code

• 2xx Success - the action requested by the client was received, understood, and accepted

• 3xx Redirection - the requested URL has moved

• 4xx Client Error - there was an error, typically a bad URL request by the client

• 5xx Server Error - the server failed to resolve a request

REST:
REpresentational State Transfer (REST) > standard for APIs proposed in 2000 It is an approach to building APIs on top of HTTP Protocol. Every RESTful API at minimum: - is stateless like HTTP - supoort common HTTP verbs (GET, POST, PUT, DELETE, etc) - returns data in either JSON or XML (tags like html)

APIs can be written in python, go, java, javascript, ruby on rails, etc

https://www.geeksforgeeks.org/rest-api-introduction/#
https://aws.amazon.com/what-is/restful-api/
https://www.techtarget.com/searchapparchitecture/definition/RESTful-API#:~:text=A%20RESTful%20API%20is%20an,deleting%20of%20operations%20concerning%20resources.
Django Library App (API that is REST Compliant)
We will first create a regular Django WebApp and then later convert it to a REST API

Create Django Library WebApp to list our books
Create a Django Project:

mkdir 5django_rest
django-admin startproject library_project .

Create sqlite3 Database:

python manage.py migrate

Run the Server:

python manage.py runserver

Create a new books App (standard WebApp, later we will create APIs):

python manage.py startapp books

Let our Django Project know we have a new app and where to find it: library_project/settings.py

"books.apps.BooksConfig", # Books App
Let the project know to expect new tables from our new books app:

python manage.py makemigrations books
python manage.py migrate # this creates the new table called "books_book"

We will create a webpage from templates but this time we will locate the templates folder inside the app instead of putting it in the main folder.

mkdir books/templates
touch books/templates/book_list.html

library_project/urls.py: Let the project know that find url for home page in books app folder's urls.py:

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path('', include('books.urls')), # Letting the project know of the book app
]
Create urls.py file in the books app folder:

touch books/urls.py

Modify the file: books/urls.py

from django.urls import path
from .views import BookListView

urlpatterns = [
    
    path('', BookListView.as_view(), name='home'), # Letting the project know of the book app
]
books/templates/book_list.html: Update the newly created file

<h2>All Books</h2>

{% for book in object_list %}
    
    <h3>Title: {{ book.title }}</h3>
    <ul>
        <li>Subtitle: {{ book.subtitle }}</li>
        <li>Author: {{ book.author }}</li>
        <li>ISBN: {{ book.isbn }}</li>

    </ul>
{% endfor %}
Run the Server:

python manage.py runserver

http://127.0.0.1:8000/

Notice the List of Books displayed on the homepage

Convert this Django WebApp into a REST API
We call it a WebApp because we have used the template to generate a html response and not a JSON reponse.

We will create an API Endpoint which will list all the books from the same database created in the books app (WebApp)

Install Django REST Framework, it is added just like any other third-party app using pip:

pip install djangorestframework

library_project/settings.py: let the project know to use the "rest_framework" app while keeping app priority in mind

# Application definition

INSTALLED_APPS = [ # Order of the apps is very important
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",

    # 3rd Party Apps
    # REST APP
    "rest_framework",

    # Local App
    "books.apps.BooksConfig", # Books App
]
Design our REST API:
Our REST API will expose a single endpoint and it will list out all the books in a JSON Response.

We will create: - a new URL route, - a new view, and - a new serializer file.

There are several ways to organize these new files but the best practice is to create a dedicated api app, so that in future if we add more apps then our api app will be more portable.

python manage.py startapp api

library_project/settings.py:

# Application definition

INSTALLED_APPS = [ # Order of the apps is very important
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",

    # 3rd Party Apps
    # REST APP
    "rest_framework",

    # Local App
    "books.apps.BooksConfig", # Books App
    "api.apps.ApiConfig", # API App
]
Let Project-Level urls know of the route location of our new api app is in the urls.py file within the api folder library_project/urls:

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path('', include('books.urls')), # Letting the project know of the book app
    path('api/', include('api.urls')), # Letting the project know of our new API app

]
Create this urls.py file within the api folder:

touch api/urls.py

Update this new and blank api/urls.py:

from django.urls import path
from .views import BookAPIView

urlpatterns = [
    
    path('', BookAPIView.as_view()), # Letting the project know of the book app
]
Update api/views.py file:

from rest_framework import generics

from books.models import Book
from .serializers import BookSerializer

class BookAPIView(generics.ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
Create a Serializer file to convert the JSON objects in other formats:

touch api/serializers.py

Update this new and blank api/serializers.py file:

from rest_framework import serializers

from books.models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ('title', 'subtitle', 'author','isbn')
That's it, run our django project:

$ python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
December 05, 2023 - 19:19:49
Django version 4.1, using settings 'library_project.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
[05/Dec/2023 19:19:56] "GET /api/ HTTP/1.1" 200 301  ## Curl Command is logged
cURL program to execute HTTP requests via the command line.

Open a new terminal and check what our new API Endpoint looks like using the Curl Command:

## Open a new terminal:

$ curl http://127.0.0.1:8000/api/   

[{"title":"Django Book","subtitle":"MIT's manual for Django","author":"Divergence","isbn":"1231239123"},{"title":"Dango 4 by Example","subtitle":"Learn by doing","author":"Antonio Mele","isbn":"978-1-80181-305-1"},{"title":"Python 3","subtitle":"Learn Python","author":"Unknown","isbn":"1283912983A"}]%

Open the api app in the browser: http://127.0.0.1:8000/api/

Notice this new page in Inspect Developer Tools (right-click anywhere on the webpage) of your browser.

HW:
1) REST API in Django: follow the steps in Day 7.md 2) Install and verify docker version: docker --version

Day 8 Plan:
Deploy 4django to AppService
Learn Docker
AZ-204 Containers


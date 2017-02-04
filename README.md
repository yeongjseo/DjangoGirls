## 장고 걸스 튜토리얼 따라하기

참고 사이트: [https://tutorial.djangogirls.org/ko/](https://tutorial.djangogirls.org/ko/)

소스: [https://github.com/yeongjseo/djangogirls-tutorial.git](https://github.com/yeongjseo/djangogirls-tutorial.git)

---
### 개발환경 설치
 


#### Python 설치
https://www.python.org/downloads/release/python-343/  
설치 중 Add python.exe to the Path 체크


#### 쉘 구분
	$ 			: conemu 쉘
 	# 			: cygwin 쉘 
	(myenv)$	: 장고 가상환경 쉘  
	>>> 		: 파이썬 쉘


#### 장고 가상환경(Virtual ENV) 만들기
	$ python -m venv myvenv 
	
생성된 폴더/파일 확인 

	# tree -L 2
	.
	└── myvenv
	    ├── Include
	    ├── Lib
	    ├── pyvenv.cfg
	    └── Scripts

#### 장고 가상환경(Virtual ENV) 사용하기
	$ myvenv\Scripts\activate
	(myvenv) > 

	
#### 장고 가상환경에 장고 설치하기

	(myvenv) $ pip install django==1.8
	Collecting django==1.8
	  Using cached Django-1.8-py2.py3-none-any.whl
	Installing collected packages: django
	Successfully installed django-1.8

생성된 폴더/파일 확인

	# tree myvenv/Lib/site-packages/django/ -L 1
	myvenv/Lib/site-packages/django/
	├── __init__.py
	├── __pycache__
	├── apps
	├── bin
	├── conf
	├── contrib
	├── core
	├── db
	├── dispatch
	├── forms
	├── http
	├── middleware
	├── shortcuts.py
	├── template
	├── templatetags
	├── test
	├── utils
	└── views

#### 장고 가상환경에 장고 설치하기

	(myvenv) $ django-admin startproject mysite .

생성된 폴더/파일 확인

	# tree  
	├───manage.py		: 웹서버 시작 
	└───mysite
        settings.py		: 웹사이트 설정
        urls.py			: URL 패턴 
        wsgi.py			: 
        __init__.py

#### 장고 설정 변경 및 확인

> mysite/settings.py

	TIME_ZONE = 'Asia/Seoul'
	# 정적파일 경로
	STATIC_URL = '/static/'
    STATIC_ROOT = os.path.join(BASE_DIR, 'static')

	# 데이터베이스: 기본 sqlite3 사용
	DATABASES = {
    	'default': {
        	'ENGINE': 'django.db.backends.sqlite3',
         	'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
     	}
 	}

	# 새로 만들 blog 어플 추가 
	INSTALLED_APPS = (
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'blog',
    )

#### 데이터베이스 설치

	$ python manage.py migrate
	Operations to perform:
	  Synchronize unmigrated apps: messages, staticfiles
	  Apply all migrations: sessions, auth, contenttypes, admin
	Synchronizing apps without migrations:
	  Creating tables...
	    Running deferred SQL...
	  Installing custom SQL...
	Running migrations:
	  Rendering model states... DONE
	  Applying contenttypes.0001_initial... OK
	  Applying auth.0001_initial... OK
	  Applying admin.0001_initial... OK
	  Applying contenttypes.0002_remove_content_type_name... OK
	  Applying auth.0002_alter_permission_name_max_length... OK
	  Applying auth.0003_alter_user_email_max_length... OK
	  Applying auth.0004_alter_user_username_opts... OK
	  Applying auth.0005_alter_user_last_login_null... OK
	  Applying auth.0006_require_contenttypes_0002... OK
	  Applying sessions.0001_initial... OK

db.sqlite3 파일 확인

#### 웹 서버 시작
	(myvenv) $ python manage.py runserver 0:8000
	Performing system checks...
	
	System check identified no issues (0 silenced).
	February 03, 2017 - 19:58:08
	Django version 1.8, using settings 'mysite.settings'
	Starting development server at http://0:8000/
	Quit the server with CTRL-BREAK.

http://127.0.0.1:8000/ 접속 확인

---
### 어플 제작

#### 어플 (blog) 기본 설치 
	(myvenv) $ python manage.py startapp blog

생성폴더/파일 확인

	# tree -L 2
	.
	├── blog
	│   ├── __init__.py
	│   ├── admin.py
	│   ├── migrations
	│   ├── models.py
	│   ├── tests.py
	│   └── views.py
	├── db.sqlite3
	├── manage.py
	├── mysite
	│   ├── __init__.py
	│   ├── __pycache__
	│   ├── settings.py
	│   ├── urls.py
	│   └── wsgi.py
	└── myvenv
	    ├── Include
	    ├── Lib
	    ├── pip-selfcheck.json
	    ├── pyvenv.cfg
	    └── Scripts

#### blog 모델 만들기 

> blog/models.py

	from django.db import models
	from django.utils import timezone
	
	class Post(models.Model):
	    author = models.ForeignKey('auth.User')
	    title = models.CharField(max_length=200)
	    text = models.TextField()
	    created_date = models.DateTimeField(default=timezone.now)
	    published_date = models.DateTimeField(blank=True, null=True)
	
	    def publish(self):
	        self.published_date = timezone.now()
	        self.save()
	
	    def __str__(self):
	        return self.title


#### 데이터베이스에 blog 모델 (Post) 테이블 만들기

	(myvenv) $ python manage.py makemigrations blog
	Migrations for 'blog':
  	  0001_initial.py:
  	  - Create model Post

	(myvenv) ~/djangogirls$ python manage.py migrate blog
	Operations to perform:
	  Apply all migrations: blog
	Running migrations:
	  Rendering model states... DONE
	  Applying blog.0001_initial... OK
	

#### Post 테이블 확인

> db.sqlite3  
 
	CREATE TABLE "blog_post" (  
		"id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, 
		"title" varchar(200) NOT NULL, 
		"text" text NOT NULL, 
		"created_date" datetime NOT NULL, 
		"published_date" datetime NULL, 
		"author_id" integer NOT NULL REFERENCES "auth_user" ("id"))

#### 장고 관리자 사이트에 blog 모델 (Post) 추가 

> blog/admin.py

	from django.contrib import admin
	from .models import Post
	
	# Register your models here.
	admin.site.register(Post)

http://127.0.0.1:8000/admin/ 접속

#### 장고 관리자 추가 

	(myvenv) $ python manage.py createsuperuser
	Username: admin
	Email address: admin@admin.com
	Password:
	Password (again):
	Superuser created successfully.


#### 싸이트 URL 패턴 수정 

> mysite/urls.py

	from django.conf.urls import include, url
	from django.contrib import admin
	
	urlpatterns = [
	    url(r'^admin/', include(admin.site.urls)),
		# 모든 URL blog.urls로 보냄
	    url(r'', include('blog.urls')),
	]



#### blog 어플 URL 패턴 수정 

> blog/urls.py

	from django.conf.urls import url
	from . import views
	
	urlpatterns = [
		# 앞뒤로 아무것도 없으면 리스트로
	    url(r'^$', views.post_list, name='post_list'),
		# post/숫자/ 이면 상세
	    url(r'^post/(?P<pk>[0-9]+)/$', views.post_detail, name='post_detail'),
		# post/new/ 이면 신규
	    url(r'^post/new/$', views.post_new, name='post_new'),
	]

#### blog 장고뷰 만들기

MVC 패턴의 View가 아닌 Controller와 비슷

요청에 맞는 처리 후 템플릿 파일(HTML파일)과 연결해줌 


> blog/views.py

	from django.shortcuts import render, get_object_or_404
	from django.shortcuts import redirect
	from django.utils import timezone
	from .models import Post
	from .forms import PostForm
	
	# Create your views here.
	def post_list(request):
		# Post 모델 오브젝트에서 리스트 얻어옴
	    posts = Post.objects.filter(published_date__lte=timezone.now()).order_by('published_date')
		# 리스트을 해당 템플릿(post_list.html)으로 보냄
	    return render(request, 'blog/post_list.html', {'posts': posts})
	
	def post_detail(request, pk):
		# Post 모델에서 Primary Key에 해당하는 post 얻어옴
	    post = get_object_or_404(Post, pk=pk)
		# post을 해당 템플릿으로 보냄 
	    return render(request, 'blog/post_detail.html', {'post': post})
	
	
	def post_new(request):
	    print("User: " + str(request.user))
	
	    if request.method == "POST":
			# 신규 작성 처리 (Post 방식)
	        form = PostForm(request.POST)
	        if form.is_valid():
				# 폼에서 DB에 커밋하지 않고 post 가져옴
	            post = form.save(commit=False)
	            print(request.user)
				# post 속성 추가
	            post.author = request.user
	            post.published_date = timezone.now()
				# DB에 커밋
	            post.save()
	            return redirect('post_detail', pk=post.pk)
	    else:
			# 신규 작성 폼 (Get 방식)
	        form = PostForm()
	        return render(request, 'blog/post_edit.html', {'form': form, 'request': request})


#### 신규 폼 형식

> blog/forms.py

	from django import forms
	from .models import Post
	
	class PostForm(forms.ModelForm):
	    class Meta:
	        model = Post
	        fields = ('title', 'text',)


#### 템플릿 베이스 

> blog/templates/blog/base.html

	{% load staticfiles %}
	<html>
	<head>
	    <title>Django Girls blog</title>
	    <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
	    <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css">
	    <link rel="stylesheet" href="{% static 'css/blog.css' %}">
	    <link href="https://fonts.googleapis.com/css?family=Lobster&subset=latin,latin-ext" rel="stylesheet" type="text/css">
	</head>
	<body>
	    <div class="page-header">
	        {% if user.is_authenticated %}
	            <a href="{% url 'post_new' %}" class="top-menu"><span class="glyphicon glyphicon-plus"></span></a>
	        {% endif %}
	        <h1><a href="">Django Girls Blog</a></h1>
	    </div>
	
	    <div class="content container">
	        <div class="row">
	            <div class="col-md-8">
	                {% block content %}
	                {% endblock %}
	            </div>
	        </div>
	    </div>
	</body>
	</html>

#### 포스트 리스트 
> blog/templates/blog/post_list.html

	{% extends 'blog/base.html' %}
	
	{% block content %}
	    {% for post in posts %}
	        <div class="post">
	            <div class="date">
	                {{ post.published_date }}
	            </div>
	            <h1><a href="{% url 'post_detail' pk=post.pk %}">{{ post.title }}</a></h1>
	            <p>{{ post.text|linebreaksbr }}</p>
	        </div>
	    {% endfor %}
	{% endblock content %}


#### 포스트 상세
> blog/templates/blog/post_detail.html

	{% extends 'blog/base.html' %}
	
	{% block content %}
	    <div class="post">
	        {% if post.published_date %}
	            <div class="date">
	                {{ post.published_date }}
	        </div>
	        {% endif %}
	    <h1>{{ post.title }}</h1>
	    <p>{{ post.text|linebreaksbr }}</p>
	    </div>
	{% endblock %}

#### 포스트 신규
> blog/templates/blog/post_edit.html

	{% extends 'blog/base.html' %}
	
	{% block content %}
	<h1>New post</h1>
	<form method="POST" class="post-form">{% csrf_token %}
	    {{ form.as_p }}
	    <button type="submit" class="save btn btn-default">Save</button>
	</form>
	{% endblock %}

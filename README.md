# Django Admin Site

## Overview
The code in this repository uses the django framework.

## Learning Objectives
* Understand the concepts of Django admin site
* Create and customize your Django admin site to manage the content of an onlinecourse app
* Use PostgreSQL an open-soutce relational database management system.

## The main files of interest are:

admin.py: contains the definitions for each model in the admin panel
models.py: definitions for each data model
urls.py: urls for each viewport
views.py: defines views for each webpage (see .html files in templates folder)

## Setting up

Cloning the repository
```
git clone https://github.com/josuecross/adminsite_django
```

Install dependencies:
```
python3 -m pip install -U -r requirements.txt
```

Open settings.py to configure database credentials in DATABASES section.

Activate models for an onlinecourse app wich will be managed by admin site:
```
python3 manage.py makemigrations
python3 manage.py migrate
```

To create a superuser for acces admin site:
```
python3 manage.py createsuperuser
```

To run de server:
```
python3 manage.py runserver
```


## Django Administration:

![image](https://github.com/josuecross/adminsite_django/assets/85675115/c6a2f919-a5ce-4239-afd8-f8d3c1db3c6d)


## Register Models with Admin site
You can add models to the admin site adminsite/admin.py: For example a Instructor model:

```
# Instructor model
class Instructor(models.Model):
    user = models.ForeignKey(
        settings.AUTH_USER_MODEL,
        on_delete=models.CASCADE,
    )
    full_time = models.BooleanField(default=True)
    total_learners = models.IntegerField()

    def __str__(self):
        return self.user.username

```

Can be added to the adminsite by registering its model in admin.py:
```
admin.site.register(Instructor)
```

![image](https://github.com/josuecross/adminsite_django/assets/85675115/67559d78-1155-4911-a535-091620eb3f43)

## Customize Admin Site

Adding a new class to adminsite/admin.py you can choose wich fiels to include in the admin site
```
class Course(models.Model):
    name = models.CharField(null=False, max_length=30, default='online course')
    image = models.ImageField(upload_to='course_images/')
    description = models.CharField(max_length=1000)
    pub_date = models.DateField(null=True)
    instructors = models.ManyToManyField(Instructor)
    users = models.ManyToManyField(settings.AUTH_USER_MODEL, through='Enrollment')
    total_enrollment = models.IntegerField(default=0)
    is_enrolled = False

    def __str__(self):
        return "Name: " + self.name + "," + \
               "Description: " + self.description

    def total_score(self):
        total_score = 0
        for question in self.question_set.all():
            total_score += question.grade
        return total_score
```

You can include only selected fields in admin.py like:
```
class CourseAdmin(admin.ModelAdmin):
    fields = ['pub_date', 'name', 'description']

admin.site.register(Course, CourseAdmin)
```
![image](https://github.com/josuecross/adminsite_django/assets/85675115/40c7f0cd-83c7-4170-bfb4-51b4b27b5eb3)


## Associate Related Models

Django admin privides also a convinient way to associate related objects on a single model managing page.
In adminsite/admin.py lesson models can be managed together with course objects:
```
class LessonInline(admin.StackedInline):
    model = Lesson 
    extra = 5

class CourseAdmin(admin.ModelAdmin):
    fields = ['pub_date', 'name', 'description']
    inlines = [LessonInline]

admin.site.register(Course, CourseAdmin)
```
![Captura de pantalla 2023-09-16 232837](https://github.com/josuecross/adminsite_django/assets/85675115/1a5dc56d-6b1c-448e-a84a-43cd14dd705a)







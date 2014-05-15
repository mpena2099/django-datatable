# Django-table

![preview](https://dl.dropboxusercontent.com/u/94696700/example.png)

## Overview

Django-table is a simple Django app to origanize data in tabular form and 
based on [datatable](http://datatables.net) and [bootstrap](http://getbootstrap.com/).

It is worth mention that design of this project makes reference to [django-table2](https://github.com/bradleyayers/django-tables2)
and mainly for the purpose of learning. I really appreciate that anyone make pull-request to improve it.


## Requirement

* jQuery 1.6+

* Bootstrap 3.0

* Django 1.4+

## Quick start

- Setup Django-table application in Python environment:

        $ python setup.py install

- Add "table" to your INSTALLED_APPS setting like this:

        INSTALLED_APPS = (
            ...,
            'table',
        )

- Define a simple model named Person:

        # example/app/models.py
        class Person(models.Model):
            name = models.CharField(max_length=100)

- Add some data so you have something to display in the table.
Now define a PersonTable class without any options in table file.

        # example/app/tables.py
        from models import Person
        from table import Table
        from table.columns import Column
        
        class PersonTable(Table):
            id = Column(field='id')
            name = Column(field='name')
            class Meta:
                model = Person

   And pass a table instance to the view.

        # example/app/views.py
        from django.shortcuts import render
        from app.tables import PersonTable

        def people(request):
            people = PersonTable()
            return render(request, "index.html", {'people': people})

- Finally, implement the template:

        {# example/templates/index.html}
        {% load static %}
        {% load table_tags %}

        <link href="{% static 'css/bootstrap.min.css' %}" rel="stylesheet" media="screen">
        <script src="{% static 'js/jquery.min.js' %}"></script>
        <script src="{% static 'js/bootstrap.min.js' %}"></script>

        <!DOCTYPE html>
        <html>
            <head>
                <meta http-equiv="content-type" content="text/html; charset=utf-8" />
                <title>person</title>
            </head>
            <body>
                <div class="container" style="margin-top: 10px">
                    <h1>people</h1>
                    <br />
                    {% render_table people %}
                </div>
            </body>
        </html>

## Tag

Render the whole table by simple tag `{% render_table %}`, pass `Table` instance as single argument.

    {% render_table table %}


## DataSource

* Model

  Use a django MTV model as table data source, and queries all data in database by default.
  see **model** in table options for details.

* QuerySet

  Similiar to **Model**, but pass queryset when you initialize table instance instead of defining model option.
  Basically, it used to filtering or sorting data your want to display in table.

        # models.py
        class Person(models.Model):
            name = models.CharField(max_length=100)
            
        # tables.py
        from models import Person
        from table import Table
		    from table.columns import Column

        class PersonTable(Table):
            id = Column(field='id')
            name = Column(field='name')
            
        # views.py
        from django.shortcuts import render
        from models import Person
        from app.tables import PersonTable

        def people(request):
            people = PersonTable(Person.objects.all())
            return render(request, "index.html", {'people': people})

* Dict-List

  Use a list of directories as table data source. Fields which declared in columns correspond to the keys of directory.

        # tables.py
		from table import Table
	    from table.columns import Column

		class PersonTable(Table):
            id = Column(field='id')
            name = Column(field='name')

		# views.py
        from django.shortcuts import render
        from app.tables import PersonTable

        def people(request):
			data = [{'id': 1, 'name': 'John'}, {'id': 2, 'name': 'Tom'}]
            people = PersonTable(data)
            return render(request, "index.html", {'people': people})
					
* Ajax

  For large number of data, load them on front-end entirely is impossible.
  So, django-table provides a simle option 'ajax' to load data from server-side asynchronous.

  Note that once toggle `ajax`, `model` option is necessary. Django-table will make paging/searching/sorting
  based on `ModelClass.objects.all()`.

        # tables.py
		from table import Table
	    from table.columns import Column

		class PersonTable(Table):
            id = Column(field='id')
            name = Column(field='name')

            class Meta:
                model = Person
                ajax = True

  If you want to customize base data, use `ajax_source` option and
  implement your own Class-based View by subclassing `FeedDataView`.

        # tables.py
		class PersonTable(Table):
            id = Column(field='id')
            name = Column(field='name')

            class Meta:
                model = Person
                ajax = True
                ajax_source = reverse('table_data')

        # urls.py
        urlpatterns = patterns('',
            url(r'^tabledata$', MyDataView.as_view(), name='table_data'),
        )
        
        # views.py
        from table.views import FeedDataView

        class MyDataView(FeedDataView):
            def get_queryset(self):
                # override parent function
                queryset = super(MyDataView, self).get_queryset()
                return queryset.filter(id__gt=5)

## Columns

* Column

* Link Column

* Datetime Column

## Widgets

* search-box

* info-label

* pagination

* length-menu

* extense-button

## API Reference

* [wiki](https://github.com/shymonk/django-table/wiki/API-Reference)

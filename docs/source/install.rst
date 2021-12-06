Setting Up
=====

.. _installation:

Installation
------------

This project runs on two main frameworks: one for the backend (Django) and another for the frontend (React). For django you will need to have an updated python intallation. As for react a node.js installation is required.

	node.js: https://nodejs.org/en/
	
	python: https://www.python.org/

For the database we used MongoDB so please install a MongoDB Community Server. It is also advised to install Mongo Compass a database tool to query the databse.

	MongoDB Community Server: https://www.mongodb.com/try/download/community
	



pip install djongo

pip install django-filter

pip install django-rest-auth
pip install django-allauth

python -m pip install django-cors-headers

pip install apscheduler

pip install fpdf

python manage.py makemigrations
python manage.py migrate
pip install djangorestframework


mongodb tools


.. _run:

Run the Project
------------

For the Django Application (Running on Port:8000) you should open a new terminal and do the following:

Go to the backend folder:

.. code-block:: 

   cd \backend

Run the python enviroment:

.. code-block:: 

   env\Scripts\activate

Go to the maap folder:

.. code-block:: 

   cd maap

Run the Django App:

.. code-block:: 

   python manage.py runserver 0.0.0.0:8000 --noreload
    
    
    
Next for the React Application (Running on Port:3000) you should just run this lines:
	
Start by going to the gui folder:

.. code-block:: 

	cd \frontend\gui

Then just run the ract app:

.. code-block:: 

	npm start

	

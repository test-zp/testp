.. _quickstart:

=============
Quick start
=============

A Locust performance test is specified in a plain python file:

.. code-block:: python

    import time
    from locust import HttpUser, task

    class QuickstartUser(HttpUser):
        @task
        def hello_world(self):
            self.client.get("/hello")
            self.client.get("/world")
        
        @task(3)
        def view_item(self):
            for item_id in range(10):
                self.client.get(f"/item?id={item_id}", name="/item")
                time.sleep(1)
        
        def on_start(self):
            self.client.post("/login", json={"username":"foo", "password":"bar"})


.. rubric:: Let's break it down

.. code-block:: python

    import time
    from locust import HttpUser, task

A locust file is just a normal Python module, it can import code from other files or packages.

.. code-block:: python

    class QuickstartUser(HttpUser):

The behaviour of a simulated user is represented by a class in your locust file. When you start a test run, Locust will create an instance of the class for each concurrent user.

.. code-block:: python

    @task
    def hello_world(self):
        ...

Methods decorated with ``@task`` are the core of your locust file. For every running user, Locust creates a greenlet (micro-thread), that will call those methods.

.. code-block:: python

    @task
    def hello_world(self):
        self.client.get("/hello")
        self.client.get("/world")

The self.client attribute makes it possible to make HTTP calls that will be logged by Locust. For information on how to make other kinds of requests, validate the response, etc, see `Using the HTTP Client <writing-a-locustfile.html#using-the-http-client>`_.

.. code-block:: python

    @task(3)
    def view_item(self):
        ...

Tasks are picked at random, but you can give them different weighting. The above configuration will make Locust three times likelier to pick ``view_item`` than ``hello_world``.

.. code-block:: python

    @task(3)
    def view_item(self):
        for item_id in range(10)
            self.client.get(f"/item?id={item_id}", name="/item")
            time.sleep(1)

In the ``view_item`` task we load 10 different URLs by using a query parameter based on a variable. In order to not get 10 separate entries in Locust's statistics - since the stats is grouped on the URL - we use 
the :ref:`name parameter <name-parameter>` to group all those requests under an entry named ``"/item"`` instead.

.. code-block:: python

    def on_start(self):
        self.client.post("/login", json={"username":"foo", "password":"bar"})

If you declare a method called `on_start`, it will be called once for each user. For more info see :ref:`on-start-on-stop`.

Start Locust
============

Put the above code in a file named *locustfile.py* in your current directory and run:

.. code-block:: console

    $ locust


If your Locust file is located somewhere else, you can specify it using ``-f``

.. code-block:: console

    $ locust -f locust_files/my_locust_file.py

.. note::

    To see all available options type: ``locust --help`` or check :ref:`configuration`

Locust's web interface
==============================

Once you've started Locust using one of the above command lines, you should open up a browser
and point it to http://127.0.0.1:8089. Then you should be greeted with something like this:

.. image:: images/webui-splash-screenshot.png

Fill out the form and try it out! (but note that if you don't change your locust file to match your actual target system you'll mostly get error responses)

.. image:: images/webui-running-statistics.png

.. image:: images/webui-running-charts.png


More options
============

To run Locust distributed across multiple Python processes or machines, you can start a single Locust master process 
with the ``--master`` command line parameter, and then any number of Locust worker processes using the ``--worker`` 
command line parameter. See :ref:`running-locust-distributed` for more info.

To start tests directly, without using the web interface, use ``--headless``. 

Parameters can also be set through :ref:`environment variables <environment-variables>`, or in a
:ref:`config file <configuration-file>`.

To add/remove users during a headless run press w or W (1, 10) to spawn users and s or S to stop(1, 10).

How to write a *real* locust file?
""""""""""""""""""""""""""""""""""

The above example was just the bare minimum, see :ref:`writing-a-locustfile` for more info.

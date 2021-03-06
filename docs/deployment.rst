==========
Deployment
==========

Currently the application has only been deployed using `Heroku <http://www.heroku.com>`_
but there are no reasons it cannot be deployed to any hosting platform or
server that supports the `Flask framework <http://flask.pocoo.org>`_.

Heroku
======

Heroku has a `good guide for Python apps <https://devcenter.heroku.com/articles/getting-started-with-python#introduction>`_
that gives a nice overview of the concepts you'll need to know to get going,
but some of the specifics for this setup are slightly different.

`Real Python <https://realpython.com>`_ also has a `great guide on setting up a
basic flask app on Heroku <https://realpython.com/blog/python/flask-by-example-part-1-project-setup/>`_.

The following steps assume you have the basic `Heroku toolbelt installed <https://devcenter.heroku.com/articles/getting-started-with-python#set-up>`_.

1. Create `Heroku <http://heroku.com>`_ app
    * `heroku create [name]`
    * You can specify a name but it must be unique. You can also leave it blank
      and Heroku will create a unique name for you.
2. Add git remote for your app
    * `git remote add heroku git@heroku.com:<name>.git` where <name> is the
      name of your Heroku app from step 1.
3. Setup Heroku config
    * See example_config.py for a listing of the environment variables that
      must be setup in your Heroku config.
    * You can set Heroku config variables with the following syntax:
        * `heroku config:set APP_SETTINGS=config.ProductionConfig`
        * ... (see example_config.py for full list)
        * Or something like the following if you have multiple remotes for Heroku
        * `heroku config:set APP_SETTINGS=config.ProductionConfig --app pro`
        * `heroku config:set APP_SETTINGS=config.StagingConfig --app stage`
        * ... (see example_config.py for full list)
4. Deploy changes
    * `git push heroku master`
    * Or something like the following if you have multiple remotes for Heroku
    * `git push stage master` where <stage> is remote name for Heroku and
      master is local branch you want to push.
    * **Make sure your changes are committed locally first!**

By default the application will be served up by `Gunicorn <http://gunicorn.org>`_
only.  This is great for testing, but gunicorn is best when run behind a proxy
like `nginx <http://nginx.org>`_.

You can slightly improve your performance on Heroku by using setting the
`WEB_CONCURRENCY` environment variable, which gunicorn automatically honors.
You can set that variable with the following command:

* `heroku config:set WEB_CONCURRENCY=3`

You'll want to set this to something suitable for the size of your
`Heroku dyno <https://www.heroku.com/pricing>`_ and the memory requirements of
your the flask application.

--------------------------------------------
Run application locally with Heroku Procfile
--------------------------------------------

You'll need to complete the setup below for getting things running on Heroku
before doing this, or at least setting up your Heroku environment variables as
described below.  Then:

1. Run `heroku config --app <app_name>` to see all the configuration
2. Copy all the these configuration values into a file with the `key=value` format instead of `key:value` which is the output of the Heroku command.
3. Run `heroku local --env <file_from_step_2>`

Adding Redis caching on Heroku
------------------------------

1. Determine if you want to use a
   `caching addon <https://elements.heroku.com/addons#caching>`_ or
   `redis addon <https://elements.heroku.com/addons#data-stores>`_.

   * This application has been tested with the `redis cloud addon <https://elements.heroku.com/addons/rediscloud>`_ for caching data from the Github API.
   * Redis was chosen for the following reasons:
        * Cache value larger than 1MB (for large articles)
        * Use the same service for other things later instead of just caching
2. Add your addon
    * `heroku addons:create rediscloud:30 --app <app_name>`
3. See docs related to `using Python with redis on Heroku <https://devcenter.heroku.com/articles/rediscloud#using-redis-from-python>`_

Useful Heroku add-ons
---------------------

1. `Papertrail <https://elements.heroku.com/addons/papertrail>`_
    * Provides bigger log for debugging issues and enables easy searching

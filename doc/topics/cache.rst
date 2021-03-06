.. _cache:

Caching Backends
================
PyPICloud stores the packages in a storage backend (typically S3), but that backend
is not necessarily efficient for frequently reading metadata. So instead of
hitting S3 every time we need to find a list of package versions, we store all
that metadata in a cache. The cache does not have to be backed up because it is
only a local cache of data that is permanently stored in the storage backend.

SQLAlchemy
----------
Set ``pypi.db = sql`` OR ``pypi.db = pypicloud.cache.SQLCache`` OR leave it out
completely since this is the default.

``db.url``
~~~~~~~~~~
**Argument:** string

The database url to use for the caching database. Should be a `SQLAlchemy url
<http://docs.sqlalchemy.org/en/rel_0_9/core/engines.html>`_

* sqlite: ``sqlite:///%(here)s/db.sqlite``
* sqlite (in-memory): ``sqlite://``
* mysql: ``mysql://root@127.0.0.1:3306/pypi?charset=utf8mb4``
* postgres: ``postgresql://postgres@127.0.0.1:5432/postgres``

.. warning::

  You must specify the ``charset=`` parameter if you're using MySQL, otherwise
  it will choke on unicode package names. If you're using 5.5.3 or greater you
  can specify the ``utf8mb4`` charset, otherwise use ``utf8``.

``db.graceful_reload``
~~~~~~~~~~~~~~~~~~~~~~
**Argument:** bool, optional

When reloading the cache from storage, keep the cache in a usable state while
adding and removing the necessary packages. Note that this may take longer
because multiple passes will be made to ensure correctness. (default ``False``)

Redis
-----
Set ``pypi.db = redis`` OR ``pypi.db = pypicloud.cache.RedisCache``

You will need to ``pip install redis`` before running the server.

``db.url``
~~~~~~~~~~
**Argument:** string

The database url to use for the caching database. The format looks like this:
``redis://username:password@localhost:6379/0``

``db.graceful_reload``
~~~~~~~~~~~~~~~~~~~~~~
**Argument:** bool, optional

When reloading the cache from storage, keep the cache in a usable state while
adding and removing the necessary packages. Note that this may take longer
because multiple passes will be made to ensure correctness. (default ``False``)

DynamoDB
--------
Set ``pypi.db = dynamo`` OR ``pypi.db = pypicloud.cache.dynamo.DynamoCache``

You will need to ``pip install pypicloud[dynamo]`` before running the server.

``db.region_name``
~~~~~~~~~~~~~~~~~~
**Argument:** string

The AWS region to use for the cache tables. You must specify either this or
``db.host``.

.. _dynamo_credentials:

``db.aws_access_key_id``, ``db.aws_secret_access_key``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
**Argument:** string, optional

Your AWS access key id and secret access key. If they are not specified then
pypicloud will attempt to get the values from the environment variables
``AWS_ACCESS_KEY_ID`` and ``AWS_SECRET_ACCESS_KEY`` or any other `credentials
source
<http://boto3.readthedocs.io/en/latest/guide/configuration.html#configuring-credentials>`__.

``db.namespace``
~~~~~~~~~~~~~~~~
**Argument:** string, optional

If specified, all of the created Dynamo tables will have this as a prefix in
their name. Useful to avoid name collisions.

``db.host``
~~~~~~~~~~~
**Argument:** string

The hostname to connect to. This is normally used to connect to a DynamoDB
Local instance. You must specify either this or ``db.region``.

``db.port``
~~~~~~~~~~~
**Argument:** int, optional

The port to connect to when using ``db.host`` (default 8000)

``db.secure``
~~~~~~~~~~~~~
**Argument:** bool, optional

Force https connection when using ``db.host`` (default False)

``db.graceful_reload``
~~~~~~~~~~~~~~~~~~~~~~
**Argument:** bool, optional

When reloading the cache from storage, keep the cache in a usable state while
adding and removing the necessary packages. Note that this may take longer
because multiple passes will be made to ensure correctness. (default ``False``)

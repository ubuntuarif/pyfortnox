pyfortnox
==============

Fortnox API V3 library client for Python

Installation
------------

pyfortnox package can be installed either via pip or easy\_install:

.. code:: bash

    $ pip install --upgrade pyfortnox

or

.. code:: bash

    $ easy_install --upgrade pyfortnox

You can install from the source code as well. First clone the repo and
then execute:

.. code:: bash

    $ python setup.py install

After installing, import ``pyfortnox`` package:

.. code:: python

    import fortnox

Usage
-----

.. code:: python

    import fortnox

    # Then we instantiate a client (as shown below)

Build a client
~~~~~~~~~~~~~~

**Using this api without authentication gives an error**

.. code:: python

    client = fortnox.Client(access_token='<YOUR_PERSONAL_ACCESS_TOKEN>', client_secret='<YOUR_APPS_CLIENT_SECRET>')

Client Options
~~~~~~~~~~~~~~

The following options are available while instantiating a client:

-  **access\_token**: Personal access token
-  **client\_secret**: Private/public integration app's client secret
-  **base\_url**: Base url for the api
-  **timeout**: Request timeout
-  **verbose**: Verbose/debug mode

Architecture
~~~~~~~~~~~~

The library follows few architectural principles you should understand
before digging deeper. 1. Interactions with resources are done via
service objects. 2. Service objects are exposed as properties on client
instances. 3. Service objects expose resource-oriented actions. 4.
Actions return dictionaries that support attribute-style access, a la
JavaScript (thanks to Bunch and it's form Munch).

For example, to interact with projects API you will use
``fortnox.ProjectService``, which you can get if you call:

.. code:: python

    client = fortnox.Client(access_token='<YOUR_PERSONAL_ACCESS_TOKEN>', client_secret='<YOUR_APPS_CLIENT_SECRET>')
    client.projects # fortnox.ProjectService

To retrieve list of resources and use filtering you will call ``#list``
method:

.. code:: python

    client = fortnox.Client(access_token='<YOUR_PERSONAL_ACCESS_TOKEN>', client_secret='<YOUR_APPS_CLIENT_SECRET>')
    client.projects.list(organization_id=google.id, hot=True) # list(dict|Munch)
    or simply, client.projects.list()

To find custom field by name and its value pass kwargs as an argument:

.. code:: python

    client = fortnox.Client(access_token='<YOUR_PERSONAL_ACCESS_TOKEN>', client_secret='<YOUR_APPS_CLIENT_SECRET>')
    client.projects.list(**{'ProjectNumber': 1})

To find a resource by its unique identifier use ``#retrieve`` method:

.. code:: python

    client = fortnox.Client(access_token='<YOUR_PERSONAL_ACCESS_TOKEN>', client_secret='<YOUR_APPS_CLIENT_SECRET>')
    client.projects.retrieve(id=1)

When you'd like to create a resource, or update it's attributes you want
to use either ``#create`` or ``#update`` methods. For example if you
want to create a new project you will call:

.. code:: python

    client = fortnox.Client(access_token='<YOUR_PERSONAL_ACCESS_TOKEN>', client_secret='<YOUR_APPS_CLIENT_SECRET>')
    project = client.projects.create(Description='Website design', Status='ONGOING')

    project.ProjectNumber = 1
    project.Status = 'ONGOING'
    project.Description = 'Website redesign'

    client.projects.update(project.ProjectNumber, StartDate='2014-02-28')

To destroy a resource use ``#destroy`` method:

.. code:: python

    client = fortnox.Client(access_token='<YOUR_PERSONAL_ACCESS_TOKEN>', client_secret='<YOUR_APPS_CLIENT_SECRET>')
    client.projects.destroy(project.ProjectNumber)

There other non-CRUD operations supported as well. Please contact
corresponding service files for in-depth documentation.

Full example
~~~~~~~~~~~~

Obtain an access token from a new authorization_code and after get access_token for further user
(website).

.. code:: python

    client = fortnox.Client(authorization_code='<YOUR_APP_INTEGRATION_AUTHORIZATION_CODE>', client_secret='<YOUR_APPS_CLIENT_SECRET>')
    obtained_token = client.token.client.token.access_token()
    access_token = obtained_token.AccessToken

When you instantiate a client or make any request via service objects,
exceptions can be raised for multiple of reasons e.g. a network error,
an authentication error, an invalid param error etc.

Sample below shows how to properly handle exceptions:

.. code:: python

    try:
        # Instantiate a client.
        client = fortnox.Client(access_token='<YOUR_PERSONAL_ACCESS_TOKEN>', client_secret='<YOUR_APPS_CLIENT_SECRET>')
        project = client.projects.create(Description='Website design', Status='ONGOING')
        print(project)
    except fortnox.ConfigurationError as e:
        #  Invalid client configuration option
        pass
    except fortnox.ResourceError as e:
        # Resource related error
        print 'Http status = ' + e.http_status
        print 'Request ID = ' + e.logref
        for error in e.errors:
            print 'field = ' + error.field
            print 'code = ' + error.code
            print 'message = ' + error.message
            print 'details = ' + error.details
    except fortnox.RequestError as e:
        # Invalid query parameters, authentication error etc.
        pass
    except Exception as e:
        # Other kind of exceptioni, probably connectivity related
        pass
Sample below shows how to send files via inbox service with handled exceptions:
.. code:: python

    try:
        # Instantiate a client.
        client = fortnox.Client(access_token='<YOUR_PERSONAL_ACCESS_TOKEN>', client_secret='<YOUR_APPS_CLIENT_SECRET>')
        
        from io import BytesIO
        file =  open('/your/local/file/path/voucher_file.jpeg', 'rb')
        
        buffered_file = BytesIO(file.read())
        file_name = 'voucher1.jpg'
        
        voucher_file = client.inbox.create(path='inbox_v', file=buffered_file, file_name=file_name)
        
        print(voucher_file)
    except fortnox.ConfigurationError as e:
        #  Invalid client configuration option
        pass
    except fortnox.ResourceError as e:
        # Resource related error
        print 'Http status = ' + e.http_status
        print 'Request ID = ' + e.logref
        for error in e.errors:
            print 'field = ' + error.field
            print 'code = ' + error.code
            print 'message = ' + error.message
            print 'details = ' + error.details
    except fortnox.RequestError as e:
        # Invalid query parameters, authentication error etc.
        pass
    except Exception as e:
        # Other kind of exceptioni, probably connectivity related
        pass

Resources and actions
---------------------

Documentation for every action can be found under ``fortnox/services/``
files.

To know about available services, see Fortnox's Official Developer Documentation
---------------------------------------------------------------------------------
https://developer.fortnox.se/documentation/

N.B. Below services are not implemented in the latest release of pyfortnox:

1. Digital Receipt
2. Warehouse Custom Inbound Documents
3. Warehouse Custom Outbound Documents
4. Warehouse Information
5. Warehouse Item Summary
6. Warehouse Resource Specific Fields


Tests
-----

To run all test suites:

.. code:: bash

    $ python setup.py test

And to run a single suite:

.. code:: bash

    $ python setup.py test -s fortnox.test.test_associated_project_service.ProjectServiceTests

Thanks
------

I would like to give huge thanks to my wife, fellow colleagues, mentors and friends for
their continuous inspiration and supports to contribute to this package.
``pyfortnox`` was named from ``pythonic fortnox`` and I was lucky to publish wrapper under **pyfortnox** name.

Thank You!

License
-------

MIT

Bug Reports
-----------

Report `here <https://github.com/xalien10/pyfortnox/issues>`.

Contact
-------

Mahmudul Hasan (ikhtiarcse10ruet@gmail.com)

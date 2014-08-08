.. _ruby_swift:

=====================
 Ruby Swift Examples
=====================

The most well-supported Swift API is included in the Fog cloud services library.
While the older CloudFiles library currently works, it is no longer developed
or supported.

Fog provides a standardized abstraction layer using provider-neutral concepts
of directories (Swift containers) and files (Swift objects).

If you'd prefer to work at a lower level with Swift-specific terminology, refer
to Fog's `OpenStack Storage documentation`_.

.. _OpenStack Storage documentation: https://github.com/fog/fog/blob/master/lib/fog/openstack/docs/storage.md

Create a Connection
===================

This creates a connection so that you can interact with the server:

.. code-block:: ruby

	require 'fog'
	username = 'account_name:user_name'
	api_key  = 'your_secret_key'

  conn = Fog::Storage.new(
    provider: 'OpenStack',
    openstack_username: username,
    openstack_api_key: api_key,
    openstack_auth_url: 'http://objects.dreamhost.com/auth'
  )


Create a Container
==================

This creates a new container called ``my-new-container``

.. code-block:: ruby

	container = conn.directories.create(key: 'my-new-container')


Create an Object
================

This creates a file ``hello.txt`` from the local file named ``my_hello.txt``

.. code-block:: ruby

	obj = container.files.create(
    key: 'hello.txt',
    body: File.open './my_hello.txt'
  )
	obj.content_type = 'text/plain'



List Owned Containers
=====================

This gets a list of Containers that you own, and also prints out
the container name:

.. code-block:: ruby

	conn.directories.each do |container|
		puts container.key
	end

The output will look something like this::

   mahbuckat1
   mahbuckat2
   mahbuckat3


List a Container's Contents
===========================

This gets a list of objects in the container, and prints out each
object's name, the file size, and last modified date:

.. code-block:: ruby

	container.files.each do |object|
		puts "#{object.key}\t#{object.content_length}\t#{object.last_modified}"
	end

The output will look something like this::

   myphoto1.jpg	251262	2011-08-08 21:35:48 UTC
   myphoto2.jpg	262518	2011-08-08 21:38:01 UTC



Retrieve an Object
==================

This downloads the object ``hello.txt`` and saves it in
``./my_hello.txt``:

.. code-block:: ruby

	obj = container.files.get('hello.txt')
	File.write('./my_hello.txt', obj.body)


Delete an Object
================

This deletes the object ``goodbye.txt``:

.. code-block:: ruby

	container.files.destroy('goodbye.txt')

You may also delete a file using its reference:

.. code-block:: ruby

  obj.destroy


Delete a Container
==================

.. note::

   The container must be empty or the request will fail!

.. code-block:: ruby

	container.destroy

You may also delete a container without a reference:

.. code-block:: ruby

  conn.directories.destroy('my-new-container')

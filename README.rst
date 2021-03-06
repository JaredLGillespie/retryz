Retry Decorator
===============

.. image:: https://img.shields.io/travis/jealous/retryz.svg
    :target: https://travis-ci.org/jealous/retryz
.. image:: https://img.shields.io/coveralls/jealous/retryz.svg
    :target: https://coveralls.io/github/jealous/retryz
.. image:: https://img.shields.io/pypi/v/retryz.svg
    :target: https://pypi.python.org/pypi/retryz

VERSION: 0.1.9

Introduction
------------

Function decorator that helps to retry the function under certain criteria.

This package contains the ``retry`` decorator and a bunch of configuration 
parameters for this decorator. 

Tested on python 2.7 and python 3.4.

For quick start, check the tutorial section of this page.
Check [test_retryz.py](test/test_retryz.py) for detail examples.

Installation
------------

``pip install retryz``


License
-------

`Apache License version 2`_

Tutorial
--------

- Retry if `ValueError` is caught.

.. code-block:: python

    @retry(on_error=ValueError)
    def my_func():
        ...

- Retry if `ValueError` or `TypeError` is caught.

.. code-block:: python

    @retry(on_error=lambda e: isinstance(e, (ValueError, TypeError)))
    def my_func():
        ...

- Retry until `TypeError` is caught.

.. code-block:: python

    @retry(on_error=lambda e: not isinstance(e, TypeError))
    def my_func():
        ...

- Retry until `TypeError` or `AttributeError` is caught.

.. code-block:: python

    @retry(on_error=lambda e: not isinstance(e, (TypeError, AttributeError)))
    def my_func():
        ...

- When ``on_error`` is a callback,
  it will retry until ``on_error`` returns ``False``.  Note that callback 
  takes one parameter which is the error instance raised by the decorated 
  function.

.. code-block:: python

    def _error_callback(self, ex):
        assert_that(ex, instance_of(TypeError))
        return self.call_count != 4
    
    @retry(on_error=_error_callback)
    def error_call_back(self):
        ...

- Retry if returns certain value.

.. code-block:: python

    @retry(on_return=True)
    def my_func(self):
        ...

- Retry if return value in the list.

.. code-block:: python

    @retry(on_return=lambda x: x in (1, 2, 3, 4, 5))
    def my_func(self):
        ...

- Retry until certain value is returned.

.. code-block:: python

    @retry(on_return=lambda x: x != 4)
    def my_func(self):
        ...

- Retry until any of the value is returned.

.. code-block:: python

    @retry(on_return=lambda x: x not in [3, 4])
    def my_func(self):
        ...


- When ``on_return`` is a callback, 
  it will retry until ``on_return`` returns ``False``.  Note that callback 
  takes one parameter which is the return value of the decorated function.

.. code-block:: python

    def _return_callback(ret):
        return 4 + ret < 7
    
    @retry(on_return=_return_callback)
    def my_func(self):
        ...

- Retry until timeout (in seconds)

.. code-block:: python
    
    @retry(timeout=0.1)
    def my_func():
        ...

- Retry maximum X times.

.. code-block:: python

    @retry(limit=3)
    def my_func():
        ...

    # or you could specify a callback
    @retry(limit=lambda: 4)
    def my_func_x():
        ...


- Wait X seconds between each retry.

.. code-block:: python

    @retry(wait=0.1, timeout=0.3)
    def my_func():
        ...


- When ``wait`` is a callback, it will wait for the amount of
  seconds returned by the callback.
  The callback takes one parameter which is the current count of retry.

.. code-block:: python

    def _wait_callback(self, tried):
        return 2 ** tried
    
    @retry(wait=_wait_callback, timeout=0.1)
    def my_func():
        ...


- ``on_retry`` could be used to specify a callback.  This callback
  is a function with no parameter.  It will be invoked before each
  retry.  Here is a typical usage.

.. code-block:: python

    def do_login():
        # login if not
        ...
        
    @retry(on_retry=do_login, limit=2)
    def requests(resource_id):
        ...


- ``retry`` could also be called in a functional style.
  Note that the return value is a function.  If you want to call
  it, you need to add an extra ``()``.

.. code-block:: python

    def foo():
        ...
        
    retry(foo, limit=3, timeout=5)()

 

To file issue, please visit:

https://github.com/jealous/retryz


Contact author:

- Cedric Zhuang <jealous@163.com>

.. _Apache License version 2: LICENSE.txt
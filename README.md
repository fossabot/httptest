# httptest

[![Build Status](https://travis-ci.org/pdxjohnny/httptest.svg?branch=master)](https://travis-ci.org/pdxjohnny/httptest) [![codecov](https://codecov.io/gh/pdxjohnny/httptest/branch/master/graph/badge.svg)](https://codecov.io/gh/pdxjohnny/httptest)

HTTP testing inspired by golang's httptest package. Supports wrapping asyncio
coroutine functions (`async def`).

## Usage

### Simple HTTP Server Handler

```python
import unittest
import urllib.request

import httptest

class TestHTTPServer(httptest.Handler):

    def do_GET(self):
        self.send_response(200)
        self.send_header("Content-type", "text/plain")
        self.end_headers()
        self.wfile.write(bytes("what up", "utf-8"))

class TestHTTPTestMethods(unittest.TestCase):

    @httptest.Server(TestHTTPServer)
    def test_call_response(self, ts=httptest.NoServer()):
        with urllib.request.urlopen(ts.url()) as f:
            self.assertEqual(f.read().decode('utf-8'), "what up")

if __name__ == '__main__':
    unittest.main()
```

### Asyncio Support

Asyncio support for the unittest package hasn't yet landed in Python.
[python/issue32972](https://bugs.python.org/issue32972).

If you want a quick way to add `asyncio` test cases you can import the helper
from [intel/dffml](https://github.com/intel/dffml).

```python
import unittest
import urllib.request
from dffml.util.asynctestcase import AsyncTestCase

import httptest

class TestHTTPServer(httptest.Handler):

    def do_GET(self):
        self.send_response(200)
        self.send_header("Content-type", "text/plain")
        self.end_headers()
        self.wfile.write(bytes("what up", "utf-8"))

class TestHTTPTestMethods(AsyncTestCase):

    @httptest.Server(TestHTTPServer)
    async def test_call_response(self, ts=httptest.NoServer()):
        with urllib.request.urlopen(ts.url()) as f:
            self.assertEqual(f.read().decode('utf-8'), "what up")

if __name__ == '__main__':
    unittest.main()
```

In your project's `setup.py`, add `dffml` in `tests_require`.

```python
setup(
    name='your_package',
    ...
    tests_require=[
        'httptest>=0.0.14',
        'dffml>=0.1.2'
    ]
)
```

## Auto Install

If you're making a python package, you'll want to add `httptest` to your
`setup.py` file's `tests_require` section.

This way, when your run `python setup.py test` setuptools will install
`httptest` for you in a package local directory, if it's not already installed.

```python
setup(
    name='your_package',
    ...
    tests_require=[
        'httptest>=0.0.14'
    ]
)
```
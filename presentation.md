class: center, middle, inverse

# Developing Python Packages

.footnote[Vitor Baptista]

.footnote[vitor@vitorbaptista.com]

---

background-image: url(img/oki.svg)
class: inverse

---

# Our first package

```
helloworld/
    helloworld/
*       __init__.py
    setup.py
```

```python
def helloworld():
    return 'Hello world'
```

---

# Our first package

```
helloworld/
    helloworld/
        __init__.py
*   setup.py
```

```python
from setuptools import setup, find_packages

setup(
    name='pyconuk_helloworld',
    version='0.0.1',
    description='A hello world package',
    url='https://github.com/vitorbaptista/helloworld',
    author='Vitor Baptista',
    author_email='vitor@vitorbaptista.com',
    license='MIT',
    packages=find_packages(exclude=('tests')),
)
```

---

# Let's build it!

```bash
$ python setup.py sdist bdist_wheel
# ...
$ ls dist/
pyconuk_helloworld-0.0.1-py2-none-any.whl
pyconuk_helloworld-0.0.1.tar.gz
```
---

# Does it work?

```bash
*$ pip install dist/pyconuk_helloworld-0.0.1.tar.gz 
# ...
$ python
Python 2.7.12 (default, Jul  1 2016, 15:12:24) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
*>>> import helloworld
*>>> helloworld.helloworld()
*'Hello world'
```

---

# Publishing to PyPI test server

Signup to [PyPI's test server](https://testpypi.python.org/) and create a
`~/.pypirc` file with the configuration:

```ini
[distutils]
index-servers =
    test

[test]
repository = https://testpypi.python.org/pypi
username = <YOUR USERNAME>
password = <YOUR PASSWORD>
```

---

# Publishing to PyPI test server

```bash
*$ twine register -r test dist/pyconuk_helloworld-0.0.1-py2-none-any.whl
*$ twine register -r test dist/pyconuk_helloworld-0.0.1.tar.gz
*$ twine upload -r test dist/pyconuk_helloworld-0.0.1*
Uploading distributions to https://testpypi.python.org/pypi
Uploading pyconuk_helloworld-0.0.1-py2-none-any.whl
[================================] 4667/4667 - 00:00:02
Uploading pyconuk_helloworld-0.0.1.tar.gz
[================================] 3360/3360 - 00:00:02k
```
---

# Installing

```bash
*$ pip install -i https://testpypi.python.org/pypi pyconuk_helloworld
Collecting pyconuk_helloworld
  Downloading https://testpypi.python.org/packages/d4/4f/c5bbc77549774bdc29a9d283229f19aaecadbae69a2b6b953261eb7c24cc/pyconuk_helloworld-0.0.1-py2-none-any.whl
Installing collected packages: pyconuk-helloworld
Successfully installed pyconuk-helloworld-0.0.1
```
---

# Publishing to PyPI

Register for an account in [PyPI](https://pypi.python.org/pypi) and alter your
`~/.pypirc`, adding:

```ini
[distutils]
index-servers =
*   pypi
    test

[test]
repository = https://testpypi.python.org/pypi
username = <YOUR USERNAME>
password = <YOUR PASSWORD>

*[pypi]
*repository = https://pypi.python.org/pypi
*username = <YOUR USERNAME>
*password = <YOUR PASSWORD>
```

---

# Publishing to PyPI

```bash
$ twine register dist/pyconuk_helloworld-0.0.1-py2-none-any.whl
$ twine register dist/pyconuk_helloworld-0.0.1.tar.gz
$ twine upload dist/pyconuk_helloworld-0.0.1*
```

---

class: center, middle, inverse

# Congratulations!

Now anyone can do `pip install pyconuk_helloworld` and use our code.

---

class: center, middle, inverse

# ... but that's not enough.
# Let's improve it!

---

# Add .gitignore

To avoid commiting unecessary files to the repository. You can generate one on
https://www.gitignore.io/.

---

# Write README

Unless you have a **very good** reason, use reStructuredText (so PyPI can render it).

```rst
Hello World
===========

Sample package that simply returns "Hello world"

Installing
------------

.. code-block:: shell

    $ pip install pyconuk_helloworld
```

---

# Write README

```python
# setup.py
from setuptools import setup, find_packages

def read(*parts):
    # Return file contents as string

setup(
    name='pyconuk_helloworld',
    version='0.0.1',
    description='A hello world package',
*   long_description=read('README.rst'),
    url='https://github.com/vitorbaptista/helloworld',
    author='Vitor Baptista',
    author_email='vitor@vitorbaptista.com',
    license='MIT',
    packages=find_packages(exclude=('tests')),
)
```

---

# Add classifiers

```python
setup(
    # ...
    classifiers=[
        'Development Status :: 3 - Alpha',
        'License :: OSI Approved :: MIT License',
        'Operating System :: OS Independent',
        'Programming Language :: Python',
        'Programming Language :: Python :: 2',
        'Programming Language :: Python :: 2.7',
        'Programming Language :: Python :: 3',
        'Programming Language :: Python :: 3.3',
        'Programming Language :: Python :: 3.4',
        'Programming Language :: Python :: 3.5',
    ],
)
```

---

# Add LICENSE file

The http://choosealicense.com/ can help you pick a license. I usually pick
[MIT](http://choosealicense.com/licenses/mit/) for my projects.

---

# Add setup.cfg file

```ini
[bdist_wheel]
universal = 1  # Our code runs on Py2 and Py3
```

---

# Add MANIFEST.in

It specifies extra files to be included in the package. You can check which
files are included by default on
https://docs.python.org/3/distutils/sourcedist.html#specifying-the-files-to-distribute.

```ini
include LICENSE
```

---

# Our current structure

```bash
helloworld/
    helloworld/
        __init__.py
    LICENSE
    MANIFEST.in
    README.rst
    setup.cfg
    setup.py
```

---

# Extract the code to a separate file

```bash
helloworld/
    helloworld/
*       __init__.py
*       helloworld.py
    LICENSE
    MANIFEST.in
    README.rst
    setup.cfg
    setup.py
```

```python
# helloworld/__init__.py
from .helloworld import helloworld

# helloworld/helloworld.py
def helloworld():
    return 'Hello world'
```

---

class: center, middle, inverse

# Testing

---

class: center, middle, inverse

# "Legacy code is code without tests."
Michael Feathers in Working Effectively with Legacy Code

---

# Types of test

* Unit tests, integration tests, etc.
* Code coverage
* Linting
* Security
* ... and many others

---

# Test runner

Before writing any test, let's set up a test runner. We'll use
[tox](https://tox.readthedocs.io).

It makes it very easy to maintain our test suite, including testing with
different Python versions and dependencies (if needed).

```bash
$ pip install tox
```

---

# Configuring tox and pytest

There are many test frameworks for Python (e.g. `unittest` and `nose`). My
favorite is [pytest](http://pytest.org). I highly recommend reading its
documentation. To configure `tox` and `pytest`, create a `tox.ini` as:

```ini
[tox]
env =
    py27
    py33
    py34
    py35
# Don't fail if the Python version isn't available
skip_missing_interpreters = true

[testenv]
deps =
    pytest
commands =
    py.test {posargs}
```

---

# Our first test

```
helloworld/
    # ...
    helloworld/
        __init__.py
        helloworld.py
*   tests/
*       test_helloworld.py
```

```python
import helloworld


class TestHelloWorld(object):
    def test_returns_hello_world(self):
        assert helloworld.helloworld() == 'Hello world'
```

---

# Running tests

```bash
$ tox
# ...
____________________ summary _____________________
  py27: commands succeeded
  py33: commands succeeded
  py34: commands succeeded
  py35: commands succeeded
  congratulations :)
```

---

# Code coverage

Change your `tox.ini` file adding:

```ini
[testenv]
deps =
    pytest
*   pytest-cov
*usedevelop = true
commands =
    py.test \
*       --cov helloworld \
        {posargs}

```

---

# Code coverage

```bash
$ tox
# ...
----------- coverage: platform linux, python 3.5.2-final-0 -----------
Name                       Stmts   Miss  Cover
----------------------------------------------
helloworld/__init__.py         1      0   100%
helloworld/helloworld.py       2      0   100%
----------------------------------------------
TOTAL                          3      0   100%
```

---

# Linting

Use a linter to make sure our code follow the same standard and catch a few
mistakes. Alter your `tox.ini` file adding:

```ini
[tox]
envlist =
    # ...
*   lint

# ...

*[testenv:lint]
*deps =
*    flake8
*commands =
*    flake8 .
```

---

# Linting

Let's run the linting tool.

```bash
$ tox -e lint
# ...
./helloworld/__init__.py:1:1: F401 '.helloworld.helloworld' imported but unused
# ...
```

That error isn't important. To make `flake8` ignore it, change
`helloworld/__init__.py` to:

```python
from .helloworld import helloworld  # noqa: F401
```

---

# Security linter

[Bandit](https://github.com/openstack/bandit) is a tool by OpenStack that looks
for common security issues in Python code. To configure it, change your
`tox.ini` as:

```ini
[tox]
envlist =
# ...
    security

# ...

[testenv:security]
deps =
    bandit
commands =
    bandit -r .
```

---

# Security linter

We also need to tell Bandit to ignore test files and our virtual environments.
We do this by creating a `.bandit` file with:

```ini
[bandit]
exclude: .tox,env,tests
```

Now we can run `tox -e security`. It should find no issues.

---

# Validate setup.py

This checks if your `setup.py` is valid, including the reStructuredText strings.

```ini
[tox]
envlist =
# ...
    setuppy

# ...

[testenv:setuppy]
deps =
    docutils
    pygments
commands =
    python setup.py check \
        --metadata \
        --restructuredtext \
        --strict
```

---

# Validate MANIFEST

It's common to forget to add files to the MANIFEST, only realizing the error
when testing the package. To avoid this, you can use the
[check-manifest](https://github.com/mgedmin/check-manifest) tool.

```ini
[tox]
envlist =
# ...
    manifest

# ...
[testenv:manifest]
deps =
    check-manifest
commands =
    check-manifest
```

---

# Validate MANIFEST

If you run `tox -e manifest` now, it'll return a few errors regarding `tests`
and configuration files. We can tell `check-manifest` to ignore those by adding
to our `setup.cfg`:

```ini
[check-manifest]
ignore =
    .bandit
    tests
    tests/*
    tox.ini
```

After this, `tox -e manifest` should be successful.

---

class: center, middle, inverse

# Avoiding "works on my machine"

---

# Continuous integration tools

* [Travis-CI](https://travis-ci.org)
* [AppVeyor](https://appveyor.com)
* [Coveralls](https://coveralls.io)

---

# Travis-CI

```yaml
# .travis.yml
language: python
os: linux
python:
    - 2.7
    - 3.3
    - 3.4
    - 3.5

env:
    - TOXENV="py${PYTHON_VERSION//./}"

install: pip install tox
script: tox
```

---

class: center, middle

# Doesn't Travis support OSX?

---

# AppVeyor

```yaml
# appveyor.yml
build: off
environment:
  matrix:
    - PYTHON: 'C:\\Python27'
      TOXENV: 'py27'
    - PYTHON: 'C:\\Python33'
      TOXENV: 'py33'
    - PYTHON: 'C:\\Python34'
      TOXENV: 'py34'
    - PYTHON: 'C:\\Python35'
      TOXENV: 'py35'

install: '%PYTHON%\\python.exe -m pip install tox'
test_script: '%PYTHON%\\scripts\\tox'
```

.footnote[Add `appveyor.yml` to the files ignored by `check-manifest` in `setup.cfg`]
---

# Coveralls

Change the `tox.ini` file with:

```yaml
[testenv]
*passenv = TRAVIS TRAVIS_JOB_ID TRAVIS_BRANCH
deps =
    pytest
    pytest-cov
*   coveralls
usedevelop = true
commands =
    pytest \
        --cov helloworld \
        {posargs}
*   python {toxinidir}/tests/run_coveralls.py
```

---

# Coveralls

Add `./tests/run_coveralls.py` file:

```python
#!/bin/env/python

import os
import subprocess


if (__name__ == '__main__') and ('TRAVIS' in os.environ):
    rc = subprocess.call('coveralls')
    raise SystemExit(rc)
```

---

class: center, middle

# Don't forget to add your badges

![Badges](./img/badges.png)

---

class: center, middle, inverse

# Releasing a new version

---

# Releasing a new version

1. Decide new version number
1. Write your changelog
1. Bump the version
1. Run all tests
1. Create a new git tag and push code
1. Push new version to PyPI

---

# Decide new version number

Use [Semantic Versioning](http://semver.org/). In a nutshell, it defines
version numbers as `MAJOR.MINOR.PATCH` (e.g. `0.0.1`). You bump the numbers when:

1. MAJOR version when you make incompatible API changes,
1. MINOR version when you add functionality in a backwards-compatible manner, and
1. PATCH version when you make backwards-compatible bug fixes.

There're other values for pre-release and build metadata. Please refer to their
official website to learn more.

---

# Writing changelog

A good changelog is very useful, specially to check if a new versions breaks
compatibility with an old one (and if so, how to fix). The site
http://keepachangelog.com/ has some great tips and suggestions on how to
maintain your CHANGELOG. 

```markdown
## [0.1.0] - 2016-09-15
### Added
- Support multiple languages
- Test suite (including linting, security checks, setup.py and MANIFEST validation)

## 0.0.1 - 2016-09-14
### Added
- Initial package

[0.1.0]: https://github.com/vitorbaptista/pyconuk_helloworld/compare/v0.0.1...v0.1.0
```

---

# Bump the version

```python
# setup.py
setup(
    name='pyconuk_helloworld',
*   version='0.1.0',
    # ...
)
```

---

# Run all tests

Just to be safe.

---

# Create new tag and push code

```bash
$ git tag v0.1.0
$ git push --tags
```

---

# Push to PyPI

```bash
$ python setup.py sdist bdist_wheel
$ twine upload dist/pyconuk_helloworld-0.1.0*
```

---

class: center, middle, inverse

# Bonus
(if we have time)

---

# How to add dependencies?

* Don't pin exact versions, instead require the earliest version known to be
  compatible
* Add the requirements in `setup.py`, not `requirements.txt`

```python
setup(
    # ...
*   install_requires=[
*       'requests >= 2.0',
*   ],
)
```

---

# How to add an executable file?

```python
# helloworld/cli.py
import helloworld

def main():
    print(helloworld.helloworld())
```

```python
# setup.py
setup(
    # ...
    entry_points={
        'console_scripts': [
            'helloworld=helloworld.cli:main',
        ],
    },
)
```

---

# How to add an executable file?

```python
# tests/test_cli.py
import subprocess
import helloworld


class TestCLI(object):
    def test_prints_helloworld_result(self, capfd):
        subprocess.call(['helloworld'])

        out, err = capfd.readouterr()
        assert not err
        assert out.strip() == helloworld.helloworld()
```

---

class: center, middle, inverse

# Thank you!

.footnote[Vitor Baptista]

.footnote[vitor@vitorbaptista.com]

.footnote[https://github.com/vitorbaptista]

---

# References

* Python Packaging User Guide (https://packaging.python.org/)
* The Hitchhiker's Guide to Python (http://docs.python-guide.org/en/latest/shipping/packaging/)
* Sharing your labor of love: PyPI quick and dirty (https://hynek.me/articles/sharing-your-labor-of-love-pypi-quick-and-dirty/)

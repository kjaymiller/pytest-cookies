# pytest-cookies

[pytest][pytest] is a mature testing framework for Python that is developed
by a thriving and ever-growing community of volunteers. It uses plain assert
statements and regular Python comparisons. At the core of the pytest test
framework is a powerful hook-based plugin system.

**pytest-cookies** is a pytest plugin that comes with a ``cookies`` fixture
*which is a
wrapper for the [cookiecutter][cookiecutter] API for generating projects. It
helps you verify that your template is working as expected and takes care of
cleaning up after running the tests. 🍪

# Installation

**pytest-cookies** is available for download from [PyPI][pypi] via [pip][pip]:

```text
pip install pytest-cookies
```
This will automatically install [pytest][pytest] and
[cookiecutter][cookiecutter].

# Usage

## Generate a new project

The ``cookies.bake()`` method generates a new project from your template
based on the default values specified in ``cookiecutter.json``:

```python
def test_bake_project(cookies):
    result = cookies.bake(extra_context={"repo_name": "helloworld"})

    assert result.exit_code == 0
    assert result.exception is None
    assert result.project.basename == "helloworld"
    assert result.project.isdir()
```

This also accepts the ``extra_context`` keyword argument that will be passed
to cookiecutter. The given dictionary will override the default values of the
template context, effectively allowing you to test arbitrary user input data.

For more information on injecting extra context, please check out the [cookiecutter documentation][extra-context].

## Specify the template directory

By default ``cookies.bake()`` looks for a cookiecutter template in the
current directory. This can be overridden on the command line by passing a
``--template`` parameter to pytest:

```text
pytest --template TEMPLATE
```

You can customize the cookiecutter template directory from a test by passing
in the optional ``template`` paramter:

```python
@pytest.fixture
def custom_template(tmpdir):
    template = tmpdir.ensure("cookiecutter-template", dir=True)
    template.join("cookiecutter.json").write('{"repo_name": "example-project"}')

    repo_dir = template.ensure("{{cookiecutter.repo_name}}", dir=True)
    repo_dir.join("README.rst").write("{{cookiecutter.repo_name}}")

    return template


def test_bake_custom_project(cookies, custom_template):
    """Test for 'cookiecutter-template'."""
    result = cookies.bake(template=str(custom_template))

    assert result.exit_code == 0
    assert result.exception is None
    assert result.project.basename == "example-project"
    assert result.project.isdir()
```

## Keep output directories for debugging

By default ``cookies`` removes baked projects.

However, you can pass the ``keep-baked-projects`` flag if you'd like to keep
them ([it won't clutter][temporary-directories] as pytest only keeps the
three newest temporary directories):

```text
pytest --keep-baked-projects
```

# Community

# License

[cookiecutter]: https://github.com/audreyr/cookiecutter
[pytest]: https://github.com/pytest-dev/pytest
[pip]: https://pypi.org/project/pip/
[pypi]: https://pypi.org/project/pytest-cookies/
[extra-context]: https://cookiecutter.readthedocs.io/en/latest/advanced/injecting_context.html
[temporary-directories]: https://docs.pytest.org/en/latest/tmpdir.html#the-default-base-temporary-directory
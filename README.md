# Web testing with Robot Framework and SeleniumLibrary

This project demonstrates browser-based UI testing using [Robot Framework](https://robotframework.org/) and [SeleniumLibrary](https://github.com/robotframework/SeleniumLibrary). It includes a small login demo application and a suite of test cases covering valid login, invalid login, and Gherkin-style scenarios.

## Prerequisites

- Python 3.9 or newer
- [Firefox](https://www.mozilla.org/firefox/) (default) and/or [Chrome/Chromium](https://www.google.com/chrome/)
- Matching browser driver on your `PATH`:
  - Firefox → [geckodriver](https://github.com/mozilla/geckodriver/releases)
  - Chrome → [chromedriver](https://chromedriver.chromium.org/downloads)

## Installation

```bash
pip install -r requirements.txt
```

## Starting demo application

Running tests requires the [demo application](demoapp) located under the `demoapp`
directory to be running.

```bash
python demoapp/server.py
python3 demoapp/server.py
```

After the demo application is started, it will be available at the URL
http://localhost:7272. You can test it manually; valid credentials are
`demo/mode`, and it needs to be running while executing the automated
tests.

Shutdown the server: `Ctrl-C`.

## Running tests

The [test cases](login_tests) are located in the `login_tests` directory. They can be
executed using the `robot` command:

```bash
robot login_tests
```

You can also run an individual test case file and use various command line
options supported by Robot Framework:

```bash
robot login_tests/valid_login.robot
robot --test InvalidUserName --loglevel DEBUG login_tests
```

## Using different browsers

The browser that is used is controlled by the `${BROWSER}` variable defined in
the `resource.robot` resource file. The Firefox browser is used by default, but that
can be easily overridden from the command line:

```bash
robot --variable BROWSER:Chrome login_tests
robot --variable BROWSER:IE login_tests
```

## Test results

After a test run, Robot Framework writes the following files to the current directory:

| File          | Description                                   |
| ------------- | --------------------------------------------- |
| `output.xml`  | Raw machine-readable results                  |
| `log.html`    | Detailed execution log (open in a browser)    |
| `report.html` | High-level summary report (open in a browser) |

Use `--outputdir` to write results to a different location:

```bash
robot --outputdir results login_tests
```

## Project structure

```
demoapp/          # Simple Python web application used as the test target
login_tests/
  resource.robot        # Shared keywords and variables
  valid_login.robot     # Tests for successful login
  invalid_login.robot   # Tests for failed/invalid login attempts
  gherkin_login.robot   # Gherkin (Given/When/Then) style login test
requirements.txt  # Python dependencies
Dockerfile        # Container setup for running tests in CI
```

## Running with Docker

A [Dockerfile](Dockerfile) is provided to run the demo application and all tests inside a container (Chromium is used as the browser):

```bash
# Build the image
docker build -t robot-web-demo .

# Run tests — results are written to /results inside the container
docker run --rm -v "$(pwd)/results:/results" robot-web-demo
```

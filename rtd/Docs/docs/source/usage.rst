Usage
=====

Requirements
------------

PrimeScore currently depends on:

* Python 3.10 or newer
* PostgreSQL running locally
* an API-Football API key

Tech stack:

* Python
* Flask
* PostgreSQL
* HTML
* CSS
* Vanilla JavaScript
* API-Football: ``https://v3.football.api-sports.io``

Environment variables used by the app:

Required:

* ``FOOTBALL_API_KEY``

Usually needed:

* ``DB_HOST``
* ``DB_NAME``
* ``DB_USER``
* ``DB_PASSWORD``
* ``DB_PORT``

Optional:

* ``SECRET_KEY``
* ``CURRENT_SEASON``
* ``FLASK_DEBUG``

Default database values in ``config.py``:

* host: ``localhost``
* database: ``primescore``
* user: ``postgres``
* port: ``5432``


Installation
------------

1. Open the project folder
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: powershell

   cd C:\path\to\primescore


2. Create a virtual environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: powershell

   py -m venv .venv


3. Activate the virtual environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: powershell

   .\.venv\Scripts\Activate.ps1


4. Install dependencies
~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: powershell

   pip install -r requirements.txt


5. Create the PostgreSQL database
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: powershell

   psql -U postgres -c "CREATE DATABASE primescore;"


6. Load the schema
~~~~~~~~~~~~~~~~~~

.. code-block:: powershell

   psql -U postgres -d primescore -f db\schema.sql


7. Set environment variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: powershell

   $env:FOOTBALL_API_KEY="YOUR_API_FOOTBALL_KEY"
   $env:DB_HOST="localhost"
   $env:DB_NAME="primescore"
   $env:DB_USER="postgres"
   $env:DB_PASSWORD="YOUR_POSTGRES_PASSWORD"
   $env:DB_PORT="5432"


Running the App
---------------

Start the server from the project root:

.. code-block:: powershell

   python app.py

Then open one of these addresses in the browser:

.. code-block:: text

   http://127.0.0.1:5000
   http://localhost:5000

The app runs on port ``5000`` because ``app.py`` starts Flask with:

.. code-block:: python

   app.run(host="0.0.0.0", port=5000, debug=debug_mode)


Version Control
---------------

Suggested Git workflow:

.. code-block:: powershell

   git clone <repository-url>
   cd primescore
   git checkout -b feature/short-description

Good practice for this project:

* keep commits focused on one feature or bug fix
* write clear commit messages
* avoid committing ``.venv``, ``__pycache__``, ``*.pyc`` files, or local editor folders
* run the app before pushing changes

Typical commands:

.. code-block:: powershell

   git add .
   git commit -m "Add favourites route tests and update documentation"
   git push origin feature/short-description


Definitions
-----------

User Account
~~~~~~~~~~~~

A PrimeScore account stores:

* username
* email
* password hash
* optional display name
* optional bio


Favourite Team
~~~~~~~~~~~~~~

A favourite team is a club selected by the user so the app can show
team-focused football data.


Favourite Player
~~~~~~~~~~~~~~~~

A favourite player is a player selected by the user so the app can show and
compare player statistics.


Favourite League
~~~~~~~~~~~~~~~~

A favourite league is a saved competition such as the Premier League, Serie A,
or La Liga.


Match
~~~~~

A match is a fixture returned by API-Football. Depending on status, it may be:

* live
* not started
* finished


Statistics
~~~~~~~~~~

Statistics are football values returned by API-Football or calculated from
fixtures, such as:

* goals
* assists
* appearances
* wins
* draws
* losses
* clean sheets


Common Problems
---------------

``psql`` is not recognized
~~~~~~~~~~~~~~~~~~~~~~~~~~

PostgreSQL command-line tools are probably not in your PATH.


PowerShell does not accept ``export``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

PowerShell uses this format:

.. code-block:: powershell

   $env:FOOTBALL_API_KEY="YOUR_KEY"

Do not use Linux/macOS ``export`` syntax in PowerShell.


The app starts but football data is missing
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Check that:

* ``FOOTBALL_API_KEY`` is set
* the key is valid
* the free API plan has not rate-limited the app
* the daily request quota has not been exhausted


The browser shows old UI behaviour
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Hard refresh the page:

.. code-block:: text

   Ctrl + F5


Fixtures or current-season data look empty
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This can be caused by API-Football plan limits rather than local code errors.
Some routes depend on seasons and query types allowed by the current plan.


Note
----

PrimeScore is still under active development. The backend, frontend, testing,
and documentation can still be improved further with:

* more browser-based end-to-end testing
* clearer API quota and plan-limit messages
* more frontend automation
* more documentation pages if the project grows

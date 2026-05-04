PrimeScore
==========

Welcome to PrimeScore's documentation.

PrimeScore is a Flask and PostgreSQL football statistics application built
around API-Football. It lets users register, log in, manage favourites, view
live matches, check fixtures and results, browse league tables, compare teams
and players, and manage profile and notification settings from one
browser-based dashboard.

This documentation is written to help a new teammate, marker, or developer
understand:

* what the project does
* how to install and run it
* which requirements it implements
* how the files connect to each other
* where the important logic lives
* how the project can be tested

Contents
--------

.. contents::
   :local:
   :depth: 2


Requirements
============

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
============

1. Open the project folder
--------------------------

.. code-block:: powershell

   cd C:\path\to\primescore


2. Create a virtual environment
-------------------------------

.. code-block:: powershell

   py -m venv .venv


3. Activate the virtual environment
-----------------------------------

.. code-block:: powershell

   .\.venv\Scripts\Activate.ps1


4. Install dependencies
-----------------------

.. code-block:: powershell

   pip install -r requirements.txt


5. Create the PostgreSQL database
---------------------------------

.. code-block:: powershell

   psql -U postgres -c "CREATE DATABASE primescore;"


6. Load the schema
------------------

.. code-block:: powershell

   psql -U postgres -d primescore -f db\schema.sql


7. Set environment variables
----------------------------

.. code-block:: powershell

   $env:FOOTBALL_API_KEY="YOUR_API_FOOTBALL_KEY"
   $env:DB_HOST="localhost"
   $env:DB_NAME="primescore"
   $env:DB_USER="postgres"
   $env:DB_PASSWORD="YOUR_POSTGRES_PASSWORD"
   $env:DB_PORT="5432"


Running the App
===============

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
===============

Suggested Git workflow:

.. code-block:: powershell

   git clone <your-repo-url>
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
===========

User Account
------------

A PrimeScore account stores:

* username
* email
* password hash
* optional display name
* optional bio


Favourite Team
--------------

A favourite team is a club selected by the user so the app can show
team-focused football data.


Favourite Player
----------------

A favourite player is a player selected by the user so the app can show and
compare player statistics.


Favourite League
----------------

A favourite league is a saved competition such as the Premier League, Serie A,
or La Liga.


Match
-----

A match is a fixture returned by API-Football. Depending on status, it may be:

* live
* not started
* finished


Statistics
----------

Statistics are football values returned by API-Football or calculated from
fixtures, such as:

* goals
* assists
* appearances
* wins
* draws
* losses
* clean sheets


Requirement 1: Manage User Accounts
===================================

Users must be able to create and use accounts securely.

Implemented behaviour:

* users can register new accounts
* users can log in with existing credentials
* users can log out
* the app keeps session state for authenticated use
* the app identifies first-time users based on empty favourites
* users can request password reset messaging

Consequences or side effects:

* protected API routes check ``session["user_id"]``
* registration seeds default favourites and notification settings rows

Main code:

* ``app.py``
* ``routes/auth.py``
* ``templates/pages/login_page.html``
* ``static/js/auth.js``


Requirement 2: Manage Favourites
================================

Users must be able to save and update favourite teams, players, and leagues.

Implemented behaviour:

* users can save up to 5 favourite teams
* users can save up to 10 favourite players
* users can save up to 3 favourite leagues
* favourites are displayed using readable names
* favourites return safe empty lists when a user is logged out

Consequences or side effects:

* favourites are used by the home screen
* player favourites use team-based squad loading

Main code:

* ``routes/favourites.py``
* ``routes/utils.py``
* ``templates/pages/favourites_page.html``
* ``static/js/favourites.js``


Requirement 3: Show a Personalised Home Screen
==============================================

The home screen should show useful football data before and after favourites
are chosen.

Implemented behaviour:

* the home page displays a welcome message
* first-time users are prompted to add favourites
* the home page shows favourite summary counts
* the home page shows live matches
* the home page shows upcoming fixtures
* the home page shows recent results
* the home page shows a league table

Consequences or side effects:

* the home screen coordinates several data sources
* the screen depends on API availability and API plan limits

Main code:

* ``routes/favourites.py``
* ``templates/pages/home_page.html``
* ``static/js/home.js``


Requirement 4: Show Live Matches, Fixtures, and Results
=======================================================

Users should be able to see current and recent football matches.

Implemented behaviour:

* live matches come from API-Football using the ``fixtures`` endpoint
* fixtures are returned through ``/api/fixtures``
* results are returned through ``/api/results``
* routes support league-based and team-based filtering

Consequences or side effects:

* these flows depend heavily on API availability
* the frontend must handle cases where the API returns no data

Main code:

* ``routes/matches.py``
* ``templates/pages/live_page.html``
* ``templates/pages/fixtures_page.html``
* ``templates/pages/results_page.html``
* ``static/js/matches.js``


Requirement 5: Show League Standings
====================================

Users should be able to view league tables.

Implemented behaviour:

* standings are retrieved using mapped league codes
* the backend formats raw API-Football standings into a frontend-friendly shape
* the home page and leagues page both use standings data

Consequences or side effects:

* internal codes such as ``PL``, ``SA``, ``PD``, and ``BL1`` are translated to API league IDs

Main code:

* ``routes/stats.py``
* ``services/api.py``
* ``templates/pages/leagues_page.html``
* ``static/js/search.js``
* ``static/js/matches.js``


Requirement 6: Show Team and Player Statistics
==============================================

Users should be able to inspect football statistics for teams and players.

Implemented behaviour:

* player statistics are fetched by player ID and season
* player statistics are summed across available competition entries
* team statistics are calculated from finished matches
* comparison tables can show unavailable values as ``N/A``

Consequences or side effects:

* statistics quality depends on the external API
* player data may depend on the season available in API-Football

Main code:

* ``routes/stats.py``
* ``services/api.py``
* ``templates/pages/compare_page.html``
* ``static/js/compare.js``


Requirement 7: Search and Compare Football Data
===============================================

Users should be able to search for leagues, teams, and players, then compare
relevant data.

Implemented behaviour:

* team names can be searched through autocomplete
* leagues can be searched through autocomplete
* players are selected through a team squad picker
* team and player comparison flows use resolved IDs
* comparison tables highlight the highest numeric values

Consequences or side effects:

* lookup is important because the UI works with names, while the API mainly works with IDs
* safe error handling is needed when data is missing

Main code:

* ``routes/utils.py``
* ``routes/stats.py``
* ``templates/pages/compare_page.html``
* ``static/js/compare.js``
* ``static/js/search.js``


Requirement 8: Manage Profile and Notification Settings
=======================================================

Users should be able to edit account details and preferences.

Implemented behaviour:

* users can update display name
* users can update bio
* users can change password
* users can save notification preference booleans
* default notification settings are returned if no settings row exists yet

Consequences or side effects:

* profile updates also update session display values where needed
* notification preferences are stored, but real push notification delivery is not implemented

Main code:

* ``routes/profile.py``
* ``routes/notifications.py``
* ``templates/pages/profile_page.html``
* ``templates/pages/settings_page.html``
* ``static/js/profile.js``
* ``static/js/notifications.js``


Architecture and Data Flow
==========================

PrimeScore follows a simple 3-layer structure:

1. Presentation layer

   * HTML templates
   * CSS
   * JavaScript modules

2. Application layer

   * Flask routes
   * request validation
   * response formatting
   * session handling

3. Data and integration layer

   * PostgreSQL
   * API-Football

Typical request flow:

.. code-block:: text

   Browser UI
     -> JavaScript handler
     -> Flask route (/api/...)
     -> Database lookup and/or API-Football call
     -> Response formatting
     -> JSON returned to frontend
     -> UI updated in the page

The biggest connection points in the system are:

* ``app.py``

  * creates the Flask app
  * registers all blueprints
  * serves ``dashboard.html``

* ``routes/utils.py``

  * handles health checks
  * searches teams and competitions
  * resolves teams, players, and leagues by ID

* ``routes/favourites.py``

  * handles favourites
  * builds the ``/api/home-screen`` payload

* ``routes/stats.py``

  * serves standings
  * serves team statistics
  * serves player statistics

* ``services/api.py``

  * centralises external API requests
  * handles caching
  * formats standings
  * computes team statistics

* ``db/connection.py``

  * centralises database access through pooled connections and ``DBContext``


Code Map
========

Application Entry Point: app.py
-------------------------------

Key responsibilities:

* create the Flask app
* apply configuration from ``config.py``
* register all route blueprints
* serve the main dashboard shell
* define basic HTTP error handlers


Configuration: config.py
------------------------

Key responsibilities:

* read environment variables
* store database config
* store session/cookie config
* store API-Football base URL, key, timeout, and season


Database Layer: db/connection.py and db/schema.sql
--------------------------------------------------

``db/connection.py``:

* sets up PostgreSQL connection pooling
* provides ``get_db_connection()``
* provides ``release_db_connection()``
* provides ``DBContext`` for safe commit/rollback handling

``db/schema.sql``:

* defines the project tables
* stores user accounts
* stores favourites
* stores notification settings


Authentication: routes/auth.py
------------------------------

Key responsibilities:

* register
* login
* logout
* session lookup
* forgot password response
* login validation
* simple rate-limit protection

Connected frontend:

* ``templates/pages/login_page.html``
* ``static/js/auth.js``


Home and Favourites: routes/favourites.py
-----------------------------------------

Key responsibilities:

* get favourites
* save favourites
* build the ``/api/home-screen`` payload
* return football data for the dashboard
* map fixture data into frontend shape

Connected frontend:

* ``templates/pages/home_page.html``
* ``templates/pages/favourites_page.html``
* ``static/js/home.js``
* ``static/js/favourites.js``


Match Data: routes/matches.py
-----------------------------

Key responsibilities:

* live matches
* upcoming fixtures
* recent results
* league or team filtering
* route-level mapping of match data for the UI

Connected frontend:

* ``templates/pages/live_page.html``
* ``templates/pages/fixtures_page.html``
* ``templates/pages/results_page.html``
* ``static/js/matches.js``


Standings and Statistics: routes/stats.py
-----------------------------------------

Key responsibilities:

* league standings
* team statistics
* player statistics
* standings lookup used by comparison fallback logic
* aggregation of player statistics across competition entries

Connected frontend:

* ``templates/pages/leagues_page.html``
* ``templates/pages/compare_page.html``
* ``static/js/compare.js``
* ``static/js/search.js``


Search and Resolution: routes/utils.py
--------------------------------------

Key responsibilities:

* health check
* search endpoint
* resolve team endpoint
* resolve league endpoint
* get team players endpoint
* resolve team, player, and league by ID

Why it matters:

* this file connects user-friendly search input to API-Football IDs


Profile and Notifications
-------------------------

``routes/profile.py``:

* load profile data
* update profile fields
* change password

``routes/notifications.py``:

* load notification settings
* save notification settings

Connected frontend:

* ``templates/pages/profile_page.html``
* ``templates/pages/settings_page.html``
* ``static/js/profile.js``
* ``static/js/notifications.js``


External Football API Integration: services/api.py
--------------------------------------------------

Key responsibilities:

* define supported endpoint mappings
* make HTTP requests to API-Football
* add request headers
* cache selected responses in memory
* retry failed server requests
* log rate-limit information
* format standings
* compute team stats from finished matches

Why it matters:

* this is the main boundary between PrimeScore and the external football API


Layout and Page Shell
---------------------

``templates/base.html``:

* global page structure
* includes header, sidebar, footer, and scripts

``templates/dashboard.html``:

* includes all page sections into one shell
* login, home, live, fixtures, results, leagues, compare, favourites, settings, and profile are mounted here


Shared Partials
---------------

Important files:

* ``templates/partials/header.html``
* ``templates/partials/sidebar.html``
* ``templates/partials/footer.html``
* ``templates/partials/scripts.html``


Page Sections
-------------

Important files:

* ``templates/pages/login_page.html``
* ``templates/pages/home_page.html``
* ``templates/pages/live_page.html``
* ``templates/pages/fixtures_page.html``
* ``templates/pages/results_page.html``
* ``templates/pages/leagues_page.html``
* ``templates/pages/compare_page.html``
* ``templates/pages/favourites_page.html``
* ``templates/pages/settings_page.html``
* ``templates/pages/profile_page.html``


Frontend Scripts
----------------

Important files:

* ``static/js/core.js``

  * shared client-side state
  * API fetch wrapper
  * message helpers
  * HTML escaping
  * debounce helper

* ``static/js/uihelpers.js``

  * compare tabs
  * password visibility
  * login/register tab switching
  * profile dropdown
  * sidebar toggle

* ``static/js/auth.js``

  * registration
  * login
  * logout
  * forgot password

* ``static/js/home.js``

  * home screen rendering
  * match cards
  * league tables
  * favourites summary

* ``static/js/favourites.js``

  * favourites form
  * autocomplete
  * favourite tags
  * squad picker
  * saving favourites

* ``static/js/matches.js``

  * live matches
  * fixtures
  * results
  * standings loading

* ``static/js/compare.js``

  * team comparison
  * player comparison
  * autocomplete
  * squad picker
  * comparison table rendering

* ``static/js/notifications.js``

  * notification settings form

* ``static/js/profile.js``

  * profile editing
  * password change

* ``static/js/search.js``

  * league search
  * standings search

* ``static/js/nav.js``

  * page switching
  * sidebar active states

* ``static/js/init.js``

  * starts the client-side app
  * binds events
  * checks session state


Testing
=======

Manual tests should cover the following areas.

Authentication Tests
--------------------

Check that users can:

* register with valid details
* receive errors for invalid registration details
* log in with valid credentials
* receive an error for invalid credentials
* log out successfully
* remain logged in during an active session


Favourites Tests
----------------

Check that users can:

* search for teams
* add favourite teams
* search a team and load its squad
* add favourite players
* search for leagues
* add favourite leagues
* remove favourite tags
* save favourites
* reload the app and still see saved favourites


Home Page Tests
---------------

Check that the home page displays:

* welcome message
* favourite summary
* live matches
* upcoming fixtures
* recent results
* league table


Match Tests
-----------

Check that users can:

* view live matches
* refresh live matches
* view upcoming fixtures
* view recent results
* filter fixtures and results by league


League Tests
------------

Check that users can:

* search for a league
* select a league from suggestions
* view league standings


Comparison Tests
----------------

Check that users can:

* compare 2, 3, or 4 teams
* compare 2, 3, or 4 players
* search teams through autocomplete
* select players from squad lists
* see ``N/A`` where data is unavailable


Profile and Settings Tests
--------------------------

Check that users can:

* load profile data
* update display name
* update bio
* save notification settings
* change password
* receive an error when the current password is incorrect


Common Problems
===============

``psql`` is not recognized
--------------------------

PostgreSQL command-line tools are probably not in your PATH.


PowerShell does not accept ``export``
-------------------------------------

PowerShell uses this format:

.. code-block:: powershell

   $env:FOOTBALL_API_KEY="YOUR_KEY"

Do not use Linux/macOS ``export`` syntax in PowerShell.


The app starts but football data is missing
-------------------------------------------

Check that:

* ``FOOTBALL_API_KEY`` is set
* the key is valid
* the free API plan has not rate-limited the app
* the daily request quota has not been exhausted


The browser shows old UI behaviour
----------------------------------

Hard refresh the page:

.. code-block:: text

   Ctrl + F5


Fixtures or current-season data look empty
------------------------------------------

This can be caused by API-Football plan limits rather than local code errors.
Some routes depend on seasons and query types allowed by the current plan.


Note
====

PrimeScore is still under active development. The backend, frontend, testing,
and documentation can still be improved further with:

* more browser-based end-to-end testing
* clearer API quota and plan-limit messages
* more frontend automation
* more documentation pages if the project grows

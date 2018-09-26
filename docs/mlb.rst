MLB Package
===========

The MLB package offers multiple modules which can be used to retrieve
information and statistics for Major League Baseball, such as team names,
season stats, game schedules, and boxscore metrics.

Boxscore
--------

The Boxscore module can be used to grab information from a specific game.
Metrics range from number of runs scored to the number of sacrifice flies, to
the slugging percentage and much more. The Boxscore can be easily queried by
passing a boxscore's URI on sports-reference.com which can be retrieved from the
``Schedule`` class (see ``Schedule`` module below for more information on
retrieving game-specific information).

.. code-block:: python

    from sportsreference.mlb.boxscore import Boxscore

    game_data = Boxscore('BOS/BOS201808020')
    print(game_data.home_runs)  # Prints 15
    print(game_data.away_runs)  # Prints 7
    df = game_data.dataframe  # Returns a Pandas DataFrame of game metrics

The Boxscore module also contains a ``Boxscores`` class which searches for all
games played on a particular day and returns a dictionary of matchups between
all teams on the requested day. The dictionary includes the names and
abbreviations for each matchup as well as the boxscore link if applicable.

.. code-block:: python

    from datetime import datetime
    from sportsreference.mlb.boxscore import Boxscores

    games_today = Boxscores(datetime.today())
    print(games_today.games)  # Prints a dictionary of all matchups for today

.. automodule:: sportsreference.mlb.boxscore
    :members:
    :undoc-members:
    :show-inheritance:

Roster
------

The Roster module contains detailed player information, allowing each player to
be queried by their player ID using the ``Player`` pseudo-class which returns an
instance of either the ``Fielder`` or ``Pitcher`` class depending on the
player's listed position. Only the ``Player`` function should be used to
retrieve an instance of the player instead of pulling directly from the
``Fielder``, ``Pitcher``, or ``PlayerBaseClass`` classes. The returned instance
contains detailed information ranging from career home runs to single-season
stats and player height, weight, and nationality. The following is an example on
collecting career information for José Altuve.

.. code-block:: python

    from sportsreference.mlb.roster import Player

    altuve = Player('altuvjo01')
    print(altuve.name)  # Prints 'José Altuve'
    print(altuve.hits)  # Prints Altuve's career hits total
    # Prints a Pandas DataFrame of all relevant stats per season for Altuve
    print(altuve.dataframe)

The process of getting information on pitchers is the same as with fielders
above:

.. code-block:: python

    from sportsreference.mlb.roster import Player

    verlander = Player('verlaju01')
    print(verlander.name)  # Prints 'Justin Verlander'
    print(verlander.wins)  # Prints Verlander's career wins
    # Prints a Pandas DataFrame of all relevant stats per season for Verlander
    print(verlander.dataframe)

By default, the player's career stats are returned whenever a property is
called. To get stats for a specific season, call the class instance with the
season string. All future property requests will return the season-specific
stats.

.. code-block:: python

    from sportsreference.mlb.roster import Player

    altuve = Player('altuvjo01')  # Currently pulling career stats
    print(altuve.hits)  # Prints Altuve's CAREER hits total
    # Prints Altuve's hits total only for the 2017 season
    print(altuve('2017').hits)
    # Prints Altuve's home runs total for the 2017 season only
    print(altuve.home_runs)

After requesting single-season stats, the career stats can be requested again
by calling the class without arguments or with the 'Career' string passed.

.. code-block:: python

    from sportsreference.mlb.roster import Player

    altuve = Player('altuvjo01')  # Currently pulling career stats
    # Prints Altuve's hits total only for the 2017 season
    print(altuve('2017').hits)
    print(altuve('Career').hits)  # Prints Altuve's career hits total

In addition, the Roster module also contains the ``Roster`` class which can be
used to pull all players on a team's roster during a given season and creates
instances of the Player class for each team member and adds them to a list to be
easily queried.

.. code-block:: python

    from sportsreference.mlb.roster import Roster

    astros = Roster('HOU')
    for player in astros.players:
        # Prints the name of all players who played for the Astros in the most
        # recent season.
        print(player.name)

.. automodule:: sportsreference.mlb.roster
    :members:
    :undoc-members:
    :show-inheritance:

Schedule
--------

The Schedule module can be used to iterate over all games in a team's schedule
to get game information such as the date, score, result, and more. Each game
also has a link to the ``Boxscore`` class which has much more detailed
information on the game metrics.

.. code-block:: python

    from sportsreference.mlb.schedule import Schedule

    houston_schedule = Schedule('HOU')
    for game in houston_schedule:
        print(game.date)  # Prints the date the game was played
        print(game.result)  # Prints whether the team won or lost
        # Creates an instance of the Boxscore class for the game.
        boxscore = game.boxscore

.. automodule:: sportsreference.mlb.schedule
    :members:
    :undoc-members:
    :show-inheritance:

Teams
-----

The Teams module exposes information for all MLB teams including the team name
and abbreviation, the number of games they won during the season, the total
number of bases they've stolen, and much more.

.. code-block:: python

    from sportsreference.mlb.teams import Teams

    teams = Teams()
    for team in teams:
        print(team.name)  # Prints the team's name
        print(team.batting_average)  # Prints the team's season batting average

Each Team instance contains a link to the ``Schedule`` class which enables easy
iteration over all games for a particular team. A Pandas DataFrame can also be
queried to easily grab all stats for all games.

.. code-block:: python

    from sportsreference.mlb.teams import Teams

    teams = Teams()
    for team in teams:
        schedule = team.schedule  # Returns a Schedule instance for each team
        # Returns a Pandas DataFrame of all metrics for all game Boxscores for
        # a season.
        df = team.schedule.dataframe_extended

Lastly, each Team instance also contains a link to the ``Roster`` class which
enables players from the team to be easily queried. Each Roster instance
contains detailed stats and information for each player on the team.

.. code-block:: python

    from sportsreference.mlb.teams import Teams

    for team in Teams():
        roster = team.roster  # Gets each team's roster
        for player in roster.players:
            print(player.name)  # Prints each players name on the roster

.. automodule:: sportsreference.mlb.teams
    :members:
    :undoc-members:
    :show-inheritance:

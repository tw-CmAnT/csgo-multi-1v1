csgo-multi-1v1
=======================================

[![Build status](http://ci.splewis.net/job/csgo-multi-1v1/badge/icon)](http://ci.splewis.net/job/csgo-multi-1v1/)
[![GitHub Downloads](https://img.shields.io/github/downloads/splewis/csgo-multi-1v1/total.svg?style=flat-square&label=Downloads)](https://github.com/splewis/csgo-multi-1v1/releases/latest)

**Status: Supported.**

This is home of the CS:GO multi1v1 arena plugin. It sets up any number of players in 1v1-situations on specially made maps and they fight in a ladder-type system. The winners move up, the losers go down.

Also see the [AlliedModders thread](https://forums.alliedmods.net/showthread.php?t=241056) and the [the wiki](https://github.com/splewis/csgo-multi-1v1/wiki) for more information.

## Features
- Round types: there are 3 round types: rifle, pistol, and awp
- Player selection: players can select to allow pistol and awp rounds or ban them, rifle rounds are always allowed
- Player preference: players can also select a preference of round type, if player preferences match they will play that type
- Weapon selection: players can select their primary (i.e. their rifle) and their pistol
- Armor on pistol rounds: helmets are taken away, and kevlar is also taken away if the player selected an upgraded pistol
- ELO ranking system: optionally, player statistics can be stored in a database, see below for details

## For plugin developers
Check the [multi1v1.inc](scripting/include/multi1v1.inc) file to see what natives and forwards are avaliable to tweak the behavior of the plugin in more sophisticated ways.


## Extra plugins
Sometimes it's easier to add something in a seperate plugin than add more convars, thus some features may be in support plugins. These are all optional.

- **multi1v1_flashbangs**: if both players in an arena say "yes" to getting flashbangs, a flashbang is given to each player
- **multi1v1_kniferounds**: adds unranked knife rounds
- **multi1v1_online_stats_viewer**: adds the !stats and related commands that open up a stats webpage in a MOTD panel


## Download
Stable releases are in the [GitHub Releases](https://github.com/splewis/csgo-multi-1v1/releases) section.

I **strongly** recommend using the [Updater](https://forums.alliedmods.net/showthread.php?t=169095) plugin which can automatically update the plugin for bug fixes.
Any changes made through an automatic update will be backwards compatible.

You may also download the [latest development build](http://ci.splewis.net/job/csgo-multi-1v1/lastSuccessfulBuild/) if you wish. If you report any bugs from these, make sure to include the build number (when typing ``sm plugins list`` into the server console, the build number will be displayed with the plugin version).

## Installation

#### Requirements

Sourcemod 1.7 or later.

#### Instructions
Download the archive and extract the files to the game server. From the download, you should have installed the following (to the ``csgo`` directory):
- ``addons/sourcemod/plugins/multi1v1.smx``
- ``addons/sourcemod/configs/multi1v1_weapons.cfg``
- ``addons/sourcemod/translations``
- ``cfg/sourcemod/multi1v1``

If you are going to use a web-stats interface, you should also add the ``multi1v1_online_stats_viewer.smx`` plugin, which is under the ``plugins/disabled`` directory by default. **Note:** you still have to create a website for this. This project only provides the game server plugin.


#### Configuration

The file ``cfg/sourcemod/multi1v1/multi1v1.cfg`` will be autogenerated when the plugin is first run and you can tweak it if you wish.

You may also tweak the values in ``cfg/sourcemod/multi1v1/game_cvars.cfg``, which is executed by the plugin each map start.

Here is a brief list of **some** cvars available. See the auto-generated ``cfg/sourcemod/multi1v1/multi1v1.cfg`` file for descriptions.
- sm_multi1v1_autoupdate: whether the plugin attempts to use the auto-updater plugin
- sm_multi1v1_pistol_behavior: what types of pistols (if any) should be given in non-pistol rounds
- sm_multi1v1_roundtime: length of the round
- sm_multi1v1_use_database: whether the plugin attempts to store player statistics (e.g. elo ranking) in a MySQL database
- sm_multi1v1_verbose_spawns: whether the plugin will dump information on player-spawn clustering on map starts


``addons/sourcemod/configs/multi1v1_weapons.cfg`` contains the list of weapons that are available under the rifle and pistol menus. You are free to add or remove weapons from this as long as they match the correct format. Note that the ``team`` part is only for making sure the player gets the correct weapon skin, otherwise it has no effect.


## More help on setting up the stats system
There is a [wiki page](https://github.com/splewis/csgo-multi-1v1/wiki/Standard-stats-setup) that explains how to setup the stats system with the provided components.


## Building
The build process is managed by my [smbuilder](https://github.com/splewis/sm-builder) project. You can still compile multi1v1.sp without it, however.

To compile, you will need:
- [SMLib](https://github.com/bcserv/smlib) (required)

You should make sure you have a relatively recent version of smlib - some changes were made to accommodate sourcemod 1.7 changes.


## Maps
I have a [workshop collection](http://steamcommunity.com/sharedfiles/filedetails/?id=249376192) of maps I know of. The "am_" prefix stands for aim_multi, reflecting the fact that the maps are similar to aim_ maps but there are multiple copies of them.

**Note: standard maps (de_dust2, etc.) or aim maps (aim_map, etc.) will not work with this plugin. Maps must be custom-made with multiple arenas.**

Guidelines for making a multi-1v1 map:
- Create 1 arena and test it well, and when are you happy copy it
- Create a bunch of arenas, I'd recommend making at least **16**
- The players shouldn't be able to see each other on spawn
- Each group of spawns (e.g. all CT spawns in arena 1) must be within 1600.0 units of each other, this is required to cluster spawns into the arenas and not configurable
- Ensure that the arenas are sufficiently far apart so players don't hear shooting in other arenas
- If you want to edit your map, it's easiest to delete all but 1 arena and re-copy them. Be warned this can cause issues with the game's lighting and clients may crash the first time they load the new map if they had downloaded the old one previously
- You should avoid areas where it's easy for 1 player to hide; ideally they should have to cover multiple angles if they sit in one spot
- Here is an example map: [am_grass2.vmf](misc/am_grass2.vmf)
- The cvar ``sm_multi1v1_verbose_spawns`` can be set to 1 to log information about how the spawns were partitioned into arenas on map changes


#### Using the statistics database

**Note: SQLite is not supported. Only MySQL is.**

You should add a database named mult1v1 to your databases.cfg file like so:

    "multi1v1"
    {
        "driver"            "mysql"
        "host"              "123.123.123.123"   // localhost works too
        "database"          "game_servers_database"
        "user"              "mymulti1v1server"
        "pass"              "strongpassword"
        "timeout"           "10"
        "port"          "3306"  // whatever port MySQL is set up on, 3306 is default
    }

To create a MySQL user and database on the database server, you can run:

    CREATE DATABASE game_servers_database;
    CREATE USER 'mymulti1v1server'@'123.123.123.123' IDENTIFIED BY 'strongpassword';
    GRANT ALL PRIVILEGES ON game_servers_database.multi1v1_stats TO 'mymulti1v1server'@'123.123.123.123';
    FLUSH PRIVILEGES;

Make sure to change the IP, the username, and the password. You should probably change the database as well, especially if you already have one set up you can use.

Schema:

    mysql> describe multi1v1_stats;
    +--------------+-------------+------+-----+---------+-------+
    | Field        | Type        | Null | Key | Default | Extra |
    +--------------+-------------+------+-----+---------+-------+
    | accountID    | int(11)     | NO   | PRI | 0       |       |
    | serverID     | int(11)     | NO   | PRI | 0       |       |
    | auth         | varchar(64) | NO   |     |         |       |
    | name         | varchar(64) | NO   |     |         |       |
    | wins         | int(11)     | NO   |     | 0       |       |
    | losses       | int(11)     | NO   |     | 0       |       |
    | rating       | float       | NO   |     | 1500    |       |
    | lastTime     | int         | NO   |     | 0       |       |
    | recentRounds | int         | NO   |     | 0       |       |
    +--------------+-------------+------+-----+---------+-------+


Note that the ``accountID`` field is what is returned by [GetSteamAccountID](https://wiki.alliedmods.net/SourceMod_1.5.0_API_Changes#Clients), which is "the lower 32 bits of the full 64-bit Steam ID (referred to as community id by some) and is unique per account."

``auth`` is the steam ID auth string, and the ``lastTime`` field is the last time the player connected to the server.
The time comes from [GetTime](http://docs.sourcemod.net/api/index.php?fastload=show&id=601&), which returns the "number of seconds since unix epoch".

``recentRounds`` is simply incremented each time the player completes a round. This can be used, for example, to check the rounds played on a daily basis and lower ratings if a player didn't play a certain number of rounds.


## Clientprefs Usage/Cookies
Player choices (round type preferences, weapon choices) can be saved so they persist across maps for players (via the SourceMod clientprefs API). Installing SQLite should be sufficient for this to work.

If you have a game-hosting specific provider, they may already have SQLite installed


## Custom Round Types

There are two ways to add your own round types: through writing another plugin using the forward and natives in [multi1v1.inc](scripting/include/multi1v1.inc), and
defining a round type in a config file.

### Adding round types via a config file

This is the simpler approach, but you are fairly restricted in the logic you can use. The file to edit is ``addons/sourcemod/configs/multi1v1_customrounds.cfg``.

Here is an example file that adds a scout round and a knife round:
```
"CustomRoundTypes"
{
    "scout"
    {
        "name"      "Scout"
        "ranked"        "1"
        "ratingFieldName"       "scoutRating"
        "optional"      "1"
        "enabled"       "1"
        "armor"     "1"
        "helmet"        "1"
        "weapons"
        {
            "weapon_knife"      ""
            "weapon_ssg08"      ""
        }
    }
    "knife"
    {
        "name"      "Knife"
        "ranked"        "0"
        "optional"      "1"
        "enabled"       "1"
        "armor"     "1"
        "helmet"        "1"
        "weapons"
        {
            "weapon_knife"      ""
        }
    }
}
```

### Adding round types via another plugin

Using the natives in [multi1v1.inc](scripting/include/multi1v1.inc), you can write more complex logic into a round type. To get a simple example, check [multi1v1_kniferounds.sp](scripting/multi1v1_kniferounds.sp). The key is calling ``Multi1v1_AddRoundType`` within the ``Multi1v1_OnRoundTypesAdded`` forward.

```sourcepawn
typedef RoundTypeWeaponHandler = function void (int client);
typedef RoundTypeMenuHandler = function void (int client);

// Registers a new round type by the plugin.
native int Multi1v1_AddRoundType(const char[] displayName,
                                 const char[] internalName,
                                 RoundTypeWeaponHandler weaponsHandler=Multi1v1_NullWeaponHandler,
                                 bool optional=true,
                                 bool ranked=false,
                                 const char[] ratingFieldName="",
                                 bool enabled=true);
```

Note that the multi1v1 plugin will
- create and update the column for the round-type stats if you set the round type as ranked and give a non-empty string as the ``ratingFieldName`` parameter ( note that these columns are only created on database-connections)
- create and update the "allow x rounds" clientprefs cookie for you (it uses the interalName when naming the cookie)


## Contribution and Suggestions
First, check the [issue tracker](https://github.com/splewis/csgo-multi-1v1/issues?state=open) to ask questions or make a suggestion.
If you have a suggestion you can mark it as an enhancement.

Guidelines
- Create a fork on github, clone that, then create a branch to work on ``git checkout -b mybranchname``
- Follow the code-style already used as much as you can
- Submit a pull request when you're happy with the new feature/enhancement/bugfix
- Favor readability and correctness over all else
- For a moderately advanced feature, it may be simpler to write it as a plugin that uses the multi1v1 natives from [multi1v1.inc](scripting/include/multi1v1.inc)
- **Keep it simple, stupid**

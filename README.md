# NFL Play-by-play dbt models

This repo contains [dbt](https://www.getdbt.com) models to transform NFL Play-by-Play (pbp) data sourced from [https://github.com/ryurko/nflscrapR-data.git](https://github.com/ryurko/nflscrapR-data.git) into analytical models.

The repo currently assumes that raw data is loaded to and transformed on a local or remote **PostgreSQL** instance. The load script outlined below and the dbt models could be easily modified to work with other databases supported by dbt such as Snowflake, BigQuery or Redshift. PRs welcome!

## Update Frequency
The `nflscrapR-data` repo is updated with some regularity, but since this is voluntary and free resource, we can't rely on play data being updated weekly. So, this dataset and the analytical models are best used for teaching and model building purposes, and perhaps less so for weekly decision on sports bets etc.

## Models
- `dates`: list of all game dates by season and season type (`PRE`, `REG`, `POST`)
- `games`: game id, dates, teams and final scores by game 
- `players`: player id and name for every player
- `plays`: combines play data from all available seasons (2009 to 2019) into a single table for easier analysis
- `teams`: team code and consolidated code, in case of team moves of renames
- `teams_players`: team rosters by season, showing player and (primary) position for the season

### Notes 
- a few missing `player_id` values in the `players` and `teams_player` models have been (at least attempted to be) fixed
- any duplicate plays (likely a result of the scraping process) are removed from `plays`

## Data Load
The repo assumes that the raw scraped data has been loaded to a **PostgreSQL** database, with one raw file corresponding to a single table in a database called `raw`.

The included Python script [`prep.py`](prep.py) is intended to do the following:
- Clone and/or locally refresh the `nflscrapR-data` repo
- Create empty tables in a local Postgres instance
- Load raw data files to Postgres using a `dbt run-operation` to load each file using Postgres' `copy` command

The `prep.py` file can be easily configured to work with a remote Postgres server, e.g. hosted on AWS RDS. With a little bit of extra work this can also be modified to work with Snowflake, BigQuery or Redshift.

## Future Work
The following items would make great natural extensions and improvements to the repo:
- Update `prep.py` to use connection info from `~/.dbt/profiles.yml`
- Add support for Snowflake, BigQuery and Redshift
- Add report models to more easily enable analytical models:
    - Player stats
    - Game stats
    - Season stats
- Remove dependency on `nflscrapR-data` and include `R` scripts to scrape the data independently
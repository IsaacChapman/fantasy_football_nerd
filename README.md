Fantasy Football Nerd API
==========================

A Ruby Gem for the [Fantasy Football Nerd API](http://www.fantasyfootballnerd.com/api).

[Fantasy Football Nerd](http://www.fantasyfootballnerd.com) "takes the 'wisdom of the crowd' to a new level by aggregating the fantasy football rankings of the best fantasy football sites... and weighting the rankings based upon past accuracy."

This gem currently supports all Level 2 streams. Level 2 access costs $9/season which is a total no-brainer if you're into fantasy sports and programming. Give the man his money.  

Here's what you get: 

* Current Week
* Team List
* Season schedule
* Player list
* Bye weeks
* Injuries
* Auction draft values
* Draft rankings - projected fantasy points for the entire season
* Draft projections - projected stats for each scoring category for the entire season
* Weekly rankings - projected fantasy points (PPR || Standard)
* Weekly projections - projected stats for each scoring category

v1.0
----------------

Fantasy Football Nerd recently overhauled to their API which a) drastically improved their service and b) completely deprecated the old API. The previous version of this gem won't work anymore so update your Gemfiles. 

Cache your data!
----------------
Take heed to the warning on the [Fantasy Football Nerd API page](http://www.fantasyfootballnerd.com):

>The data does not generally change more than once or twice per day, so it becomes unnecessary to continually make the same calls. Please store the results locally and reference the cached responses... Your account may be suspended or API access revoked if you are found to be making excessive data calls.

Seriously, FFN is not a big operation. Don't abuse their servers.

Setup
=================
First, [sign up](http://www.fantasyfootballnerd.com/create-account) for a Fantasy Football Nerd account. Then... 

In plain ol' Ruby, install the gem:

```
gem install fantasy_football_nerd
```

And require it:

````ruby
require 'rubygems'
require 'fantasy_football_nerd'
````

If you're using Rails, add the gem to your Gemfile and run ```bundle install```

````ruby
gem 'fantasy_football_nerd'
````

Before you can access the feeds you must set your API key (found on the [Fantasy Football Nerd API dashboard](http://www.fantasyfootballnerd.com/api)) via one of two ways: 

Set an environment variable via the terminal. 

````term
export FFNERD_API_KEY = 123456789
````

Set a class variable from your script. 

```ruby
FFNerd.api_key = 123456789
```

API Resources
===================

This gem supports all of FFNerd's Level 1 and Level 2 resources. Results are typically returned as an array of [ostructs](http://www.ruby-doc.org/stdlib-2.0/libdoc/ostruct/rdoc/OpenStruct.html) (which is basically a hash that you can access with "dot notation" as if it were an object instance variable). Fantasy Football Nerd returns keys in CamelCase, but that's not very Ruby like, so I've added snake_case attributes so that you can do ```player.displayName``` or ```player.display_name```, whichever suits your fancy. 

Teams
--------------------------
Returns an array of teams. 

```ruby
team = FFNerd.teams.first
team.code        # "ARI"
team.full_name   # "Arizona Cardinals"
team.short_name  # "Arizona"
```

Schedule
--------------------------
Returns an array of games. 

```ruby
game = FFNerd.schedule.first
game.game_id         # "1"
game.game_week       # "1"
game.game_date       # "2013-09-05"
game.away_team       # "BAL"
game.home_team       # "DEN"
```

Players
--------------------------
Returns an array of players. 

```ruby
player = FFNerd.players.first
player.player_id     # "2"
player.active        # "1"
player.jersey        # "3"
player.lname         # "Anderson"
player.fname         # "Derek"
player.display_name  # "Derek Anderson"
player.team          # "CAR"
player.position      # "QB"
player.height        # "6-6"
player.weight        # "240"
player.dob           # "1983-06-15"
player.college       # "Oregon State"
```

Bye Weeks
--------------------------
Returns an array of the teams on bye for the given week. 

```ruby
bye = FFNerd.byes(4).first
bye.team         # "CAR"
bye.bye_week     # "4"
bye.display_name # "Carolina Panthers"
```

Injuries
--------------------------
Returns an array of injured players. Takes an optional parameter for the week number, or defaults to the current week. 

```ruby
injury = FFNerd.injuries(6)     # all injuries for week 6
injury = FFNerd.injuries.first  # the first injury of the current week
injury.week               # "1"
injury.player_id          # "0"
injury.player_name        # "Javier Arenas"
injury.team               # "ARI"
injury.position           # "CB"
injury.injury             # "Hip"
injury.practice_status    # "Full Practice"
injury.game_status        # "Probable"
injury.notes              # ""
injury.last_update        # "2013-09-09"
injury.practice_status_id # 0
```


Auction Values
--------------------------
Returns an array of draft auction values. 

```ruby
value = FFNerd.auction_values.first
value.player_id     # "259"
value.min_price     # "60"
value.max_price     # "66"
value.avg_price     # "63"
value.display_name  # "Adrian Peterson"
value.team          # "MIN"
value.position      # "RB"
```

Current Week
--------------------------
Returns the current week. 

```ruby
FFNerd.current_week  # 17
```

Standard Draft Rankings
--------------------------
Ranks each player according to their projected draft values based on standard scoring. In this example I'm looking at the fourth element in the array, because the PPR and standard rankings don't deviate until then in the test data. 

```ruby   
player = FFNerd.standard_draft_rankings[3]
player.playerId      # "1136"
player.position      # "RB"
player.displayName   # "C.J. Spiller"
player.fname         # "C.J."
player.lname         # "Spiller"
player.team          # "BUF"
player.byeWeek       # "12"
player.nerdRank      # "6.140"
player.positionRank  # "4"
player.overallRank   # "4"
```

PPR Draft Rankings
--------------------------
Ranks each player according to their projected draft values based on PPR scoring. 

```ruby
player = FFNerd.ppr_draft_rankings[3]
player.player_id     # "454"
player.position      # "WR"
player.display_name  # "Calvin Johnson"
player.fname         # "Calvin"
player.lname         # "Johnson"
player.team          # "DET"
player.bye_week      # "9"
player.nerd_rank     # "7.209"
player.position_rank # "1"
player.overall_rank  # "4"
```

Draft Projections
--------------------------
Must pass in a valid position. Projected stats for a number of scoring categories. This data is useless once the season starts. 

```ruby
player = FFNerd.draft_projections('QB').first
player.player_id         # "14"
player.completions       # "422"
player.attempts          # "640"
player.passing_yards     # "4992"
player.passing_td        # "40"
player.passing_int       # "17"
player.rush_yards        # "28"
player.rush_td           # "1"
player.fantasy_points    # "335"
player.display_name      # "Drew Brees"
player.team              # "NO"
```

Weekly Rankings
--------------------------
Returns an array of players with expected fantasy points for both standard and PPR scoring. 

Must requests a specific position: QB, RB, WR, TE, K, DEF. You can also send along the specific week number (1-17). If you omit a week, it defaults to the current week. 

```ruby
player = FFNerd.weekly_rankings('QB', 2).first
player.week             # "2"
player.player_id        # "14"
player.name             # "Drew Brees"
player.position         # "QB"
player.team             # "NO"
player.standard         # "24.80"
player.standard_low     # "18.92"
player.standard_high    # "32.00"
player.ppr              # "24.80"
player.ppr_low          # "18.92"
player.ppr_high         # "32.00"
player.injury           # nil
player.practice_status  # nil
player.game_status      # nil
player.last_update      # nil
```

Weekly Projections
--------------------------

Must requests a specific position: QB, RB, WR, TE, K (But *NOT DEF!*). You can also send along the specific week number (1-17). If you omit a week, it defaults to the current week. 

```ruby
player = FFNerd.weekly_projections('QB', 1).first
player.week             # "1"
player.player_id        # "14"
player.position         # "QB"
player.pass_att         # "39.0"
player.pass_cmp         # "25.0"
player.pass_yds         # "317.0"
player.pass_td          # "2.0"
player.pass_int         # "1.0"
player.rush_att         # "1.0"
player.rush_yds         # "1.0"
player.rush_td          # "0.0"
player.fumbles_lost     # "0.0"
player.receptions       # "0.0"
player.rec_yds          # "0.0"
player.rec_td           # "0.0"
player.fg               # "0.0"
player.fg_att           # "0.0"
player.xp               # "0.0"
player.def_int          # "0.0"
player.def_fr           # "0.0"
player.def_ff           # "0.0"
player.def_sack         # "0.0"
player.def_td           # "0.0"
player.def_ret_td       # "0.0"
player.def_safety       # "0.0"
player.def_pa           # "0.0"
player.def_yds_allowed  # "0.0"
player.display_name     # "Drew Brees"
player.team             # "NO"
```

Tests
===================

Set your API key to "test" to access FFNerd's test feeds. This is useful if you're developing your app prior to the beginning of the season or if you'd like to have a static dataset that won't change as the season progresses. All of the Rspec tests here use the test feeds. Speaking of rspec... 

This gem includes RSpec tests and uses VCR to cache http responses. If you're going to contribute, please write some tests. 

Contributors
-----------------
Greg Baugues ([greg@baugues.com](mailto:greg@baugues.com))<br/>
[www.baugues.com](http://www.baugues.com)



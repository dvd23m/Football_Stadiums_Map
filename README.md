# The map of stadiums of football teams

With this script you can obtain a map with some stadiums of football teams. The data are obtained through the dataset Summer22_FootballTransefers.csv and, once the necessary data have been obtained, a wikidata query is performed.

## Considerations about Wikidata

When we do queries on wikidata, we have to take into account that the limit is 30 queries per minute. Then is a good idea send single request handling multiple entities. For this task the list of teams is divided into 25 parts which, later on, will be send in a query. The code below allows this:

```
# Get unique values
teams = list(set(np.append(df_players.origin_club.values, df_players.new_club.values)))
# Split data
splits = np.array_split(teams, 25)
```


# The map of stadiums of football teams

> With this script you can obtain a map with some stadiums of football teams. The data are obtained through the dataset Summer22_FootballTransefers.csv and, once the necessary data have been obtained, a wikidata query is performed.

## Which teams appear  
> This map only show the teams that compose the 5 major leagues:  
- LaLiga (Spain)  
- Bundesliga (Germany)   
- Serie A (Italy)  
- Ligue 1 (France)  
- Premier League (England)  
And those teams that have had some transaction with ones  

## Considerations about Wikidata

> When we do queries on wikidata, we have to take into account that the limit is 30 queries per minute. Then is a good idea send single request handling multiple entities. For this task the list of teams is divided into 4 parts which, later on, will be send in a query. The code below allows this:

```
# Get unique values
teams = list(set(np.append(df_players.origin_club.values, df_players.new_club.values)))
# Split data
splits = np.array_split(teams, 4)
```

## How query it works

> Query allow obtain the follow data about teams:  
- Name of club  
- Name of stadium  
- Longitude and latitude  
- Capacity (total of people) in the stadium  
- Image from stadium  

For do this task I used the follow query: 

```
for sublists in splits:
    for i in sublists:        
        tokens += f"\"{i}\" "  
    query="""select ?club ?clubLabel ?stadiumLabel ?lon ?lat ?capacity ?image
              {VALUES ?token {%s}
               service wikibase:mwapi{
               bd:serviceParam wikibase:endpoint 'www.wikidata.org'; 
                               wikibase:api 'EntitySearch';       
                               mwapi:search ?token; 
                               mwapi:language 'en';
                               mwapi:limit 1 .
                ?club wikibase:apiOutputItem mwapi:item.
                 }  
                ?club wdt:P31 wd:Q476028 .
                ?club wdt:P115 ?stadium .
                ?stadium wdt:P18 ?image .
                ?stadium wdt:P1083 ?capacity .
                ?stadium wdt:P625 ?coordinate .
                ?stadium p:P625 ?coord . 
                ?coord psv:P625 ?node .
                ?node wikibase:geoLatitude ?lat.
                ?node wikibase:geoLongitude ?lon .
                SERVICE wikibase:label { bd:serviceParam wikibase:language 'en'. }}"""%(tokens)
```







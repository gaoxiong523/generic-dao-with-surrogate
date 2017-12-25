# generic-dao-with-surrogate
The Generic JPA DAO with an extension to support entities with a surrogate index

When we have a surrogate key for an entity, JPA allows the Sequence number generator functionality to take over the creation of the surrogate index. However, this feature is not quite useful if one does not want to depend on database provided sequence since this forces the API to lose DB vendor agnostic nature. 

Now here is the problem we are trying to address:

Assume a sporting league event taking place somewhere in the world. The playersin this league are all well known and an entity (GlobalPayerEntity) containing their data is already made and somehow obtained. The event itself is tracked in another Entity, named the LeagueEventEntity. The event entity looks basically like this in a mysql database:

(EventCode, StartDate, EndDate)

Say, there are two entities named Team and Player. The teams are unique for this event and so are the players (selected from the GlobalPlayer). The event itself gives each player an identity (confined to the context). The EventPlayer looks like this:

([EventCode, EventPlayerId], PlayerIdTakenFromGlobalList),..... 
The entries in the [] indicate the key of the physical table.

On a similar note, the Team entity looks like this;

([EventCode, TeamId], teamlogoURL,.....)

The entries in the [] indicate the key of the physical table.

With the table structure laid as above, the problem now is to create index values specific to each event. That is an event "E1" with 10 teams will have the data as follows:
Data for Team1: E1,1
Data for Team2: E1,2
Data for Team2: E1,3

Likewise for Event E2
Data for Team1: E1,1
Data for Team2: E1,2
Data for Team2: E1,3

Note that the index is reset to zero after E1.

JPA's Generator strategies do not allow this kind of an arrangement. Application logic based (surrogate) index Generation strategies in JPA cannot be invoked just before an object is saved. What we need is one such hook, which allows us to mark an Entity as using a SurrogateKey and an before persistence, the API looks to call the index generator class ormethod and then takes the Object to the DB.

I am modifying the existing GenericDAO API to do this because it can in general be invoked by entities of all types. The GenericDAO takes care of all objects without discrimination.There may be other strategies. But if you find any that solvethe specific problem, please comment 

What is required therefore is an APi that extracts the ids of 

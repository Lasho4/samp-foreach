# SA-MP-foreach

foreach standalone include (non y_iterate version).

Y_Less dropped support for this version of foreach.

This version of foreach is 19 (0.4.4). This is the standalone version, so it does not require YSI. Use this version if you do not want to include YSI.

## Installation with sampctl

```bash
sampctl package install karimcambridge/SAMP-foreach
```

Then simply include into code:

```pawn
#include <foreach>
```

## Option with more iterators

https://github.com/Open-GTO/foreach

## How to use
#### Gives $100 to all connected players
```Pawn
foreach (new playerid : Player) {
	GivePlayerMoney(playerid, 100);
}
```

#### Sets 130 and 170 colors to all vehicles with model 400
```Pawn
foreach (new vehicleid : Vehicle) {
	if (GetVehicleModel(vehicleid) == 400) {
		ChangeVehicleColor(vehicleid, 130, 170);
	}
}
```

#### Remove all existed vehicles and actors
For removing `vehicles` or `actors` in foreach loop you should use `Safe` functions:
```Pawn
foreach (new vehicleid : Vehicle) {
	DestroyVehicleSafe(vehicleid);
}

foreach (new actorid : Actor) {
	DestroyActorSafe(actorid);
}
```

#### Custom iterators
Adds two iterators with players in team 1 and 2.
```Pawn
new
	Iterator:Team1<MAX_PLAYERS>,
	Iterator:Team2<MAX_PLAYERS>;

stock Team_SetPlayerTeam(playerid, teamid)
{
	switch (GetPlayerTeam(playerid)) {
		case 1: {
			Iter_Remove(Team1, playerid);
		}
		case 2: {
			Iter_Remove(Team2, playerid);
		}
	}

	switch (teamid) {
		case 1: {
			Iter_Add(Team1, playerid);
		}
		case 2: {
			Iter_Add(Team2, playerid);
		}
	}

	return SetPlayerTeam(playerid, teamid);
}
#if defined _ALS_SetPlayerTeam
	#undef SetPlayerTeam
#else
	#define _ALS_SetPlayerTeam
#endif

#define SetPlayerTeam Team_SetPlayerTeam
```
It can be used like this:
```Pawn
foreach (new i : Team1) {
	GivePlayerMoney(i, 1000);
}

foreach (new i : Team2) {
	SetPlayerHealth(i, 0.0);
}
```
This script gives $1000 to all players from team with id 1, and kills players from team with id 2.

## Custom array of iterators
Adds array of iterators with players in teams:

```Pawn
#define MAX_TEAMS 255

new
	Iterator:Team[MAX_TEAMS]<MAX_PLAYERS>;

public OnGameModeInit()
{
	Iter_Init(Team);

	#if defined Team_OnGameModeInit
		return Team_OnGameModeInit();
	#else
		return 1;
	#endif
}
#if defined _ALS_OnGameModeInit
	#undef OnGameModeInit
#else
	#define _ALS_OnGameModeInit
#endif

#define OnGameModeInit Team_OnGameModeInit
#if defined Team_OnGameModeInit
	forward Team_OnGameModeInit();
#endif



stock Team_SetPlayerTeam(playerid, teamid)
{
	new current_team = GetPlayerTeam(playerid);
	if (current_team != NO_TEAM) {
		Iter_Remove(Team[current_team], playerid);
	}

	if (teamid != NO_TEAM) {
		Iter_Add(Team[teamid], playerid);
	}

	return SetPlayerTeam(playerid, teamid);
}
#if defined _ALS_SetPlayerTeam
	#undef SetPlayerTeam
#else
	#define _ALS_SetPlayerTeam
#endif

#define SetPlayerTeam Team_SetPlayerTeam
```
It can be used like this:
```Pawn
new scores[MAX_TEAMS];
for (new teamid; teamid < MAX_TEAMS; teamid++) {
	foreach (new playerid : Team[teamid]) {
		scores[teamid] += GetPlayerScore(playerid);
	}
}

for (new teamid; teamid < MAX_TEAMS; teamid++) {
	printf("Team %d: %d", teamid, scores[teamid]);
}
```
This script sums up score from each team and prints it.
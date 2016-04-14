---
title: Airport format
---
[back to index](index.html)

# Airport format

The airport JSON file must be in `assets/airports`; the filename
should be `icao.json` where `icao` is the lowercase four-letter ICAO
airport code, such as `ksfo` or `kmsp`.

```
{
  "name": "Human-readable name of the airport",
  "level": "beginner, easy, medium, hard or expert",
  "radio": {
    "twr": "controller callsign for tower",
    "app": "controller callsign for approach control",
    "dep": "controller callsign for departure control"
  },
  "icao": "KSFO",             // uppercase ICAO airport code
  "iata": "SFO",              // uppercase IATA airport code
  "magnetic_north": 13.7,     // magnetic declination, in degrees EAST!
  "ctr_radius": 80,           // radius from 'position' that the airspace extends
  "ctr_ceiling": 10000,       // elevation up to which the airspace extends
  "initial_alt": 5000         // alt departures climb to if given "as-filed" clearance, but no "climb-via-sid" or altitude assignment
  "position", ["lat", "lon"]  // the latitude/longitude of the "center" of the airport; see comments below
  "rr_radius_nm": 5,          // radius of range rings, nautical miles
  "rr_center": ["lat", "lon"],// position where range rings are centered, nautical miles
  "has_terrain": true,        // true/false for if has an associated GeoJSON terrain file in assets/airports/terrain
  "wind": {     // wind is used for score, and can affect aircraft's ground tracks if enabled in settings
    "angle": 0, // the heading, in degrees, that the wind is coming from
    "speed": 3  // the speed, in knots, of the wind
  },
  "fixes": {
    "FIXNAME", ["lat", "lon"] // the position, in GPS coordinates, of the fix
  },
  "runways": [
    {
      "name":        ["36", "18"],     // the name of each end of the runway
      "name_offset": [[0, 0], [0, 0]], // the offset, in km, of the runway text when drawn on the map
      "end":         [                 // the ends of the runway
                       ["lat", "lon"],
                       ["lat", "lon"]
                     ],
      "delay":       [2, 2],           // the number of seconds it takes to taxi to the end of the runway
      "sepFromAdjacent": [2.5, 2.5],   // Distance in nautical miles that another aircraft can come while on parallel approach paths, a violation will occur at 85% of this value
      "ils":         [true, false]     // not used yet; indicates whether or not that end of the runway has ILS
    }
  ],
  "sids": {   // contains all SIDs available at this airport
    "icao": "OFFSH9",           // (req) ICAO identifier for SID (this is NOT the full name, always 2-6 characters)
    "name": "Offshore Nine" ,   // (req) Name of SID as it would be said aloud (it is used by speech synthesis to pronounce "OFFSH9")
    "suffix": {"1L":"", "1R":"", "28L":"", "28R":""},   // (optional) defines suffixes to SID name based on runway (eg '2C' for 'EKERN 2C').
                                                        // Common for European-style SIDs. If not needed (like in USA), leave this part out.
    "rwy": {  // (req) ALL runways usable on this SID must be listed below. If a runway isn't listed, aircraft departing
              // that runway will need to be re-assigned a different SID or runway (this is realistic and intended).
        "1L" : [["SEPDY", "A19+"], "ZUPAX"],  // Each runway for which this SID is valid must be listed here. The value assigned to each runway is an array 
        "1R" : [["SEPDY", "A19+"], "ZUPAX"],  // of fixes, entered as strings. As shown, you may also enter an array containing the fix name and restrictions
        "28L": [["SENZY", "A25+"], "ZUPAX"],  // at that fix, separated by a pipe symbol ('|'). For example, see the following: ["FIXNAME", "A50-|S220+"]. In 
        "28R": [["SENZY", "A25+"], "ZUPAX"]   // that example, restrictions of Altitude 5,000' or lower, and Speed 220kts or higher would be placed on that fix.
      },
      "body": ["EUGEN", "SHOEY"],   // (optional) If there is a very long series of fixes in a SID, it may be 
                                    // helpful to put some of it here, while all segments follow the same path.
      "transitions": {    // (optional) Defines transitions for a given SID. Common for FAA-style (USA) SIDs. If not needed (like in Europe), leave this part out.
          "SNS": ["SNS"], // defines the "OFFSH9.SNS" transition as being a single fix, "SNS". Is often a list instead.
          "BSR": ["BSR"], // Note that this connects to the end of previous sections, so an example route: SEPDY->ZUPAX->EUGEN->SHOEY->BSR
          "SHOEY": []     // Even empty transitions are allowable
      },
      "draw": [["SEPDY","ZUPAX"], ["SENZY","ZUPAX","EUGEN","SHOEY*"], ["SHOEY","SNS*"], ["SHOEY","BSR*"]]
        // (req) This "draw" section is what defines how the SID is to be drawn on the scope in blue.
        // The array contains multiple arrays that are a series of points to draw fixes between.
        // In this case, SEPDY->ZUPAX, SENZY->ZUPAX->EUGEN->SHOEY, SHOEY->SNS, SHOEY->BSR are the lines drawn.
        // Additionally, you'll notice three asterisks ('*'). This is an optional flag that, if invoked for "FIXXX"
        // will tell canvas.js to write "OFFSH9.FIXXX" next to FIXXX on the scope. If no such flags are present,
        // then the ICAO identifier for the SID will be drawn at the last point of the "draw" array. For european-
        // style SIDs, where they always end at the fix for which the SID is named, don't use the flags. But if your SID
        // has transitions, like in the N/S Americas, United Kingdom, etc, be sure to flag all the transition fixes.
  }
  }
  "departures": {
    "airlines": [
      ["three-letter ICAO airline code/fleet", 0], // the number is the weight; if the weight is ["BAW", 1], ["UAL", 0], "BAW" will always get chosen.
      ...
    ],
    "destinations": [
      "LISST", "OF", "SIDS", "ACRFT", "WILLL", "FLYYY", "TO"  // these must each be a defined SID above
    ],
    "type": ,
    "offset": ,
    "frequency": [3, 4] // the frequency, in minutes, of a new departing aircraft. A random number is chosen between the two.
  },
  "arrivals": [ // note that all arrival positions are fuzzed a little to increase the realism
    { // this is a single arrival direction
      "radial": 180,            // the direction, in degrees, of arriving aircraft when they spawn; these will come from the south
      "heading": 0,             // the direction airplanes will be pointing when they spawn; will be opposite of "radial" if omitted
      "airlines": [ ... ],      // see above
      "frequency": [3, 6],      // see above
      "altitude": [3000, 4500]  // the altitude aircraft spawn at
    },
    ...
  ]
}
```

For `lat, lon` fields, you can either use the standard `[x, y]`
notation or the new `["LAT", "LON"]` notation. The latter uses this
format:

    ["N or S followed by a latitude", "W or E followed by a longitude", "optional altitude ending in 'ft' or 'm'"]
where latitude and longitude are numbers that follow this format:
    <degrees>[d|°][<minutes>m[<seconds>s]]

Examples of acceptable positions:
  [  40.94684722   ,  -76.61727778   ], // decimal degrees
  [ "N40.94684722" , "W76.61727778"  ], // decimal degrees
  [  "N40d56.811"  ,  "W076d37.037   ], // degrees, decimal minutes
  [ "N40d56m48.65" , "W076d37m02.20" ]  // degrees, minutes, decimal seconds

If you use a `["lat", "lon"]` combination for any `position`, you
_must_ set the `position` of the airport as well; if you use `end`,
`length` and `angle` are not needed (but can be used to override
`end`).

Aircraft scheduling
-------------------

If a 'type' key is present in an arrival or departure block the
algorithm may be changed from the default.

### Default

This algorithm may be selected by omitting the 'type' key.  The
'frequency' key is required and is specified as an array containing a
lower bound in minutes and an upper bound in minutes.  An aircraft is
generated by randomly selecting a delay between the lower bound and
the upper bound and waiting until the delay has past.  Repeat for the
next aircraft.

The average of the upper and lower bounds will be how often an
aircraft is generated on average.

Example:
```
"departures": {
  ...,
  "frequency": [1, 5]
},
```

This example generates an aircraft every 3 minutes on average with a
delay of 1 minute to 5 minutes between every aircraft.  The resulting
number of aircraft per hour is 60/3 = 20 Aircraft per hour.

### Random

The random algorithm requires a 'frequency' key which is specified as
a lower and upper bound in aircraft per hour.  A delay is randomly
chosen between generating each aircraft which will result in at least
the lower bound of aircraft per hour and at most the upper bound of
aircraft per hour.

This will usually result in a steady stream of aircraft with some
variation between individual aircraft.

Example:
```
"departures": {
  ...,
  "type": "random",
  "frequency": [20, 30]
}
```

This spawns 20 to 30 aircraft per hour which is an aircraft every 2-3 minutes.

### Cyclic

Whereas the random algorithms result in a relatively steady stream of
aircraft over time the cyclic algorithm creates a stream of varying
density.  The 'frequency' key is required and specified in aircraft
per hour as an upper and lower bound.  The 'period' key may be
specified and sets how long a cycle is in minutes, if it is not set a
cycle is 60 minutes long.  The 'offset' key may be used to specify how
long after the game starts the cycle will peak, it's value is in
minutes and defaults to peaking at the game start or 0 minutes.

Example:
```
"departures": {
  ...,
  "type": "cyclic",
  "frequency": [10, 60],
  "period": 90,
  "offset": 45
},
```

This will spawn an average of 35 aircraft per hour. At the start of
the game it will spawn one aircraft every 6 minutes (10 aircraft per
hour), this delay will steadily decrease until 45 minutes of play when
it will be spawning one aircraft every minute (60 aircraft per hour).

### Wave

The wave algorithm generates aircraft as one group with minimal
spacing between them.  For departures the spacing is 10 seconds, for
arrivals it is 7.5 nmi in trail (general en-route ATC spacing).  The
'frequency' key is required and is specified in aircraft per hour.
For arrivals the 'speed' key is required and specified in knots, it
will be the speed the aircraft are initially flying.  An optional
'period' key may be specified in minutes to set how often a wave
comes, if not set it defaults to 60 minutes.  An optional 'offset' key
may be specified in minutes to set the time when the center of the
wave will happen after the game starts, if not set it defaults to 0.

If the frequency requests more aircraft than can be spawned with the
given spacing the frequency will be reduced to create a continuous
stream of aircraft with the given spacing.

Example:
```
"departures": {
  ...,
  "type": "wave",
  "frequency": 40,
  "period": 30,
  "offset": 15
},
```

This creates 20 aircraft every 30 minutes (40 per hour), the first
aircraft in the group will spawn at 14:20 with another aircraft every
10 seconds after the preceding one.

Example:
```
"arrivals": [
  {
    ...,
    "type": "wave",
    "frequency": 25,
    "speed": 250
  }
],
```

This spawns 25 aircraft in a group every hour.  It starts with a group
of 13 aircraft at the beginning of the game.  An aircraft is spawned
every 108 seconds.

Aircraft/Airline selectors
--------------------------

Both departure and arrival blocks specify a weighted list of airlines
to use for that block.  The airline code may be optionally followed by
a particular fleet for that operator.

Example:
```
"airlines": [
  ["BAW", 10],
  ["AAL/long", 2]
]

Select an aircraft from BAW's (British Airways) default fleet five
times as often as an aircraft is selected from AAL's (American
Airlines) long haul fleet.

Arrival blocks
--------------


### Waypoints

An arrival may specify a set of waypoints which the aircraft should
follow after spawning including speed and altitude changes.  This
allows spawning at high altitude and descending to the terminal area.

Example:
    ...,
    "waypoints": [
      {
        "fix": "SKUNK",
        "speed": 250,
        "altitude": 10000
      },
      {
        "fix": "BOLDR",
        "speed": 230,
        "altitude": 8000
      }
    ],
    ...

#### Fixes

A simplified set of waypoints may be created by specifing a list of
fixes only.  If specified it will overide any waypoints configured.

Example:
    ...,
    "fixes": ["SKUNK", "BOLDR"],
    ...

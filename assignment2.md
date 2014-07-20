================================================================================
# An Analysis of some Weather Data to Determine Economic and Human Costs

For coursera course Reproducible Data with Roger Peng et. al., July 2014

================================================================================

## Synopsis

This is a short analysis of NOAA weather storm database, specifically the 
National Climactic Data Center Storm Events, at 

TO DO - how do I put a URL in a .Rmd file?

================================================================================

## Data Processing

TO DO: unzip the data successfully (so far no luck). But on my system I can 
read the file.


```r
sessionInfo()
```

```
## R version 3.1.1 (2014-07-10)
## Platform: x86_64-w64-mingw32/x64 (64-bit)
## 
## locale:
## [1] LC_COLLATE=English_United States.1252 
## [2] LC_CTYPE=English_United States.1252   
## [3] LC_MONETARY=English_United States.1252
## [4] LC_NUMERIC=C                          
## [5] LC_TIME=English_United States.1252    
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] knitr_1.6
## 
## loaded via a namespace (and not attached):
## [1] evaluate_0.5.5 formatR_0.10   stringr_0.6.2  tools_3.1.1
```


```r
# on my system, I had to change https to http in the URL from te web site.
# URL <- "http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"
# download.file(URL,"test.bz2")

# Since we have downloaded the file and I've used cygwin cmp to verify that it's
# the same, we just use the file downloaded from the web site.
# for posterity's sake, we use the file that's checked in to this github repository.

# We use the cache=TRUE Rmd option, since this is the slowest part

main_data <- read.table("repdata-data-StormData.csv.bz2",
                         sep=",",
                         header=TRUE,
                         strip.white=TRUE)
```

================================================================================


## A Further Note on Data Processing - PROBLEMS WITH THE DATA!

The strip.white=TRUE begins the process of cleaning the EVTYPE variable.
According to the documentation INSERT URL HERE, there are 48 allowed 
event types. And yet casual inspection of the data set shows a lot! What is a lot?
I mean, like a thousand, which I will demonstrate here. and recall that we have
already stripped white space in the read.table above, because visual inspection
showed EVTYPES such as "WIND", " WIND", "   WIND     ", etc.

Since we got rid of white space, let's get rid of all the upper and lower case
combinatorics, which eliminates about 100 of the 1000, or 10%:


```r
main_data$EVTYPE <- toupper(main_data$EVTYPE)
# when we use toupper, it converts it to char from factor so we put it back
# the way it was
main_data$EVTYPE <- as.factor(main_data$EVTYPE)

event_types  <- with(main_data,table(EVTYPE))

# I cannot make heads nor tails of how to print this neatly, but if I do

event_types <- as.data.frame(event_types)


# then printing y to the terminal ( i.e. "> y <CR> " ) gives me
# a nice columnar output. You can really see the repetition. SO -
event_types
```

```
##                             EVTYPE   Freq
## 1               HIGH SURF ADVISORY      1
## 2                    COASTAL FLOOD      1
## 3                      FLASH FLOOD      1
## 4                        LIGHTNING      1
## 5                        TSTM WIND      4
## 6                  TSTM WIND (G45)      1
## 7                       WATERSPOUT      1
## 8                             WIND      1
## 9                                ?      1
## 10                 ABNORMAL WARMTH      4
## 11                  ABNORMALLY DRY      2
## 12                  ABNORMALLY WET      1
## 13            ACCUMULATED SNOWFALL      4
## 14             AGRICULTURAL FREEZE      6
## 15                   APACHE COUNTY      1
## 16          ASTRONOMICAL HIGH TIDE    103
## 17           ASTRONOMICAL LOW TIDE    174
## 18                        AVALANCE      1
## 19                       AVALANCHE    386
## 20                    BEACH EROSIN      1
## 21                   BEACH EROSION      4
## 22     BEACH EROSION/COASTAL FLOOD      1
## 23                     BEACH FLOOD      2
## 24      BELOW NORMAL PRECIPITATION      2
## 25               BITTER WIND CHILL      1
## 26  BITTER WIND CHILL TEMPERATURES      3
## 27                       BLACK ICE     17
## 28                        BLIZZARD   2719
## 29  BLIZZARD AND EXTREME WIND CHIL      2
## 30         BLIZZARD AND HEAVY SNOW      1
## 31                BLIZZARD SUMMARY      1
## 32                BLIZZARD WEATHER      1
## 33          BLIZZARD/FREEZING RAIN      1
## 34             BLIZZARD/HEAVY SNOW      2
## 35              BLIZZARD/HIGH WIND      1
## 36           BLIZZARD/WINTER STORM      1
## 37                   BLOW-OUT TIDE      1
## 38                  BLOW-OUT TIDES      1
## 39                    BLOWING DUST      4
## 40                    BLOWING SNOW     17
## 41  BLOWING SNOW- EXTREME WIND CHI      1
## 42  BLOWING SNOW & EXTREME WIND CH      2
## 43  BLOWING SNOW/EXTREME WIND CHIL      1
## 44                BREAKUP FLOODING      1
## 45                      BRUSH FIRE      3
## 46                     BRUSH FIRES      1
## 47       COASTAL  FLOODING/EROSION      1
## 48                 COASTAL EROSION      1
## 49                   COASTAL FLOOD    656
## 50                COASTAL FLOODING    183
## 51        COASTAL FLOODING/EROSION      5
## 52                   COASTAL STORM     10
## 53                   COASTAL SURGE      2
## 54             COASTAL/TIDAL FLOOD      2
## 55                    COASTALFLOOD      1
## 56                    COASTALSTORM      1
## 57                            COLD     82
## 58                 COLD AIR FUNNEL      4
## 59                COLD AIR FUNNELS      2
## 60                COLD AIR TORNADO      1
## 61                  COLD AND FROST      7
## 62                   COLD AND SNOW      1
## 63         COLD AND WET CONDITIONS      1
## 64                COLD TEMPERATURE      2
## 65               COLD TEMPERATURES      4
## 66                       COLD WAVE      3
## 67                    COLD WEATHER      4
## 68    COLD WIND CHILL TEMPERATURES      6
## 69                 COLD/WIND CHILL    539
## 70                      COLD/WINDS      1
## 71                    COOL AND WET      1
## 72                      COOL SPELL      1
## 73           CSTL FLOODING/EROSION      2
## 74                       DAM BREAK      4
## 75                     DAM FAILURE      1
## 76                 DAMAGING FREEZE      8
## 77                       DEEP HAIL      1
## 78                       DENSE FOG   1293
## 79                     DENSE SMOKE     10
## 80                       DOWNBURST      2
## 81                 DOWNBURST WINDS      2
## 82                    DRIEST MONTH      1
## 83                   DRIFTING SNOW      1
## 84                         DROUGHT   2488
## 85          DROUGHT/EXCESSIVE HEAT     13
## 86                        DROWNING      1
## 87                             DRY      9
## 88                  DRY CONDITIONS      6
## 89                 DRY HOT WEATHER      1
## 90                  DRY MICROBURST    186
## 91               DRY MICROBURST 50      1
## 92               DRY MICROBURST 53      1
## 93               DRY MICROBURST 58      2
## 94               DRY MICROBURST 61      1
## 95               DRY MICROBURST 84      1
## 96            DRY MICROBURST WINDS      5
## 97            DRY MIRCOBURST WINDS      1
## 98                     DRY PATTERN      1
## 99                       DRY SPELL      4
## 100                    DRY WEATHER      4
## 101                        DRYNESS      1
## 102                     DUST DEVEL      1
## 103                     DUST DEVIL    149
## 104          DUST DEVIL WATERSPOUT      1
## 105                     DUST STORM    427
## 106          DUST STORM/HIGH WINDS      1
## 107                      DUSTSTORM      1
## 108                   EARLY FREEZE      1
## 109                    EARLY FROST      2
## 110                     EARLY RAIN      1
## 111                     EARLY SNOW      3
## 112                 EARLY SNOWFALL      7
## 113             EROSION/CSTL FLOOD      2
## 114                      EXCESSIVE      1
## 115                 EXCESSIVE COLD      2
## 116                 EXCESSIVE HEAT   1678
## 117         EXCESSIVE HEAT/DROUGHT      1
## 118        EXCESSIVE PRECIPITATION      1
## 119                 EXCESSIVE RAIN      5
## 120             EXCESSIVE RAINFALL      4
## 121                 EXCESSIVE SNOW     25
## 122              EXCESSIVE WETNESS      1
## 123                EXCESSIVELY DRY      1
## 124                  EXTENDED COLD      1
## 125                   EXTREME COLD    657
## 126        EXTREME COLD/WIND CHILL   1002
## 127                   EXTREME HEAT     22
## 128             EXTREME WIND CHILL      6
## 129 EXTREME WIND CHILL/BLOWING SNO      1
## 130            EXTREME WIND CHILLS      1
## 131              EXTREME WINDCHILL    204
## 132 EXTREME WINDCHILL TEMPERATURES     19
## 133            EXTREME/RECORD COLD      4
## 134                  EXTREMELY WET      1
## 135               FALLING SNOW/ICE      2
## 136                    FIRST FROST      1
## 137                     FIRST SNOW      9
## 138                    FLASH FLOOD  54277
## 139       FLASH FLOOD - HEAVY RAIN      2
## 140      FLASH FLOOD FROM ICE JAMS      5
## 141         FLASH FLOOD LANDSLIDES      1
## 142              FLASH FLOOD WINDS      1
## 143                   FLASH FLOOD/      1
## 144             FLASH FLOOD/ FLOOD      2
## 145            FLASH FLOOD/ STREET      1
## 146              FLASH FLOOD/FLOOD     22
## 147         FLASH FLOOD/HEAVY RAIN      1
## 148          FLASH FLOOD/LANDSLIDE      1
## 149                 FLASH FLOODING    682
## 150           FLASH FLOODING/FLOOD      8
## 151 FLASH FLOODING/THUNDERSTORM WI      1
## 152                   FLASH FLOODS     32
## 153                FLASH FLOOODING      1
## 154                          FLOOD  25327
## 155             FLOOD & HEAVY RAIN      2
## 156                    FLOOD FLASH      3
## 157              FLOOD FLOOD/FLASH      1
## 158                   FLOOD WATCH/      1
## 159                    FLOOD/FLASH      2
## 160              FLOOD/FLASH FLOOD    625
## 161           FLOOD/FLASH FLOODING      2
## 162              FLOOD/FLASH/FLOOD      1
## 163               FLOOD/FLASHFLOOD      1
## 164                FLOOD/RAIN/WIND      1
## 165               FLOOD/RAIN/WINDS      6
## 166              FLOOD/RIVER FLOOD      1
## 167              FLOOD/STRONG WIND      1
## 168                       FLOODING    120
## 169            FLOODING/HEAVY RAIN      1
## 170                         FLOODS      3
## 171                            FOG    538
## 172      FOG AND COLD TEMPERATURES      1
## 173                   FOREST FIRES      1
## 174                         FREEZE     76
## 175               FREEZING DRIZZLE     24
## 176  FREEZING DRIZZLE AND FREEZING      1
## 177                   FREEZING FOG     46
## 178                  FREEZING RAIN    260
## 179        FREEZING RAIN AND SLEET      6
## 180         FREEZING RAIN AND SNOW      1
## 181        FREEZING RAIN SLEET AND      1
## 182  FREEZING RAIN SLEET AND LIGHT      1
## 183            FREEZING RAIN/SLEET      9
## 184             FREEZING RAIN/SNOW      4
## 185                 FREEZING SPRAY      1
## 186                          FROST     57
## 187                   FROST/FREEZE   1343
## 188                  FROST\\FREEZE      1
## 189                         FUNNEL     46
## 190                   FUNNEL CLOUD   6844
## 191                  FUNNEL CLOUD.      1
## 192              FUNNEL CLOUD/HAIL      1
## 193                  FUNNEL CLOUDS     87
## 194                        FUNNELS      1
## 195                          GLAZE     43
## 196                      GLAZE ICE      2
## 197                GLAZE/ICE STORM      1
## 198                  GRADIENT WIND      9
## 199                 GRADIENT WINDS      8
## 200                    GRASS FIRES      1
## 201                GROUND BLIZZARD      2
## 202                       GUSTNADO      6
## 203                   GUSTNADO AND      1
## 204                GUSTY LAKE WIND      1
## 205        GUSTY THUNDERSTORM WIND      3
## 206       GUSTY THUNDERSTORM WINDS      5
## 207                     GUSTY WIND     24
## 208                GUSTY WIND/HAIL      1
## 209            GUSTY WIND/HVY RAIN      1
## 210                GUSTY WIND/RAIN      1
## 211                    GUSTY WINDS     65
## 212                           HAIL 288661
## 213                      HAIL 0.75     18
## 214                      HAIL 0.88      1
## 215                       HAIL 075      1
## 216                       HAIL 088      1
## 217                      HAIL 1.00      6
## 218                      HAIL 1.75      4
## 219                     HAIL 1.75)      1
## 220                       HAIL 100     13
## 221                       HAIL 125      1
## 222                       HAIL 150      2
## 223                       HAIL 175     13
## 224                       HAIL 200      1
## 225                       HAIL 225      1
## 226                       HAIL 275      3
## 227                       HAIL 450      1
## 228                        HAIL 75     29
## 229                        HAIL 80      2
## 230                        HAIL 88      1
## 231                     HAIL ALOFT      1
## 232                    HAIL DAMAGE      2
## 233                  HAIL FLOODING      1
## 234                     HAIL STORM      1
## 235                     HAIL(0.75)      1
## 236                 HAIL/ICY ROADS      1
## 237                      HAIL/WIND      3
## 238                     HAIL/WINDS      2
## 239                      HAILSTORM      3
## 240                     HAILSTORMS      1
## 241                    HARD FREEZE      7
## 242                 HAZARDOUS SURF      1
## 243                           HEAT    767
## 244                   HEAT DROUGHT      1
## 245                      HEAT WAVE     75
## 246              HEAT WAVE DROUGHT      1
## 247                     HEAT WAVES      2
## 248                   HEAT/DROUGHT      1
## 249                      HEATBURST      1
## 250                HEAVY LAKE SNOW     25
## 251                      HEAVY MIX      8
## 252            HEAVY PRECIPATATION      1
## 253            HEAVY PRECIPITATION      3
## 254                     HEAVY RAIN  11742
## 255           HEAVY RAIN AND FLOOD      1
## 256            HEAVY RAIN AND WIND      4
## 257             HEAVY RAIN EFFECTS      1
## 258            HEAVY RAIN/FLOODING      2
## 259           HEAVY RAIN/HIGH SURF      1
## 260           HEAVY RAIN/LIGHTNING      1
## 261     HEAVY RAIN/MUDSLIDES/FLOOD      1
## 262      HEAVY RAIN/SEVERE WEATHER      2
## 263  HEAVY RAIN/SMALL STREAM URBAN      1
## 264                HEAVY RAIN/SNOW      1
## 265         HEAVY RAIN/URBAN FLOOD      1
## 266                HEAVY RAIN/WIND      4
## 267 HEAVY RAIN; URBAN FLOOD WINDS;      1
## 268                 HEAVY RAINFALL      3
## 269                    HEAVY RAINS     26
## 270           HEAVY RAINS/FLOODING      9
## 271                     HEAVY SEAS      2
## 272                   HEAVY SHOWER      2
## 273                  HEAVY SHOWERS      1
## 274                     HEAVY SNOW  15708
## 275             HEAVY SNOW-SQUALLS     15
## 276     HEAVY SNOW   FREEZING RAIN      1
## 277               HEAVY SNOW & ICE      1
## 278                 HEAVY SNOW AND      1
## 279      HEAVY SNOW AND HIGH WINDS      2
## 280             HEAVY SNOW AND ICE      2
## 281       HEAVY SNOW AND ICE STORM      2
## 282    HEAVY SNOW AND STRONG WINDS      1
## 283     HEAVY SNOW ANDBLOWING SNOW      1
## 284              HEAVY SNOW SHOWER      1
## 285             HEAVY SNOW SQUALLS     32
## 286            HEAVY SNOW/BLIZZARD      3
## 287  HEAVY SNOW/BLIZZARD/AVALANCHE      1
## 288        HEAVY SNOW/BLOWING SNOW      1
## 289       HEAVY SNOW/FREEZING RAIN      2
## 290                HEAVY SNOW/HIGH      1
## 291           HEAVY SNOW/HIGH WIND      1
## 292          HEAVY SNOW/HIGH WINDS      1
## 293  HEAVY SNOW/HIGH WINDS & FLOOD      1
## 294 HEAVY SNOW/HIGH WINDS/FREEZING      1
## 295                 HEAVY SNOW/ICE      5
## 296           HEAVY SNOW/ICE STORM      2
## 297               HEAVY SNOW/SLEET      1
## 298             HEAVY SNOW/SQUALLS      2
## 299                HEAVY SNOW/WIND      1
## 300        HEAVY SNOW/WINTER STORM      1
## 301                 HEAVY SNOWPACK      1
## 302                     HEAVY SURF     87
## 303            HEAVY SURF AND WIND      1
## 304    HEAVY SURF COASTAL FLOODING      1
## 305           HEAVY SURF/HIGH SURF    228
## 306                   HEAVY SWELLS      1
## 307                 HEAVY WET SNOW      1
## 308                           HIGH      1
## 309                   HIGH  SWELLS      1
## 310                    HIGH  WINDS      1
## 311                      HIGH SEAS      8
## 312                      HIGH SURF    734
## 313           HIGH SURF ADVISORIES      1
## 314             HIGH SURF ADVISORY      4
## 315                    HIGH SWELLS      5
## 316        HIGH TEMPERATURE RECORD      3
## 317                     HIGH TIDES      2
## 318                     HIGH WATER      6
## 319                     HIGH WAVES      3
## 320                      HIGH WIND  20214
## 321                HIGH WIND (G40)      2
## 322                   HIGH WIND 48      1
## 323                   HIGH WIND 63      1
## 324                   HIGH WIND 70      1
## 325       HIGH WIND AND HEAVY SNOW      1
## 326       HIGH WIND AND HIGH TIDES      2
## 327             HIGH WIND AND SEAS      1
## 328               HIGH WIND DAMAGE      2
## 329            HIGH WIND/ BLIZZARD      1
## 330             HIGH WIND/BLIZZARD      6
## 331 HIGH WIND/BLIZZARD/FREEZING RA      1
## 332           HIGH WIND/HEAVY SNOW      3
## 333       HIGH WIND/LOW WIND CHILL      1
## 334                 HIGH WIND/SEAS      1
## 335           HIGH WIND/WIND CHILL      1
## 336  HIGH WIND/WIND CHILL/BLIZZARD      1
## 337                     HIGH WINDS   1533
## 338                  HIGH WINDS 55      1
## 339                  HIGH WINDS 57      1
## 340                  HIGH WINDS 58      1
## 341                  HIGH WINDS 63      2
## 342                  HIGH WINDS 66      2
## 343                  HIGH WINDS 67      1
## 344                  HIGH WINDS 73      1
## 345                  HIGH WINDS 76      1
## 346                  HIGH WINDS 80      2
## 347                  HIGH WINDS 82      1
## 348      HIGH WINDS AND WIND CHILL      1
## 349          HIGH WINDS DUST STORM      1
## 350         HIGH WINDS HEAVY RAINS      1
## 351                    HIGH WINDS/      1
## 352       HIGH WINDS/COASTAL FLOOD      1
## 353                HIGH WINDS/COLD      5
## 354            HIGH WINDS/FLOODING      1
## 355          HIGH WINDS/HEAVY RAIN      1
## 356                HIGH WINDS/SNOW      3
## 357               HIGHWAY FLOODING      1
## 358                    HOT AND DRY      2
## 359                    HOT PATTERN      1
## 360                      HOT SPELL      2
## 361                    HOT WEATHER      1
## 362                HOT/DRY PATTERN      1
## 363                      HURRICANE    174
## 364     HURRICANE-GENERATED SWELLS      3
## 365              HURRICANE EDOUARD      2
## 366                HURRICANE EMILY      1
## 367                 HURRICANE ERIN      7
## 368                HURRICANE FELIX      2
## 369               HURRICANE GORDON      1
## 370                 HURRICANE OPAL      9
## 371      HURRICANE OPAL/HIGH WINDS      1
## 372              HURRICANE/TYPHOON     88
## 373                       HVY RAIN      2
## 374          HYPERTHERMIA/EXPOSURE      1
## 375                    HYPOTHERMIA      1
## 376           HYPOTHERMIA/EXPOSURE      6
## 377                            ICE     61
## 378                   ICE AND SNOW      1
## 379                      ICE FLOES      2
## 380                        ICE FOG      2
## 381                        ICE JAM      4
## 382           ICE JAM FLOOD (MINOR      1
## 383               ICE JAM FLOODING      5
## 384                    ICE ON ROAD      1
## 385                    ICE PELLETS      1
## 386                      ICE ROADS      1
## 387                      ICE STORM   2006
## 388             ICE STORM AND SNOW      1
## 389          ICE STORM/FLASH FLOOD      1
## 390                       ICE/SNOW      5
## 391               ICE/STRONG WINDS      1
## 392              ICESTORM/BLIZZARD      1
## 393                      ICY ROADS     32
## 394                   LACK OF SNOW      1
## 395               LAKE-EFFECT SNOW    636
## 396               LAKE EFFECT SNOW     23
## 397                     LAKE FLOOD      1
## 398                LAKESHORE FLOOD     23
## 399                      LANDSLIDE    600
## 400          LANDSLIDE/URBAN FLOOD      1
## 401                     LANDSLIDES      8
## 402                      LANDSLUMP      2
## 403                      LANDSPOUT      2
## 404               LARGE WALL CLOUD      1
## 405           LATE-SEASON SNOWFALL      1
## 406                    LATE FREEZE      1
## 407               LATE SEASON HAIL      1
## 408               LATE SEASON SNOW      1
## 409           LATE SEASON SNOWFALL      2
## 410                      LATE SNOW      2
## 411            LIGHT FREEZING RAIN     23
## 412                     LIGHT SNOW    176
## 413           LIGHT SNOW AND SLEET      2
## 414            LIGHT SNOW/FLURRIES      3
## 415     LIGHT SNOW/FREEZING PRECIP      1
## 416                 LIGHT SNOWFALL      1
## 417                       LIGHTING      3
## 418                      LIGHTNING  15754
## 419             LIGHTNING  WAUSEON      1
## 420       LIGHTNING AND HEAVY RAIN      1
## 421 LIGHTNING AND THUNDERSTORM WIN      1
## 422            LIGHTNING AND WINDS      1
## 423               LIGHTNING DAMAGE      1
## 424                 LIGHTNING FIRE      1
## 425               LIGHTNING INJURY      1
## 426   LIGHTNING THUNDERSTORM WINDS      1
## 427  LIGHTNING THUNDERSTORM WINDSS      1
## 428                     LIGHTNING.      1
## 429           LIGHTNING/HEAVY RAIN      1
## 430                      LIGNTNING      1
## 431              LOCAL FLASH FLOOD      1
## 432                    LOCAL FLOOD      1
## 433             LOCALLY HEAVY RAIN      1
## 434                LOW TEMPERATURE      7
## 435         LOW TEMPERATURE RECORD      1
## 436                 LOW WIND CHILL      1
## 437                    MAJOR FLOOD      3
## 438                MARINE ACCIDENT      1
## 439                    MARINE HAIL    442
## 440               MARINE HIGH WIND    135
## 441                  MARINE MISHAP      2
## 442             MARINE STRONG WIND     48
## 443       MARINE THUNDERSTORM WIND   5812
## 444               MARINE TSTM WIND   6175
## 445            METRO STORM, MAY 26      1
## 446                     MICROBURST      9
## 447               MICROBURST WINDS      5
## 448           MILD AND DRY PATTERN      1
## 449                   MILD PATTERN      1
## 450               MILD/DRY PATTERN      1
## 451                    MINOR FLOOD      1
## 452                 MINOR FLOODING      4
## 453                   MIXED PRECIP     10
## 454            MIXED PRECIPITATION     37
## 455                  MODERATE SNOW      1
## 456              MODERATE SNOWFALL    101
## 457          MONTHLY PRECIPITATION     36
## 458               MONTHLY RAINFALL     13
## 459               MONTHLY SNOWFALL      2
## 460            MONTHLY TEMPERATURE      4
## 461                 MOUNTAIN SNOWS      1
## 462                      MUD SLIDE      7
## 463                     MUD SLIDES      1
## 464      MUD SLIDES URBAN FLOODING      1
## 465                 MUD/ROCK SLIDE      1
## 466                       MUDSLIDE     17
## 467             MUDSLIDE/LANDSLIDE      1
## 468                      MUDSLIDES      9
## 469               NEAR RECORD SNOW      1
## 470              NO SEVERE WEATHER      1
## 471         NON-SEVERE WIND DAMAGE      1
## 472                  NON-TSTM WIND      1
## 473                NON SEVERE HAIL      7
## 474                  NON TSTM WIND      2
## 475                           NONE      2
## 476           NORMAL PRECIPITATION      3
## 477                NORTHERN LIGHTS      1
## 478                          OTHER     52
## 479               PATCHY DENSE FOG      3
## 480                     PATCHY ICE      1
## 481                   PROLONG COLD     22
## 482              PROLONG COLD/SNOW      1
## 483                 PROLONG WARMTH      4
## 484                 PROLONGED RAIN      4
## 485                           RAIN     16
## 486                   RAIN (HEAVY)      1
## 487                  RAIN AND WIND      1
## 488                    RAIN DAMAGE      1
## 489                      RAIN/SNOW      5
## 490                      RAIN/WIND      1
## 491                      RAINSTORM      1
## 492           RAPIDLY RISING WATER      1
## 493                   RECORD  COLD      1
## 494                    RECORD COLD     67
## 495      RECORD COLD AND HIGH WIND      1
## 496              RECORD COLD/FROST      2
## 497                    RECORD COOL      5
## 498               RECORD DRY MONTH      1
## 499                 RECORD DRYNESS      2
## 500                    RECORD HEAT     82
## 501               RECORD HEAT WAVE      1
## 502                    RECORD HIGH      7
## 503        RECORD HIGH TEMPERATURE      3
## 504       RECORD HIGH TEMPERATURES      1
## 505                     RECORD LOW      4
## 506            RECORD LOW RAINFALL      2
## 507                RECORD MAY SNOW      1
## 508           RECORD PRECIPITATION      1
## 509                RECORD RAINFALL     14
## 510                    RECORD SNOW      8
## 511               RECORD SNOW/COLD      1
## 512                RECORD SNOWFALL      6
## 513             RECORD TEMPERATURE     16
## 514            RECORD TEMPERATURES      5
## 515                    RECORD WARM      1
## 516             RECORD WARM TEMPS.      1
## 517                  RECORD WARMTH    154
## 518             RECORD WINTER SNOW      3
## 519          RECORD/EXCESSIVE HEAT      3
## 520      RECORD/EXCESSIVE RAINFALL      1
## 521              RED FLAG CRITERIA      2
## 522               RED FLAG FIRE WX      2
## 523              REMNANTS OF FLOYD      2
## 524                    RIP CURRENT    470
## 525                   RIP CURRENTS    304
## 526        RIP CURRENTS HEAVY SURF      1
## 527        RIP CURRENTS/HEAVY SURF      2
## 528         RIVER AND STREAM FLOOD      2
## 529                    RIVER FLOOD    173
## 530                 RIVER FLOODING     29
## 531                     ROCK SLIDE      2
## 532                     ROGUE WAVE      1
## 533            ROTATING WALL CLOUD      5
## 534                     ROUGH SEAS      3
## 535                     ROUGH SURF      4
## 536                    RURAL FLOOD      2
## 537                   SAHARAN DUST      4
## 538              SEASONAL SNOWFALL      1
## 539                         SEICHE     21
## 540                    SEVERE COLD      1
## 541            SEVERE THUNDERSTORM     13
## 542      SEVERE THUNDERSTORM WINDS      5
## 543           SEVERE THUNDERSTORMS     23
## 544              SEVERE TURBULENCE      1
## 545                          SLEET     59
## 546          SLEET & FREEZING RAIN      1
## 547                    SLEET STORM     12
## 548            SLEET/FREEZING RAIN      2
## 549                SLEET/ICE STORM      1
## 550                SLEET/RAIN/SNOW      1
## 551                     SLEET/SNOW      2
## 552                     SMALL HAIL     53
## 553                   SMALL STREAM      1
## 554               SMALL STREAM AND      1
## 555   SMALL STREAM AND URBAN FLOOD      2
## 556 SMALL STREAM AND URBAN FLOODIN      1
## 557             SMALL STREAM FLOOD      7
## 558          SMALL STREAM FLOODING      4
## 559       SMALL STREAM URBAN FLOOD      1
## 560       SMALL STREAM/URBAN FLOOD      5
## 561                 SML STREAM FLD      2
## 562                          SMOKE     11
## 563                           SNOW    617
## 564    SNOW- HIGH WIND- WIND CHILL      1
## 565              SNOW ACCUMULATION      2
## 566                  SNOW ADVISORY      1
## 567                  SNOW AND COLD      2
## 568            SNOW AND HEAVY SNOW      2
## 569                   SNOW AND ICE     34
## 570             SNOW AND ICE STORM      1
## 571                 SNOW AND SLEET      5
## 572                  SNOW AND WIND      1
## 573                   SNOW DROUGHT      7
## 574             SNOW FREEZING RAIN     11
## 575                   SNOW SHOWERS      6
## 576                     SNOW SLEET      1
## 577                    SNOW SQUALL     19
## 578                   SNOW SQUALLS     22
## 579              SNOW/ BITTER COLD      1
## 580                      SNOW/ ICE      1
## 581              SNOW/BLOWING SNOW      7
## 582                      SNOW/COLD      2
## 583             SNOW/FREEZING RAIN      6
## 584                SNOW/HEAVY SNOW      1
## 585                SNOW/HIGH WINDS      2
## 586                       SNOW/ICE      7
## 587                 SNOW/ICE STORM     17
## 588                      SNOW/RAIN      1
## 589                SNOW/RAIN/SLEET      1
## 590                     SNOW/SLEET     10
## 591       SNOW/SLEET/FREEZING RAIN      6
## 592                SNOW/SLEET/RAIN      1
## 593                     SNOW\\COLD      1
## 594                SNOWFALL RECORD      1
## 595              SNOWMELT FLOODING      5
## 596                      SNOWSTORM      1
## 597                      SOUTHEAST      1
## 598              STORM FORCE WINDS      1
## 599                    STORM SURGE    261
## 600               STORM SURGE/TIDE    148
## 601                STREAM FLOODING      1
## 602                   STREET FLOOD      3
## 603                STREET FLOODING      3
## 604                    STRONG WIND   3569
## 605               STRONG WIND GUST      2
## 606                   STRONG WINDS    204
## 607              SUMMARY AUGUST 10      2
## 608              SUMMARY AUGUST 11      2
## 609              SUMMARY AUGUST 17      1
## 610             SUMMARY AUGUST 2-3      1
## 611              SUMMARY AUGUST 21      1
## 612              SUMMARY AUGUST 28      1
## 613               SUMMARY AUGUST 4      1
## 614               SUMMARY AUGUST 7      1
## 615               SUMMARY AUGUST 9      1
## 616                 SUMMARY JAN 17      1
## 617             SUMMARY JULY 23-24      1
## 618             SUMMARY JUNE 18-19      1
## 619               SUMMARY JUNE 5-6      1
## 620                 SUMMARY JUNE 6      1
## 621            SUMMARY OF APRIL 12      2
## 622            SUMMARY OF APRIL 13      1
## 623            SUMMARY OF APRIL 21      2
## 624            SUMMARY OF APRIL 27      1
## 625           SUMMARY OF APRIL 3RD      1
## 626            SUMMARY OF AUGUST 1      1
## 627             SUMMARY OF JULY 11      1
## 628              SUMMARY OF JULY 2      1
## 629             SUMMARY OF JULY 22      1
## 630             SUMMARY OF JULY 26      1
## 631             SUMMARY OF JULY 29      1
## 632              SUMMARY OF JULY 3      1
## 633             SUMMARY OF JUNE 10      1
## 634             SUMMARY OF JUNE 11      1
## 635             SUMMARY OF JUNE 12      1
## 636             SUMMARY OF JUNE 13      2
## 637             SUMMARY OF JUNE 15      1
## 638             SUMMARY OF JUNE 16      1
## 639             SUMMARY OF JUNE 18      1
## 640             SUMMARY OF JUNE 23      1
## 641             SUMMARY OF JUNE 24      1
## 642              SUMMARY OF JUNE 3      2
## 643             SUMMARY OF JUNE 30      1
## 644              SUMMARY OF JUNE 4      1
## 645              SUMMARY OF JUNE 6      1
## 646            SUMMARY OF MARCH 14      1
## 647            SUMMARY OF MARCH 23      2
## 648            SUMMARY OF MARCH 24      1
## 649         SUMMARY OF MARCH 24-25      1
## 650            SUMMARY OF MARCH 27      1
## 651            SUMMARY OF MARCH 29      1
## 652              SUMMARY OF MAY 10      1
## 653              SUMMARY OF MAY 13      1
## 654              SUMMARY OF MAY 14      1
## 655              SUMMARY OF MAY 22      1
## 656           SUMMARY OF MAY 22 AM      1
## 657           SUMMARY OF MAY 22 PM      1
## 658           SUMMARY OF MAY 26 AM      1
## 659           SUMMARY OF MAY 26 PM      1
## 660           SUMMARY OF MAY 31 AM      1
## 661           SUMMARY OF MAY 31 PM      1
## 662            SUMMARY OF MAY 9-10      1
## 663            SUMMARY SEPT. 25-26      1
## 664           SUMMARY SEPTEMBER 20      1
## 665           SUMMARY SEPTEMBER 23      2
## 666            SUMMARY SEPTEMBER 3      1
## 667            SUMMARY SEPTEMBER 4      1
## 668               SUMMARY: NOV. 16      2
## 669              SUMMARY: NOV. 6-7      1
## 670            SUMMARY: OCT. 20-21      1
## 671            SUMMARY: OCTOBER 31      1
## 672              SUMMARY: SEPT. 18      1
## 673             TEMPERATURE RECORD     43
## 674              THUDERSTORM WINDS      2
## 675            THUNDEERSTORM WINDS      2
## 676            THUNDERESTORM WINDS      1
## 677                    THUNDERSNOW      1
## 678             THUNDERSNOW SHOWER      1
## 679                   THUNDERSTORM     45
## 680            THUNDERSTORM  WINDS      7
## 681            THUNDERSTORM DAMAGE      2
## 682         THUNDERSTORM DAMAGE TO      1
## 683              THUNDERSTORM HAIL      1
## 684            THUNDERSTORM W INDS      1
## 685              THUNDERSTORM WIND  82564
## 686        THUNDERSTORM WIND (G40)      1
## 687           THUNDERSTORM WIND 50      2
## 688           THUNDERSTORM WIND 52      1
## 689           THUNDERSTORM WIND 56      1
## 690           THUNDERSTORM WIND 59      1
## 691       THUNDERSTORM WIND 59 MPH      1
## 692      THUNDERSTORM WIND 59 MPH.      1
## 693       THUNDERSTORM WIND 60 MPH      4
## 694       THUNDERSTORM WIND 65 MPH      1
## 695        THUNDERSTORM WIND 65MPH      1
## 696           THUNDERSTORM WIND 69      1
## 697       THUNDERSTORM WIND 98 MPH      1
## 698          THUNDERSTORM WIND G50      4
## 699          THUNDERSTORM WIND G51      1
## 700          THUNDERSTORM WIND G52      2
## 701          THUNDERSTORM WIND G55      1
## 702          THUNDERSTORM WIND G60      2
## 703          THUNDERSTORM WIND G61      1
## 704        THUNDERSTORM WIND TREES      1
## 705             THUNDERSTORM WIND.      1
## 706        THUNDERSTORM WIND/ TREE      1
## 707       THUNDERSTORM WIND/ TREES      4
## 708       THUNDERSTORM WIND/AWNING      1
## 709         THUNDERSTORM WIND/HAIL      1
## 710    THUNDERSTORM WIND/LIGHTNING      1
## 711             THUNDERSTORM WINDS  20843
## 712 THUNDERSTORM WINDS      LE CEN      1
## 713          THUNDERSTORM WINDS 13      1
## 714           THUNDERSTORM WINDS 2      1
## 715          THUNDERSTORM WINDS 50      1
## 716          THUNDERSTORM WINDS 52      1
## 717          THUNDERSTORM WINDS 53      1
## 718          THUNDERSTORM WINDS 60      1
## 719          THUNDERSTORM WINDS 61      1
## 720          THUNDERSTORM WINDS 62      1
## 721      THUNDERSTORM WINDS 63 MPH      1
## 722         THUNDERSTORM WINDS AND      2
## 723 THUNDERSTORM WINDS FUNNEL CLOU      2
## 724           THUNDERSTORM WINDS G      2
## 725         THUNDERSTORM WINDS G60      1
## 726        THUNDERSTORM WINDS HAIL     61
## 727  THUNDERSTORM WINDS HEAVY RAIN      1
## 728   THUNDERSTORM WINDS LIGHTNING      7
## 729 THUNDERSTORM WINDS SMALL STREA      1
## 730 THUNDERSTORM WINDS URBAN FLOOD      1
## 731            THUNDERSTORM WINDS.      3
## 732      THUNDERSTORM WINDS/ FLOOD      2
## 733       THUNDERSTORM WINDS/ HAIL      1
## 734 THUNDERSTORM WINDS/FLASH FLOOD      1
## 735    THUNDERSTORM WINDS/FLOODING      1
## 736 THUNDERSTORM WINDS/FUNNEL CLOU      1
## 737        THUNDERSTORM WINDS/HAIL     24
## 738  THUNDERSTORM WINDS/HEAVY RAIN      1
## 739           THUNDERSTORM WINDS53      1
## 740         THUNDERSTORM WINDSHAIL      1
## 741            THUNDERSTORM WINDSS     51
## 742              THUNDERSTORM WINS      1
## 743                  THUNDERSTORMS      4
## 744             THUNDERSTORMS WIND      6
## 745            THUNDERSTORMS WINDS     14
## 746                  THUNDERSTORMW      1
## 747               THUNDERSTORMW 50      1
## 748            THUNDERSTORMW WINDS      3
## 749              THUNDERSTORMWINDS      1
## 750              THUNDERSTROM WIND      1
## 751             THUNDERSTROM WINDS      2
## 752              THUNDERTORM WINDS      3
## 753              THUNDERTSORM WIND      1
## 754              THUNDESTORM WINDS      2
## 755              THUNERSTORM WINDS      1
## 756                    TIDAL FLOOD      1
## 757                 TIDAL FLOODING     25
## 758                        TORNADO  60652
## 759                 TORNADO DEBRIS      1
## 760                     TORNADO F0     19
## 761                     TORNADO F1      4
## 762                     TORNADO F2      3
## 763                     TORNADO F3      2
## 764             TORNADO/WATERSPOUT      1
## 765                      TORNADOES      2
## 766     TORNADOES, TSTM WIND, HAIL      1
## 767                       TORNADOS      1
## 768                        TORNDAO      1
## 769                TORRENTIAL RAIN      1
## 770            TORRENTIAL RAINFALL      1
## 771            TROPICAL DEPRESSION     60
## 772                 TROPICAL STORM    690
## 773         TROPICAL STORM ALBERTO      1
## 774            TROPICAL STORM DEAN      2
## 775          TROPICAL STORM GORDON      1
## 776           TROPICAL STORM JERRY      3
## 777                           TSTM      1
## 778                TSTM HEAVY RAIN      3
## 779                      TSTM WIND 219942
## 780               TSTM WIND  (G45)      1
## 781                 TSTM WIND (41)      1
## 782                TSTM WIND (G35)      1
## 783                TSTM WIND (G40)     10
## 784                TSTM WIND (G45)     39
## 785                   TSTM WIND 40      1
## 786                   TSTM WIND 45      1
## 787                   TSTM WIND 50      1
## 788                   TSTM WIND 51      2
## 789                   TSTM WIND 52      5
## 790                   TSTM WIND 55      3
## 791                  TSTM WIND 65)      1
## 792        TSTM WIND AND LIGHTNING      1
## 793               TSTM WIND DAMAGE      1
## 794                  TSTM WIND G45      1
## 795                  TSTM WIND G58      1
## 796                 TSTM WIND/HAIL   1028
## 797                     TSTM WINDS      6
## 798                       TSTM WND      1
## 799                          TSTMW      1
## 800                        TSUNAMI     20
## 801               TUNDERSTORM WIND      1
## 802                        TYPHOON     11
## 803              UNSEASONABLE COLD      1
## 804              UNSEASONABLY COLD     23
## 805              UNSEASONABLY COOL     12
## 806        UNSEASONABLY COOL & WET      2
## 807               UNSEASONABLY DRY     56
## 808               UNSEASONABLY HOT     10
## 809              UNSEASONABLY WARM    126
## 810        UNSEASONABLY WARM & WET      1
## 811      UNSEASONABLY WARM AND DRY     13
## 812         UNSEASONABLY WARM YEAR      2
## 813          UNSEASONABLY WARM/WET      2
## 814               UNSEASONABLY WET     19
## 815            UNSEASONAL LOW TEMP      2
## 816                UNSEASONAL RAIN      2
## 817                 UNUSUAL WARMTH     10
## 818          UNUSUAL/RECORD WARMTH      2
## 819                 UNUSUALLY COLD      8
## 820            UNUSUALLY LATE SNOW      1
## 821                 UNUSUALLY WARM      4
## 822                URBAN AND SMALL      2
## 823         URBAN AND SMALL STREAM      3
## 824   URBAN AND SMALL STREAM FLOOD      3
## 825 URBAN AND SMALL STREAM FLOODIN      6
## 826                    URBAN FLOOD    251
## 827          URBAN FLOOD LANDSLIDE      1
## 828                 URBAN FLOODING     99
## 829                   URBAN FLOODS      3
## 830                    URBAN SMALL      1
## 831       URBAN SMALL STREAM FLOOD      2
## 832                    URBAN/SMALL      2
## 833           URBAN/SMALL FLOODING      1
## 834             URBAN/SMALL STREAM      8
## 835      URBAN/SMALL STREAM  FLOOD      2
## 836       URBAN/SMALL STREAM FLOOD     30
## 837    URBAN/SMALL STREAM FLOODING      4
## 838          URBAN/SMALL STRM FLDG      1
## 839           URBAN/SML STREAM FLD   3392
## 840          URBAN/SML STREAM FLDG      1
## 841          URBAN/STREET FLOODING      3
## 842                       VERY DRY      2
## 843                      VERY WARM      1
## 844                            VOG      1
## 845                   VOLCANIC ASH     23
## 846             VOLCANIC ASH PLUME      1
## 847               VOLCANIC ASHFALL      3
## 848              VOLCANIC ERUPTION      2
## 849                  WAKE LOW WIND      2
## 850                     WALL CLOUD      5
## 851        WALL CLOUD/FUNNEL CLOUD      1
## 852            WARM DRY CONDITIONS      1
## 853                   WARM WEATHER      1
## 854                    WATER SPOUT      1
## 855                     WATERSPOUT   3796
## 856                    WATERSPOUT-     10
## 857             WATERSPOUT-TORNADO      2
## 858        WATERSPOUT FUNNEL CLOUD      1
## 859             WATERSPOUT TORNADO      1
## 860                    WATERSPOUT/      1
## 861            WATERSPOUT/ TORNADO      2
## 862             WATERSPOUT/TORNADO      8
## 863                    WATERSPOUTS     37
## 864                    WAYTERSPOUT      1
## 865                  WET MICOBURST      1
## 866                 WET MICROBURST      6
## 867                      WET MONTH      4
## 868                       WET SNOW      1
## 869                    WET WEATHER      1
## 870                       WET YEAR      4
## 871                      WHIRLWIND      3
## 872                     WILD FIRES      4
## 873               WILD/FOREST FIRE   1457
## 874              WILD/FOREST FIRES      1
## 875                       WILDFIRE   2761
## 876                      WILDFIRES      8
## 877                           WIND    346
## 878                  WIND ADVISORY     12
## 879                  WIND AND WAVE      1
## 880                     WIND CHILL     18
## 881           WIND CHILL/HIGH WIND      1
## 882                    WIND DAMAGE     31
## 883                     WIND GUSTS      3
## 884                     WIND STORM      1
## 885                      WIND/HAIL      1
## 886                          WINDS     36
## 887                     WINTER MIX      3
## 888                   WINTER STORM  11433
## 889        WINTER STORM HIGH WINDS      1
## 890         WINTER STORM/HIGH WIND      1
## 891        WINTER STORM/HIGH WINDS      1
## 892                  WINTER STORMS      3
## 893                 WINTER WEATHER   7045
## 894             WINTER WEATHER MIX      6
## 895             WINTER WEATHER/MIX   1104
## 896                    WINTERY MIX      2
## 897                     WINTRY MIX     94
## 898                            WND      1
```

You will probably have to scroll up at this point if you forgot the question,
but that's my point: there are 48 allowed EVTYPEs, and we still have 
898 values in the EVTYPE field.

Of these 898 values, a simple visual inspection shows that many
are variants, and a little work with grepl to replace like terms with 
exactly the same term will quickly yield a better analysis.

## AN EXECUTIVE DECISION: COMBINE EVENT TYPES

It would be nice to use grep and so forth to combine all disallowed
types into allowed types, without combinbing allowed types, but that gets messy.

Therefore, even if it means **combining allowed values**, I will combine event types.
I will do this for two reasons. First, for example, what is the difference
between Blizzard, Heavy Snow, Winter Storm, and Winter Weather?
Plus, one could argue that misclassification among **allowed types**
disguises the economic and health costs of certain events.

So as to make the analysis more reproducible, I will combine them into 
my own added variable. As we recall from the prior classes, we can add a column
to a data frame merly by naming it. We'll do this to event_types first
to see how it looks.


```r
# create an empty additional variable
event_types$EDITED <- rep("",length(event_types$EVTYPE))

# Volcano action is pretty unique
event_types$EDITED[grepl("VOLCAN",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "VOLCANIC ASH"

# Tornado is pretty specific
event_types$EDITED[grepl("TORNADO",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "TORNADO"
event_types$EDITED[grepl("WATERSPOUT",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "TORNADO"
event_types$EDITED[grepl("FUNNEL",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "TORNADO"
event_types$EDITED[grepl("TORNDAO",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "TORNADO"

# anything with dust seems to be its own deal
event_types$EDITED[grepl("DUST",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "DUST"

# do FLOOD before other rain/snow/ice events
event_types$EDITED[grepl("FLOOD",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "FLOOD"

# keep FROST separate (crop damage)
event_types$EDITED[grepl("FROST",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "FROST"

# hail is its own deal (crop damage)
event_types$EDITED[grepl("HAIL",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "HAIL"

# put all ICE events together
event_types$EDITED[grepl("FREEZING",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "SLEET"
event_types$EDITED[grepl("SLEET",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "SLEET"
event_types$EDITED[grepl("ICE",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "SLEET"
event_types$EDITED[grepl("ICY",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "SLEET"

# combine the rest of WINTER (non-ICE) events
event_types$EDITED[grepl("WINTER",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "WINTER"
event_types$EDITED[grepl("WINTRY",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "WINTER"
event_types$EDITED[grepl("BLIZZARD",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "WINTER"
event_types$EDITED[grepl("THUNDERSNOW",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "WINTER"

# COLD
event_types$EDITED[grepl("COLD",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "COLD"
event_types$EDITED[grepl("CHILL",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "COLD"

# HEAT
event_types$EDITED[grepl("HEAT",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "HEAT"
event_types$EDITED[grepl("HOT",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "HEAT"


# Thunderstorms
event_types$EDITED[grepl("TSTM WIND",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "THUNDERSTORM WIND"
event_types$EDITED[grepl("THUNDERSTORM",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "THUNDERSTORM WIND"
# start picking off misspellings
event_types$EDITED[grepl("THUNDER",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "THUNDERSTORM WIND"
event_types$EDITED[grepl("TUNDER",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "THUNDERSTORM WIND"
event_types$EDITED[grepl("THUNDEER",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "THUNDERSTORM WIND"
event_types$EDITED[grepl("THUDER",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "THUNDERSTORM WIND"
event_types$EDITED[grepl("STORM WIND",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "THUNDERSTORM WIND"

event_types$EDITED[grepl("LIGHTNING",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "LIGHTNING"


# at this point, everything left with snow is also winter
event_types$EDITED[grepl("SNOW",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "WINTER"

# TROPICAL STORMS, etc
event_types$EDITED[grepl("TROPICAL",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "TROPICAL"
event_types$EDITED[grepl("MARINE",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "TROPICAL"
event_types$EDITED[grepl("TSUNAMI",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "TROPICAL"
event_types$EDITED[grepl("TYPHOON",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "TROPICAL"
event_types$EDITED[grepl("HURRICANE",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "TROPICAL"
event_types$EDITED[grepl("SURGE",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "TROPICAL"
event_types$EDITED[grepl("SURF",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "TROPICAL"

# OTHER RAIN
event_types$EDITED[grepl("RAIN",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "HEAVY RAIN"

# let's start picking off wind, of which there's a lot
event_types$EDITED[grepl("HIGH WIND",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "HIGH WIND"
event_types$EDITED[grepl("MICROBURST",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "HIGH WIND"
event_types$EDITED[grepl("MICOBURST",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "HIGH WIND"

# FIRE
event_types$EDITED[grepl("FIRE",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "WILDFIRE"
event_types$EDITED[grepl("SMOKE",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "WILDFIRE"


# at this point, everything left with WIND is high wind imho
event_types$EDITED[grepl("WIND",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "HIGH WIND"

# There are a LOT of events from 1996 called "summary"
event_types$EDITED[grepl("SUMMARY",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "SUMMARY in 1996"

# all the warm left means heat
event_types$EDITED[grepl("WARM",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "HEAT"

# drought
event_types$EDITED[grepl("DROUGHT",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "DROUGHT"
event_types$EDITED[grepl("DRY",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "DROUGHT"

# rip tides will have fatalities, and there are about 400 of each spelling
event_types$EDITED[grepl("RIP",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "RIP CURRENT"


# remaining urbans are flood
event_types$EDITED[grepl("URBAN",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "FLOOD"

# earth movements
event_types$EDITED[grepl("LANDSLIDE",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "AVALANCHE"
event_types$EDITED[grepl("AVA",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "AVALANCHE"
event_types$EDITED[grepl("ROCK",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "AVALANCHE"
event_types$EDITED[grepl("MUD",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "AVALANCHE"

# FOG
event_types$EDITED[grepl("FOG",event_types$EVTYPE) &
                   (event_types$EDITED == "")] <- "DENSE FOG"


# edited_event_types  <- with(event_types,table(EDITED))
# edited_event_types <- as.data.frame(edited_event_types)
```

Now we display a smaller, stylized group of event types to work with.
The frequency of my combined/corrected events is as follows.


```r
rowsum(event_types$Freq,event_types$EDITED)
```

```
##                     [,1]
##                      868
## AVALANCHE           1033
## COLD                2706
## DENSE FOG           1834
## DROUGHT             2579
## DUST                 588
## FLOOD              86141
## FROST               1413
## HAIL              290398
## HEAT                2993
## HEAVY RAIN         11862
## HIGH WIND          26314
## LIGHTNING          15764
## RIP CURRENT          774
## SLEET               2720
## SUMMARY in 1996       75
## THUNDERSTORM WIND 335691
## TORNADO            71538
## TROPICAL            2738
## VOLCANIC ASH          29
## WILDFIRE            4260
## WINTER             39979
```
This shows our expected total of
 902297, as expected.

================================================================================

## RESULTS

We now map our stylized, simplified set of events to the main data set.


```r
main_data <- merge(main_data,event_types,by="EVTYPE")
event_types2  <- with(main_data,table(EDITED))
event_types2 <- as.data.frame(event_types2)
```
================================================================================

## CONCLUSIONS


================================================================================

## SUMMARY


================================================================================

## scratch pad of stuff remove for final version


```r
# z <- main_data$BGN_DATE[grepl("summary",main_data$EVTYPE,ignore.case=TRUE)]

# z
```

================================================================================

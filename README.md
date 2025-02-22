# NEMWeb

## Easy access to Australia's electricity generation data from nemweb.com.au

## Purpose
1. A Python program providing easy access to the large amount of archived
data of Australia's main electricity grid provided by the market
operator, AEMO.
2. A power-flow analysis.
3. A net-zero 2050 simulation of AEMO's [Intergrated System Plan 2024 (ISP)](
https://aemo.com.au/energy-systems/major-publications/integrated-system-plan-isp/2024-integrated-system-plan-isp)
based on the 2024 analysis.

## Just the Results
1. For NEMWeb.py output for calendar year 2024 click to download...
>* [NEM In Out List Jan-Dec 2024.xlsx](
    NEM%20In%20Out%20List%20Jan-Dec%202024.xlsx) (<1MB)
>* [NEM Year by Category Jan-Dec 2024.xlsx](
    NEM%20Year%20by%20Category%20Jan-Dec%202024.xlsx) (14MB)

2. For the analysis
>* [NEM Analysis 2024.xlsx](NEM%20Analysis%202024.xlsx) (26MB)
>* [NEM Daily Generation 2024.pdf](NEM%20Daily%20Generation%202024.pdf)
>* [Average NEM Power Flows 2024.pdf](
    Average%20NEM%20Power%20Flows%202024.pdf)

3. For the simulation
>* [NEM ISP 2050 Simulation.xlsx](
    NEM%20ISP%202050%20Simulation.xlsx) (52MB)\
    A simulation of the ISP's NEM grid based on 2024's Hydro, Grid
    Solar, Wind, Rooftop Solar and Demand data from _NEM&nbsp;
    Analysis&nbsp;2024.xlsx_.
>* [NEM ISP 2050 Analysis.xlsx](
    NEM%20ISP%202050%20Analysis.xlsx) (66MB)\
    Derives results from the simulation including 5-day rolling
    averages plus three charts.
>* [NEM ISP 2050 Analysis.pdf](
    NEM%20ISP%202050%20Analysis.pdf)\
    Presents and discusses the results.

## 1. NEMWeb.py
### Usage
There are three key files
* NEMWeb.py
* DUID Categories.csv
* requirements.txt

Follow the usual Python procedures to use pip to install all the
dependencies in requirements.txt. The first time NEMWeb.py is run
it will read in the most recent 12 calendar months of
electricity generation data from nemweb.com.au, saving binary PKL files
to %APPDATA%\\NEMWeb then saving the following two files to
%USERPROFILE%\\Documents;
1. NEM Year by Category.xlsx
    * A row for each DUID (a power source/sink on the grid) and each
	rooftop solar region: NSW1, QLD1, SA1, TAS1 and VIC1. Columns are
	In, Out and Out/In. It's 50-60 KB in size.
2. NEM In Out List.xlsx
    * a row for each 5-minute interval with two sets of category
	  columns; a negative set for power taken from the grid and a
	  positive set for power put onto the grid. It's over 11MB in size.

Progress is displayed and three interactive graphs are shown; the raw
Dispatch_SCADA graph, a repair Dispatch_SCADA graph and the final
generation graph with rooftop solar included. The graphs show overall GW
generation in 5-minute intervals. NEMWeb.py runs much faster once the
binary PKL files exist.

### Introduction (From Google AI)
The National Electricity Market (NEM) is Australia's wholesale
electricity market and physical power system, which generates, buys,
sells, and transports electricity. The NEM is one of the world's largest
interconnected power systems, supplying around 80% of Australia's
electricity consumption.

The NEM covers five regions on the east coast of Australia: Queensland,
New South Wales, Victoria, Tasmania, and South Australia.

The Australian Energy Market Operator (AEMO) is responsible for
scheduling the lowest priced generation available to meet demand, and
for restoring energy systems to a secure operating state in the event of
an emergency.

### Background
AEMO provides the last 13 months of NEM generation in the Dispatch_SCADA
and ROOFTOP_PV/ACTUAL subdirectories of
www.nemweb.com.au/REPORTS/ARCHIVE.
* SCADA - Supervisory Control And Data Acquisition

#### Dispatch_SCADA
Dispatch_SCADA data consists of over 20 million records per year. Each
active generator or storage device sends a record to AEMO every 5
minutes of its power output or input to the electricity grid. Power
values are in MW with input values being negative. Each generator or
storage device is identified using a short Dispatchable Unit Identifier
(DUID). For exanple, ER02 is Eraring unit 2, a 720 MW generator. AEMO
packages Dispatch_SCADA data into daily ZIP files.

One good source of information about the NEM;'s generators and storage
devices is the spreadsheet, nem-registration-and-exemption-list.xlsx,
at aemo.com.au/-/media/files/electricity/nem/participant_information.

#### ROOFTOP_PV
Rooftop_PV data (PV - PhotoVoltaic) is the rooftop solar generation of
the five Australian states provided by home or business owners to their
local electricity distribution network. It does not include rooftop
generation used by the home or business owner. The data is provided in
several forms as half hourly records of MW power output and packaged by
AEMO into weekly ZIP files. NEMWeb uses the total power output for each
state, identified with region identifiers as NSW1, QLD1, SA1, TAS1 and
VIC1. 365 day's worth of this data contains 87,600 records.

### Execution
* Load the most recent 12 months of
  * 'Dispatch_SCADA' 5-minute interval electricity generator/storage
	data into a set of daily dataframe and
  * 'ROOFTOP_PV/ACTUAL' 30-minute interval rooftop solar data into a set
	of weekly dataframes.
* Assemble a single 12 month dataframe, each row a 5-minute interval,
  and load it with the generator/storage Dispatch_SCADA data. Five empty
  columns are included for loading rooftop solar data.
  * Note: This dataframe typically has around 50 million cells (100 MB).
* Display the first graph of the raw Dispatch_SCADA data.
* Find and interpolate any missing generator/storage data (a rare
  occurrence).
* Display the second graph of repaired Dispatch_SCADA data.
* Place rooftop solar data in the five empty columns by interpolating
  the 30-minute interval data.
* Display the third graph of the raw Dispatch_SCADA data.
* Generate a dataframe of the average power in to and out of each DUID
  and region ID:
  * Add an Out/In column.
  * Save the dataframe as an Excel spreadsheet.
* Generate a dataframe totalling each category's input and output power:
  * Read in a csv data file where the first column is the DUID or
    region ID and the second column is its category.
  * Build a dataframe totalling each category's input and output power, each
    row being a 5-minute interval.
  * Create an "Unknown" category if any DUID has not been categorized.
  * Save the dataframe as an Excel spreadsheet.

If run using Idle (Python's Integrated Development and Learning
Environment) the roughly 100MB of the 12 month dataframe can be saved to
an excel spreadsheet using the command nemweb.save_5_minute_dataframe().
  * The file save can take a long time and Excel typically takes
	minutes to load it.

### Repairing Missing Data
Sometimes there are short intervals of missing data. For example, nine
intervals from 1:30 PM to 2:10 PM on 5th September 2024 and four
intervals from 1:30 PM to 1:45 PM on 19th November 2024 are missing.
These durations are sufficiently short that linear estimates between the
good neighbouring data can repair this missing data.

### File Repositories
NEMWeb uses three file repositories:

* the remote source AEMO's archives at nemweb.com.au,
* an optional local mirror copy of the AEMO's remote archive and
* a local data directory for storing NEMWeb's compact pickle data.

NEMWeb takes a while to download and read in remote source files. The
user may optionally download a copy of these ARCHIVE files using, for
example wget.exe, to provide a local mirror copy of the ARCHIVE files to
speed up the time it takes NEMWeb's to read in the source data. The data
from each daily Dispatch_SCADA or weekly ROOFTOP_PV ZIP file is then
saved as Python pickle data in the local data directory using the
original ZIP filename but with a .pkl extension.

## 2. Analysis
The worksheet in [NEM Year by Category Jan-Dec 2024.xlsx](
NEM%20Year%20by%20Category%20Jan-Dec%202024.xlsx) is copied (by value)
into the first tab of [NEM Analysis 2024.xlsx](
NEM%20Analysis%202024.xlsx). The following tabs are self explanatory:
Row Calcs, Column Calcs, Daily Calcs, Calcs and Results.

'Daily Calcs' includes a graph of average, min and max daily power
generation values. This graph is copied to
[NEM Daily Generation 2024.pdf](NEM%20Daily%20Generation%202024.pdf).

'Calcs and Results' has seven input parameters (shown in orange cells)
and calculates the average annual power flows through the NEM system.
The results are shown diagrammatically in
[Average NEM Power Flows 2024.pdf](
Average%20NEM%20Power%20Flows%202024.pdf).

### Demand Curve - i.e. paying customers
The whole role of the NEM is to deliver electricity to paying customers.
The demand curve is the graph showing this power. In this analysis it is
the commercial and residential power consumption plus the consumption of
aluminium smelters.

### What's Missing?
1. The power consumption of other large smelters and refineries apart from
the four aluminium smelters is not known. Presumably their power delivery
would not include distribution networks and so their distribution network
losses should be removed from the analysis.

2. The industry's typical 5% transmission loss factor is based on historic
grids where most power transmission is over a relatively short distance
from generators located nearby the cities they power. Power from
renewables travels, on average, much larger distances and so there will
be increased losses. This is not accounted for.

3. New power transmission equipment is being added to deal with the added
complexity of a renewables grid. For example, in addition to two
synchronous condensers the Buronga substation in NSW has
five phase-shifting transformers and four shunt reactors. Further
research is needed to account for the power consumption of this new
equipment although it's unlikely to be significant.

4. State governments are paying smelters and refineries to shut down
during periods of high demand to avoid blackouts in the general
community. The impact on the power flow analysis is insignificant but,
when considering potential blackout situations, it is very significant.
These shutdowns buy back power that the facility was to purchase,
effectively implementing a blackout of the facility although that term
is avoided in polite circles. The payments are not publically disclosed
and, FWIW, there is suspicion that the cost per kWh is massive.

## 3. ISP 2050 Simulation
The simulation and results are two separate Excel spreadsheets since
Excel's performance dramatically drops when the results calculations
are in the same spreadsheet as the simulation. The simulation takes
under a minute to run after which the simulation cells (excluding
row 1) in the 'MW' tab are manually copy-pasted-by-value into the results
'MW' tab.

### Abbreviations, Prefix, Constants & Parameters
|Abbreviation|
|-|-
|Trnsn|Transmission Grid
|DN|Distribution Network
|CR|Commercial & Residential
|SynCon|Synchronous Condenser

|Prefix|
|-|-
|n|a negated value

|Constant|Value| |
|-|-
|IntervalsPerHour|60 / 5|twelve 5-minute intervals per hour

#### ISP Derived Parameters
|Parameter|Typical Value|Source
|-|-|-
|Hydro_Factor|83%|ISP Page 11 graph, 7.5/9 - as measured from the graph
|Grid_Solar_Factor|6|ISP Page 11, bottom dot point
|Wind_Factor|6|ISP Page 11, bottom dot point
|Rooftop_Solar_Factor|4|ISP Page 12 third dot point
|Storage_Capacity|646 GWh|ISP Page 12 second dot point
|Storage_Generation_Capacity|49 GW|ISP Page 12 second dot point
|Non_hydro_storage_Baseload|19 GW|See below

* ISP Page 11, bottom dot point\
   "Triple grid-scale variable renewable energy (VRE) by 2030, and
   increase it six-fold by 2050"
* ISP Page 12 third dot point\
   "Support a forecast four-fold increase in rooftop solar capacity"
* ISP Page 12 second dot point\
   "This includes 49 GW/ 646 gigawatt hours (GWh) of dispatchable
   storage"
* Non_hydro_storage_Baseload
  * 19 GW = 75 GW - 8.5 GW x 83% - 49 GW
    * ISP Page 73 2nd paragraph\
       "75 GW of firming technology"
    * ISP Page 65 4th paragraph\
       "Firming technologies include storage, hydro, gas and other
	   fuelled generation."
    * ISP Page 6\
       "8.5 GW of hydropower assets in operation across Australia today"

#### Other Parameters
|Parameter|Typical Value|Source
|-|-|-
|Average_Dist_Network_Loss_Factor|5%|typically cited by the industry
|Average_Transmission_Loss_Factor|5% (typically cited) +3%|for longer average transmission distances
|Storage_Discharging_Efficiency|82%|from 2024's average for pumped-hydro & battery turnaround efficiency
|Storage_Charging_Efficiency|82%|ditto
|Aluminium_Smelters_Consumption|2,118 MW|from industry info with Tomago removed
|SynCon_Power_Consumption|400 MW|see discussion below
|Rooftop_to_Dist_Network_Factor|95%|educated guess
|Starting_Storage|323 GWh|chosen value - 50% of Storage_Capacity
|Storage_Threshhold_May_to_Aug|95%|chosen value for low wind+solar months
|Storage_Threshhold_Sep_to_Apr|30%|chosen value

##### SynCon_Power_Consumption
Information on actual power consumption of synchronous condensers has
been difficult to find. Synchronous condensers do have significant
cooling systems attached to them so there is significant power
consumption.

An initial approach was based on comparison with the UK grid, having an
average annual power of 37 GW and 220 GWs of inertia. Research did lead
to a power consumption factor of 2.9MW/GWs although confidence in this
value is not high. The estimated syncon consumption was over 800 MW for
the 2050 NEM.

Feedback from industry experts suggested, for a 55 GW average grid, a
syncon VA power rating of 10 GVA was reasonable and losses were
typically 2% of the VA rating for the syncons plus 2% losses for the
transformers connecting them to the grid. So a 400 MW syncon loss was
used in the simulation.

Further research or actual performance data could improve these values.
The 2024 ISP is superbly silent on this issue.

### Algorithm
#### Create the input arrays by scaling 2024's 5-minute interval results.
The following is pseudo code intended to describe the Excel formula in
the spreadsheet. Refer to the spreadsheet for the actual formulae.
```
// Scale each array of data.
Hydro = Hydro_2024 * Hydro_Factor
Grid_Solar = Grid_Solar_2024 * Grid_Solar_Factor
Wind = Wind_2024 * Wind_Factor
Rooftop_Solar = Rooftop_Solar_2024 * Rooftop_Solar_Factor
nDemand = nDemand_2024 * Demand_Factor
```

#### Initialise
```
// Note: [-1] indicates the previous interval which, in this case, is 1-Jan-2050 00:00.
StrgLevel[-1] = Starting_Storage
Average_Demand = -AVERAGE(nDemand)

// Determine the percentage of non-aluminium smelter demand.
CR_Factor = (Average_Demand - Aluminium_Smelters_Consumption) / Average_Demand
```

#### Loop over each 5-minute interval
##### Scale 2024's actual data to create the input data for the 2050 model.
```
Hydro = Hydro_2024 * Hydro_Factor
Grid_Solar = Grid_Solar_2024 * Grid_Solar_Factor
Wind = Wind_2024 * Wind_Factor
Rooftop_Solar = Rooftop_Solar_2024 * Rooftop_Solar_Factor
nDemand = nDemand_2024 * Demand_Factor
```
##### Determine transmission grid losses for hydro, wind and grid solar.
```
nHGWTrnsnLosses = -(Hydro + Wind + Grid_Solar) * Average_Trnsn_Loss_Factor
```
##### Assume constant synchronous condenser consumption.
```
nSynCon = -SynCon_Power_Consumption
```
##### Determine the maximum storage generation.
```
StrgGenLimit = MIN(StrgLevel[-1] * IntervalsPerHour * Storage_Discharging_Efficiency,
                   Storage_Generation_Capacity)
```
##### Determine the maximum transmission grid delivery.
```
TrnsnSupplyLimit = Hydro + Grid_Solar + Wind + nHGWTrnsnLosses + nSynCon
                   + (Non_hydro_storage_Baseload + StrgGenLimit)
                   * (1 - Average_Trnsn_Loss_Factor)
```
##### Calculate the rooftop solar power delivered to the distribution network.
```
CR_to_DN =Rooftop_Solar * Rooftop_to_Dist_Network_Factor
```
##### Determine the distribution network supply needed to avoid blackouts.
```
NonBlackoutDN_to_CR = -nDemand-Aluminium_Smelters_Consumption
                      - Rooftop_Solar * (1 - Rooftop_to_Dist_Network_Factor)
```
##### Determine the power input to the distribution network demand needed to avoid blackouts.
```
NonBlackoutDNDemand = NonBlackoutDN_to_CR / (1 - Average_Dist_Network_Loss_Factor) - CR_to_DN
```
##### See if the transmission grid can supply the demand.
```
TrnsnSupply = MIN(NonBlackoutDNDemand, TrnsnSupplyLimit - Aluminium_Smelters_Consumption)
```
##### Calculate the distribution network losses.
```
nDNLosses = -(TrnsnSupply + CR_to_DN) * Average_Dist_Network_Loss_Factor
```
##### Calculate the distribution network to commercial & residential demand.
```
DN_to_CR = TrnsnSupply + CR_to_DN + nDNLosses
```
##### Calculate the amount of blackout power, if any.
```
nBlackouts = DN_to_CR - NonBlackoutDN_to_CR
```
##### Calculate the power supplied before baseload may be needed.
```
BalanceBeforeBaseload = Hydro + Grid_Solar + Wind + nHGWTrnsnLosses + nSynCon - TrnsnSupply
                        - Aluminium_Smelters_Consumption
```
##### Calculate the power provided by non-hydro-storage baseload generators.
```
NonHydroStrgBaseloadGen	= MIN((MAX(0, StrgThreshhold[-1] - StrgLevel[-1]) * IntervalsPerHour
                              - MIN(MIN(BalanceBeforeBaseload, 0)
                                / (1 - Average_Transmission_Loss_Factor) + StrgGenLimit, 0))
                              , Non_hydro_storage_Baseload)
```
##### Calculate transmission losses from non-hydro-storage baseload power.
```
nBaseloadTrnsnLosses = -NonHydroStrgBaseloadGen * Average_Trnsn_Loss_Factor
```
##### Calculate the power supplied before storage may be needed.
```
BalanceAfterBaseload = BalanceBeforeBaseload + NonHydroStrgBaseloadGen + nBaseloadTrnsnLosses
```
##### Calculate the power provided from storage.
```
StrgGen = MIN(-MIN(BalanceAfterBaseload, 0) / (1 - Average_Trnsn_Loss_Factor),
	      StrgGenLimit)

```
##### Calculate transmission losses of the power supplied from storage.
```
nStrgTrnsnLosses = -StrgGen * Average_Trnsn_Loss_Factor
```
##### Calculate the power absorbed by storage.
```
nStrgLoad = MIN(-MIN(MAX(BalanceAfterBaseload, 0), Storage_Generation_Capacity),
                MAX(Storage_Capacity - StrgLevel[-1], 0) * IntervalsPerHour
                / (1 - Storage_Charging_Efficiency))
```
##### Calculate the final power balance.
```
BalanceAfterStrg = BalanceAfterBaseload + StrgGen + nStrgTrnsnLosses + nStrgLoad
```
##### Determine any spillage.
```
nSpillage = -MAX(BalanceAfterStrg, 0)
```
##### Calculate storage losses and storage level.
```
nStrgLosses = nStrgLoad * (1 - Storage_Charging_Efficiency)
              - StrgGen * (1 - Storage_Discharging_Efficiency)
StrgLevel = StrgLevel[-1] + (-StrgGen - nStrgLoad + nStrgLosses) / IntervalsPerHour

```
##### Specify the storage threshhold used to turn on baseload generation.
```
// The four months May to Aug have low average wind and solar generation and so
// may need more baseload support.
StrgThreshhold = IF(_5_minute_interval<DATE(year,9,1),
		    IF(_5_minute_interval<DATE(year,5,1),
		       Storage_Threshhold_Sep_to_Apr,
		       Storage_Threshhold_May_to_Aug),
		    Storage_Threshhold_Sep_to_Apr)
```
### Results
* [NEM ISP 2050 Simulation.xlsx](
   NEM%20ISP%202050%20Simulation.xlsx) (52MB)\
   A simulation of the ISP's NEM grid based on 2024's Hydro, Grid
   Solar, Wind, Rooftop Solar and Demand data from _NEM&nbsp;
   Analysis&nbsp;2024.xlsx_.
* [NEM ISP 2050 Analysis.xlsx](NEM%20ISP%202050%20Analysis.xlsx) (66MB)\
   Derives results from the simulation including 5-day rolling
   averages plus three charts.
* [NEM ISP 2050 Analysis.pdf](
   NEM%20ISP%202050%20Analysis.pdf)\
   Presents and discusses the results.

<hr style="margin-left:20%; width:60%; align-self:center" />

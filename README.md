# About
This is the repository containing the release and analysis of the power production and consumption data from the Smart Energy Network Demonstrator (SEND) facility at Keele University. 

# How to Use

## Installation 
To use this repository, please consider one of the following options:

[Fork and clone it](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo#forking-a-repository) by following [this link](https://github.com/SENDatKeele/Renewables2023/fork) or by copying the code

```bash
gh repo fork SENDatKeele/Renewables2023 --clone=true
```

Alternatively, [mirror it](https://docs.github.com/en/repositories/creating-and-managing-repositories/duplicating-a-repository#mirroring-a-repository) by coping and modifying the following code:

```bash
git clone --bare https://github.com/SENDatKeele/Renewables2023
```
```bash
cd Renewables2023
git push --mirror https://github.com/EXAMPLE-USER/NEW-REPOSITORY.git
```
```bash
cd ..
rm -rf Renewables2023
```

## Solcast data

### Downloading Solcast data

> #### ⚠️ N.B.
>
> To make full use of this repository, first consider exporting data from Solcast (available at [solcast.com](https://www.solcast.com)).

This should be an historical time series request with: 
- **Time granularity**: 5 minutes
- **Date period**: 01/01/2023 to 31/12/2023
- **Timezone**: UTC
- **Location**: 52.99728748753422, -2.2601603042521288

The following parameters should be included in your request: 
- **`air_temp`**
- **`gti`** (tilt of 35º, azimuth of 180º)
- **`surface_pressure`**
- **`wind_speed_100m`**

Once downloaded, you must upload it to the folder [`Renewables2023_Private/Private_Data/Solcast/Solcast.csv`](Renewables2023_Private/Private_Data/Solcast/Solcast.csv) and run the script [`Scripts/Utils/Format_Solcast.ipynb`](Scripts/Utils/Format_Solcast.ipynb). This will save a readable version of the data to the [`Renewables2023_Private/Private_Data/Solcast`](Renewables2023_Private/Private_Data/Solcast/) folder, with which you can run other scripts in the [`Scripts`](Scripts/) folder. 

### Key to Solcast Data
Below is a list of the names of parameters used (from the formatted version of the data) and their definitions (text sourced from Solcast, available [here](https://kb.solcast.com.au/glossary-of-terms))

- `DateTime` (index): Python-readable version of `period_end` (created using `pd.to_datetime()`)

- `air_temp`: Temperature $\left[\mathrm{^{\circ}C}\right]$: The temperature of the air in the given location (10 meters above ground level).

- `gti`: Global Tilted Irradiance $\left[\mathrm{Wm^{-2}}\right]$: Total irradiance received on a surface with defined tilt (35º) and azimuth (180º, south) (sum of direct, diffuse and reflected components), fixed or tracking.

- `surface_pressure`: Surface Pressure $\left[\mathrm{h\ Pa}\right]$: The air pressure at ground level.

- `wind_speed_100m`: Wind Speed $\left[\mathrm{ms^{-2}}\right]$: The wind speed (100 meters above ground level).

## DEOP Data
The main resource of this repository is the 2023 DEOP (Siemens Distributed Energy Optimization) data, available at [`Data/DEOP/2023_DEOP.csv`](Data/DEOP/2023_DEOP.csv). This is a processed version of monthly `.csv` files, formatted using the script [`Scripts/Utils/Format_DEOP.ipynb`](Scripts/Utils/Format_DEOP.ipynb).

Note that a further modified version, used by scripts, is provided at [`Data/DEOP/2023_DEOP_Interp.csv`](Data/DEOP/2023_DEOP_Interp.csv), where campus load is interpolated between values of `0` or `NaN` in order to provide more realistic values.

> ### ⚠️ N.B.
> These assumptions were chosen to be conservative and, in some cases, over estimate the campus load.

### Key to DEOP Data
Below is a list of the names of parameters used (in the formatted versions).

- `DateTime` (index): Python-readable version of `date0` and `time0` (found in the original data, created using `pd.to_datetime()`).

- `power-gen-ave`: Average power generated from the wind turbines and solar panels over a 5 minute period $\left[\mathrm{kW} \right]$.

- `power-con-ave`: Average power consumed by the campus over a 5 minute period $\left[\mathrm{kW} \right]$.
       
- `power-gen-wt-ave`: Average power generated from the wind turbines over a 5 minute period $\left[\mathrm{kW}\right]$.

- `power-gen-pv-ave`: Average power generated from the solar panels over a 5 minute period $\left[\mathrm{kW}\right]$.

## Scripts
The following describes what each file in the `Scripts` directory does.
- [**`Monthly_Power_Energy.ipynb`**](Scripts/Monthly_Power_Energy.ipynb)
    - This provides plots of consumed/produced power and energy, averaged (for power) and summed (for energy) over each month during 2023. Monthly values are then saved in tables.

- [**`Plot_Hydrogen_Simulations.ipynb`**](Scripts/Plot_Hydrogen_Simulations.ipynb)
    - This plots the outcomes of the [`Simulate_Hydrogen_Production.ipynb`](Scripts/Simulate_Hydrogen_Production.ipynb) script (saved in [`Data/Analysis/Simulations`](Data/Analysis/Simulations/)), saving the value for total expected hydrogen production per scenario to a table.
    > ### ⚠️ N.B.
    > Requires simulation data saved at [`Data/Analysis/Simulations`](Data/Analysis/Simulations/) from simulations run from [`Simulate_Hydrogen_Production.ipynb`](Scripts/Simulate_Hydrogen_Production.ipynb) to function correctly.

- [**`Renewable_Energy_Efficiency.ipynb`**](Scripts/Renewable_Energy_Efficiency.ipynb)
    - This calculates the average efficiency of the wind turbines and solar panels on campus by comparing the incident wind and solar power respectively (from Solcast data) comparing this (at each data point) to the relevant DEOP data (filtering likely-erroneous data: missing data points and those with wind speeds and irradiance values outside the range of production). 
    - Values are plotted and averaged (for use in [`Monthly_Power_Energy.ipynb`](Scripts/Monthly_Power_Energy.ipynb) and [`Seasonal_Plots.ipynb`](Scripts/Seasonal_Plots.ipynb)). 
    - For context, the distribution fo wind speeds and global tilted irradiance and the true and expected fractional activity of solar panels and wind turbines are also plotted.

    > ### ⚠️ N.B.
    > Requires Solcast saved at [`Renewables2023_Private/Private_Data/Solcast/Solcast.csv`](Renewables2023_Private/Private_Data/Solcast/Solcast.csv) to have full functionality. See [here](#solcast-data) for how to do this.

- [**`Review_All_Data.ipynb`**](Scripts/Review_All_Data.ipynb)
    - This provides a function to (over)plot all data available, allowing the user to gain a better understanding of the characteristics and potential challenges of data available.

- [**`Seasonal_Plots.ipynb`**](Scripts/Seasonal_Plots.ipynb)
    - A plot for each season is produced from data, highlighting key characteristics of the data.
    - **Spring:** A sunny spring day, showing curtailment of solar power when the $4.4\mathrm{\ MW}$ threshold is met and a more random `dip' pattern, likely due to variable cloud cover.
    - **Summer:** A sunny summer day, showing little cloud cover and curtailment of solar power during midday hours.
    - **Autumn:** A dull windy autumn day, showing little solar power production but significant wind power.
    - **Winter:** A sunny winter day, showing erratic solar power production due to cloud cover.

    > ### ⚠️ N.B.
    > Requires Solcast saved at [`Renewables2023_Private/Private_Data/Solcast/Solcast.csv`](Renewables2023_Private/Private_Data/Solcast/Solcast.csv) to have full functionality. See [here](#solcast-data) for how to do this. 

- [**`Simulate_Hydrogen_Production.ipynb`**](Scripts/Simulate_Hydrogen_Production.ipynb)
    - A script to simulate the production of hydrogen in various scenarios. To gain full understanding of the simulation, please see the notebook.

## Other Files
Below is an explanation of other files available.
- At [`Data/Analysis/Data_Tables/`](Data/Analysis/Data_Tables/), `csv` and `tex` outputs of scripts are saved.
    - `Monthly_Energy`
        - Monthly energy consumption, generation and excess $\left[\mathrm{MWh}\right]$
        - Outputted from [`Monthly_Power_Energy.ipynb`](Scripts/Monthly_Power_Energy.ipynb).
    - `Monthly_Power`
        - Monthly power consumption, production and excess $\left[\mathrm{kW}\right]$.
        - Outputted from [`Monthly_Power_Energy.ipynb`](Scripts/Monthly_Power_Energy.ipynb).
    - `VaryBattery`
        - Mass of hydrogen and that relative to the size of the power source modelled for varying battery sizes.
        - Uses [`Simulate_Hydrogen_Production.ipynb`](Scripts/Simulate_Hydrogen_Production.ipynb).
        - Outputted from [`Plot_Hydrogen_Simulations.ipynb`](Scripts/Plot_Hydrogen_Simulations.ipynb).
    - `VaryElectrolyser`
        - Mass of hydrogen and that relative to the size of the power source modelled for varying electrolyser sizes.  
        - Uses [`Simulate_Hydrogen_Production.ipynb`](Scripts/Simulate_Hydrogen_Production.ipynb)
        - Outputted from [`Plot_Hydrogen_Simulations.ipynb`](Scripts/Plot_Hydrogen_Simulations.ipynb).
    - `statistics`
        - Mean (`mean`) and standard deviation (`stdev`) 
            - Outputted from [`Renewable_Energy_Efficiency.ipynb`](Scripts/Renewable_Energy_Efficiency.ipynb)
            - Wind turbine (`eta_Wind`) and solar panel (`eta_Solar`) efficiencies.
            - Wind speed $\left[\mathrm{ms^{-1}}\right]$ (`Wind Speed [m/s]`).
            - Global tilted irradiance $\left[\mathrm{Wm^{-2}}\right]$ (`Global Tilted Irradiance [W/m2]`).
- At [`Data/Analysis/Simulations/`](Data/Analysis/Simulations/)
    - outputs of [`Simulate_Hydrogen_Production.ipynb`](Scripts/Simulate_Hydrogen_Production.ipynb) are stored for use in [`Plot_Hydrogen_Simulations.ipynb`](Scripts/Plot_Hydrogen_Simulations.ipynb).
    - `Battery` refers to varying the battery capacity (titles of `csv` files are battery capacities in $\mathrm{kWh}$).
    - `Electrolyser` refers to varying the electrolyser size (titles of `csv` files are electrolyser size in $\mathrm{kW}$).
    - `Excess` are those simulations using excess energy (after campus load) to generate hydrogen.
    - `Solar` are those simulations using solar power (minus a $100\mathrm{kW}$ load) to generate hydrogen.
    - `Wind`  are those simulations using wind power (minus a $100\mathrm{kW}$ load) to generate hydrogen.
- At [`Figures/`](Figures/).
    - [`pdf`](Figures/pdf/): PDF outputs from scripts (with no title).
    - [`png`](Figures/png/): PNG outputs from scripts (with title).
- At [`Data/Analysis/Const`](Data/Analysis/Const/).
    - A table of constants (in `md` and `csv` format) containing all constants used in this repository, indicating their units, where they are used, their source and relevance.
    > ### ⚠️ N.B.
    > To adapt this repository to another case study, some of these constants should be updated to match the new specification.
- At [`Hydrogen_Flowchart.md`](Hydrogen_Flowchart.md).
    - A flowchart illustrate how the script in [`Simulate_Hydrogen_Production.ipynb`](Scripts/Simulate_Hydrogen_Production.ipynb) simulates the production of hydrogen. 
# Contributors

# Citation

# License
This projects is licenced under the MIT License, as detailed [here](LICENSE).
# Project Status
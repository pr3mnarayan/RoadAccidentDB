# RoadAccidentDB

This project involves designing and creating a multi-table MySQL database for 2022 road accident data sourced from data.gov.in. It includes detailed definitions, data import processes, and a series of SQL-based analyses to uncover key patterns and trends. Insights are further visualized through interactive Power BI dashboards. The goal is to identify patterns that highlight **when**, **where**, and **how** accidents occur most frequently.

---

## 📌 Project Objectives

- Identify **most accident-prone states** in India.
- Find the **deadliest time slots** and **months** for road accidents.
- Compare accident data across **road types** (NH, SH, Others).
- Analyze **modes of transport** with the highest accident and fatality rates.
- Visualize insights in Power BI dashboards
- Practice SQL skills through real-world data.

---

## 📁 Dataset

- **Source:** [Accidental Deaths and Suicides in India 2022 - NCRB](https://www.data.gov.in/catalog/accidental-deaths-suicides-india-adsi-2022)
- **Format:** Cleaned `.csv` files by category
- **Scope:** Road accidents only (railway-related data excluded)
- **Level:** State-wise data for all Indian states and UTs

> 📌 **Note:** One dataset (`state&Transport2021.csv`) uses **2021 data**, as state-wise breakdown by transport mode is not available for 2022.

### 🧾 Data Categories

| File Name                   | Description                                |
|-----------------------------|--------------------------------------------|
| `monthWise2022.csv`         | Accidents by month per state               |
| `timeWise2022.csv`          | Accidents by time slot per state           |
| `roadType2022.csv`          | Accidents by road type (NH/SH/Other)       |
| `modeOfTransport2022.csv`   | Accidents by vehicle/transport type        |
| `state&Transport2021.csv`   | Accidents by mode of transport per state   |

---

## 🧱 Database Schema

The following tables were created in MySQL Workbench:

- `month_wise_accidents`
- `time_wise_accidents`
- `road_type_accidents`
- `transport_mode_accidents`
- `transport_mode_by_state_2021`

Each table contains:

- `id` (Primary Key)
- `State_UT` (transport_mode_accidents does not contain State_UT column)
- Category-specific columns (e.g. `January`, `Evening_6pm_to_9pm`, `National_Highway`, `Two_Wheelers`, etc.)
- `Total_Accidents`, `Total_Killed`, `Total_Injured` (if applicable)

---

## 💻 Tools & Technologies

- **MySQL Workbench** (database setup, import, queries)
- **Excel** (data preprocessing)
- **Power BI** (Visualization)
- **Git & GitHub** (version control and sharing)
- **Markdown** (for documentation)

---

## 🏗️ Database Setup

### 1. Create the Database

```sql
CREATE DATABASE road_accident_analysis;
USE road_accident_analysis;
```

### 2. Create month_wise_accidents table

```sql
CREATE TABLE month_wise_accidents (
  id INT PRIMARY KEY AUTO_INCREMENT,
  State_UT VARCHAR(50),
  January INT,
  February INT,
  March INT,
  April INT,
  May INT,
  June INT,
  July INT,
  August INT,
  September INT,
  October INT,
  November INT,
  December INT,
  Total INT
);
```
### 3. Import cleaned month wise accidents data into 'month_wise_accidents' table

-Use **MySQL Workbench's Table Data Import Wizard** to import cleaned CSV file into its table in the `road_accident_analysis` database.

### 4. Create transport_mode_accidents table

```sql
CREATE TABLE transport_mode_accidents (
    id INT PRIMARY KEY AUTO_INCREMENT,
    mode_of_transport VARCHAR(100),
    injured INT,
    died INT,
    percentage_injured DECIMAL(5,2),
    percentage_deaths DECIMAL(5,2)
    
);
```
### 5. Create road_type_accidents table

```sql
CREATE TABLE road_type_accidents (
    id INT PRIMARY KEY,
    State_UT VARCHAR(50),

    National_Highways_Cases INT,
    National_Highways_Injured INT,
    National_Highways_Died INT,

    State_Highways_Cases INT,
    State_Highways_Injured INT,
    State_Highways_Died INT,

    Expressways_Cases INT,
    Expressways_Injured INT,
    Expressways_Died INT,

    Other_Roads_Cases INT,
    Other_Roads_Injured INT,
    Other_Roads_Died INT,

    Total_Cases INT,
    Total_Injured INT,
    Total_Died INT
);
```
### 6. Create transport_mode_by_state_2021 table

```sql
CREATE TABLE transport_mode_by_state_2021 (
    id INT PRIMARY KEY,
    State_UT VARCHAR(50),

    Truck_Lorry_MiniTruck_Injured INT,
    Truck_Lorry_MiniTruck_Died INT,

    Bus_Injured INT,
    Bus_Died INT,

    SUV_Car_Jeep_etc_Injured INT,
    SUV_Car_Jeep_etc_Died INT,

    Tractor_Injured INT,
    Tractor_Died INT,

    ThreeWheeler_AutoRickshaw_Injured INT,
    ThreeWheeler_AutoRickshaw_Died INT,

    TwoWheeler_Injured INT,
    TwoWheeler_Died INT,

    Other_Motor_Vehicles_Injured INT,
    Other_Motor_Vehicles_Died INT,

    Bicycle_Injured INT,
    Bicycle_Died INT,

    HandDrawnVehicle_CycleRickshaw_Injured INT,
    HandDrawnVehicle_CycleRickshaw_Died INT,

    AnimalDrawnVehicle_Injured INT,
    AnimalDrawnVehicle_Died INT,

    Others_Injured INT,
    Others_Died INT,

    Pedestrian_Injured INT,
    Pedestrian_Died INT,

    Total_Injured INT,
    Total_Died INT
);
```
### 7. Create time_wise_accidents table

```sql
CREATE TABLE time_wise_accidents (
    id INT PRIMARY KEY,
    State_UT VARCHAR(50),

    RoadAccidents_0_3 INT,
    RoadAccidents_3_6 INT,
    RoadAccidents_6_9 INT,
    RoadAccidents_9_12 INT,
    RoadAccidents_12_15 INT,
    RoadAccidents_15_18 INT,
    RoadAccidents_18_21 INT,
    RoadAccidents_21_24 INT,

    RoadAccidents_Total INT
);
```

### 8. Similar to step 3, import data into their respective tables using MySQL Workbench's Table Data Import Wizard.

---

## 🚀 Sample Queries

### 1. Most Fatal Road Type in Each State

```sql
select 
    State_UT,
    GREATEST(
        National_Highways_Died,
        State_Highways_Died,
        Expressways_Died,
        Other_Roads_Died
    ) as Max_Deaths,

    CASE GREATEST(
        National_Highways_Died,
        State_Highways_Died,
        Expressways_Died,
        Other_Roads_Died
    )
        when National_Highways_Died then 'National Highway'
        when State_Highways_Died then 'State Highway'
        when Expressways_Died then 'Expressway'
        when Other_Roads_Died then 'Other Roads'
    END AS Most_Fatal_Road_Type

FROM road_type_accidents
ORDER BY State_UT;
```

### 2. State-Wise Severity Index

```sql
select
    State_UT,
    Total_Injured,
    Total_Died,
    ROUND(Total_Died / Total_Injured, 3) as Severity_Index
from road_type_accidents
where Total_Injured > 0
ORDER BY Severity_Index DESC;
```
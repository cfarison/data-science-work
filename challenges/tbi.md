Traumatic Brain Injury Trends
================
Charlie Farison
2020-08-08

  - [Background](#background)
  - [Data Dictionaries](#data-dictionaries)
  - [Initial Data Exploration](#initial-data-exploration)

*Purpose*: We’d like to understand trends in causes of traumatic brain
injury.

# Background

<!-- -------------------------------------------------- -->

Brain Injury Awareness Month, observed each March, was established 3
decades ago to educate the public about the incidence of brain injury
and the needs of persons with brain injuries and their families. Caused
by a bump, blow, or jolt to the head, or penetrating head injury, a
traumatic brain injury (TBI) can lead to short- or long-term changes
affecting thinking, sensation, language, or emotion.

TBI is very common. One of every 60 people in the U.S. lives with a TBI
related disability. Moderate and severe traumatic brain injury (TBI) can
lead to a lifetime of physical, cognitive, emotional, and behavioral
changes.

**Sources:**

  - CDC: <https://www.cdc.gov/mmwr/volumes/68/wr/mm6810a1.htm>
  - TidyTuesday:
    <https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-03-24/readme.md>

# Data Dictionaries

<!-- -------------------------------------------------- -->

**tbi\_age**

| Variable          | Class     | Description                      |
| ----------------- | --------- | -------------------------------- |
| age\_group        | character | Age group                        |
| type              | character | Type of measure                  |
| injury\_mechanism | character | Injury mechanism                 |
| number\_est       | double    | Estimated observed cases in 2014 |
| rate\_est         | double    | Rate/100,000 in 2014             |

**tbi\_year**

| Variable          | Class     | Description                           |
| ----------------- | --------- | ------------------------------------- |
| injury\_mechanism | character | Injury mechanism                      |
| type              | character | Type of measure                       |
| year              | character | Year (2006 - 2014)                    |
| rate\_est         | double    | Rate/100,000 in 2014                  |
| number\_est       | integer   | Estimated observed cases in each year |

type has 3 possible values:

  - Emergency Department Visit  
  - Hospitalizations  
  - Deaths

injury\_mechanism has 7 possible values:

  - Motor Vehicle Crashes  
  - Unintentional Falls  
  - Unintentionally struck by or against an object  
  - Other unintentional injury, mechanism unspecified  
  - Intentional self-harm  
  - Assault  
  - Other or no mechanism specified

**tbi\_military**

| Variable  | Class     | Description                                 |
| --------- | --------- | ------------------------------------------- |
| service   | character | Military branch                             |
| component | character | Military component (active, guard, reserve) |
| severity  | character | Severity/type of TBI                        |
| diagnosed | double    | Number diagnosed                            |
| year      | integer   | Year for observation (2006 - 2014)          |

service has 4 possible values:

  - Army  
  - Navy  
  - Air Force  
  - Marines

component has 3 possible values:

  - Active  
  - Guard  
  - Reserve

severity has 5 possible values:

  - Penetrating  
  - Severe  
  - Moderate  
  - Mild  
  - Not Classifiable

# Initial Data Exploration

<!-- -------------------------------------------------- -->

*Questions to explore:*

  - What is the most common injury mechanism? Has that changed over
    time?
  - Does the most common injury mechanism vary by age group?
  - Do certain type of measures correlate with certain injury
    mechanisms?
  - Do certain type of measures correlate with certain ages?
  - Which service in the military has the highest rate of TBI? Has that
    changed over time?
  - Which service in the military has the most severe TBIs? Has that
    changed over time?
  - Which component of the military has the most TBIs? Does it vary by
    service or over time?

# Overview of sample input data schema to calculate OEE for manufacturing lines

## Input Data Schema

### Assets

In real world, assets can have more hierarchies such as plants, lines, cells, machines and more. This file stores the simplified asset mapping between manufacturing sites (plants, factories) and the production lines in each site. `Line_Name` is unique across all sites, and serves as the unique key for this table.

| Site_Name | Site_Description | Line_Name | Line_Description |
| :-------- | :--------------- | :-------- | :--------------- |
| s1        | seattle westlake | p01       | pike01           |
| s1        | seattle westlake | p02       | pike02           |
| s2        | seattle eastlake | p03       | pike03           |

### Shifts

Shifts could also vary by manufactures, plants, lines, and could have more complex schedules. Here we assume each shift is 8 hours, and there are 3 shifts every day, including weekends and holidays. Unique key of the table is `Shift_Name`.

| Shift_Name | Start_Hour | End_Hour |
| :--------- | :--------- | :------- |
| a          | 00:00      | 07:59    |
| b          | 08:00      | 15:59    |
| c          | 16:00      | 23:59    |

### Equipment Faults

This file contains the fault name and description mapping. Faults could be the cause of production downtime. Unique key is `Fault Code`.

| Fault_Code | Fault_Description  |
| :--------- | :----------------- |
| f01        | door jam           |
| f02        | out of glue        |
| f03        | wire loose         |
| f04        | electrical problem |
| f05        | over heat          |

### Production Line Measurement Timeseries

This file represents performance and quality data collected from the production lines.

- `Timestamp`: UTC timestamp, microsecond precision.
- `Set_Point_Speed`: Target speed of the production line. For example, 100 means 100 parts per time unit.
- `Actual_Speed`: Actual speed of the production line. If actual speed is below certain threshold, then the production line is considered "down", which is used to compute the _availability_ component of OEE. Also the actual speed as a percentage of the set point is used to calculate the _performance_ component of OEE.
- `Line_Pass_Fail`: Number of parts produced at the timestamp that are considered good or defect. This is used to calculate the _quality_ component of OEE.
- `Machine{X}_Pass_Fail`: Number of parts passing through individual machines on the production line that are considered good or defect. Defects from individual machines contribute to the overall defects on a production line. However, each defect reported by a Machine doesn't necessarily lead to a defect on the line.

| Line_Name | Timestamp               | Set_Point_Speed | Actual_Speed | Line_Pass_Fail | Machine1_Pass_Fail | Machine2_Pass_Fail | Machine3_Pass_Fail |
| :-------- | :---------------------- | :-------------- | :----------- | :------------- | :----------------- | :----------------- | :----------------- |
| p01       | 2021-04-01T12:01:23.000 | 100             | 99           | 1              | 1                  | 0                  | 1                  |
| p01       | 2021-04-01T12:01:23.050 | 100             | 100          | 1              | 1                  | 0                  | 1                  |
| p01       | 2021-04-01T12:01:23.100 | 100             | 99           | 0              | 0                  | 0                  | 1                  |
| p01       | 2021-04-01T12:01:24.000 | 100             | 95           | 1              | 1                  | 1                  | 1                  |
| p01       | 2021-04-01T12:01:24.100 | 100             | 89           | 1              | 1                  | 1                  | 1                  |
| p02       | 2021-04-01T12:01:23.000 | 100             | 100          | 1              | 1                  | 0                  | 1                  |
| p02       | 2021-04-01T12:01:23.020 | 100             | 99           | 1              | 1                  | 0                  | 1                  |
| p02       | 2021-04-01T12:01:23.110 | 100             | 99           | 1              | 1                  | 0                  | 0                  |
| p02       | 2021-04-01T12:01:24.000 | 100             | 95           | 1              | 1                  | 1                  | 1                  |
| p02       | 2021-04-01T12:01:24.120 | 100             | 89           | 1              | 1                  | 1                  | 1                  |

### Equipment Faults Timeseries

This file contains the time period for each fault on each line. Faults cause downtime of production lines. Note that this info represents _events_ with a begin and end time rather than timeseries data with a point of time.

| Line_Name | Fault_Code | Start_Time              | End_Time                |
| :-------- | :--------- | :---------------------- | :---------------------- |
| p01       | f01        | 2021-04-01T12:01:23.000 | 2021-04-01T12:01:24.100 |
| p02       | f02        | 2021-04-01T12:01:23.500 | 2021-04-01T12:01:24.898 |

## How is OEE calculated?

- **Availability**: uptime/total time
- **Performance**: actual_speed/target_speed
- **Quality**: good_parts/total_parts
- **OEE**: Availability X Performance X Quality

More important than the numbers themselves are the insights into causes for downtime and defects. Only when the reasons for downtime and defects are understood, can the manufacturing process be effectively improved.

- **Cause of Downtime**: which fault(s) lasted longest or happened most frequently
- **Cause of Defect**: which machine(s) produced most of the defects

Additional sample questions that can be answered by this data:

- Which shift has the best performance on each line? Why?
- Why is one line performing better than the other?

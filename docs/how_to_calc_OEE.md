# Overview of sample input data schema to calculate OEE for manufacturing lines

## Input Data Schema

### Assets

In real world assets can have more hierarchies such as plants, lines, cells, machines and more. In this sample, we store simplified asset mapping between manufacturing sites (plants, factories) and the production lines in each site. `Line_Name` is unique across all sites, and serves as the unique key for this file.

| Site_Name | Site_Description | Line_Name | Line_Description |
| :-------- | :--------------- | :-------- | :--------------- |
| s1        | seattle westlake | p01       | pike01           |
| s1        | seattle westlake | p02       | pike02           |
| s2        | seattle eastlake | p03       | pike03           |

### Shifts

Shifts could also vary by manufactures, plants, lines, and could have more complex schedules. In this sample we assume each shift is 8 hours, and there are 3 shifts every day, including weekends and holidays. Unique key of this file is `Shift_Name`.

| Shift_Name | Start_Hour | End_Hour |
| :--------- | :--------- | :------- |
| a          | 00:00      | 07:59    |
| b          | 08:00      | 15:59    |
| c          | 16:00      | 23:59    |

### Equipment Faults

This file contains the fault name and description mapping. In this sample, we assume faults are the main cause of production downtime. Unique key is `Fault Code`.

| Fault_Code | Fault_Description  |
| :--------- | :----------------- |
| f01        | door jam           |
| f02        | out of glue        |
| f03        | wire loose         |
| f04        | electrical problem |
| f05        | over heat          |

### Production Timeseries Data

This file represents performance and quality data collected from the production lines. `Line_Name` and `Timestamp` combined form the unique key.

- `Timestamp`: UTC timestamp. In this sample, we assume data comes in per second, although at microsecond precision.
- `Set_Point_Speed`: Target speed of the production line. For example, 100 means 100 parts per second.
- `Actual_Speed`: Actual speed of the production line. If actual speed is below certain threshold, for example, 80%, then the production line is considered "down". This is used to compute the _availability_ component of OEE. Also, the actual speed as a percentage of the set point is used to calculate the _performance_ component of OEE.
- `Line_Pass`: Number of parts produced at the timestamp that passed quality check. This is used to calculate the _quality_ component of OEE.
- `Line_Fail`: Number of parts produced at the timestamp that failed quality check. This is used to calculate the _quality_ component of OEE.
- `Machine{X}_Pass`: Number of parts passing through individual machines on a production line that passed the quality check. Defects from individual machines contribute to the overall defects on a production line. However, each defect reported by a machine doesn't necessarily lead to a defect on the line.

| Line_Name | Timestamp               | Set_Point_Speed | Actual_Speed | Line_Pass | Line_Fail | Machine1_Pass | Machine2_Pass | Machine3_Pass |
| :-------- | :---------------------- | :-------------- | :----------- | :-------- | :-------- | :------------ | :------------ | :------------ |
| p01       | 2021-04-01T12:01:23.000 | 100             | 99           | 5         | 2         | 7             | 5             | 7             |
| p01       | 2021-04-01T12:01:23.050 | 100             | 100          | 5         | 0         | 5             | 5             | 5             |
| p01       | 2021-04-01T12:01:23.100 | 100             | 99           | 0         | 1         | 0             | 0             | 1             |
| p01       | 2021-04-01T12:01:24.000 | 100             | 95           | 1         | 0         | 1             | 1             | 1             |
| p01       | 2021-04-01T12:01:24.100 | 100             | 89           | 10        | 0         | 10            | 10            | 10            |
| p02       | 2021-04-01T12:01:23.000 | 100             | 100          | 11        | 1         | 12            | 11            | 12            |
| p02       | 2021-04-01T12:01:23.020 | 100             | 99           | 3         | 0         | 2             | 3             | 1             |
| p02       | 2021-04-01T12:01:23.110 | 100             | 99           | 2         | 1         | 1             | 2             | 2             |
| p02       | 2021-04-01T12:01:24.000 | 100             | 95           | 10        | 1         | 9             | 10            | 11            |
| p02       | 2021-04-01T12:01:24.120 | 100             | 89           | 1         | 0         | 1             | 1             | 1             |

### Equipment Faults Timeseries

This file contains the time period for each fault on each line. Faults cause downtime of production lines. Note that this info represents _events_ with a begin and end time rather than timeseries data at a point of time. Unique key is a combination of `Line_Name`, `Fault_Code`, and `Start_Time`.

| Line_Name | Fault_Code | Start_Time              | End_Time                |
| :-------- | :--------- | :---------------------- | :---------------------- |
| p01       | f01        | 2021-04-01T12:01:23.000 | 2021-04-01T12:01:24.100 |
| p02       | f02        | 2021-04-01T12:01:23.500 | 2021-04-01T12:01:24.898 |

## How is OEE calculated?

- **Availability**: uptime/total time
- **Performance**: actual_speed/set_point_speed
- **Quality**: good_parts/total_parts, total number of parts produced is calculated as `Line_Pass` + `Line_Fail`
- **OEE**: Availability X Performance X Quality

More important than the numbers themselves are the insights into causes for downtime and defects. Only when the reasons for downtime and defects are understood, can the manufacturing process be effectively improved.

- **Cause of Downtime**: which fault(s) lasted longest or happened most frequently
- **Cause of Defect**: which machine(s) produced most of the defects

Additional sample questions that can be answered by this data:

- Which shift has the best performance on each line? Why?
- Why is one line performing better than the other?

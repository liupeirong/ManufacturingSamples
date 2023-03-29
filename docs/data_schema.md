# Input/Output data schema as a sample to calculate OEE for manufacturing lines

## Assets

| Site_Name | Site_Description | Line_Name | Line_Description |
| :-------- | :--------------- | :-------- | :--------------- |
| a         | b                | c         | d                |

## Shifts

| Line_Name | Shift_Name | Start_Time | End_Time |
| :-------- | :--------- | :--------- | :------- |

## Equipment Faults

| Fault_Code | Fault_Description |
| :--------- | :---------------- |

## Production Line Measurement Timeseries

| Line_Name | Timestamp | Set_Point_Speed | Actual_Speed | Line_Pass_Fail | Machine1_Pass_Fail | Machine2_Pass_Fail | Machine3_Pass_Fail |
| :-------- | :-------- | :-------------- | :----------- | :------------- | :----------------- | :----------------- | :----------------- |

## Equipment Fault Events

| Line_Name | Fault_Code | Start_Time | End_Time |
| :-------- | :--------- | :--------- | :------- |

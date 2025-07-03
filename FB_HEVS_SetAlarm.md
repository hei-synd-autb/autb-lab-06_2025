<h1 align="left">
  <br>
  <img src="./img/hei-en.png" alt="HEI-Vs Logo" width="350">
  <br>
  Industrial Automation Base
  <br>
</h1>

Course AutB

Author: [Cédric Lenoir](mailto:cedric.lenoir@hevs.ch)

# HEVS Alarm

-   You can use a Warning **ID** between 10 and 99.
-   Each **ID** must be unique for each alarm
-   **Value** is free for user
-   **Message** is free for user.
-   **bAckAlarmTrig** must be FALSE
-   **bSetAlarm** depends of condition of the program
-   **Category** peut être sélectionner selon les niveaus suivants de **E_EventCategory**
    - Complete
    - Abort
    - Stop
    - Hold
    - Suspend
-   **plcDateTimePack**, **stAdminAlarm** and **stAdminAlarmHistory** must be as usage below


## Declaration
```iecst
   fbSetAlarm_SafetyOpen : FB_HEVS_SetAlarm;
```

## Usage
Write your alarms in **PRG_Student ACT_WarningAndAlarms**

## Must be updated from PackTag v1.0.0

```iecst
(*
	Call some alarms for test
*) 
fbSetAlarm_0(// Bit activation of Alarm and Ack
             xSetAlarm := PackTag.hevsPackAlarm_UI.uiSetAlarm_0,
             xAckAlarmTrig := PackTag.hevsPackAlarm_UI.uiAckAlarm_0 OR FC_HEVS_GetAckAlarmById(1),
             // Alarm Parameters
             ID := 1,
             Value := 35,
             Message := 'Abort 4, E-Stop',
             Category := E_EventCategory.Abort,
             // Reference to plc time from PackTag
             plcDateTimePack	:= PackTag.Admin.PLCDateTime,
             // Link to PackTag Admin
             stAdminAlarm := PackTag.Admin.Alarm,
             stAdminAlarmHistory := PackTag.Admin.AlarmHistory);
```
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

```iecst
(*
	Safety loop open
*)
fbSetAlarm_SafetyOpen(bSetAlarm := NOT GVL_Abox.uaAboxInterface.uaButtonAndSignal.In_Estop,
                      bAckAlarmTrig := FALSE,
                      ID := 10,
                      Value := 1,
                      Message := 'E-Stop or Door open',
                      Category := E_EventCategory.Abort,
                      // Reference to plc time from PackTag
                      plcDateTimePack	:= PackTag.Admin.PLCDateTime,
                      // Link to PackTag Admin
                      stAdminAlarm := PackTag.Admin.Alarm,
                      stAdminAlarmHistory := PackTag.Admin.AlarmHistory);
```
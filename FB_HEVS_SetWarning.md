<h1 align="left">
  <br>
  <img src="./img/hei-en.png" alt="HEI-Vs Logo" width="350">
  <br>
  Industrial Automation Base
  <br>
</h1>

Course AutB

Author: [Cédric Lenoir](mailto:cedric.lenoir@hevs.ch)

# HEVS Warning

-   You can use a Warning **ID** between 10 and 99.
-   Each **ID** must be unique for each warning.
-   **Value** is free for user
-   **Message** is free for user.
-   **bAckWarningTrig** must be FALSE
-   **bSetWarning** depends of condition of the program
-   **Category**, **plcDateTimePack** and **stAdminWarning** must be as usage below


## Declaration
```iecst
   fbSetWarning_SystemHold   : FB_HEVS_SetWarning;
```

## Usage

Write your warnings in **PRG_Student ACT_WarningAndAlarms**

```iecst
(*
	Call some warnings for test
*)
fbSetWarning_SystemHold(bSetWarning := (actualState = E_PackState.eHeld),
                        bAckWarningTrig := FALSE,
                        // Warning Parameters
                        ID := 11,
                        Value := 31,
                        Message := 'System Hold, Unhold to restart',
                        Category := E_EventCategory.Warning,
                        // Reference to plc time from PackTag
                        plcDateTimePack	:= PackTag.Admin.PLCDateTime,
                        // Link to PackTag Admin
                        stAdminWarning := PackTag.Admin.Warning);
```
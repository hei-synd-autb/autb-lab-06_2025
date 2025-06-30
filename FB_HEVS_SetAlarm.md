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

```iecst
(*
	If the function is called with input bSetAlarm = TRUE, 
	the function verifies whether the specified alarm message 
	is already in the list of active alarms.

	If not, the function obtains the RTC of the controller
	and writes it together with the specified alarm message
	into the list of active alarms.

	If the maximum number of messages in the list is reached,
	no new message is added and bMaxNbOfAlarmReached is set to TRUE.
*)

rTrigTime(CLK := bSetAlarm);
IF rTrigTime.Q THEN
	dateTimeAtTrig := plcDateTimePack;
END_IF


IF ID = 0 THEN
	stErrorString := 'Event ID cannot be 0';
END_IF

IF bSetAlarm THEN
	ThisTrigger := TRUE;
	// Loop in stAdminAlarm to check if alarm ID exist is stAdminAlarm
	// I could use WHILE, but I use FOR..LOOP to have a constant time check.
	bThisEventExistsInBuffer := FALSE;
	diFirstEmptyBufferId := 0;
	bFoundFirstEventFalse := FALSE;
	FOR diAlarmLoop := 0 TO HEVS_PackTag_GVL.C_ADMIN_MAXALARMS BY 1 DO
		IF (stAdminAlarm[diAlarmLoop].ID = ID) THEN
			bThisEventExistsInBuffer := TRUE;
		END_IF;

		// Detect first empty index of buffer
		IF NOT stAdminAlarm[diAlarmLoop].Trigger AND
		   NOT bFoundFirstEventFalse              THEN	
			bFoundFirstEventFalse := TRUE;
			diFirstEmptyBufferId := diAlarmLoop;
		END_IF
	END_FOR
	
	// Check if buffer is Full
	bMaxNbOfAlarmReached := NOT bThisEventExistsInBuffer AND
	                        NOT bFoundFirstEventFalse;

	// If NOT bThisEventExistsInBuffer
	IF NOT bMaxNbOfAlarmReached 	AND
	   NOT bThisEventExistsInBuffer THEN
	   stAdminAlarm[diFirstEmptyBufferId].Trigger := TRUE;
	   stAdminAlarm[diFirstEmptyBufferId].ID := ID;
	   stAdminAlarm[diFirstEmptyBufferId].Value := Value;
	   stAdminAlarm[diFirstEmptyBufferId].Message := Message;
	   stAdminAlarm[diFirstEmptyBufferId].Category := Category;
	   stAdminAlarm[diFirstEmptyBufferId].DateTime := dateTimeAtTrig;
	   // Force reset Ack when written.
	   stAdminAlarm[diFirstEmptyBufferId].AckDateTime := DT#1970-1-1-0:0:0;
	
	ELSE
	   ;	// Do nothing if event exist or buffer full.
	END_IF
ELSE // This Event is no more active with bSetAlarm FALSE
	ThisTrigger := FALSE;
	
	// IF alarm is no more active, it must be canceled, then
	// the buffer should be modifed to remove empty space
	// by changing place of all events after it.
	bThisEventExistsInBuffer := FALSE;
	FOR diAlarmLoop := 0 TO HEVS_PackTag_GVL.C_ADMIN_MAXALARMS BY 1 DO
		IF stAdminAlarm[diAlarmLoop].ID = ID THEN
			bThisEventExistsInBuffer := TRUE;
			diBufferIdToRemove := diAlarmLoop;
		END_IF
	END_FOR
	
	// Before to Erase the Alarm, This alarm is stored in History.
	IF bThisEventExistsInBuffer THEN
		// Move all Event form Buffer(n-1) to Buffer(n)
		// Except the last one which will be lost (ALARM_HISTORY_EXTENT + 1).
		// As this buffer will be often full, there is no reason to use
		// something else than a FOR LOOP
		FOR diBufferHistoryLoop := HEVS_PackTag_GVL.C_ADMIN_MAXHISTORYALARMS TO 1 BY -1 DO
			stAdminAlarmHistory[diBufferHistoryLoop] := stAdminAlarmHistory[diBufferHistoryLoop - 1];
		END_FOR
		// Write This Alarm in Alarm History before to erase it from Alarms.
		stAdminAlarmHistory[0] := stAdminAlarm[diBufferIdToRemove];
	END_IF
	
	// Event exist in buffer Erase it.
	IF bThisEventExistsInBuffer THEN
		stAdminAlarm[diBufferIdToRemove].AckDateTime := DT#1970-1-1-0:0:0;
		stAdminAlarm[diBufferIdToRemove].Category := 0;
		stAdminAlarm[diBufferIdToRemove].DateTime := DT#1970-1-1-0:0:0;
		stAdminAlarm[diBufferIdToRemove].ID := 0;
		stAdminAlarm[diBufferIdToRemove].Message := '';
		stAdminAlarm[diBufferIdToRemove].Trigger := FALSE;
		stAdminAlarm[diBufferIdToRemove].Value := 0;

		// Remove empty index in buffer by moving next alarms.
		// IF positions in buffer are not empty (trigger false)
		diAlarmLoop := diBufferIdToRemove;
		
		WHILE diAlarmLoop < HEVS_PackTag_GVL.C_ADMIN_MAXALARMS AND 
              stAdminAlarm[diAlarmLoop + 1].Trigger       DO
			// Cannot be out of buffer because diAlarmLoop < GVL_PackConstant.ALARM_EXTENT
			// Move buffer (n+1) to buffer(n)
			stAdminAlarm[diAlarmLoop] := stAdminAlarm[diAlarmLoop + 1]; 
			diAlarmLoop := diAlarmLoop + 1;
		END_WHILE
		
		// Erase last position on buffer.
		IF diAlarmLoop <> diBufferIdToRemove THEN
			stAdminAlarm[diAlarmLoop].AckDateTime := DT#1970-1-1-0:0:0;
			stAdminAlarm[diAlarmLoop].Category := 0;
			stAdminAlarm[diAlarmLoop].DateTime := DT#1970-1-1-0:0:0;
			stAdminAlarm[diAlarmLoop].ID := 0;
			stAdminAlarm[diAlarmLoop].Message := '';
			stAdminAlarm[diAlarmLoop].Trigger := FALSE;
			stAdminAlarm[diAlarmLoop].Value := 0;
		END_IF
	END_IF 
END_IF

(*
	Set AckDateTime
	Do not set if NOT bSetAlarm because Event was removed with the above code.
*)
rTrigAck(CLK := bAckAlarmTrig);

IF rTrigAck.Q AND bSetAlarm THEN
	FOR diAlarmLoop := 0 TO HEVS_PackTag_GVL.C_ADMIN_MAXALARMS BY 1 DO
		IF (stAdminAlarm[diAlarmLoop].ID = ID) THEN
			stAdminAlarm[diAlarmLoop].AckDateTime := plcDateTimePack;
		END_IF
	END_FOR	
END_IF


```
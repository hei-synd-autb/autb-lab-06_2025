<h1 align="left">
  <br>
  <img src="./img/hei-en.png" alt="HEI-Vs Logo" width="350">
  <br>
  Industrial Automation Base
  <br>
</h1>

Course AutB

Author: [Cédric Lenoir](mailto:cedric.lenoir@hevs.ch)

# Function Blocks for Gripper


## FB_GripperState

```iecst
FUNCTION_BLOCK FB_GripperState
VAR_INPUT
	// Enable Function Blocl
	Enable    : BOOL;
	// Threshold for gripper is open
	thOpen    : WORD := 50;
	// Threshold for gripper is closed
	thClose   : WORD := 950;
	// Threshold for part present in gripper minimum
	thPartMin : WORD := 800;
	// Threshold for part present in gripper maximum
	thPartMax : WORD := 860;
END_VAR
VAR_IN_OUT
	hw: UA_Schunk_mms;
END_VAR
VAR_OUTPUT
	/// Default Outputs 
	InOperation   : BOOL;
	Error         : BOOL;
	IsOpen        : BOOL;
	IsClosed      : BOOL;
	PartPresent   : BOOL;
	// Position of sensor on gripper
	SensorGripper : REAL;
END_VAR

```

---

## FB_OpenGripper

```iecst
FUNCTION_BLOCK FB_OpenGripper

VAR_INPUT
	Execute: BOOL;
	// Threshold for gripper is open    
	thOpenMax: WORD := 50;
END_VAR

VAR_IN_OUT
	hwSensor: UA_Schunk_mms;
	hwEV: UA_Festo;
END_VAR

VAR_OUTPUT
	Done: BOOL;
	Active: BOOL;
	Error: BOOL;
END_VAR
```

---

## FB_CloseGripper

```iecst
FUNCTION_BLOCK FB_CloseGripper
VAR_INPUT
	Execute: BOOL;
	// Threshold for gripper is closed    
	thClosedMin: WORD := 950;
END_VAR
VAR_IN_OUT
	hwSensor: UA_Schunk_mms;
	hwEV: UA_Festo;
END_VAR
VAR_OUTPUT
	Done: BOOL;
	Active: BOOL;
	Error: BOOL;
END_VAR

```
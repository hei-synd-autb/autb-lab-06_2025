
<h1 align="left">
  <br>
  <img src="./img/hei-en.png" alt="HEI-Vs Logo" width="350">
  <br>
  Industrial Automation Base
  <br>
</h1>

Course AutB

Author: [Cédric Lenoir](mailto:cedric.lenoir@hevs.ch)

# HEVS PackTag User Interface


```iecst
TYPE HEVS_PackTag_UI :
STRUCT
    // Bool commands for UI User inteface
    uiCmdReset         		: BOOL;
    uiCmdStart         		: BOOL;
    uiCmdStop          		: BOOL;
    uiCmdHold          		: BOOL;
    uiCmdUnhold        		: BOOL;
    uiCmdSuspend       		: BOOL;
    uiCmdUnsuspend     		: BOOL;
    uiCmdAbort         		: BOOL;
    uiCmdClear         		: BOOL;
    uiCmdComplete      		: BOOL; 

    // Bool states for UI User Inteface
	uiStateClearing      	: BOOL;
	uiStateStopped      	: BOOL;
	uiStateStarting      	: BOOL;
	uiStateIdle          	: BOOL;
	uiStateSuspended     	: BOOL;
	uiStateExecute       	: BOOL;
	uiStateStopping      	: BOOL;
	uiStateAborting      	: BOOL;
	uiStateAborted       	: BOOL;
	uiStateHolding       	: BOOL;
	uiStateHeld          	: BOOL;
	uiStateUnholding     	: BOOL;
	uiStateSuspending    	: BOOL;
	uiStateUnsuspending  	: BOOL;
	uiStateResetting     	: BOOL;
	uiStateCompleting    	: BOOL;
	uiStateCompleted     	: BOOL;
	
	// Some Modes of UI User Interface
	uiModeProduction 		: BOOL;
	uiModeMaintenance 		: BOOL;
	uiModeManual 			: BOOL;
    uiModeTest 				: BOOL;
	uiModeUser_05			: BOOL;
	uiModeUser_06			: BOOL;
	uiModeUser_07			: BOOL;
	uiModeUser_08			: BOOL;

	// Some strings for diagnostic
	uiStateMasterCurrent	: STRING := 'Not linked';	
	uiStateMasterDiagnostic	: STRING := 'Not linked';	
	uiModeMasterCurrent		: STRING := 'Not linked';	
	uiModeMasterDiagnostic	: STRING := 'Not linked';	    
```
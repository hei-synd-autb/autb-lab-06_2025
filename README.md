<h1 align="left">
  <br>
  <img src="./img/hei-en.png" alt="HEI-Vs Logo" width="350">
  <br>
  Industrial Automation Base
  <br>
</h1>

Course AutB

Author: [Cédric Lenoir](mailto:cedric.lenoir@hevs.ch)

# LAB 06 Pack & PLCopen
[Die deutsche Version ist hier verfügbar:](README_DE.md)

## Objectif du travail pratique
-   [ ] Utiliser des Function Block du type PLCopen dans un contexte PackML
-   [ ] Implémenter des alarmes et des warnings pour surveiller le système.

## Règles de codage
-   Vous devez travailler uniquement dans **PRG_Student**.
-   **PRG_Student** est un module parmis d'autres.
-   Les modules d'axes servent à gérer la mise sous couple des moteurs et inversément vous n'avez pas à le faire.

### Sécurité
L'axe Z est prévu pour remonter pendant la phase de Resetting afin d'éviter les risques de Crash.

Il sert d'exemple: **FB_ModuleAxis_Z** utilise **ACT_Resetting**
```iecst
(*
	Manage Resetting
*)
IF actualState = E_PackState.eResetting THEN
   // Set to TRUE if no action requested
   CASE axisResetting OF
      E_AxisResetting.eIdle :
         // For Z axis, position 0 is safe
         mcMoveAbs.Position := 0;
         axisResetting := E_AxisResetting.eMotionResetPos;
		
      E_AxisResetting.eMotionResetPos :
         IF mcMoveAbs.Done THEN
            axisResetting := E_AxisResetting.eMotionIdleDone;
         ELSIF mcMoveAbs.Error THEN
            axisResetting := E_AxisResetting.eMotionIdleError;
         END_IF
      E_AxisResetting.eMotionIdleDone :
         // Do Nothing, SC
         ;
      E_AxisResetting.eMotionIdleError :
         // Do nothing, wait for debug
         ;		
   END_CASE
	
   stActing.Resetting_SC := (axisResetting = E_AxisResetting.eMotionIdleDone);
ELSE
   axisResetting := E_AxisResetting.eIdle;	
   stActing.Resetting_SC := FALSE;
END_IF

```

Le FB mcMoveAbs est appelé en dehors de la State Machine afin dêtre toujours appelé et ne pas rester dans un état indéterminé.

```iecst
mcMoveAbs.Execute := (axisExecute = E_AxisExecute.eSetAbsolutePosition) OR
                     (axisResetting = E_AxisResetting.eMotionResetPos) 	OR
                     (axisStarting = E_AxisStarting.eMotionStarting);

```

Vous pouvez réutiliser le même FB ``MC_MoveAbsolute``, mais en utilisant une nouvelle instance, par exemple: ``mcMyMove``.

### Etat réservé
L'état Execute en mode Manuel est réservé pour une activité de jog.
Vous pouvez utiliser les autres état et modes.

### Principe PackML HEVS
Le Pack façon HEVS gère automatiquement les états.
**ACT_PackManager** et réservé pour la gestion du module.

Vous ne codez que dans les actions des états qui sont nécessaire, principalement dans **Pack_Execute**. 


### Implémentation des alarmes et des warnings
Vous appelez vos Warnings et Alarms dans **ACT_WarningAndAlarms**. Voir ci-dessous dans [documentation](#documentation) les exemples de code.

### A propos du Gripper
Le gripper est programmé pour être pilotable manuellement, sauf dans les conditions suivantes:

```iecst
IF NOT ((PackTag.hevsUI.uiStateExecute AND (PackTag.Status.UnitModeCurrent = E_PackModes.Production)) OR
         PackTag.hevsUI.uiStateClearing  OR
         PackTag.hevsUI.uiStateResetting)    THEN
	fbOpenGripper.Execute := stTestFbGripperHmi.openGripper;
	fbCloseGripper.Execute := stTestFbGripperHmi.closeGripper;	
END_IF

```
Ce qui signifie que les deux Function Blocks peuvent être utilisés dans tous les autres états.


## URS User Request Specification

1. **Execute**
1.1 Les axes X et Z doivent se déplacer selon le tableau et [dessin ci-dessous](#details-for-square).
1.2. On peut varier la vitesse, l'accélération et le jerk depuis le HMI.
1.3. Le gripper est activé selon le [tableau ci-dessous](#details-for-square).
1.4. Le mouvement continue en boucle, 1-2-3-5-1...
2. **Hold**
2.1 Si on appuie sur le bouton Hold du HMI Node-RED, les axes sont stoppés immédiatement avec le FB_Stop.
2.2 Si on appuie sur le bouton Unhold du HMI Node-RED, le système recommance la séquence Execute.
3. **Suspend**
3.1. Après 3 cycles de mouvements, le programme passe en Suspended pendant 3 secondes, puis recommence pour 3 cycles supplémentaire et ainsi de suite.
3.2. L'action Suspended doit être activée par une **alarme** de niveau Suspend.
4. **Warnings**
4.1. En Aborted, on indique qu'il faut un clearing pour démarrer.
4.2. En Stopped, on indique qu'il faut un reset pour démarrer.
4.3. En Held, on indique qu'il faut un unhold pour démarrer.
5. Alarm Gripper
5.1 Si on coupe l'air comprimé et que le gripper ne se ferme pas, on génère une alarme de niveau Stoppe avec un message.
5.2 Si on coupe l'air comprimé et que le gripper ne s'ouver pas, on génère une alarme de niveau Stoppe avec un message.


### Complément
On peut aussi tester Complete qui devrait pouvoir repartir en resetting avec un Reset.


### Details for Square

|Id |Move To Position X|M.T.P Z |Action      |Delay [ms] |Next Id|
|---|------------------|--------|------------|-----------|-------|
|1  |0                 |50      |eOpen       |500        |2      |
|2  |0                 |-50       |eClose      |0          |3      |
|3  |100               |-50       |eOpen       |0          |4      |
|4  |100               |50      |eClose      |0          |1      |
|1  |0                 |50      |eOpen       |500        |2      |

<figure>
    <img src="./img/DriveSquare.png"
         alt="Image Lost DriveSquare">
    <figcaption>2D motion with a square</figcaption>
</figure>

## Documentation

-   [For Gripper Function Blocks online](FB_Gripper.md)
-   [HEVS PackTag User Interface](HEVS_PackTag_UI.md)
-   [HEVS Alarms](FB_HEVS_SetAlarm.md)
-   [HEVS Warnings](FB_HEVS_SetWarning.md)


### About Programs

In cltrX PLC Engineering

Function blocks for robot are in **HEVS_Robot**.

Manual motions are set in **HEVS_UnitBox** -> **PRG_Process**.

**PRG_Student** is called in **PRG_Process**.

#### About SC State Complete
The synthesis of modules for the process/Unit are here in **ACT_Build_Pack_SC** of the program **PRG_Process**.

```iecst
(*
	This section should group all EM and CM State Complete
	This Unit_SC is used by PLC_PACK to manager
	States SC
*)
xProcess_SC := fbModuleAxis_X.SC AND
		       fbModuleAxis_Y.SC AND
		       fbModuleAxis_Z.SC AND
               PRG_Student.SC;
```

#### In the task manager
Only one program is in the task manager it calls all other programs of the Unit Box. PRG_UnitBox.

```iecst
(*
	Check and init program
*)
uliLoopUnitBox := uliLoopUnitBox + 1;
IF NOT xInitUnitBox THEN
	xInitUnitBox := TRUE;
END_IF

(*
	Map program
	Specific to this hardware.
	Mainly access to IO via Profinet and S7-1500
*)
PRG_MapBox();

(*
	Device Manager
	Processing of hardware values.
	May include alarms if devices not included in process
	Ideally, Alarms should be only part of EM and CM in PRG_Process 
*)
PRG_DeviceManager();

(*
	Tools for processing
	Include
		Pack States and Modes
		Alarms
		Date and Time
*)
PLC_PACK();
PRG_TestAlarm();
PRG_GetTime_CtrlX();

(*
	Process
	Management of Unit, EM and CM if any
*)
PRG_Process();
```
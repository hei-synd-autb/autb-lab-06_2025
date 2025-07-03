# Pack
Ajouter la durée pour chaque state et chaque mode sur le HMI.

# Diagnostic
Récupérer au minimum la première info DIAG, puis l'afficher et permettre le reset (ack)


# About Ack.
URS

Si l'alarme disparait parce que bSetAlarm passe à FALSE, c'est simple, on remplit le 

rTrigAck(CLK := bAckWarningTrig);

IF rTrigAck.Q AND bSetWarning THEN
	FOR diWarningLoop := 0 TO HEVS_PackTag_GVL.C_ADMIN_MAXWARNINGS BY 1 DO
		IF (stAdminWarning[diWarningLoop].ID = ID) THEN
			stAdminWarning[diWarningLoop].AckDateTime := PackTag.Admin.PLCDateTime;
		END_IF
	END_FOR	
END_IF

Vérifier l'effacement des diagnostics

Il faudrait remplacer tous les bSet.. par xSet...

Il ne faut pas empêcher la machine de démarrer avec un Warning.

Il n'y a pas de raison d'empêcher la machine de démarrer si j'ai un Hold qui n'est pas concerné par un clearing.

La liaison avec la gestion des états et les alarmes se fait via le FB **FUNCTION_BLOCK FB_HEVS_StopReason**.




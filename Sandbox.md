Notes de rédaction.

Parti du module 05

plc/app/Application/sym/PackTag/hevsUI/uiCmdHold



Pour chaque état, sauf pour Execute, on veut un warning qui nous informe que faire pour passer à la suite.

On veut un alarme après 10 cycles qui provoque un Suspend en attendant la suite.

Si le gripper ne se ferme pas ou ne s'ouvre pas, on stoppe la machine avec une alarme.


On veut un warning si le système est en Hold et indiquer comment redémarrer.
On veut un warning si le système est en 

Avec un hold, on arrête à la fin du mouvement.

Essayer MC_SetOverride pour un Hold, se débrouiller pour le trouver dans la documentation.
Library Manager --> CXA_PLCOPEN.

Utiliser une variable globale

PackTag.hevsUI.uiCmdSuspend,
                   Unsuspend := PackTag.hevsUI.uiCmdUnsuspend,

GVL_Abox.uaAboxInterface                   
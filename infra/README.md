
# ThingsLogicApp V1

The Logic App is embedded in the ARM template.

The Computer Vision service must be created.  The key must then be configured in the Computer Vision Connector

### Intialise variables

```
RG="Thingz-rg"
LOGICAPPNAME="thingzlogicapp"

```

### Get URL endpoint for logic app

```
printf -v PSCMD "write-output (Get-AzLogicAppTriggerCallbackUrl -ResourceGroupName $RG -Name $LOGICAPPNAME -TriggerName manual -verbose).Value;"
pwsh -command $PSCMD
PSOUTPUT=$(pwsh -command $PSCMD)
LAENDPOINT=${PSOUTPUT/*http/http}
echo $LAENDPOINT

```

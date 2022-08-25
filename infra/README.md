
# ThingsLogicApp V1

## Infrastructure as code using AZ CLI

The Logic App is embedded in the ARM template.

The Computer Vision service must be created.  The key must then be configured in the Computer Vision Connector

### Intialise variables

```
RG="Thingz-rg"
LOCATION="uksouth"
LOGICAPPNAME="thingzlogicapp"
THINGSAPIURL="https://????????.azurewebsites.net"
COMPUTERVISIONCONNECTIONNAME="ThingzCompVisionConn"
COMPUTERVISIONNAME="ThingzCompVision"

```

### Create a RG - if needed

```
az group create -g $RG -l $LOCATION  -o table 

```

### Create Computer Vision Cognitive Service - get keys

```
az cognitiveservices account create -g $RG -l $LOCATION   \
    --name $COMPUTERVISIONNAME \
    --kind ComputerVision \
    --sku F0 \
    --yes

COMPUTERVISIONKEY=$(az cognitiveservices account keys list -g $RG -o tsv \
    --name $COMPUTERVISIONNAME \
    --query "key1" )

COMPUTERVISIONAPIURL=$(az cognitiveservices account show -g $RG  -o tsv \
   --name $COMPUTERVISIONNAME \
   --query "properties.endpoint" )

```
  
### Create LogicApp 

```
az deployment group create \
  --resource-group $RG \
  --template-uri "https://raw.githubusercontent.com/markharrison/ThingsLogicAppV1/main/ThingsLogicApp/LogicApp.json" \
  --parameters \
    thingsAPIUrl=$THINGSAPIURL \
    logicAppName=$LOGICAPPNAME \
    logicAppLocation=$LOCATION \
    cognitiveservicescomputervision_apiKey=$COMPUTERVISIONKEY \
    cognitiveservicescomputervision_siteUrl=$COMPUTERVISIONAPIURL \
    cognitiveservicescomputervision_Connection_Name=$COMPUTERVISIONCONNECTIONNAME \
    cognitiveservicescomputervision_Connection_DisplayName=$COMPUTERVISIONNAME

```
 
### Get URL endpoint for logic app

```
printf -v PSCMD "write-output (Get-AzLogicAppTriggerCallbackUrl -ResourceGroupName $RG -Name $LOGICAPPNAME -TriggerName manual -verbose ).Value;" 
pwsh -command $PSCMD
PSOUTPUT=$(pwsh -command $PSCMD)
LAENDPOINT=${PSOUTPUT/*http/http}
echo $LAENDPOINT

```
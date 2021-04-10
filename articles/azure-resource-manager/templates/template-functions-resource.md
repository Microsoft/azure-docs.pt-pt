---
title: Funções do modelo - recursos
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure (modelo ARM) para recuperar valores sobre recursos.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: efd7c554e6da8b60d4834d1a1290407a6b9e94d4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544116"
---
# <a name="resource-functions-for-arm-templates"></a>Funções de recursos para modelos ARM

O Gestor de Recursos fornece as seguintes funções para obter valores de recursos no seu modelo de Gestor de Recursos Azure (modelo ARM):

* [extensãoResourceId](#extensionresourceid)
* [lista*](#list)
* [pickZones](#pickzones)
* [fornecedores](#providers)
* [referência](#reference)
* [resourceGroup](#resourcegroup)
* [recursosId](#resourceid)
* [subscrição](#subscription)
* [subscriçãoResourceId](#subscriptionresourceid)
* [inquilinoResourceId](#tenantresourceid)

Para obter valores a partir de parâmetros, variáveis ou da implementação atual, consulte as [funções de valor de implantação](template-functions-deployment.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="extensionresourceid"></a>extensãoResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

Devolve o ID de recurso para um [recurso de extensão](../management/extension-resource-types.md), que é um tipo de recurso que é aplicado a outro recurso para adicionar às suas capacidades.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| resourceId |Yes |string |O ID de recurso para o recurso a que o recurso de extensão é aplicado. |
| resourceType |Yes |string |Tipo de recurso, incluindo espaço de nome do fornecedor de recursos. |
| recursoName1 |Yes |string |Nome do recurso. |
| recursoName2 |No |string |Próximo segmento de nome de recurso, se necessário. |

Continue a adicionar nomes de recursos como parâmetros quando o tipo de recurso inclui mais segmentos.

### <a name="return-value"></a>Valor devolvido

O formato básico do ID de recurso devolvido por esta função é:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

O segmento de âmbito varia de acordo com o recurso que está a ser alargado.

Quando o recurso de extensão é aplicado a um **recurso,** o ID de recurso é devolvido no seguinte formato:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a um **grupo de recursos,** o formato é:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a uma **subscrição,** o formato é:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a um **grupo de gestão,** o formato é:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensãoResourceD exemplo

O exemplo a seguir devolve o ID do recurso para um bloqueio de grupo de recursos.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "lockName": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "lockResourceId": {
      "type": "string",
      "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param lockName string

output lockResourceId string = extensionResourceId(resourceGroup().Id, 'Microsoft.Authorization/locks', lockName)
```

---

Uma definição de política personalizada implantada num grupo de gestão é implementada como um recurso de extensão. Para criar e atribuir uma política, desloque o modelo seguinte para um grupo de gestão.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/quickstart-templates/managementgroup-deployments/mg-policy/azuredeploy.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param targetMG string
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

var mgScope = tenantResourceId('Microsoft.Management/managementGroups', targetMG)
var policyDefinition = 'LocationRestriction'

resource myDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: policyDefinition
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      'if': {
        'not': {
          'field': 'location'
          'in': allowedLocations
        }
      }
      'then': {
        'effect': 'deny'
      }
    }
  }
}

resource myAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'location-lock'
  properties: {
    scope: mgScope
    policyDefinitionId: extensionResourceId(mgScope, 'Microsoft.Authorization/policyDefinitions', policyDefinition)
  }
}
```

---

As definições políticas incorporadas são recursos ao nível dos inquilinos. Para um exemplo de implementação de uma definição de política incorporada, consulte [o inquilinoResourceId](#tenantresourceid).

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>lista*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

A sintaxe para esta função varia em função do nome das operações da lista. Cada implementação devolve valores para o tipo de recurso que suporta uma operação de lista. O nome da operação deve começar `list` e pode ter um sufixo. Alguns usos comuns `list` `listKeys` `listKeyValue` são, e `listSecrets` .

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| recursoName ou identificador de recursos |Yes |string |Identificador único para o recurso. |
| apiVersion |Yes |string |Versão API do estado de execução de recursos. Tipicamente, no formato, **yyy-mm-dd**. |
| funçõesValues |No |objeto | Um objeto que tem valores para a função. Apenas forneça este objeto para funções que suportem a receção de um objeto com valores de parâmetros, como **listAccountSas** numa conta de armazenamento. Neste artigo é mostrado um exemplo de valores de função de passagem. |

### <a name="valid-uses"></a>Usos válidos

As funções da lista podem ser utilizadas nas propriedades de uma definição de recurso. Não utilize uma função de lista que exponha informações sensíveis na secção de saídas de um modelo. Os valores de saída são armazenados no histórico de implementação e podem ser recuperados por um utilizador malicioso.

Quando utilizado com [iteração de propriedade,](copy-properties.md)pode utilizar as funções da lista `input` porque a expressão é atribuída à propriedade de recursos. Não pode usá-los `count` porque a contagem deve ser determinada antes da função da lista ser resolvida.

### <a name="implementations"></a>Implementações

As possíveis utilizações da lista* são apresentadas na tabela seguinte.

| Tipo de recurso | Nome da função |
| ------------- | ------------- |
| Microsoft.Addons/supportProviders | listas deporteportinfo |
| Microsoft.AnalysisServices/servidores | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.ApiManagement/service/authorizationServers | [listSecrets](/rest/api/apimanagement/2019-12-01/authorizationserver/listsecrets) |
| Microsoft.ApiManagement/service/gateways | [listKeys](/rest/api/apimanagement/2019-12-01/gateway/listkeys) |
| Microsoft.ApiManagement/service/identityProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/identityprovider/listsecrets) |
| Microsoft.ApiManagement/service/namedValues | [listValue](/rest/api/apimanagement/2019-12-01/namedvalue/listvalue) |
| Microsoft.ApiManagement/service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/openidconnectprovider/listsecrets) |
| Microsoft.ApiManagement/service/subscrições | [listSecrets](/rest/api/apimanagement/2019-12-01/subscription/listsecrets) |
| Microsoft.AppConfiguration/configurationStores | [ListKeys](/rest/api/appconfiguration/configurationstores/listkeys) |
| Microsoft.AppPlatform/primavera | [listTestKeys](/rest/api/azurespringcloud/services/listtestkeys) |
| Microsoft.Automation/automation | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batcontas ch/batch | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiências/empregos | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.BotService/botServices/canais | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/contas | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/agentpools | listQueueStatus |
| Microsoft.ContainerRegistry/registries/buildTasks | listSourceRepositoryProperties |
| Microsoft.ContainerRegistry/registries/buildTasks/steps | listBuildArguments |
| Microsoft.ContainerRegistry/registries/taskruns | listDetails |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/fábricas/integrationruns | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAcounts/Contentores | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/contas/ações | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/contas/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/contas/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/contas/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServserves/chaves | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/laboratórios | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/base de dadosAcontas | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/base de dadosAcontas | [listKeys](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/databaseaccounts/listkeys) |
| Microsoft.DocumentDB/base de dadosAconsse/notebookS | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/notebookworkspaces/listconnectioninfo) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domínios | [listKeys](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/tópicos | [listKeys](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/autorizaçõesRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/eventhubs/autorizaçõesRules | [listkeys](/rest/api/eventhub) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Bases de Dados | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/utilizadores | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/utilizadores | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAconselhos/acordos | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationContas/conjuntos | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAcontas | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAcontas | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAconselhos/mapas | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAconselhos/parceiros | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAconselhos/esquemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetições | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versões/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/contas | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/ativos | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/ativos | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/autorizaçõesRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationrules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [lista](/rest/api/loganalytics/workspaces/list) |
| Microsoft.OperationalInsights/workspaces | listKeys |
| Microsoft.PolicyInsights/remediações | [listaDeployments](/rest/api/policy/remediations/listdeploymentsatresourcegroup) |
| Microsoft.RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Microsoft.Relay/namespaces/autorizaçõesRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationrules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/autorizaçõesRules | [listkeys](/rest/api/servicebus/stable/namespaces%20-%20authorization%20rules/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/filas/autorizaçõesRules | [listkeys](/rest/api/servicebus/stable/queues%20-%20authorization%20rules/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/stable/topics%20–%20authorization%20rules/listkeys) |
| Microsoft.SignalrService/Signalr | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAcontas | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAcontas | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAcontas | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivaçãoKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Synapse/workspaces/integrationRuntimes | [listAuthKeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/conexões | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [lista](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [lista](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/funções | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/funções | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [lista](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [lista](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Para determinar quais os tipos de recursos que têm uma operação de lista, tem as seguintes opções:

* Consulte as [operações da API REST](/rest/api/) para um fornecedor de recursos e procure operações de lista. Por exemplo, as contas de armazenamento têm a [operação listKeys](/rest/api/storagerp/storageaccounts).
* Utilize o [cmdlet Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell. O exemplo a seguir obtém todas as operações da lista para contas de armazenamento:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```

* Utilize o seguinte comando Azure CLI para filtrar apenas as operações da lista:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Valor devolvido

O objeto devolvido varia de acordo com a função de lista que utiliza. Por exemplo, o listKeys para uma conta de armazenamento devolve o seguinte formato:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Outras funções da lista têm diferentes formatos de retorno. Para ver o formato de uma função, inclua-o na secção de saídas, como mostrado no modelo de exemplo.

### <a name="remarks"></a>Observações

Especifique o recurso utilizando o nome do recurso ou a [função resourceId](#resourceid). Quando utilizar uma função de lista no mesmo modelo que implementa o recurso referenciado, utilize o nome do recurso.

Se utilizar uma função **de lista** num recurso que esteja implantado condicionalmente, a função é avaliada mesmo que o recurso não seja implantado. Obtém-se um erro se a função **de lista** se refere a um recurso que não existe. Utilize a função **se** funcionar para se certificar de que a função só é avaliada quando o recurso está a ser implantado. Consulte a [função se](template-functions-logical.md#if) for para um modelo de amostra que utiliza se e lista com um recurso implantado condicionalmente.

### <a name="list-example"></a>Exemplo da lista

O exemplo a seguir utiliza listKeys ao definir um valor para [scripts de implementação](deployment-script-template.md).

# <a name="json"></a>[JSON](#tab/json)

```json
"storageAccountSettings": {
  "storageAccountName": "[variables('storageAccountName')]",
  "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
storageAccountSettings: {
  storageAccountName: storageAccountName
  storageAccountKey: listKeys(resourceId('Microsoft.Storage/storageAccounts', storageAccountName), '2019-06-01').keys[0].value
}

```

---

O exemplo seguinte mostra uma função de lista que toma um parâmetro. Neste caso, a função é **listAccountSas**. Passe um objeto pelo tempo de validade. O prazo de validade deve ser no futuro.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "accountSasProperties": {
    "type": "object",
    "defaultValue": {
      "signedServices": "b",
      "signedPermission": "r",
      "signedExpiry": "2020-08-20T11:00:00Z",
      "signedResourceTypes": "s"
    }
  }
},
...
"sasToken": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties')).accountSasToken]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param accountSasProperties object {
  default: {
    signedServices: 'b'
    signedPermission: 'r'
    signedExpiry: '2020-08-20T11:00:00Z'
    signedResourceTypes: 's'
  }
}
...
sasToken: listAccountSas(storagename, '2018-02-01', accountSasProperties).accountSasToken
```

---

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

Determina se um tipo de recurso suporta zonas para uma região.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| providerNamespace | Yes | string | O espaço de nome do fornecedor de recursos para o tipo de recurso para verificar o suporte da zona. |
| resourceType | Yes | string | O tipo de recurso para verificar o suporte da zona. |
| localização | Yes | string | A região para verificar o apoio da zona. |
| númeroOfZones | No | número inteiro | O número de zonas lógicas para regressar. A predefinição é 1. O número deve ser um número inteiro positivo de 1 a 3.  Utilize 1 para recursos de zona única. No que diz a favor de recursos multi-zonas, o valor deve ser inferior ou igual ao número de zonas apoiadas. |
| offset | No | número inteiro | A compensação da zona lógica inicial. A função retorna um erro se o offset plus numberOfZones exceder o número de zonas suportadas. |

### <a name="return-value"></a>Valor devolvido

Uma matriz com as zonas apoiadas. Ao utilizar os valores predefinidos para compensar e númeroOfZones, um tipo de recurso e região que suporta zonas devolve o seguinte conjunto:

```json
[
    "1"
]
```

Quando o `numberOfZones` parâmetro é definido para 3, retorna:

```json
[
    "1",
    "2",
    "3"
]
```

Quando o tipo de recurso ou região não suporta zonas, uma matriz vazia é devolvida.

```json
[
]
```

### <a name="pickzones-example"></a>exemplo pickZones

O modelo a seguir mostra três resultados para a utilização da função pickZones.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "supported": {
      "type": "array",
      "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')]"
    },
    "notSupportedRegion": {
      "type": "array",
      "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')]"
    },
    "notSupportedType": {
      "type": "array",
      "value": "[pickZones('Microsoft.Cdn', 'profiles', 'westus2')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output supported array = pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')
output notSupportedRegion array = pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')
output notSupportedType array = pickZones('Microsoft.Cdn', 'profiles', 'westus2')
```

---

A saída dos exemplos anteriores devolve três matrizes.

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| Suportado | matriz | [ "1" ] |
| nãoSupouregion | matriz | [] |
| nãoSupportouTip | matriz | [] |

Pode utilizar a resposta a partir de pickZones para determinar se fornece nulos para zonas ou atribui máquinas virtuais a diferentes zonas. O exemplo a seguir define um valor para a zona com base na disponibilidade de zonas.

# <a name="json"></a>[JSON](#tab/json)

```json
"zones": {
  "value": "[if(not(empty(pickZones('Microsoft.Compute', 'virtualMachines', 'westus2'))), string(add(mod(copyIndex(),3),1)), json('null'))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> Loops e copyIndex() ainda não estão implementados.  Ver [Loops](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

## <a name="providers"></a>fornecedores

`providers(providerNamespace, [resourceType])`

Devolve informações sobre um fornecedor de recursos e os seus tipos de recursos suportados. Se não fornecer um tipo de recurso, a função devolve todos os tipos suportados para o fornecedor de recursos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| providerNamespace |Yes |string |Espaço de nome do fornecedor |
| resourceType |No |string |O tipo de recurso dentro do espaço de nome especificado. |

### <a name="return-value"></a>Valor devolvido

Cada tipo suportado é devolvido no seguinte formato:

```json
{
  "resourceType": "{name of resource type}",
  "locations": [ all supported locations ],
  "apiVersions": [ all supported API versions ]
}
```

A encomenda dos valores devolvidos não está garantida.

### <a name="providers-example"></a>Exemplo de fornecedores

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) a seguir mostra como utilizar a função do fornecedor:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "providerNamespace": {
      "type": "string"
    },
    "resourceType": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "providerOutput": {
      "type": "object",
      "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param providerNamespace string
param resourceType string

output providerOutput array = providers(providerNamespace, resourceType)
```

---

Para o fornecedor de recursos **Microsoft.Web** e o tipo de recursos **dos sites,** o exemplo anterior devolve um objeto no seguinte formato:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>referência

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Devolve um objeto que representa o estado de execução de um recurso.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| recursoName ou identificador de recursos |Yes |string |Nome ou identificador único de um recurso. Ao fazer referência a um recurso no modelo atual, forneça apenas o nome do recurso como parâmetro. Quando se refere a um recurso previamente implantado ou quando o nome do recurso for ambíguo, forneça o ID do recurso. |
| apiVersion |No |string |Versão API do recurso especificado. **Este parâmetro é necessário quando o recurso não é aprovisionado dentro do mesmo modelo.** Tipicamente, no formato, **yyy-mm-dd**. Para versões API válidas para o seu recurso, consulte [a referência do modelo](/azure/templates/). |
| 'Cheio' |No |string |Valor que especifica se deve devolver o objeto de recurso completo. Se não `'Full'` especificar, apenas o objeto de propriedades do recurso é devolvido. O objeto completo inclui valores como o ID de recurso e a localização. |

### <a name="return-value"></a>Valor devolvido

Cada tipo de recurso devolve diferentes propriedades para a função de referência. A função não devolve um único formato predefinido. Além disso, o valor devolvido difere com base no valor do `'Full'` argumento. Para ver as propriedades de um tipo de recurso, devolva o objeto na secção de saídas, como mostra o exemplo.

### <a name="remarks"></a>Observações

A função de referência recupera o estado de tempo de execução de um recurso previamente implantado ou de um recurso implantado no modelo atual. Este artigo mostra exemplos para ambos os cenários.

Normalmente, utiliza-se a função **de referência** para devolver um valor particular a um objeto, como o ponto final blob URI ou o nome de domínio totalmente qualificado.

# <a name="json"></a>[JSON](#tab/json)

```json
"outputs": {
  "BlobUri": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]"
  },
  "FQDN": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output BlobUri string = reference(resourceId('Microsoft.Storage/storageAccounts', storageAccountName)).primaryEndpoints.blob
output FQDN string = reference(resourceId('Microsoft.Network/publicIPAddresses', ipAddressName)).dnsSettings.fqdn
```

---

Use `'Full'` quando precisar de valores de recursos que não fazem parte do esquema de propriedades. Por exemplo, para definir as políticas de acesso ao cofre chave, obtenha as propriedades de identidade para uma máquina virtual.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "vaultName",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource myVault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'vaultName'
  properties: {
    tenantId: subscription().tenantId
    accessPolicies: [
      {
        'tenantId': reference(resourceId('Microsoft.Compute/virtualMachines', vmName), '2019-03-01', 'Full').identity.tenantId
        'objectId': reference(resourceId('Microsoft.Compute/virtualMachines', vmName), '2019-03-01', 'Full').identity.principalId
        'permissions': {
          'keys': [
            'all'
          ]
          'secrets': [
            'all'
          ]
        }
      }
    ]
  }
}
```

---

### <a name="valid-uses"></a>Usos válidos

A função de referência só pode ser utilizada nas propriedades de uma definição de recurso e na secção de saídas de um modelo ou implantação. Quando utilizado com [iteração de propriedade,](copy-properties.md)pode utilizar a função de referência porque `input` a expressão é atribuída à propriedade de recursos.

Não é possível utilizar a função de referência para definir o valor da `count` propriedade num ciclo de cópia. Pode utilizar para definir outras propriedades no loop. A referência está bloqueada para a propriedade da contagem porque essa propriedade deve ser determinada antes da função de referência ser resolvida.

Para utilizar a função de referência ou qualquer função de lista* na secção de saídas de um modelo aninhado, deve definir  ```expressionEvaluationOptions``` a avaliação [do âmbito interno](linked-templates.md#expression-evaluation-scope-in-nested-templates) ou utilizar um modelo ligado em vez de um modelo aninhado.

Se utilizar a função **de referência** num recurso que esteja implantado condicionalmente, a função é avaliada mesmo que o recurso não seja implantado.  Obtém-se um erro se a função **de referência** se referir a um recurso que não existe. Utilize a função **se** funcionar para se certificar de que a função só é avaliada quando o recurso está a ser implantado. Consulte a [função se](template-functions-logical.md#if) for para um modelo de amostra que utiliza se e referenciar com um recurso implantado condicionalmente.

### <a name="implicit-dependency"></a>Dependência implícita

Ao utilizar a função de referência, declara implicitamente que um recurso depende de outro recurso se o recurso referenciado for aprovisionado dentro do mesmo modelo e se referir ao recurso pelo seu nome (não iD de recurso). Você não precisa também usar a propriedade dependOn. A função não é avaliada até que o recurso referenciado tenha concluído a implementação.

### <a name="resource-name-or-identifier"></a>Nome de recurso ou identificador

Ao fazer referência a um recurso que é implantado no mesmo modelo, forneça o nome do recurso.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(parameters('storageAccountName'))]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(storageAccountName)
```

---

Ao fazer referência a um recurso que não seja implantado no mesmo modelo, forneça o ID do recurso e `apiVersion` .

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(resourceId(storageResourceGroup, 'Microsoft.Storage/storageAccounts', storageAccountName), '2018-07-01')]"
```

---

Para evitar ambiguidades sobre o recurso a que se refere, pode fornecer um identificador de recursos totalmente qualificado.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(resourceId('Microsoft.Network/publicIPAddresses', ipAddressName))
```

---

Ao construir uma referência totalmente qualificada a um recurso, a ordem de combinar segmentos do tipo e nome não é simplesmente uma concatenação dos dois. Em vez disso, após o espaço de nome, utilize uma sequência de pares de *tipo/nome* de menos específicos para os mais específicos:

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name-name}]**

Por exemplo:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` está correto `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` não é correto

Para simplificar a criação de qualquer ID de recurso, utilize as `resourceId()` funções descritas neste documento em vez da `concat()` função.

### <a name="get-managed-identity"></a>Obter identidade gerida

[Identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md) são [tipos de recursos de extensão](../management/extension-resource-types.md) que são criados implicitamente para alguns recursos. Como a identidade gerida não está explicitamente definida no modelo, deve referenciar o recurso a que a identidade é aplicada. Use `Full` para obter todas as propriedades, incluindo a identidade implicitamente criada.

O padrão é:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

Por exemplo, para obter o ID principal para uma identidade gerida que é aplicada a uma máquina virtual, use:

# <a name="json"></a>[JSON](#tab/json)

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
reference(resourceId('Microsoft.Compute/virtualMachines', vmName),'2019-12-01', 'Full').identity.principalId
```

---

Ou, para obter a identificação do inquilino para uma identidade gerida que é aplicada a um conjunto de escala de máquina virtual, use:

# <a name="json"></a>[JSON](#tab/json)

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  vmNodeType0Name), 2019-12-01, 'Full').Identity.tenantId
```

---

### <a name="reference-example"></a>Exemplo de referência

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) a seguir implementa um recurso, e faz referências a esse recurso.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string

resource myStorage 'Microsoft.Storage/storageAccounts@2016-12-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  tags: {}
  properties: {}
}

output referenceOutput object = reference(storageAccountName)
output fullReferenceOutput object = reference(storageAccountName, '2016-12-01', 'Full')
```

---

O exemplo anterior devolve os dois objetos. O objeto de propriedades está no seguinte formato:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

O objeto completo está no seguinte formato:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) a seguir refere uma conta de armazenamento que não é implantada neste modelo. A conta de armazenamento já existe dentro da mesma subscrição.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageResourceGroup": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "ExistingStorage": {
      "type": "object",
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageResourceGroup string
param storageAccountName string

output ExistingStorage object = reference(resourceId(storageAccountName), 'Microsoft.Storage/storageAccounts', storageAccountName), '2018-07-01')
```

---

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Devolve um objeto que representa o grupo de recursos atual.

### <a name="return-value"></a>Valor devolvido

O objeto devolvido encontra-se no seguinte formato:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

A propriedade **geridaBy** é devolvida apenas para grupos de recursos que contêm recursos que são geridos por outro serviço. Para Aplicações Geridas, Databricks e AKS, o valor da propriedade é o ID de recursos do recurso de gestão.

### <a name="remarks"></a>Observações

A `resourceGroup()` função não pode ser utilizada num modelo que seja [implantado ao nível de subscrição](deploy-to-subscription.md). Só pode ser usado em modelos que são implantados num grupo de recursos. Pode utilizar a `resourceGroup()` função num [modelo ligado ou aninhado (com âmbito interno)](linked-templates.md) que se destina a um grupo de recursos, mesmo quando o modelo dos pais é implantado na subscrição. Nesse cenário, o modelo ligado ou aninhado é implantado ao nível do grupo de recursos. Para obter mais informações sobre como direcionar um grupo de recursos numa implementação de nível de subscrição, consulte [os recursos do Deploy Azure para mais de uma subscrição ou grupo de recursos](./deploy-to-resource-group.md).

Uma utilização comum da função grupo de recursos é criar recursos no mesmo local que o grupo de recursos. O exemplo a seguir utiliza a localização do grupo de recursos para um valor de parâmetro padrão.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

Também pode utilizar a função grupo de recursos para aplicar tags do grupo de recursos a um recurso. Para obter mais informações, consulte [aplicar etiquetas do grupo de recursos.](../management/tag-resources.md#apply-tags-from-resource-group)

Ao utilizar modelos aninhados para implantar em vários grupos de recursos, pode especificar o âmbito para avaliar a função Grupo de Recursos. Para obter mais informações, consulte [os recursos do Deploy Azure para mais de uma subscrição ou grupo de recursos.](./deploy-to-resource-group.md)

### <a name="resource-group-example"></a>Exemplo de grupo de recursos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) a seguir devolve as propriedades do grupo de recursos.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "resourceGroupOutput": {
      "type" : "object",
      "value": "[resourceGroup()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output resourceGroupOutput object = resourceGroup()
```

---

O exemplo anterior devolve um objeto no seguinte formato:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

Devolve o identificador único de um recurso. Utilize esta função quando o nome do recurso é ambíguo ou não é aprovisionado dentro do mesmo modelo. O formato do identificador devolvido varia em base se a implementação ocorre no âmbito de um grupo de recursos, subscrição, grupo de gestão ou inquilino.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |cadeia (no formato GUID) |O valor predefinido é a subscrição atual. Especifique este valor quando necessitar de recuperar um recurso noutra subscrição. Apenas forneça este valor ao implementar no âmbito de um grupo de recursos ou subscrição. |
| resourceGroupName |No |string |O valor predefinido é o grupo de recursos corrente. Especifique este valor quando necessitar de recuperar um recurso noutro grupo de recursos. Apenas forneça este valor ao implementar no âmbito de um grupo de recursos. |
| resourceType |Yes |string |Tipo de recurso, incluindo espaço de nome do fornecedor de recursos. |
| recursoName1 |Yes |string |Nome do recurso. |
| recursoName2 |No |string |Próximo segmento de nome de recurso, se necessário. |

Continue a adicionar nomes de recursos como parâmetros quando o tipo de recurso inclui mais segmentos.

### <a name="return-value"></a>Valor devolvido

Quando o modelo é implantado no âmbito de um grupo de recursos, o ID do recurso é devolvido no seguinte formato:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Pode utilizar a função resourceId para outros âmbitos de implementação, mas o formato do ID muda.

Se utilizar recursosId durante a implementação de uma subscrição, o ID do recurso é devolvido no seguinte formato:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Se utilizar recursosId durante a implementação para um grupo de gestão ou inquilino, o ID de recurso é devolvido no seguinte formato:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Para evitar confusões, recomendamos que não utilize recursosId ao trabalhar com recursos implantados na subscrição, grupo de gestão ou inquilino. Em vez disso, utilize a função ID que é concebida para o âmbito.

Para [recursos de nível de subscrição,](deploy-to-subscription.md)utilize a [função SubscriçãoResourceId.](#subscriptionresourceid)

Para [recursos de grupo de gestão,](deploy-to-management-group.md)use a [função extensionResourceId](#extensionresourceid) para referenciar um recurso que é implementado como uma extensão de um grupo de gestão. Por exemplo, as definições de política personalizada que são implementadas num grupo de gestão são extensões do grupo de gestão. Utilize a função [TenantResourceId](#tenantresourceid) para obter recursos de referência que são implantados ao arrendatário mas disponíveis no seu grupo de gestão. Por exemplo, as definições políticas incorporadas são implementadas como recursos ao nível dos inquilinos.

Para [recursos ao nível do arrendatário,](deploy-to-tenant.md)utilize a função [TenantResourceId.](#tenantresourceid) Use o inquilinoResourceId para definições políticas incorporadas porque são implementados ao nível do inquilino.

### <a name="remarks"></a>Observações

O número de parâmetros que fornece varia consoá-lo com base no facto de o recurso ser um recurso parental ou infantil, e se o recurso se encontra no mesmo grupo de subscrição ou recursos.

Para obter o ID de recurso para um recurso principal no mesmo grupo de subscrição e recursos, forneça o tipo e o nome do recurso.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')
```

---

Para obter o ID de recurso para um recurso infantil, preste atenção ao número de segmentos do tipo de recurso. Fornecer um nome de recurso para cada segmento do tipo de recurso. O nome do segmento corresponde ao recurso que existe para essa parte da hierarquia.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')
```

---

Para obter o ID de recurso para um recurso na mesma subscrição, mas grupo de recursos diferente, forneça o nome do grupo de recursos.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')
```

---

Para obter o ID de recurso para um recurso em um grupo de subscrição e recursos diferente, forneça o ID de subscrição e o nome do grupo de recursos.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
```

---

Muitas vezes, é necessário utilizar esta função quando utilizar uma conta de armazenamento ou uma rede virtual num grupo de recursos alternativos. O exemplo a seguir mostra como um recurso de um grupo de recursos externos pode ser facilmente utilizado:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "virtualNetworkName": {
      "type": "string"
    },
    "virtualNetworkResourceGroup": {
      "type": "string"
    },
    "subnet1Name": {
      "type": "string"
    },
    "nicName": {
      "type": "string"
    }
  },
  "variables": {
    "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[variables('subnet1Ref')]"
              }
            }
          }
        ]
      }
    }
  ]
}
```
# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string
param virtualNetworkName string
param virtualNetworkResourceGroup string
param subnet1Name string
param nicName string

var subnet1Ref = resourceId('virtualNetworkResourceGroup', 'Microsoft.Network/virtualNetworks/subnets', 'virtualNetworkName', 'subnet1Name')

resource myInterface 'Microsoft.Network/networkInterfaces@2015-05-01-preview' = {
  name: nicName
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipconfig1'
        properties: {
          privateIPAllocationMethod: 'Dynamic'
          subnet: {
            id: subnet1Ref
          }
        }
      }
    ]
  }
}
```

---


### <a name="resource-id-example"></a>Exemplo de ID de recursos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) a seguir devolve o ID do recurso para uma conta de armazenamento no grupo de recursos:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "sameRGOutput": {
      "type": "string",
      "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "differentRGOutput": {
      "type": "string",
      "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "differentSubOutput": {
      "type": "string",
      "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "nestedResourceOutput": {
      "type": "string",
      "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output sameRGOutput string = resourceId('Microsoft.Storage/storageAccounts','examplestorage')
output differentRGOutput string = resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
output differentSubOutput string = resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
output nestedResourceOutput string = resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscrições/{current-sub-id}/resourceGroups/exemplogroup/providers/Microsoft.Storage/storageAccounts/exemplostorage |
| diferenteRSoutput | String | /subscrições/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/exemplostorage |
| diferentesSubOutput | String | /subscrições/11111111-1111-1111-1111-111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAcontamentos/exemplostorage |
| nestedResourceOutput | String | /subscrições/{current-sub-id}/resourceGroups/exemplogroup/providers/Microsoft.SQL/servers/serverName/databases |

## <a name="subscription"></a>subscrição

`subscription()`

Devolve detalhes sobre a subscrição para a implementação atual.

### <a name="return-value"></a>Valor devolvido

A função devolve o seguinte formato:

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Observações

Ao utilizar modelos aninhados para implementar em várias subscrições, pode especificar o âmbito para avaliar a função de subscrição. Para obter mais informações, consulte [os recursos do Deploy Azure para mais de uma subscrição ou grupo de recursos.](./deploy-to-resource-group.md)

### <a name="subscription-example"></a>Exemplo de subscrição

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) a seguir mostra a função de subscrição chamada na secção de saídas.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "subscriptionOutput": {
      "value": "[subscription()]",
      "type" : "object"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output subscriptionOutput object = subscription()
```

---

## <a name="subscriptionresourceid"></a>subscriçãoResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

Devolve o identificador único para um recurso implantado ao nível da subscrição.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |cadeia (em formato GUID) |O valor predefinido é a subscrição atual. Especifique este valor quando necessitar de recuperar um recurso noutra subscrição. |
| resourceType |Yes |string |Tipo de recurso, incluindo espaço de nome do fornecedor de recursos. |
| recursoName1 |Yes |string |Nome do recurso. |
| recursoName2 |No |string |Próximo segmento de nome de recurso, se necessário. |

Continue a adicionar nomes de recursos como parâmetros quando o tipo de recurso inclui mais segmentos.

### <a name="return-value"></a>Valor devolvido

O identificador é devolvido no seguinte formato:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Observações

Utiliza esta função para obter o ID de recursos para recursos que são [implantados na subscrição](deploy-to-subscription.md) em vez de um grupo de recursos. O ID devolvido difere do valor devolvido pela função [resourceid](#resourceid) por não incluir um valor de grupo de recursos.

### <a name="subscriptionresourceid-example"></a>assinaturaResourceID exemplo

O modelo a seguir atribui um papel incorporado. Pode implantá-lo para um grupo de recursos ou para uma subscrição. Utiliza a função subscriçãoResourceId para obter o ID de recursos para funções incorporadas.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param principalId string {
  metadata: {
    'description': 'principalId'
  }
}
param builtInRoleType string {
  'allowed': [
    'Owner'
    'Contributor'
    'Reader'
  ]
  'metadata': {
      'description': 'Built-in role to assign'
  }
}
param roleNameGuid string {
  default: newGuid()
  metadata: {
    'description': 'A new GUID used to identify the role assignment'
  }
}

var roleDefinitionId = {
  Owner: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')
  }
  Contributor: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')
  }
  Reader: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')
  }
}

resource myRoleAssignment 'Microsoft.Authorization/roleAssignments@2018-09-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId[builtInRoleType].id
    principalId: principalId
  }
}
```

---

## <a name="tenantresourceid"></a>inquilinoResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

Devolve o identificador único para um recurso implantado ao nível do inquilino.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| resourceType |Yes |string |Tipo de recurso, incluindo espaço de nome do fornecedor de recursos. |
| recursoName1 |Yes |string |Nome do recurso. |
| recursoName2 |No |string |Próximo segmento de nome de recurso, se necessário. |

Continue a adicionar nomes de recursos como parâmetros quando o tipo de recurso inclui mais segmentos.

### <a name="return-value"></a>Valor devolvido

O identificador é devolvido no seguinte formato:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Observações

Você usa esta função para obter o ID de recurso para um recurso que é implantado para o inquilino. O ID devolvido difere dos valores devolvidos por outras funções de ID de recursos, não incluindo o grupo de recursos ou os valores de subscrição.

### <a name="tenantresourceid-example"></a>tenantResourceD exemplo

As definições políticas incorporadas são recursos ao nível dos inquilinos. Para implementar uma atribuição de política que refira uma definição de política incorporada, use a função TenantResourceId.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string",
      "defaultValue": "0a914e76-4921-4c19-b460-a2d36003525a",
      "metadata": {
        "description": "Specifies the ID of the policy definition or policy set definition being assigned."
      }
    },
    "policyAssignmentName": {
      "type": "string",
      "defaultValue": "[guid(parameters('policyDefinitionID'), resourceGroup().name)]",
      "metadata": {
        "description": "Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "name": "[parameters('policyAssignmentName')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "scope": "[subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)]",
        "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param policyDefinitionID string{
  default: '0a914e76-4921-4c19-b460-a2d36003525a'
  metadata: {
    'description': 'Specifies the ID of the policy definition or policy set definition being assigned.'
  }
}

param policyAssignmentName string {
  default: guid(policyDefinitionID, resourceGroup().name)
  metadata: {
    'description': 'Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides.'
  }
}

resource myPolicyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: policyAssignmentName
  properties: {
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    policyDefinitionId: tenantResourceId('Microsoft.Authorization/policyDefinitions', policyDefinitionID)
  }
}
```

---

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das secções num modelo ARM, consulte [a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
* Para fundir vários modelos, consulte [utilizando modelos ligados e aninhados ao implementar recursos Azure](linked-templates.md).
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte a iteração de [recursos nos modelos ARM](copy-resources.md).
* Para ver como implementar o modelo que criou, consulte [recursos de implantação com modelos ARM e Azure PowerShell](deploy-powershell.md).

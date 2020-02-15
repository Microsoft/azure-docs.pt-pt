---
title: Funções do modelo - recursos
description: Descreve as funções para utilizar num modelo do Azure Resource Manager para recuperar valores sobre os recursos.
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 10476f5a29c12d7437beb9a9f707feda815d7ba1
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207013"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Funções de recursos para modelos Azure Resource Manager

O Resource Manager proporciona as seguintes funções para obter valores do recurso:

* [extensãoResourceId](#extensionresourceid)
* [lista*](#list)
* [fornecedores](#providers)
* [referência](#reference)
* [recursosGroup](#resourcegroup)
* [recursosId](#resourceid)
* [subscrição](#subscription)
* [subscriçãoResourceId](#subscriptionresourceid)
* [inquilinoResourceId](#tenantresourceid)

Para obter valores a partir de parâmetros, variáveis ou a implementação atual, consulte funções de valor de [implantação](template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Devolve o ID de recurso para um recurso de [extensão,](../management/extension-resource-types.md)que é um tipo de recurso que é aplicado a outro recurso para adicionar às suas capacidades.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| resourceId |Sim |string |A ID de recurso para o recurso ao qual o recurso de extensão é aplicado. |
| resourceType |Sim |string |Tipo de recurso, incluindo o espaço de nomes de fornecedor de recursos. |
| resourceName1 |Sim |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome de recurso, se necessário. |

Continue adicionando nomes de recursos como parâmetros quando o tipo de recurso incluir mais segmentos.

### <a name="return-value"></a>Valor de retorno

O formato básico da ID do recurso retornado por essa função é:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

O segmento de escopo varia de acordo com o recurso que está sendo estendido.

Quando o recurso de extensão é aplicado a um **recurso,** o ID do recurso é devolvido no seguinte formato:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a um grupo de **recursos,** o formato é:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a uma **subscrição,** o formato é:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a um grupo de **gestão,** o formato é:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>exemplo de extensionResourceId

O exemplo a seguir retorna a ID de recurso para um bloqueio de grupo de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
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

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>list*

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

A sintaxe dessa função varia de acordo com o nome das operações de lista. Cada implementação retorna valores para o tipo de recurso que oferece suporte a uma operação de lista. O nome de funcionamento deve começar com `list`. Alguns usos comuns são `listKeys` e `listSecrets`.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| resourceName ou resourceIdentifier |Sim |string |Identificador exclusivo para o recurso. |
| apiVersion |Sim |string |Versão de API do Estado de runtime do recurso. Tipicamente, no formato, **yyy-mm-dd**. |
| functionValues |Não |objeto | Um objeto que tem valores para a função. Apenas forneça este objeto para funções que suportem a receção de um objeto com valores de parâmetros, como **listaSDeSas** numa conta de armazenamento. Um exemplo de passagem de valores de função é mostrado neste artigo. |

### <a name="valid-uses"></a>Usos válidos

As funções de lista só podem ser usadas nas propriedades de uma definição de recurso e na seção de saídas de um modelo ou implantação. Quando usado com [iteração de propriedade,](copy-properties.md)você pode usar as funções da lista para `input` porque a expressão é atribuída à propriedade do recurso. Não pode usá-los com `count` porque a contagem deve ser determinada antes que a função da lista seja resolvida.

### <a name="implementations"></a>Implementações

Os usos possíveis da lista * são mostrados na tabela a seguir.

| Tipo de recurso | Nome da função |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listaGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | ListKeys |
| Microsoft.Automation/automationAccounts | [listaKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [ficheiros de saída de listas](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [listaKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listaKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listaBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredenciais](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listaUtilizações](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listaEventos](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listaDetalhes](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listaClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listaClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listaCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listaulistas](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/contas/ações | [listSincronizações](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/contas/subscrições de ações | [listaSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/contas/subscrições de ações | [listaSincronizaçãoDetalhes](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/contas/subscrições de ações | [listSincronizações](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListaAplicável](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListaAgendas Aplicáveis](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListaAgendas Aplicáveis](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listaDeCadeias de Ligação](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listaKeys](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listaRecomendações domínio](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listaAcordos](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listaKeys](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listaKeys](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listaBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Bases de Dados | [Diretores de Listas](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListAmbientes](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft. Logic/integrationAccounts/Agreements | [listaContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/assemblies | [listaContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listaCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listaKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft. Logic/integrationAccounts/Maps | [listaContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listaContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listaContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listaCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listaExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listaExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listaCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listaCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listaKeys](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [listaKeys](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts | [listaKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listaContentorsas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listaStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listaContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [listaKeys](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listaServiçoSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listaFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listaFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listaActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listaPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Para determinar que tipos de recursos tem uma operação de lista, tem as seguintes opções:

* Consulte as operações da [API REST](/rest/api/) para um fornecedor de recursos e procure operações de lista. Por exemplo, as contas de armazenamento têm a [operação listKeys](/rest/api/storagerp/storageaccounts).
* Utilize o cmdlet [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell. O exemplo seguinte obtém todas as operações de lista para contas de armazenamento:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Utilize o seguinte comando da CLI do Azure para filtrar apenas as operações de lista:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Valor de retorno

O objeto retornado varia de acordo com a função de lista que você usa. Por exemplo, o listKeys para uma conta de armazenamento retorna o seguinte formato:

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

Outras funções de lista têm formatos de retornados diferentes. Para ver o formato de uma função, incluí-lo na secção de saídas como mostra o modelo de exemplo.

### <a name="remarks"></a>Observações

Especifique o recurso utilizando o nome do recurso ou a [função resourceId](#resourceid). Ao usar uma função de lista no mesmo modelo que implanta o recurso referenciado, use o nome do recurso.

Se utilizar uma função **de lista** num recurso que é implantado condicionalmente, a função é avaliada mesmo que o recurso não seja implantado. Obtém-se um erro se a função **da lista** se referir a um recurso que não existe. Utilize a função **se** para se certificar de que a função só é avaliada quando o recurso estiver a ser implantado. Consulte a [função se](template-functions-logical.md#if) para um modelo de amostra que utiliza se e lista com um recurso implantado condicionalmente.

### <a name="list-example"></a>Exemplo de lista

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) de exemplo seguinte mostra como devolver as chaves primárias e secundárias de uma conta de armazenamento na secção de saídas. Ele também retorna um token SAS para a conta de armazenamento.

Para obter o token SAS, passe um objeto para a hora de expiração. O tempo de expiração deve estar no futuro. Este exemplo destina-se para mostrar como utilizar as funções de lista. Normalmente, poderia usar o token SAS num valor de recursos em vez de retorná-lo como um valor de saída. Valores de saída são armazenados no histórico de implementação e não são seguras.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>Fornecedores

```json
providers(providerNamespace, [resourceType])
```

Devolve informações sobre um fornecedor de recursos e os tipos de recursos suportados. Se não fornecer um tipo de recurso, a função devolve todos os tipos suportados para o fornecedor de recursos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| providerNamespace |Sim |string |Espaço de nomes do fornecedor |
| resourceType |Não |string |O tipo de recurso dentro do espaço de nomes especificado. |

### <a name="return-value"></a>Valor de retorno

Cada tipo suportado é devolvido no seguinte formato:

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Ordenação de matriz de valores devolvidos não é garantido.

### <a name="providers-example"></a>Exemplo de provedores

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) de exemplo mostra como utilizar a função do fornecedor:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Para o fornecedor de recursos **Microsoft.Web** e tipo de recursos de **sites,** o exemplo anterior devolve um objeto no seguinte formato:

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

## <a name="reference"></a>Referência

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Devolve um objeto que representa o estado de runtime de um recurso.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| resourceName ou resourceIdentifier |Sim |string |Nome ou o identificador exclusivo de um recurso. Ao fazer referência um recurso no modelo atual, forneça apenas o nome de recurso como um parâmetro. Ao fazer referência a um recurso previamente implantado ou quando o nome do recurso for ambíguo, forneça o ID do recurso. |
| apiVersion |Não |string |Versão de API do recurso especificado. Inclua este parâmetro quando o recurso não está aprovisionado no mesmo modelo. Tipicamente, no formato, **yyy-mm-dd**. Para versões API válidas para o seu recurso, consulte a [referência do modelo](/azure/templates/). |
| "Completo" |Não |string |Valor que especifica se pretende devolver o objeto de recurso completo. Se não especificar `'Full'`, apenas as propriedades objeto do recurso são devolvidas. O objeto completo inclui valores, como o ID de recurso e o local. |

### <a name="return-value"></a>Valor de retorno

Cada tipo de recurso devolve as propriedades diferentes para a função de referência. A função não retornará um único formato predefinido. Além disso, o valor retornado é diferente com base no se especificou o objeto completo. Para ver as propriedades de um tipo de recurso, retornar o objeto na secção de saídas, conforme mostrado no exemplo.

### <a name="remarks"></a>Observações

A função de referência obtém o estado de tempo de execução de um recurso anteriormente implementado ou um recurso implementado no modelo atual. Este artigo mostra exemplos para ambos os cenários.

Normalmente, utiliza-se a função **de referência** para devolver um valor específico a um objeto, como o ponto final de blob URI ou o nome de domínio totalmente qualificado.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Use `'Full'` quando precisa de valores de recursos que não fazem parte do esquema de propriedades. Por exemplo, para definir políticas de acesso do Cofre de chaves, obter as propriedades de identidade para uma máquina virtual.

```json
{
  "type": "Microsoft.KeyVault/vaults",
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

### <a name="valid-uses"></a>Usos válidos

A função de referência pode ser apenas as propriedades de uma definição do recurso e na secção de saídas de um modelo ou a implementação. Quando utilizado com [iteração de propriedade,](copy-properties.md)pode utilizar a função de referência para `input` porque a expressão é atribuída à propriedade do recurso. Não pode usá-lo com `count` porque a contagem deve ser determinada antes de a função de referência ser resolvida.

Não pode utilizar a função de referência nas saídas de um [modelo aninhado](linked-templates.md#nested-template) para devolver um recurso que implementou no modelo aninhado. Em vez disso, use um [modelo ligado](linked-templates.md#linked-template).

Se utilizar a função de **referência** num recurso que é implantado condicionalmente, a função é avaliada mesmo que o recurso não seja implantado.  Obtém um erro se a função **de referência** se referir a um recurso que não existe. Utilize a função **se** para se certificar de que a função só é avaliada quando o recurso estiver a ser implantado. Consulte a [função se](template-functions-logical.md#if) para um modelo de amostra que utiliza se e referência com um recurso implantado condicionalmente.

### <a name="implicit-dependency"></a>Dependência implícita

Ao utilizar a função de referência, é implicitamente declarar que um recurso depende outro recurso, se o recurso referenciado está aprovisionado no mesmo modelo e consultar o recurso pelo respetivo nome (ID de recurso não). Não precisa de utilizar também a propriedade dependsOn. A função não é avaliada até que o recurso referenciado seja concluída a implementação.

### <a name="resource-name-or-identifier"></a>Identificador ou nome do recurso

Ao fazer referência a um recurso que é implantado no mesmo modelo, forneça o nome do recurso.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Ao fazer referência a um recurso que não está implantado no mesmo modelo, forneça a ID do recurso.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Para evitar ambiguidadesobre o recurso a que se refere, pode fornecer um identificador de recursos totalmente qualificado.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

Ao construir uma referência totalmente qualificada a um recurso, a ordem para combinar segmentos do tipo e do nome não é simplesmente uma concatenação dos dois. Em vez disso, após o espaço de nome, utilize uma sequência de pares de *tipo/nome* de menos específicos para os mais específicos:

**{espaço de nomedo para fornecedor de recursos}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

Por exemplo:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` está correta `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` não está correto.

Para simplificar a criação de qualquer ID de recurso, utilize as funções `resourceId()` descritas neste documento em vez da função `concat()`.

### <a name="get-managed-identity"></a>Obter identidade gerenciada

[As identidades geridas para os recursos Do Azure](../../active-directory/managed-identities-azure-resources/overview.md) são tipos de recursos de [extensão](../management/extension-resource-types.md) que são criados implicitamente para alguns recursos. Como a identidade gerenciada não é definida explicitamente no modelo, você deve referenciar o recurso ao qual a identidade é aplicada. Use `Full` para obter todas as propriedades, incluindo a identidade implicitamente criada.

Por exemplo, para obter a ID de locatário para uma identidade gerenciada que é aplicada a um conjunto de dimensionamento de máquinas virtuais, use:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Exemplo de referência

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) de exemplo seguinte implanta um recurso, e referências que o recurso.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": {
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
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

O exemplo anterior devolve os dois objetos. O objeto de propriedades é no seguinte formato:

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

O objeto completo é no seguinte formato:

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

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) de exemplo seguinte refere uma conta de armazenamento que não é implementada neste modelo. A conta de armazenamento já existe na mesma subscrição.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

```json
resourceGroup()
```

Devolve um objeto que representa o grupo de recursos atual.

### <a name="return-value"></a>Valor de retorno

O objeto devolvido é no seguinte formato:

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

A propriedade **gerida By** é devolvida apenas para grupos de recursos que contêm recursos que são geridos por outro serviço. Para aplicativos gerenciados, databricks e AKS, o valor da propriedade é a ID de recurso do recurso de gerenciamento.

### <a name="remarks"></a>Observações

A função `resourceGroup()` não pode ser usada num modelo que é [implantado ao nível de subscrição](deploy-to-subscription.md). Ele só pode ser usado em modelos implantados em um grupo de recursos. Pode utilizar a função `resourceGroup()` num [modelo ligado ou aninhado (com âmbito interno)](linked-templates.md) que visa um grupo de recursos, mesmo quando o modelo de progenitor é implantado na subscrição. Nesse cenário, o modelo ligado ou aninhado é implantado ao nível do grupo de recursos. Para obter mais informações sobre o alvo de um grupo de recursos numa implementação de nível de subscrição, consulte [os recursos do Deploy Azure para mais do que um grupo de subscrição ou recursos.](cross-resource-group-deployment.md)

Uma utilização comum da função resourceGroup é criar recursos na mesma localização que o grupo de recursos. O exemplo a seguir usa o local do grupo de recursos para um valor de parâmetro padrão.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

Você também pode usar a função resourcegroup para aplicar marcas do grupo de recursos a um recurso. Para mais informações, consulte [Apply tags do grupo de recursos](../management/tag-resources.md#apply-tags-from-resource-group).

Ao usar modelos aninhados para implantar em vários grupos de recursos, você pode especificar o escopo para avaliar a função resourcegroup. Para mais informações, consulte a Implantação de [recursos Azure para mais do que um grupo de subscrição ou recursos.](cross-resource-group-deployment.md)

### <a name="resource-group-example"></a>Exemplo de grupo de recursos

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) de exemplo devolve as propriedades do grupo de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

O exemplo anterior retorna um objeto no seguinte formato:

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

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

Devolve o identificador exclusivo de um recurso. Utilize esta função quando o nome do recurso é ambíguo ou não aprovisionada dentro do mesmo modelo. O formato do identificador devolvido varia em função de se a implantação ocorre no âmbito de um grupo de recursos, subscrição, grupo de gestão ou inquilino.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| subscriptionId |Não |a cadeia de caracteres (formato de GUID) |Valor predefinido é a subscrição atual. Especifica este valor quando precisar de recuperar um recurso noutra subscrição. Apenas forneça este valor ao ser implantado no âmbito de um grupo de recursos ou subscrição. |
| resourceGroupName |Não |string |Valor predefinido é o grupo de recursos atual. Especifica este valor quando precisar de recuperar um recurso noutro grupo de recursos. Apenas forneça este valor ao ser implantado no âmbito de um grupo de recursos. |
| resourceType |Sim |string |Tipo de recurso, incluindo o espaço de nomes de fornecedor de recursos. |
| resourceName1 |Sim |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome de recurso, se necessário. |

Continue adicionando nomes de recursos como parâmetros quando o tipo de recurso incluir mais segmentos.

### <a name="return-value"></a>Valor de retorno

Quando o modelo é implantado no âmbito de um grupo de recursos, o ID de recurso é devolvido no seguinte formato:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Quando utilizado numa implementação de [nível de subscrição,](deploy-to-subscription.md)o ID de recurso é devolvido no seguinte formato:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Quando utilizado numa implantação de [nível de grupo de gestão](deploy-to-management-group.md) ou implantação ao nível do inquilino, o ID de recurso é devolvido no seguinte formato:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Para obter a ID em outros formatos, consulte:

* [extensãoResourceId](#extensionresourceid)
* [subscriçãoResourceId](#subscriptionresourceid)
* [inquilinoResourceId](#tenantresourceid)

### <a name="remarks"></a>Observações

O número de parâmetros que você fornece varia dependendo de o recurso ser um recurso pai ou filho e se o recurso está na mesma assinatura ou grupo de recursos.

Para obter a ID de recurso de um recurso pai na mesma assinatura e grupo de recursos, forneça o tipo e o nome do recurso.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Para obter a ID de recurso de um recurso filho, preste atenção ao número de segmentos no tipo de recurso. Forneça um nome de recurso para cada segmento do tipo de recurso. O nome do segmento corresponde ao recurso que existe para essa parte da hierarquia.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Para obter a ID de recurso de um recurso na mesma assinatura, mas em um grupo de recursos diferente, forneça o nome do grupo de recursos.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Para obter a ID de recurso de um recurso em uma assinatura e um grupo de recursos diferentes, forneça a ID da assinatura e o nome do grupo de recursos.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Muitas vezes, terá de utilizar esta função quando utilizar uma conta de armazenamento ou a rede virtual num grupo de recursos alternativos. O exemplo seguinte mostra como um recurso de um grupo de recurso externo pode ser facilmente utilizado:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>Exemplo de ID de recurso

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) de exemplo seguinte devolve o ID de recurso para uma conta de armazenamento no grupo de recursos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| sameRGOutput | Cadeia | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Cadeia | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Cadeia | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Cadeia | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>subscrição

```json
subscription()
```

Devolve detalhes sobre a subscrição para a implementação atual.

### <a name="return-value"></a>Valor de retorno

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

Ao usar modelos aninhados para implantar em várias assinaturas, você pode especificar o escopo para avaliar a função de assinatura. Para mais informações, consulte a Implantação de [recursos Azure para mais do que um grupo de subscrição ou recursos.](cross-resource-group-deployment.md)

### <a name="subscription-example"></a>Exemplo de assinatura

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) de exemplo seguinte mostra a função de subscrição chamada na secção de saídas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="subscriptionresourceid"></a>subscriptionResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Retorna o identificador exclusivo de um recurso implantado no nível da assinatura.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| subscriptionId |Não |Cadeia de caracteres (no formato GUID) |Valor predefinido é a subscrição atual. Especifica este valor quando precisar de recuperar um recurso noutra subscrição. |
| resourceType |Sim |string |Tipo de recurso, incluindo o espaço de nomes de fornecedor de recursos. |
| resourceName1 |Sim |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome de recurso, se necessário. |

Continue adicionando nomes de recursos como parâmetros quando o tipo de recurso incluir mais segmentos.

### <a name="return-value"></a>Valor de retorno

O identificador é devolvido no seguinte formato:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Observações

Você usa esta função para obter o ID de recursos para recursos que são [implantados para a subscrição](deploy-to-subscription.md) em vez de um grupo de recursos. O ID devolvido difere do valor devolvido pela função [resourceId,](#resourceid) não incluindo um valor de grupo de recursos.

### <a name="subscriptionresourceid-example"></a>exemplo de subscriptionResourceID

O modelo a seguir atribui uma função interna. Você pode implantá-lo em um grupo de recursos ou assinatura. Ele usa a função subscriptionResourceId para obter a ID de recurso para funções internas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="tenantresourceid"></a>tenantResourceId

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

Retorna o identificador exclusivo de um recurso implantado no nível do locatário.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| resourceType |Sim |string |Tipo de recurso, incluindo o espaço de nomes de fornecedor de recursos. |
| resourceName1 |Sim |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome de recurso, se necessário. |

Continue adicionando nomes de recursos como parâmetros quando o tipo de recurso incluir mais segmentos.

### <a name="return-value"></a>Valor de retorno

O identificador é devolvido no seguinte formato:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Observações

Você usa essa função para obter a ID de recurso para um recurso que é implantado no locatário. A ID retornada difere dos valores retornados por outras funções de ID de recurso, não incluindo os valores de grupo de recursos ou de assinatura.

## <a name="next-steps"></a>Passos seguintes

* Para uma descrição das secções num modelo de Gestor de Recursos Azure, consulte os modelos de [Gestor de Recursos Azure da Autoria](template-syntax.md).
* Para fundir vários modelos, consulte [Utilizar modelos ligados com](linked-templates.md)o Gestor de Recursos Azure .
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [Criar múltiplas instâncias de recursos no Gestor de Recursos Azure](copy-resources.md).
* Para ver como implementar o modelo que criou, consulte [implementar uma aplicação com o modelo de Gestor](deploy-powershell.md)de Recursos Azure .


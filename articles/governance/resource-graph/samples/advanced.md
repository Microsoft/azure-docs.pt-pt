---
title: Exemplos de consultas avançadas
description: Use o grafo de recursos do Azure para executar algumas consultas avançadas, incluindo capacidade do conjunto de dimensionamento de máquinas virtuais, listagem de todas as marcas usadas e correspondência de máquinas virtuais com expressões regulares.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 8b848eac338260340cb9160a72f33294e51101f2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387581"
---
# <a name="advanced-resource-graph-queries"></a>Consultas avançadas do Azure Resource Graph

O primeiro passo para compreender as consultas com Azure Resource Graph é obter uma compreensão básica da [Linguagem de Consulta](../concepts/query-language.md). Se ainda não estiver familiarizado com o [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), recomenda-se que reveja os conceitos básicos para compreender como compor pedidos para os recursos que está a procurar.

Vamos examinar as seguintes consultas avançadas:

> [!div class="checklist"]
> - [Obter capacidade e tamanho do conjunto de dimensionamento de máquinas virtuais](#vmss-capacity)
> - [Remover colunas dos resultados](#remove-column)
> - [Listar todos os nomes de etiquetas](#list-all-tags)
> - [Máquinas virtuais correspondidas por regex](#vm-regex)
> - [Listar Cosmos DB com locais de gravação específicos](#mvexpand-cosmosdb)
> - [Key Vault com o nome da assinatura](#join)
> - [Listar bancos de dados SQL e seus pools elásticos](#join-sql)
> - [Listar máquinas virtuais com a interface de rede e o IP público](#join-vmpip)
> - [Localizar contas de armazenamento com uma marca específica no grupo de recursos](#join-findstoragetag)
> - [Combinar resultados de duas consultas em um único resultado](#unionresults)
> - [Incluir os nomes de locatário e assinatura com displaynames](#displaynames)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="language-support"></a>Suporte de idiomas

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Azure Resource Graph. Antes de executar qualquer uma das seguintes consultas, verifique se o ambiente está pronto. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter os passos para instalar e validar o seu ambiente shell escolhido.

## <a name="vmss-capacity"></a>Obter a capacidade e o tamanho do conjunto de dimensionamento de máquinas virtuais

Esta consulta procura recursos do conjunto de dimensionamento de máquinas virtuais e obtém vários detalhes, incluindo o tamanho da máquina virtual e a capacidade do conjunto de dimensionamento. A consulta utiliza a função `toint()` para converter a capacidade num número para poder ser ordenado. Por fim, o nome das colunas é mudado para propriedades de nome personalizadas.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="remove-column"></a>Remover colunas dos resultados

A consulta a seguir usa `summarize` para contar recursos por assinatura, `join` para combiná-lo com detalhes da assinatura da tabela _ResourceContainers_ e, em seguida, `project-away` para remover algumas das colunas.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

## <a name="list-all-tags"></a>Listar todos os nomes de etiquetas

Esta consulta começa com a etiqueta e cria um objeto JSON que lista todos os nomes de etiquetas exclusivos e os tipos correspondentes.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Máquinas virtuais correspondidas por regex

Esta consulta procura máquinas virtuais que correspondam a uma [expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference) (conhecida como _regex_). A **correspondência regex \@** nos permite definir o Regex para corresponder, que é `^Contoso(.*)[0-9]+$`.
Essa definição de regex é explicada como:

- `^` – A correspondência tem de começar no início da cadeia de caracteres.
- `Contoso` - a cadeia sensível às maiúsculas e minúsculas.
- `(.*)` -uma correspondência de subexpressões:
  - `.` – Corresponde a qualquer caracter único (exceto uma nova linha).
  - `*` – Corresponde ao elemento anterior zero ou mais vezes.
- `[0-9]` – O grupo de caracteres corresponde aos números 0 a 9.
- `+` – Corresponde ao elemento anterior uma ou mais vezes.
- `$` – A correspondência do elemento anterior deve ocorrer no final da cadeia de caracteres.

Depois da correspondência por nome, a consulta projeta o nome e ordena por nome em ordem crescente.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="mvexpand-cosmosdb"></a>Listar Cosmos DB com locais de gravação específicos

A consulta a seguir limita-se a Cosmos DB recursos, usa `mv-expand` para expandir o recipiente de propriedades para **Properties. writeLocations**, os campos específicos do projeto e limitar os resultados para **Propriedades. writeLocations. LocationName** valores correspondendo a ' leste dos EUA ' ou ' oeste dos EUA '.

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

## <a name="join"></a>Key Vault com o nome da assinatura

A consulta a seguir mostra um uso complexo de `join`. A consulta limita a tabela unida aos recursos de assinaturas e com `project` para incluir apenas o campo original _SubscriptionId_ e o campo de _nome_ renomeado como _subnomear_. A renomeação do campo evita `join` adicioná-lo como _Nome1_ , pois o campo já existe nos _recursos_. A tabela original é filtrada com `where` e o seguinte `project` inclui colunas de ambas as tabelas. O resultado da consulta é um único cofre de chaves que exibe o tipo, o nome do cofre de chaves e o nome da assinatura em que ele está.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

## <a name="join-sql"></a>Listar bancos de dados SQL e seus pools elásticos

A consulta a seguir usa o **leftouter** `join` para reunir os recursos do banco de dados SQL e seus pools elásticos relacionados, se eles tiverem algum.

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
on elasticPoolId
| project-away elasticPoolId1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

## <a name="join-vmpip"></a>Listar máquinas virtuais com a interface de rede e o IP público

Essa consulta usa dois comandos `join` **leftouter** para reunir máquinas virtuais, suas interfaces de rede relacionadas e qualquer endereço IP público relacionado a essas interfaces de rede.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mvexpand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mvexpand ipconfig=properties.ipConfigurations 
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

```azurecli-interactive
azure graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

## <a name="join-findstoragetag"></a>Localizar contas de armazenamento com uma marca específica no grupo de recursos

A consulta a seguir usa um **innerunique** `join` para conectar contas de armazenamento com grupos de recursos que têm um nome de marca e um valor de marca especificados.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=innerunique (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['key1'] == 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

## <a name="unionresults"></a>Combinar resultados de duas consultas em um único resultado

A consulta a seguir usa `union` para obter os resultados da tabela _ResourceContainers_ e adicioná-los aos resultados da tabela de _recursos_ .

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

## <a name="displaynames"></a>Incluir os nomes de locatário e assinatura com displaynames

Essa consulta usa o novo parâmetro **include** com opção _displaynames_ para adicionar **subscriptionDisplayName** e **tenantDisplayName** aos resultados. Esse parâmetro só está disponível para CLI do Azure e Azure PowerShell.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

> [!NOTE]
> Se a consulta não usar o **Project** para especificar as propriedades retornadas, **subscriptionDisplayName** e **tenantDisplayName** serão incluídos automaticamente nos resultados.
> Se a consulta usar o **Project**, cada um dos campos _DisplayName_ deverá ser incluído explicitamente no **projeto** ou não será retornado nos resultados, mesmo quando o parâmetro **include** for usado.

## <a name="next-steps"></a>Passos seguintes

- Ver exemplos de [Consultas de introdução](starter.md)
- Saber mais sobre a [linguagem de consulta](../concepts/query-language.md)
- Aprender a [explorar recursos](../concepts/explore-resources.md)
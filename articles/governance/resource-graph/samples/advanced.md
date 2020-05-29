---
title: Exemplos de consultas avançadas
description: Use o Gráfico de Recursos Azure para executar algumas consultas avançadas, incluindo trabalhar com colunas, listar etiquetas usadas e combinar recursos com expressões regulares.
ms.date: 03/20/2020
ms.topic: sample
ms.openlocfilehash: 7627c17d5b1c60fe9ad58328ab7f89d016a1ab72
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148009"
---
# <a name="advanced-resource-graph-query-samples"></a>Amostras avançadas de consulta de gráficos de recursos

O primeiro passo para compreender as consultas com Azure Resource Graph é obter uma compreensão básica da [Linguagem de Consulta](../concepts/query-language.md). Se ainda não estiver familiarizado com o [Azure Data Explorer](/azure/data-explorer/data-explorer-overview), recomenda-se que reveja os conceitos básicos para compreender como compor pedidos para os recursos que está a procurar.

Vamos examinar as seguintes consultas avançadas:

> [!div class="checklist"]
> - [Mostrar versão API para cada tipo de recurso](#apiversion)
> - [Obtenha capacidade e tamanho de escala de máquina virtual](#vmss-capacity)
> - [Remover colunas dos resultados](#remove-column)
> - [Listar todos os nomes de etiquetas](#list-all-tags)
> - [Máquinas virtuais correspondidas pela regex](#vm-regex)
> - [List Cosmos DB com locais de escrita específicos](#mvexpand-cosmosdb)
> - [Cofre de chave com nome de assinatura](#join)
> - [Listar bases de dados SQL e suas piscinas elásticas](#join-sql)
> - [Listar máquinas virtuais com a sua interface de rede e IP público](#join-vmpip)
> - [Encontre contas de armazenamento com uma etiqueta específica no grupo de recursos](#join-findstoragetag)
> - [Combine resultados de duas consultas num único resultado](#unionresults)
> - [Inclua o inquilino e nomes de subscrição com DisplayNames](#displaynames)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="language-support"></a>Suporte de idiomas

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Azure Resource Graph. Antes de executar qualquer uma das seguintes consultas, verifique se o ambiente está pronto. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter os passos para instalar e validar o seu ambiente shell escolhido.

## <a name="show-resource-types-and-api-versions"></a><a name="apiversion"></a>Mostrar tipos de recursos e versões API

O Resource Graph utiliza principalmente a versão não pré-visualização mais recente da API de um Fornecedor de Recursos para `GET` obter propriedades de recursos durante uma atualização. Em alguns casos, a versão API utilizada foi ultrapassada para fornecer propriedades mais atuais ou amplamente utilizadas nos resultados. A seguinte consulta detalha a versão API utilizada para recolher propriedades em cada tipo de recurso:

```kusto
Resources
| distinct type, apiVersion
| where isnotnull(apiVersion)
| order by type asc
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ícone de explorador de gráfico de recurso](../media/resource-graph-small.png) Experimente esta consulta no Azure Resource Graph Explorer:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">portal.azure.com</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">portal.azure.us</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">portal.azure.cn</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)

---

## <a name="get-virtual-machine-scale-set-capacity-and-size"></a><a name="vmss-capacity"></a>Obter a capacidade e o tamanho do conjunto de dimensionamento de máquinas virtuais

Esta consulta procura recursos do conjunto de dimensionamento de máquinas virtuais e obtém vários detalhes, incluindo o tamanho da máquina virtual e a capacidade do conjunto de dimensionamento. A consulta utiliza a função `toint()` para converter a capacidade num número para poder ser ordenado. Por fim, o nome das colunas é mudado para propriedades de nome personalizadas.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ícone de explorador de gráfico de recurso](../media/resource-graph-small.png) Experimente esta consulta no Azure Resource Graph Explorer:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.com</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.us</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.cn</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)

---

## <a name="remove-columns-from-results"></a><a name="remove-column"></a>Remover colunas dos resultados

A seguinte consulta usa `summarize` para contar recursos por subscrição, para `join` combiná-lo com detalhes de subscrição da tabela _ResourceContainers,_ em seguida, `project-away` para remover algumas das colunas.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ícone de explorador de gráfico de recurso](../media/resource-graph-small.png) Experimente esta consulta no Azure Resource Graph Explorer:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.com</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.us</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.cn</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)

---

## <a name="list-all-tag-names"></a><a name="list-all-tags"></a>Listar todos os nomes de etiquetas

Esta consulta começa com a etiqueta e cria um objeto JSON que lista todos os nomes de etiquetas exclusivos e os tipos correspondentes.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ícone de explorador de gráfico de recurso](../media/resource-graph-small.png) Experimente esta consulta no Azure Resource Graph Explorer:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">portal.azure.com</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">portal.azure.us</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">portal.azure.cn</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)

---

## <a name="virtual-machines-matched-by-regex"></a><a name="vm-regex"></a>Máquinas virtuais correspondidas por regex

Esta consulta procura máquinas virtuais que correspondam a uma [expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference) (conhecida como _regex_). Os **fósforos \@ regex** permite-nos definir o regex para combinar, que é `^Contoso(.*)[0-9]+$` .
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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ícone de explorador de gráfico de recurso](../media/resource-graph-small.png) Experimente esta consulta no Azure Resource Graph Explorer:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)

---

## <a name="list-cosmos-db-with-specific-write-locations"></a><a name="mvexpand-cosmosdb"></a>List Cosmos DB com locais de escrita específicos

Os seguintes limites de consulta aos recursos de Cosmos DB, usa `mv-expand` para expandir o saco de propriedades para **propriedades.writeLocations**, em seguida, projetar campos específicos e limitar os resultados mais a **propriedades.writeLocations.locationName valores correspondentes** a 'East US' ou 'West US'.

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ícone de explorador de gráfico de recurso](../media/resource-graph-small.png) Experimente esta consulta no Azure Resource Graph Explorer:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.com</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.us</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.cn</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)

---

## <a name="key-vault-with-subscription-name"></a><a name="join"></a>Cofre de chave com nome de assinatura

A seguinte consulta mostra uma utilização complexa de `join` . A consulta limita a tabela associada aos recursos de subscrições e `project` com a inclusão apenas da subscrição original _de campoId_ e o campo _de nome_ renomeado para _SubName_. O renomeador de campo evita `join` adicioná-lo como _nome1_ uma vez que o campo já existe em _recursos._ A tabela original é filtrada `where` e a seguinte inclui `project` colunas de ambas as tabelas. O resultado da consulta é um único tipo de exposição de um cofre de chave, o nome do cofre da chave, e o nome da subscrição em que está.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ícone de explorador de gráfico de recurso](../media/resource-graph-small.png) Experimente esta consulta no Azure Resource Graph Explorer:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">portal.azure.com</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">portal.azure.us</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">portal.azure.cn</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)

---

## <a name="list-sql-databases-and-their-elastic-pools"></a><a name="join-sql"></a>Listar bases de dados SQL e suas piscinas elásticas

A seguinte consulta usa **leftouter** `join` para reunir recursos sql base de dados e suas piscinas elásticas relacionadas, se tiverem alguma.

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ícone de explorador de gráfico de recurso](../media/resource-graph-small.png) Experimente esta consulta no Azure Resource Graph Explorer:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.com</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.us</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.cn</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)

---

## <a name="list-virtual-machines-with-their-network-interface-and-public-ip"></a><a name="join-vmpip"></a>Listar máquinas virtuais com a sua interface de rede e IP público

Esta consulta utiliza dois comandos **leftouter** `join` para reunir máquinas virtuais, as suas interfaces de rede relacionadas e qualquer endereço IP público relacionado com essas interfaces de rede.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mv-expand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mv-expand ipconfig=properties.ipConfigurations 
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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ícone de explorador de gráfico de recurso](../media/resource-graph-small.png) Experimente esta consulta no Azure Resource Graph Explorer:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">portal.azure.com</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">portal.azure.us</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">portal.azure.cn</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)

---

## <a name="find-storage-accounts-with-a-specific-tag-on-the-resource-group"></a><a name="join-findstoragetag"></a>Encontre contas de armazenamento com uma etiqueta específica no grupo de recursos

A seguinte consulta utiliza um **interior** `join` para ligar contas de armazenamento com grupos de recursos que têm um nome de etiqueta sensível a caso especificado e valor de etiqueta.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['Key1'] =~ 'Value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ícone de explorador de gráfico de recurso](../media/resource-graph-small.png) Experimente esta consulta no Azure Resource Graph Explorer:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'Key1'%5D%20%3D~%20'Value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.com</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'Key1'%5D%20%3D~%20'Value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.us</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'Key1'%5D%20%3D~%20'Value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.cn</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)

---

Se for necessário procurar um nome de etiqueta insensível e valor da etiqueta, use `mv-expand` com o parâmetro de **bagexpansion.** Esta consulta utiliza mais quota do que a consulta anterior, pelo que `mv-expand` utiliza-se apenas se necessário.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | mv-expand bagexpansion=array tags
    | where isnotempty(tags)
    | where tags[0] =~ 'key1' and tags[1] =~ 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ícone de explorador de gráfico de recurso](../media/resource-graph-small.png) Experimente esta consulta no Azure Resource Graph Explorer:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%20%7C%20join%20kind%3Dinner%20%28%20ResourceContainers%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%20%7C%20where%20isnotempty%28tags%29%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.com</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%20%7C%20join%20kind%3Dinner%20%28%20ResourceContainers%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%20%7C%20where%20isnotempty%28tags%29%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.us</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%20%7C%20join%20kind%3Dinner%20%28%20ResourceContainers%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%20%7C%20where%20isnotempty%28tags%29%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.cn</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)

---

## <a name="combine-results-from-two-queries-into-a-single-result"></a><a name="unionresults"></a>Combine resultados de duas consultas num único resultado

A seguinte consulta `union` utiliza-se para obter resultados da tabela _ResourceContainers_ e adicioná-los aos resultados da tabela _Recursos._

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ícone de explorador de gráfico de recurso](../media/resource-graph-small.png) Experimente esta consulta no Azure Resource Graph Explorer:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">portal.azure.com</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">portal.azure.us</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">portal.azure.cn</a> ![ Ligação aberta no novo ícone da janela](../../media/new-window.png)

---

## <a name="include-the-tenant-and-subscription-names-with-displaynames"></a><a name="displaynames"></a>Inclua o inquilino e nomes de subscrição com DisplayNames

Esta consulta utiliza o novo parâmetro **Incluir** com opção _DisplayNames_ para adicionar **subscriçãoDisplayName** e **inquilinoDisplayName** aos resultados. Este parâmetro só está disponível para Azure CLI e Azure PowerShell.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

Uma alternativa para obter o nome de subscrição é usar o `join` operador e ligar-se à tabela **ResourceContainers** e ao `Microsoft.Resources/subscriptions` tipo. `join`funciona em Azure CLI, Azure PowerShell, portal, e todos suportados SDK. Por exemplo, consulte [Sample - Key vault com nome de assinatura](#join).

> [!NOTE]
> Se a consulta não utilizar o **projeto** para especificar as propriedades **devolvidas, subscriçãoDisplayName** e **inquilinoDisplayName** são automaticamente incluídos nos resultados.
> Se a consulta utilizar o **projeto,** cada um dos campos _DisplayName_ deve ser explicitamente incluído no **projeto** ou não será devolvido nos resultados, mesmo quando o parâmetro **Incluir** é utilizado. O parâmetro **Incluir** não funciona com [tabelas.](../concepts/query-language.md#resource-graph-tables)

## <a name="next-steps"></a>Próximos passos

- Consulte amostras de consultas de [arranque.](starter.md)
- Saiba mais sobre a [língua de consulta.](../concepts/query-language.md)
- Saiba mais sobre como [explorar recursos.](../concepts/explore-resources.md)
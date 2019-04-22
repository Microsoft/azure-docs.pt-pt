---
title: Exemplos de consulta Starter
description: Gráfico de recursos do Azure de uso para executar alguns starter consulta, incluindo recursos de crescimento, ordenação de recursos, ou por uma etiqueta específica.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/04/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2ba48e2a21bdee0c5698bdfa314dd3bf462c1c7e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267774"
---
# <a name="starter-resource-graph-queries"></a>Consultas de introdução do Azure Resource Graph

O primeiro passo para compreender as consultas com Azure Resource Graph é obter uma compreensão básica da [Linguagem de Consulta](../concepts/query-language.md). Se ainda não estiver familiarizado com o [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), recomenda-se que reveja os conceitos básicos para compreender como compor pedidos para os recursos que está a procurar.

Vamos examinar as seguintes consultas de introdução:

> [!div class="checklist"]
> - [Contar recursos do Azure](#count-resources)
> - [Listar recursos ordenados por nome](#list-resources)
> - [Mostrar todas as máquinas virtuais ordenadas por nome em ordem descendente](#show-vms)
> - [Mostrar as primeiras cinco máquinas virtuais por nome e por tipo de SO](#show-sorted)
> - [Contar máquinas virtuais por tipo de SO](#count-os)
> - [Mostrar recursos que contêm armazenamento](#show-storage)
> - [Listar todos os endereços IP públicos](#list-publicip)
> - [Contar recursos que tenham endereços IP configurados por subscrição](#count-resources-by-ip)
> - [Listar recursos com um valor de etiqueta específico](#list-tag)
> - [Listar todas as contas de armazenamento com um valor de etiqueta específico](#list-specific-tag)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Suporte de idiomas

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Azure Resource Graph. Antes de executar qualquer uma das seguintes consultas, verifique se o ambiente está pronto. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter os passos para instalar e validar o seu ambiente shell escolhido.

## <a name="count-resources"></a>Contar recursos do Azure

Esta consulta devolve o número de recursos do Azure que existem nas subscrições às quais tem acesso. Também é uma boa consulta para confirmar que a shell escolhida tem os componentes apropriados do Azure Resource Graph instalados e em condições de funcionamento.

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="list-resources"></a>Listar recursos ordenados por nome

Esta consulta devolve qualquer tipo de recurso, mas apenas as propriedades **nome**, **tipo** e **localização**. Ela utiliza `order by` para ordenar as propriedades pela propriedade **nome** por ordem ascendente (`asc`).

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>Mostrar todas as máquinas virtuais ordenadas por nome em ordem descendente

Para listar apenas as máquinas virtuais (que são do tipo `Microsoft.Compute/virtualMachines`), podemos corresponder a propriedade **tipo** nos resultados. À semelhança da consulta anterior, `desc` altera `order by` para ser descendente. O `=~` na correspondência de tipo informa o Resource Graph para não ser sensível a maiúsculas e minúsculas.

```Query
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="show-sorted"></a>Mostrar as primeiras cinco máquinas virtuais por nome e por tipo de SO

Esta consulta vai utilizar `limit` para obter apenas cinco registos correspondentes, ordenados por nome. O tipo do recurso do Azure é `Microsoft.Compute/virtualMachines`. `project` informa o Azure Resource Graph quais as propriedades a incluir.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="count-os"></a>Contar máquinas virtuais por tipo de SO

Aproveitando a consulta anterior, estamos ainda limitados pelos recursos do Azure do tipo `Microsoft.Compute/virtualMachines`, mas já não há limite sobre o número de registros devolvidos.
Em vez disso, utilizamos `summarize` e `count()` para definir como agrupar e agregar os valores pela propriedade, que, neste exemplo, é `properties.storageProfile.osDisk.osType`. Para obter um exemplo do aspeto desta cadeia de caracteres no objeto completo, veja [explorar recursos – deteção de máquinas virtuais](../concepts/explore-resources.md#virtual-machine-discovery).

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Uma forma diferente de escrever a mesma consulta é `extend` a uma propriedade e dar-lhe um nome temporário para utilizar dentro da consulta, neste caso, **so**. **so** é, em seguida, utilizado por `summarize` e `count()` como no exemplo anterior.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Esteja ciente de que, embora `=~` permita a correspondência não sensível a maiúsculas e minúsculas, a utilização de propriedades (como **properties.storageProfile.osDisk.osType**) na consulta requer que a utilização de maiúsculas/minúsculas esteja correta. Se a propriedade tiver uma utilização incorreta das maiúsculas/minúsculas, poderá ainda devolver um valor, mas o agrupamento ou o resumo estarão incorretos.

## <a name="show-storage"></a>Mostrar recursos que contêm armazenamento

Em vez de definir explicitamente o tipo a corresponder, esta consulta de exemplo vai procurar qualquer recurso do Azure que `contains` a palavra **armazenamento**.

```Query
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>Listar todos os endereços IP públicos

À semelhança da consulta anterior, encontra tudo o que era de um tipo com a palavra **publicIPAddresses**.
Esta consulta expande esse padrão para incluir apenas os resultados em que **properties.ipAddress**
`isnotempty`, para devolver apenas os **properties.ipAddress**e, a `limit` o resultados por parte superior
100. Poderá ter de evitar as aspas consoante a shell escolhida.

```Query
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="count-resources-by-ip">Contar recursos que tenham endereços IP configurados por subscrição</a>

Com a consulta do exemplo anterior e ao adicionar `summarize` e `count()`, podemos obter uma lista por subscrição dos recursos com endereços IP configurados.

```Query
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="list-tag"></a>Listar recursos com um valor de etiqueta específico

Podemos limitar os resultados por propriedades que não sejam do tipo de recurso do Azure, como uma etiqueta. Neste exemplo, está a filtrar recursos do Azure com um nome de etiqueta de **Ambiente** que têm um valor de **Interno**.

```Query
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

Também indica as etiquetas que o recurso tem e os seus valores, adicione a propriedade **etiquetas** à palavra-chave `project`.

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>Listar todas as contas de armazenamento com um valor de etiqueta específico

Combine a funcionalidade de filtro do exemplo anterior e filtre o tipo de recurso do Azure por **tipo** de propriedade. Esta consulta também limita a nossa pesquisa para tipos específicos de recursos do Azure com um nome e valor específicos de etiqueta.

```Query
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> Este exemplo utiliza `==` para a correspondência em vez do `=~` condicional. `==` é uma correspondência sensível a maiúsculas e minúsculas.

## <a name="next-steps"></a>Passos Seguintes

- Saber mais sobre a [linguagem de consulta](../concepts/query-language.md)
- Aprender a [explorar recursos](../concepts/explore-resources.md)
- Ver exemplos de [Consultas avançadas](advanced.md)
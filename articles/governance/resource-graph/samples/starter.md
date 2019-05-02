---
title: Exemplos de consulta Starter
description: Gráfico de recursos do Azure de uso para executar alguns starter consulta, incluindo recursos de crescimento, ordenação de recursos, ou por uma etiqueta específica.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 98b05f74f0d6f7d20b5aa7ed77047818f217f147
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691181"
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
> - [Mostrar aliases para um recurso de máquina virtual](#show-aliases)
> - [Mostrar valores distintos para um alias específico](#distinct-alias-values)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Suporte de idiomas

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Azure Resource Graph. Antes de executar qualquer uma das seguintes consultas, verifique se o ambiente está pronto. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter os passos para instalar e validar o seu ambiente shell escolhido.

## <a name="a-namecount-resourcescount-azure-resources"></a><a name="count-resources"/>Recursos do Azure de contagem

Esta consulta devolve o número de recursos do Azure que existem nas subscrições às quais tem acesso. Também é uma boa consulta para confirmar que a shell escolhida tem os componentes apropriados do Azure Resource Graph instalados e em condições de funcionamento.

```kusto
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="a-namelist-resourceslist-resources-sorted-by-name"></a><a name="list-resources"/>Recursos de lista classificados por nome

Esta consulta devolve qualquer tipo de recurso, mas apenas as propriedades **nome**, **tipo** e **localização**. Ela utiliza `order by` para ordenar as propriedades pela propriedade **nome** por ordem ascendente (`asc`).

```kusto
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="a-nameshow-vmsshow-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"/>Mostrar todas as máquinas virtuais, ordenadas por nome por ordem descendente

Para listar apenas as máquinas virtuais (que são do tipo `Microsoft.Compute/virtualMachines`), podemos corresponder a propriedade **tipo** nos resultados. À semelhança da consulta anterior, `desc` altera `order by` para ser descendente. O `=~` na correspondência de tipo informa o Resource Graph para não ser sensível a maiúsculas e minúsculas.

```kusto
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

## <a name="a-nameshow-sortedshow-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"/>Mostrar os primeiros cinco máquinas virtuais por nome e o respetivo tipo de SO

Esta consulta vai utilizar `limit` para obter apenas cinco registos correspondentes, ordenados por nome. O tipo do recurso do Azure é `Microsoft.Compute/virtualMachines`. `project` informa o Azure Resource Graph quais as propriedades a incluir.

```kusto
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

## <a name="a-namecount-oscount-virtual-machines-by-os-type"></a><a name="count-os"/>Contagem de máquinas virtuais ao tipo de SO

Aproveitando a consulta anterior, estamos ainda limitados pelos recursos do Azure do tipo `Microsoft.Compute/virtualMachines`, mas já não há limite sobre o número de registros devolvidos.
Em vez disso, utilizamos `summarize` e `count()` para definir como agrupar e agregar os valores pela propriedade, que, neste exemplo, é `properties.storageProfile.osDisk.osType`. Para obter um exemplo do aspeto desta cadeia de caracteres no objeto completo, veja [explorar recursos – deteção de máquinas virtuais](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
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

```kusto
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

## <a name="a-nameshow-storageshow-resources-that-contain-storage"></a><a name="show-storage"/>Mostrar recursos que contêm o armazenamento

Em vez de definir explicitamente o tipo a corresponder, esta consulta de exemplo vai procurar qualquer recurso do Azure que `contains` a palavra **armazenamento**.

```kusto
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="a-namelist-publiciplist-all-public-ip-addresses"></a><a name="list-publicip"/>Lista de endereços IP públicos de todos os

À semelhança da consulta anterior, encontra tudo o que era de um tipo com a palavra **publicIPAddresses**.
Esta consulta expande esse padrão para incluir apenas os resultados em que **properties.ipAddress**
`isnotempty`, para devolver apenas os **properties.ipAddress**e, a `limit` o resultados por parte superior
100. Poderá ter de evitar as aspas consoante a shell escolhida.

```kusto
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

## <a name="a-namecount-resources-by-ipcount-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"/>Contagem de recursos que tenham endereços IP configurados por subscrição

Com a consulta do exemplo anterior e ao adicionar `summarize` e `count()`, podemos obter uma lista por subscrição dos recursos com endereços IP configurados.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="a-namelist-taglist-resources-with-a-specific-tag-value"></a><a name="list-tag"/>Lista de recursos com um valor de etiqueta específica

Podemos limitar os resultados por propriedades que não sejam do tipo de recurso do Azure, como uma etiqueta. Neste exemplo, está a filtrar recursos do Azure com um nome de etiqueta de **Ambiente** que têm um valor de **Interno**.

```kusto
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

```kusto
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="a-namelist-specific-taglist-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"/>Listar todas as contas de armazenamento com o valor de etiqueta específica

Combine a funcionalidade de filtro do exemplo anterior e filtre o tipo de recurso do Azure por **tipo** de propriedade. Esta consulta também limita a nossa pesquisa para tipos específicos de recursos do Azure com um nome e valor específicos de etiqueta.

```kusto
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

## <a name="a-nameshow-aliasesshow-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases"/>Mostrar aliases para um recurso de máquina virtual

[Aliases de política do Azure](../../policy/concepts/definition-structure.md#aliases) são utilizados pela política do Azure para gerir a conformidade de recursos. Gráfico de recursos do Azure pode retornar a _aliases_ de um tipo de recurso. Estes valores são úteis para comparar o valor atual de aliases, ao criar uma definição de política personalizada. O _aliases_ matriz não for fornecida por predefinição nos resultados de uma consulta. Utilize `project aliases` para adicioná-lo explicitamente para os resultados.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

## <a name="a-namedistinct-alias-valuesshow-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values"/>Mostrar valores distintos para um alias específico

Ver o valor de aliases num único recurso é útil, mas não mostra o verdadeiro valor das usando o gráfico de recursos do Azure para consulta em várias subscrições. Este exemplo aborda todos os valores de um alias específico e devolve os valores distintos.

```kusto
where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)"
```

```azurecli-interactive
az graph query -q "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

## <a name="next-steps"></a>Passos Seguintes

- Saber mais sobre a [linguagem de consulta](../concepts/query-language.md)
- Aprender a [explorar recursos](../concepts/explore-resources.md)
- Ver exemplos de [Consultas avançadas](advanced.md)
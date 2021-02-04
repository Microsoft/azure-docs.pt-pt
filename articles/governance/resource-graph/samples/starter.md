---
title: Exemplos de consultas iniciais
description: Use o Gráfico de Recursos Azure para executar algumas consultas de arranque, incluindo a contagem de recursos, encomendando recursos ou por uma etiqueta específica.
ms.date: 02/04/2021
ms.topic: sample
ms.openlocfilehash: f3ff78f52f84ad4fac74fa6e7b04de3e645a2fff
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99557970"
---
# <a name="starter-resource-graph-query-samples"></a>Amostras de consulta de gráfico de recurso de início

O primeiro passo para compreender as consultas com Azure Resource Graph é obter uma compreensão básica da [Linguagem de Consulta](../concepts/query-language.md). Se ainda não está familiarizado com [a Língua de Consulta de Kusto (KQL),](/azure/kusto/query/index)é aconselhável rever o tutorial da [KQL para](/azure/kusto/query/tutorial) entender como compor pedidos para os recursos que procura.

Vamos examinar as seguintes consultas de introdução:

- [Contar recursos do Azure](#count-resources)
- [Conte recursos chave cofre](#count-keyvaults)
- [Listar recursos ordenados pelo nome](#list-resources)
- [Mostrar todas as máquinas virtuais encomendadas pelo nome em ordem descendente](#show-vms)
- [Mostrar as primeiras cinco máquinas virtuais pelo nome e o seu tipo de SO](#show-sorted)
- [Contar máquinas virtuais por tipo de SO](#count-os)
- [Mostrar recursos que contenham armazenamento](#show-storage)
- [Listar todos os endereços IP públicos](#list-publicip)
- [Conte recursos que tenham endereços IP configurados por subscrição](#count-resources-by-ip)
- [Listar recursos com um valor específico de etiqueta](#list-tag)
- [Listar todas as contas de armazenamento com valor específico da etiqueta](#list-specific-tag)
- [Listar todas as etiquetas e seus valores](#list-all-tag-values)
- [Mostrar grupos de segurança de rede não ligados](#unassociated-nsgs)
- [Obtenha resumo de poupança de custos do Azure Advisor](#advisor-savings)
- [Conte máquinas no âmbito das políticas de Configuração de Hóspedes](#count-gcmachines)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="language-support"></a>Suporte de idiomas

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Azure Resource Graph. Antes de executar qualquer uma das seguintes consultas, verifique se o ambiente está pronto. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter os passos para instalar e validar o seu ambiente shell escolhido.

## <a name="count-azure-resources"></a><a name="count-resources"></a>Contar recursos do Azure

Esta consulta devolve o número de recursos do Azure que existem nas subscrições às quais tem acesso. Também é uma boa consulta para confirmar que a shell escolhida tem os componentes apropriados do Azure Resource Graph instalados e em condições de funcionamento.

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">portal.azure.cn</a>

---

## <a name="count-key-vault-resources"></a><a name="count-keyvaults"></a>Conte recursos chave cofre

Esta consulta utiliza `count` em vez de contar o número de `summarize` registos devolvidos. Apenas cofres chave estão incluídos na contagem.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">portal.azure.cn</a>

---

## <a name="list-resources-sorted-by-name"></a><a name="list-resources"></a>Listar recursos ordenados por nome

Esta consulta devolve qualquer tipo de recurso, mas apenas as propriedades **nome**, **tipo** e **localização**. Ela utiliza `order by` para ordenar as propriedades pela propriedade **nome** por ordem ascendente (`asc`).

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a>

---

## <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"></a>Mostrar todas as máquinas virtuais ordenadas por nome em ordem descendente

Para listar apenas as máquinas virtuais (que são do tipo `Microsoft.Compute/virtualMachines`), podemos corresponder a propriedade **tipo** nos resultados. À semelhança da consulta anterior, `desc` altera `order by` para ser descendente. O `=~` na correspondência de tipo informa o Resource Graph para não ser sensível a maiúsculas e minúsculas.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">portal.azure.cn</a>

---

## <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"></a>Mostrar as primeiras cinco máquinas virtuais por nome e por tipo de SO

Esta consulta vai utilizar `top` para obter apenas cinco registos correspondentes, ordenados por nome. O tipo do recurso do Azure é `Microsoft.Compute/virtualMachines`. `project` informa o Azure Resource Graph quais as propriedades a incluir.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.cn</a>

---

## <a name="count-virtual-machines-by-os-type"></a><a name="count-os"></a>Contar máquinas virtuais por tipo de SO

Aproveitando a consulta anterior, estamos ainda limitados pelos recursos do Azure do tipo `Microsoft.Compute/virtualMachines`, mas já não há limite sobre o número de registros devolvidos.
Em vez disso, utilizamos `summarize` e `count()` para definir como agrupar e agregar os valores pela propriedade, que, neste exemplo, é `properties.storageProfile.osDisk.osType`. Para obter um exemplo do aspeto desta cadeia de caracteres no objeto completo, veja [explorar recursos – deteção de máquinas virtuais](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">portal.azure.cn</a>

---

Uma forma diferente de escrever a mesma consulta é `extend` a uma propriedade e dar-lhe um nome temporário para utilizar dentro da consulta, neste caso, **so**. **so** é, em seguida, utilizado por `summarize` e `count()` como no exemplo anterior.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">portal.azure.cn</a>

---

> [!NOTE]
> Esteja ciente de que, embora `=~` permita a correspondência não sensível a maiúsculas e minúsculas, a utilização de propriedades (como **properties.storageProfile.osDisk.osType**) na consulta requer que a utilização de maiúsculas/minúsculas esteja correta. Se a propriedade for o caso incorreto, um valor nulo ou incorreto é devolvido e o agrupamento ou resumo estaria incorreto.

## <a name="show-resources-that-contain-storage"></a><a name="show-storage"></a>Mostrar recursos que contêm armazenamento

Em vez de definir explicitamente o tipo a corresponder, esta consulta de exemplo vai procurar qualquer recurso do Azure que `contains` a palavra **armazenamento**.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">portal.azure.cn</a>

---

## <a name="list-all-public-ip-addresses"></a><a name="list-publicip"></a>Listar todos os endereços IP públicos

À semelhança da consulta anterior, encontra tudo o que era de um tipo com a palavra **publicIPAddresses**.
Esta consulta expande-se nesse padrão para incluir apenas resultados onde **propriedades.ipAddress,** 
 `isnotempty` para apenas devolver as **propriedades.ipAddress**, e aos `limit` resultados por cima
100. Poderá ter de evitar as aspas consoante a shell escolhida.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">portal.azure.cn</a>

---

## <a name="count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"></a>Contar recursos que tenham endereços IP configurados por subscrição

Com a consulta do exemplo anterior e ao adicionar `summarize` e `count()`, podemos obter uma lista por subscrição dos recursos com endereços IP configurados.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">portal.azure.cn</a>

---

## <a name="list-resources-with-a-specific-tag-value"></a><a name="list-tag"></a>Listar recursos com um valor de etiqueta específico

Podemos limitar os resultados por propriedades que não sejam do tipo de recurso do Azure, como uma etiqueta. Neste exemplo, está a filtrar recursos do Azure com um nome de etiqueta de **Ambiente** que têm um valor de **Interno**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">portal.azure.cn</a>

---

Também indica as etiquetas que o recurso tem e os seus valores, adicione a propriedade **etiquetas** à palavra-chave `project`.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">portal.azure.cn</a>

---

## <a name="list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"></a>Listar todas as contas de armazenamento com um valor de etiqueta específico

Combine a funcionalidade de filtro do exemplo anterior e filtre o tipo de recurso do Azure por **tipo** de propriedade. Esta consulta também limita a nossa pesquisa para tipos específicos de recursos do Azure com um nome e valor específicos de etiqueta.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">portal.azure.cn</a>

---

> [!NOTE]
> Este exemplo utiliza `==` para a correspondência em vez do `=~` condicional. `==` é uma correspondência sensível a maiúsculas e minúsculas.

## <a name="list-all-tags-and-their-values"></a><a name="list-all-tag-values"></a>Listar todas as etiquetas e seus valores

Esta consulta lista etiquetas em grupos de gestão, subscrições e recursos, juntamente com os seus valores.
A consulta primeiro limita os recursos onde as `isnotempty()` etiquetas, limita os campos incluídos apenas incluindo tags no , e para obter os  `project` `mvexpand` `extend` dados emparelhados do saco de propriedade. Em seguida, `union` utiliza-se para combinar os resultados dos _ResourceContainers_ com os mesmos resultados dos _Recursos,_ dando uma ampla cobertura às quais as etiquetas são recolhidas. Por último, limita os resultados a `distinct` dados emparelhados e exclui tags ocultas do sistema.

```kusto
ResourceContainers 
| where isnotempty(tags)
| project tags
| mvexpand tags
| extend tagKey = tostring(bag_keys(tags)[0])
| extend tagValue = tostring(tags[tagKey])
| union (
    resources
    | where isnotempty(tags)
    | project tags
    | mvexpand tags
    | extend tagKey = tostring(bag_keys(tags)[0])
    | extend tagValue = tostring(tags[tagKey])
)
| distinct tagKey, tagValue
| where tagKey !startswith "hidden-"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) | union (resources | where notempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) ) | distinct tagKey, tagValue | where tagKey !startswith "hidden-""
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) | union (resources | where notempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) ) | distinct tagKey, tagValue | where tagKey !startswith "hidden-""
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%0A%7C%20where%20isnotempty%28tags%29%0A%7C%20project%20tags%0A%7C%20mvexpand%20tags%0A%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%7C%20union%20%28%0A%20%20%20%20resources%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0A%20%20%20%20%7C%20project%20tags%0A%20%20%20%20%7C%20mvexpand%20tags%0A%20%20%20%20%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%20%20%20%20%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%29%0A%7C%20distinct%20tagKey%2C%20tagValue%0A%7C%20where%20tagKey%20%21startswith%20%22hidden-%22" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%0A%7C%20where%20isnotempty%28tags%29%0A%7C%20project%20tags%0A%7C%20mvexpand%20tags%0A%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%7C%20union%20%28%0A%20%20%20%20resources%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0A%20%20%20%20%7C%20project%20tags%0A%20%20%20%20%7C%20mvexpand%20tags%0A%20%20%20%20%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%20%20%20%20%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%29%0A%7C%20distinct%20tagKey%2C%20tagValue%0A%7C%20where%20tagKey%20%21startswith%20%22hidden-%22" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%0A%7C%20where%20isnotempty%28tags%29%0A%7C%20project%20tags%0A%7C%20mvexpand%20tags%0A%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%7C%20union%20%28%0A%20%20%20%20resources%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0A%20%20%20%20%7C%20project%20tags%0A%20%20%20%20%7C%20mvexpand%20tags%0A%20%20%20%20%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%20%20%20%20%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%29%0A%7C%20distinct%20tagKey%2C%20tagValue%0A%7C%20where%20tagKey%20%21startswith%20%22hidden-%22" target="_blank">portal.azure.cn</a>

---

## <a name="show-unassociated-network-security-groups"></a><a name="unassociated-nsgs"></a>Mostrar grupos de segurança de rede não ligados

Esta consulta devolve grupos de segurança de rede (NSGs) que não estão associados a uma interface de rede ou sub-rede.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.cn</a>

---

## <a name="get-cost-savings-summary-from-azure-advisor"></a><a name="advisor-savings"></a>Obtenha resumo de poupança de custos do Azure Advisor

Esta consulta resume as economias de custos de cada recomendação [do Azure Advisor.](../../../advisor/advisor-overview.md)

```kusto
advisorresources
| where type == 'microsoft.advisor/recommendations'
| where properties.category == 'Cost'
| extend
    resources = tostring(properties.resourceMetadata.resourceId),
    savings = todouble(properties.extendedProperties.savingsAmount),
    solution = tostring(properties.shortDescription.solution),
    currency = tostring(properties.extendedProperties.savingsCurrency)
| summarize
    dcount(resources), 
    bin(sum(savings), 0.01)
    by solution, currency
| project solution, dcount_resources, sum_savings, currency
| order by sum_savings desc
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.com</a>
- Portal do Governo Azure: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.us</a>
- Azure China 21Vianet portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.cn</a>

---

## <a name="count-machines-in-scope-of-guest-configuration-policies"></a><a name="count-gcmachines"></a>Conte máquinas no âmbito das políticas de Configuração de Hóspedes

Exibe a contagem de máquinas virtuais Azure e servidores ligados ao Arco no âmbito das atribuições de [Configuração de Convidados Azure Policy.](../../policy/concepts/guest-configuration.md)

```kusto
GuestConfigurationResources
| extend vmid = split(properties.targetResourceId,'/')
| mvexpand properties.latestAssignmentReport.resources
| where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.'
| project machine = tostring(vmid[(-1)]),
    type = tostring(vmid[(-3)])
| distinct machine, type
| summarize count() by type
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.' | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]) | distinct machine, type | summarize count() by type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.' | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]) | distinct machine, type | summarize count() by type"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%20%7C%20extend%20vmid%20%3D%20split(properties.targetResourceId%2C%22%2F%22)%20%7C%20mvexpand%20properties.latestAssignmentReport.resources%20%7C%20where%20properties_latestAssignmentReport_resources.resourceId%20!%3D%20'Invalid%20assignment%20package.'%20%7C%20project%20machine%20%3D%20tostring(vmid%5B(-1)%5D)%2C%20type%20%3D%20tostring(vmid%5B(-3)%5D)%20%7C%20distinct%20machine%2C%20type%20%7C%20summarize%20count()%20by%20type" target="_blank">portal.azure.com</a>

---

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [língua de consulta.](../concepts/query-language.md)
- Saiba mais sobre como [explorar recursos.](../concepts/explore-resources.md)
- Consulte amostras de [consultas avançadas.](advanced.md)
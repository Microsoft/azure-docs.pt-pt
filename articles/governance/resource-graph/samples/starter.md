---
title: Exemplos de consultas iniciais
description: Use o grafo de recursos do Azure para executar algumas consultas iniciais, incluindo recursos de contagem, ordenação de recursos ou por uma marca específica.
ms.date: 10/21/2019
ms.topic: quickstart
ms.openlocfilehash: ca7fc2ba3e952f93f85c677eb8a8a7322901c7d0
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076123"
---
# <a name="starter-resource-graph-queries"></a>Consultas de introdução do Azure Resource Graph

O primeiro passo para compreender as consultas com Azure Resource Graph é obter uma compreensão básica da [Linguagem de Consulta](../concepts/query-language.md). Se você ainda não está familiarizado com a [linguagem de consulta Kusto (KQL)](/azure/kusto/query/index), é recomendável examinar o [tutorial de KQL](/azure/kusto/query/tutorial) para entender como compor solicitações para os recursos que você está procurando.

Vamos examinar as seguintes consultas de introdução:

> [!div class="checklist"]
> - [Contar recursos do Azure](#count-resources)
> - [Contar recursos do cofre de chaves](#count-keyvaults)
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
> - [Mostrar grupos de segurança de rede não associados](#unassociated-nsgs)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="language-support"></a>Suporte de idiomas

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Azure Resource Graph. Antes de executar qualquer uma das seguintes consultas, verifique se o ambiente está pronto. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter os passos para instalar e validar o seu ambiente shell escolhido.

## <a name="a-namecount-resources-count-azure-resources"></a>Contagem de <a name="count-resources" />recursos do Azure

Esta consulta devolve o número de recursos do Azure que existem nas subscrições às quais tem acesso. Também é uma boa consulta para confirmar que a shell escolhida tem os componentes apropriados do Azure Resource Graph instalados e em condições de funcionamento.

```kusto
Resources
| summarize count()
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

## <a name="a-namecount-keyvaults-count-key-vault-resources"></a>Contagem de <a name="count-keyvaults" />recursos do cofre de chaves

Essa consulta usa `count` em vez de `summarize` para contar o número de registros retornados. Somente os cofres de chaves são incluídos na contagem.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

## <a name="a-namelist-resources-list-resources-sorted-by-name"></a><a name="list-resources" />listar recursos classificados por nome

Esta consulta devolve qualquer tipo de recurso, mas apenas as propriedades **nome**, **tipo** e **localização**. Ela utiliza `order by` para ordenar as propriedades pela propriedade **nome** por ordem ascendente (`asc`).

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

## <a name="a-nameshow-vms-show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms" />Mostrar todas as máquinas virtuais ordenadas pelo nome em ordem decrescente

Para listar apenas as máquinas virtuais (que são do tipo `Microsoft.Compute/virtualMachines`), podemos corresponder a propriedade **tipo** nos resultados. À semelhança da consulta anterior, `desc` altera `order by` para ser descendente. O `=~` na correspondência de tipo informa o Resource Graph para não ser sensível a maiúsculas e minúsculas.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

## <a name="a-nameshow-sorted-show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted" />mostrar as primeiras cinco máquinas virtuais por nome e seu tipo de so

Esta consulta vai utilizar `top` para obter apenas cinco registos correspondentes, ordenados por nome. O tipo do recurso do Azure é `Microsoft.Compute/virtualMachines`. `project` informa o Azure Resource Graph quais as propriedades a incluir.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

## <a name="a-namecount-os-count-virtual-machines-by-os-type"></a><a name="count-os" />contagem de máquinas virtuais por tipo de so

Aproveitando a consulta anterior, estamos ainda limitados pelos recursos do Azure do tipo `Microsoft.Compute/virtualMachines`, mas já não há limite sobre o número de registros devolvidos.
Em vez disso, utilizamos `summarize` e `count()` para definir como agrupar e agregar os valores pela propriedade, que, neste exemplo, é `properties.storageProfile.osDisk.osType`. Para obter um exemplo do aspeto desta cadeia de caracteres no objeto completo, veja [explorar recursos – deteção de máquinas virtuais](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

Uma forma diferente de escrever a mesma consulta é `extend` a uma propriedade e dar-lhe um nome temporário para utilizar dentro da consulta, neste caso, **so**. **so** é, em seguida, utilizado por `summarize` e `count()` como no exemplo anterior.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

> [!NOTE]
> Esteja ciente de que, embora `=~` permita a correspondência não sensível a maiúsculas e minúsculas, a utilização de propriedades (como **properties.storageProfile.osDisk.osType**) na consulta requer que a utilização de maiúsculas/minúsculas esteja correta. Se a propriedade for o caso incorreto, um valor nulo ou incorreto será retornado e o agrupamento ou resumo estaria incorreto.

## <a name="a-nameshow-storage-show-resources-that-contain-storage"></a><a name="show-storage" />mostrar recursos que contêm armazenamento

Em vez de definir explicitamente o tipo a corresponder, esta consulta de exemplo vai procurar qualquer recurso do Azure que `contains` a palavra **armazenamento**.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

## <a name="a-namelist-publicip-list-all-public-ip-addresses"></a><a name="list-publicip" />listar todos os endereços IP públicos

À semelhança da consulta anterior, encontra tudo o que era de um tipo com a palavra **publicIPAddresses**.
Essa consulta expande esse padrão para incluir apenas os resultados em que **Properties. ipaddress**
`isnotempty`, para retornar apenas as **Propriedades. ipaddress**e para `limit` os resultados pela parte superior
100. Poderá ter de evitar as aspas consoante a shell escolhida.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

## <a name="a-namecount-resources-by-ip-count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip" />contar recursos que têm endereços IP configurados pela assinatura

Com a consulta do exemplo anterior e ao adicionar `summarize` e `count()`, podemos obter uma lista por subscrição dos recursos com endereços IP configurados.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

## <a name="a-namelist-tag-list-resources-with-a-specific-tag-value"></a><a name="list-tag" />listar recursos com um valor de marca específico

Podemos limitar os resultados por propriedades que não sejam do tipo de recurso do Azure, como uma etiqueta. Neste exemplo, está a filtrar recursos do Azure com um nome de etiqueta de **Ambiente** que têm um valor de **Interno**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

Também indica as etiquetas que o recurso tem e os seus valores, adicione a propriedade **etiquetas** à palavra-chave `project`.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

## <a name="a-namelist-specific-tag-list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag" />listar todas as contas de armazenamento com um valor de marca específico

Combine a funcionalidade de filtro do exemplo anterior e filtre o tipo de recurso do Azure por **tipo** de propriedade. Esta consulta também limita a nossa pesquisa para tipos específicos de recursos do Azure com um nome e valor específicos de etiqueta.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

> [!NOTE]
> Este exemplo utiliza `==` para a correspondência em vez do `=~` condicional. `==` é uma correspondência sensível a maiúsculas e minúsculas.

## <a name="a-nameshow-aliases-show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases" />mostrar aliases para um recurso de máquina virtual

Os [aliases de Azure Policy](../../policy/concepts/definition-structure.md#aliases) são usados pelo Azure Policy para gerenciar a conformidade de recursos. O grafo de recursos do Azure pode retornar os _aliases_ de um tipo de recurso. Esses valores são úteis para comparar o valor atual de aliases ao criar uma definição de política personalizada. A matriz de _aliases_ não é fornecida por padrão nos resultados de uma consulta. Use `project aliases` para adicioná-lo explicitamente aos resultados.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

## <a name="a-namedistinct-alias-values-show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values" />mostrar valores distintos para um alias específico

Ver o valor de aliases em um único recurso é útil, mas não mostra o valor verdadeiro de usar o grafo de recursos do Azure para consultar em assinaturas. Este exemplo examina todos os valores de um alias específico e retorna os valores distintos.

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

## <a name="a-nameunassociated-nsgs-show-unassociated-network-security-groups"></a><a name="unassociated-nsgs" />mostrar grupos de segurança de rede não associados

Essa consulta retorna NSGs (grupos de segurança de rede) que não estão associados a uma sub-rede ou interface de rede.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Gerenciador de grafo de recursos](../media/resource-graph-small.png) Experimente esta consulta no Gerenciador de grafo de recursos do Azure:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![abrir link no ícone de nova janela](../../media/new-window.png)

---

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [linguagem de consulta](../concepts/query-language.md).
- Saiba mais sobre como [explorar recursos](../concepts/explore-resources.md).
- Consulte exemplos de [consultas avançadas](advanced.md).
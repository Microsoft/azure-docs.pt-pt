---
title: Exemplos de consulta avançada
description: Use o grafo de recursos do Azure para executar algumas consultas avançadas, incluindo capacidade do conjunto de dimensionamento de máquinas virtuais, listagem de todas as marcas usadas e correspondência de máquinas virtuais com expressões regulares.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: b5742d4c14d2599b3efa73e427a5d418e5ef1c1e
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164906"
---
# <a name="advanced-resource-graph-queries"></a>Consultas avançadas do Azure Resource Graph

O primeiro passo para compreender as consultas com Azure Resource Graph é obter uma compreensão básica da [Linguagem de Consulta](../concepts/query-language.md). Se ainda não estiver familiarizado com o [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), recomenda-se que reveja os conceitos básicos para compreender como compor pedidos para os recursos que está a procurar.

Vamos examinar as seguintes consultas avançadas:

> [!div class="checklist"]
> - [Obter capacidade e tamanho do conjunto de dimensionamento de máquinas virtuais](#vmss-capacity)
> - [Listar todos os nomes de etiquetas](#list-all-tags)
> - [Máquinas virtuais correspondidas por regex](#vm-regex)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Suporte de idiomas

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Azure Resource Graph. Antes de executar qualquer uma das seguintes consultas, verifique se o ambiente está pronto. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter os passos para instalar e validar o seu ambiente shell escolhido.

## <a name="vmss-capacity"></a>Obter a capacidade e o tamanho do conjunto de dimensionamento de máquinas virtuais

Esta consulta procura recursos do conjunto de dimensionamento de máquinas virtuais e obtém vários detalhes, incluindo o tamanho da máquina virtual e a capacidade do conjunto de dimensionamento. A consulta utiliza a função `toint()` para converter a capacidade num número para poder ser ordenado. Por fim, o nome das colunas é mudado para propriedades de nome personalizadas.

```kusto
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>Listar todos os nomes de etiquetas

Esta consulta começa com a etiqueta e cria um objeto JSON que lista todos os nomes de etiquetas exclusivos e os tipos correspondentes.

```kusto
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Máquinas virtuais correspondidas por regex

Esta consulta procura máquinas virtuais que correspondam a uma [expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference) (conhecida como _regex_). A **Regex \@ corresponde** nos permite definir o Regex para corresponder, que é. `^Contoso(.*)[0-9]+$`
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
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="displaynames"></a>Incluir os nomes de locatário e assinatura com displaynames

Essa consulta usa o novo parâmetro **include** com opção displaynames para adicionar **subscriptionDisplayName** e **tenantDisplayName** aos resultados. Esse parâmetro só está disponível para CLI do Azure e Azure PowerShell.

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
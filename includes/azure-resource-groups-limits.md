---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: a528fad10144ec733a3db5340ef12dee5ce5411c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553890"
---
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Recursos por [grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), por tipo de recurso |800 |Varia por tipo de recurso |
| Implementações por grupo de recursos no histórico de implementação |800 |800 |
| Recursos por implementação |800 |800 |
| Bloqueios de gestão por âmbito exclusivo |20 |20 |
| Número de etiquetas por recurso ou grupo de recursos |15 |15 |
| Comprimento de chave de etiqueta |512 |512 |
| Comprimento do valor de etiqueta |256 |256 |


#### <a name="template-limits"></a>Limites do modelo

| Value | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Parâmetros |256 |256 |
| Variáveis |256 |256 |
| Recursos, o que inclui o número de cópias |800 |800 |
| Saídas |64 |64 |
| Expressão de modelo |24,576 carateres |24,576 carateres |
| Recursos em Modelos exportados |200 |200 | 
| Tamanho do modelo |1 MB |1 MB |
| Tamanho do ficheiro de parâmetro |64 KB |64 KB |

Pode exceder alguns limites de modelo ao utilizar um modelo aninhado. Para obter mais informações, consulte [utilizar modelos ligados ao implementar recursos do Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, pode combinar diversos valores num objeto. Para obter mais informações, consulte [objetos como parâmetros](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).

Se atingir o limite de 800 implementações por grupo de recursos, elimine as implementações do histórico de que já não são necessários. Pode eliminar as entradas do histórico com [eliminar a implementação do grupo az](/cli/azure/group/deployment) para a CLI do Azure. Também pode utilizar [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) no PowerShell. Elimina uma entrada do histórico de implementação não afeta os recursos de implantação. 
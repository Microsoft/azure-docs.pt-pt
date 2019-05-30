---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 04/19/2019
ms.author: tomfitz
ms.openlocfilehash: 8bd16378e9c82a011309c12cf241b59d03405a77
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238727"
---
| Resource | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Recursos por [grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), por tipo de recurso |800 |Varia por tipo de recurso |
| Implementações por grupo de recursos no histórico de implementação |800<sup>1</sup> |800 |
| Recursos por implementação |800 |800 |
| Bloqueios de gestão por âmbito exclusivo |20 |20 |
| Número de etiquetas por recurso ou grupo de recursos |15 |15 |
| Comprimento de chave de etiqueta |512 |512 |
| Comprimento do valor de etiqueta |256 |256 |

<sup>1</sup>se atingir o limite de 800 implementações por grupo de recursos, eliminar as implementações do histórico de que já não são necessários. Elimina uma entrada do histórico de implementação não afeta os recursos implementados. Pode eliminar as entradas do histórico com [eliminar a implementação do grupo az](/cli/azure/group/deployment) para a CLI do Azure, ou [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) no PowerShell.  Para PowerShell script que automatiza a exclusão de Implantações numa integração contínua e o cenário de entrega contínua (CI/CD), consulte [remove-deployments.ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Limites do modelo

| Value | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Parâmetros |256 |256 |
| Variáveis |256 |256 |
| Recursos (incluindo o número de cópias) |800 |800 |
| Saídas |64 |64 |
| Expressão de modelo |24,576 carateres |24,576 carateres |
| Recursos em Modelos exportados |200 |200 | 
| Tamanho do modelo |1 MB |1 MB |
| Tamanho do ficheiro de parâmetro |64 KB |64 KB |

Pode exceder alguns limites de modelo ao utilizar um modelo aninhado. Para obter mais informações, consulte [utilizar modelos ligados ao implementar recursos do Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, pode combinar diversos valores num objeto. Para obter mais informações, consulte [objetos como parâmetros](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).

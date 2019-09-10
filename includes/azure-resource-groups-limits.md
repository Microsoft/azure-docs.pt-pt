---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 25928ef35da1ce4b3824303a5d46749c32aa701f
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "69626384"
---
| Resource | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Recursos por [grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), por tipo de recurso |800 |Alguns tipos de recursos podem exceder o limite de 800. Consulte [recursos não limitados a 800 instâncias por grupo de recursos](../articles/azure-resource-manager/resources-without-rg-limit.md). |
| Implantações por grupo de recursos no histórico de implantação |800<sup>1</sup> |800 |
| Recursos por implantação |800 |800 |
| Bloqueios de gerenciamento por escopo exclusivo |20 |20 |
| Número de marcas por recurso ou grupo de recursos |50 |50 |
| Comprimento da chave de marca |512 |512 |
| Comprimento do valor da marca |256 |256 |

<sup>1</sup> Se você atingir o limite de 800 implantações por grupo de recursos, exclua as implantações do histórico que não são mais necessárias. A exclusão de uma entrada do histórico de implantação não afeta os recursos implantados. Você pode excluir entradas do histórico com [AZ Group Deployment Delete](/cli/azure/group/deployment) para CLI do Azure ou [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) no PowerShell.  Para um script do PowerShell que automatiza a exclusão de implantações em um cenário de CI/CD (integração contínua e entrega contínua), consulte [Remove-Deployments. ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Limites de modelo

| Value | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Parâmetros |256 |256 |
| Variáveis |256 |256 |
| Recursos (incluindo a contagem de cópias) |800 |800 |
| outputs |64 |64 |
| Expressão de modelo |caracteres de 24.576 |caracteres de 24.576 |
| Recursos em modelos exportados |200 |200 | 
| Tamanho do modelo |4 MB |4 MB |
| Tamanho do arquivo de parâmetro |64 KB |64 KB |

Você pode exceder alguns limites de modelo usando um modelo aninhado. Para obter mais informações, consulte [usar modelos vinculados ao implantar recursos do Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, você pode combinar vários valores em um objeto. Para obter mais informações, consulte [objetos como parâmetros](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).

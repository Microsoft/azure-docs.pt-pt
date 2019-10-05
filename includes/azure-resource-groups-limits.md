---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 1190798b234f9c73e02fda41c03ffa296246be63
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71975332"
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

<sup>1</sup> Se você atingir o limite de 800 implantações por grupo de recursos, exclua as implantações do histórico que não são mais necessárias. A exclusão de uma entrada do histórico de implantação não afeta os recursos implantados. Para obter mais informações, consulte [resolver erro quando a contagem de implantação exceder 800](../articles/azure-resource-manager/deployment-quota-exceeded.md).

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

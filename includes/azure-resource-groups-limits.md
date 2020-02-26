---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c1a52e1bc2dde74289cb270fcae832be24de5a06
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592438"
---
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Recursos por [grupo de recursos](../articles/azure-resource-manager/management/overview.md#resource-groups) | N/D | Os recursos não são limitados pelo grupo de recursos. Em vez disso, são limitados por recurso num grupo de recursos. Veja a próxima fila. | 
| Recursos por grupo de recursos, por tipo de recurso |800 |Alguns tipos de recursos podem exceder o limite de 800. Ver [Recursos não limitados a 800 instâncias por grupo de recursos](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Implantações por grupo de recursos no histórico de implantação |800<sup>1</sup> |800 |
| Recursos por implantação |800 |800 |
| Fechaduras de gestão por âmbito único |20 |20 |
| Número de etiquetas por grupo de recursos ou recursos |50 |50 |
| Comprimento da chave de etiqueta |512 |512 |
| Comprimento do valor da etiqueta |256 |256 |

<sup>1</sup> Se atingir o limite de 800 implantações por grupo de recursos, elimine as implementações da história que já não são necessárias. Apagar uma entrada do histórico de implantação não afeta os recursos implantados. Para mais informações, consulte [Resolver o erro quando a contagem de distribuição exceder 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

#### <a name="template-limits"></a>Limites de modelo

| Valor | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Parâmetros |256 |256 |
| Variáveis |256 |256 |
| Recursos (incluindo a contagem de cópias) |800 |800 |
| Saídas |64 |64 |
| Expressão do modelo |24.576 chars |24.576 chars |
| Recursos em modelos exportados |200 |200 | 
| Tamanho do modelo |4 MB |4 MB |
| Tamanho do ficheiro do parâmetro |64 KB |64 KB |

Pode exceder alguns limites de modelo usando um modelo aninhado. Para mais informações, consulte [utilize modelos ligados ao utilizar os recursos do Azure.](../articles/azure-resource-manager/templates/linked-templates.md) Para reduzir o número de parâmetros, variáveis ou saídas, pode combinar vários valores num objeto. Para mais informações, consulte [objetos como parâmetros](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).

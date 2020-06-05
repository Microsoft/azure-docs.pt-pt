---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: 33a63280f6973d2c5e29db29f7a6f3fc68c57c77
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84424760"
---
| Recurso | Limite |
| --- | --- |
| Recursos por [grupo de recursos](../articles/azure-resource-manager/management/overview.md#resource-groups) | Os recursos não são limitados por grupo de recursos. Em vez disso, são limitados por tipo de recursos num grupo de recursos. Veja a próxima fila. |
| Recursos por grupo de recursos, por tipo de recurso |800 - Alguns tipos de recursos podem exceder o limite de 800. Ver [Recursos não limitados a 800 instâncias por grupo de recursos](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Implementações por grupo de recursos no histórico de implantação |800<sup>1</sup> |
| Recursos por implantação |800 |
| Bloqueios de gestão por âmbito único |20 |
| Número de tags por recurso ou grupo de recursos |50 |
| Comprimento da chave da etiqueta |512 |
| Comprimento do valor da etiqueta |256 |

<sup>1</sup> A partir de junho de 2020, as implementações serão automaticamente eliminadas do histórico à medida que se aproxima do limite. Excluir uma entrada do histórico de implantação não afeta os recursos implantados. Para obter mais informações, consulte [as supressões automáticas do histórico de implantação](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

#### <a name="template-limits"></a>Limites de modelo

| Valor | Limite |
| --- | --- |
| Parâmetros |256 |
| Variáveis |256 |
| Recursos (incluindo a contagem de cópias) |800 |
| Saídas |64 |
| Expressão do modelo |24.576 chars |
| Recursos em modelos exportados |200 |
| Tamanho do modelo |4 MB |
| Tamanho do arquivo do parâmetro |64 KB |

Pode exceder alguns limites de modelo usando um modelo aninhado. Para obter mais informações, consulte [Utilize modelos ligados quando implementar recursos Azure](../articles/azure-resource-manager/templates/linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, pode combinar vários valores num objeto. Para obter mais informações, consulte [os Objetos como parâmetros.](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)

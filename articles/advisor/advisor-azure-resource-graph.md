---
title: Dados do Assistente no Azure Resource Graph
description: Faça consultas para dados do Advisor no Azure Resource Graph
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: 66bb500d419d1f5537afafd7a2df543ded8cc7ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87057769"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Consulta para dados do Advisor no Resource Graph Explorer (Gráfico de Recursos Azure)

Os recursos consultivos estão agora a bordo do [Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/). Isto lança as bases para muitos cenários de clientes em escala para recomendações do Advisor. Poucos cenários que não eram possíveis antes de fazer em escala e que agora podem ser alcançados usando o Gráfico de Recursos são:
* Dá capacidade para realizar consultas complexas para todas as suas subscrições no portal Azure
* Recomendações resumidas por tipos de categorias (como fiabilidade, desempenho) e tipos de impacto (altos, médios, baixos)
* Todas as recomendações para um determinado tipo de recomendação
* Contagem de recursos impactada por categoria de recomendação

![Conselheiro no explorador de gráficos de recursos Azure](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Tipos de recursos de aconselhamento no Gráfico Azure

Tipos de recursos disponíveis no [Gráfico de Recursos](../governance/resource-graph/index.yml): Existem 3 tipos de recursos disponíveis para consulta sob recursos Advisor. Aqui está a lista dos recursos que estão agora disponíveis para consulta no Gráfico de Recursos.
* Microsoft.Advisor/configurações
* Microsoft.Advisor/recomendações
* Microsoft.Advisor/supressões

Estes tipos de recursos estão listados sob uma nova tabela chamada AdvisorResources, que também pode consultar no Portal Azure.


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre recomendações do Advisor, consulte:
* [Introdução ao Assistente do Azure](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custos do Assistente](advisor-cost-recommendations.md)
* [Recomendações de fiabilidade do consultor](advisor-high-availability-recommendations.md)
* [Recomendações de desempenho do conselheiro](advisor-performance-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do consultor](advisor-operational-excellence-recommendations.md)
* [Conselheiro REST API](/rest/api/advisor/)

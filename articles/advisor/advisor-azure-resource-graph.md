---
title: Dados de aconselhamento no Gráfico de Recursos Azure
description: Faça consultas para dados do Advisor no Gráfico de Recursos Azure
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502454"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Consulta para dados do Advisor no Resource Graph Explorer (Gráfico de Recursos Azure)

Os recursos consultivos estão agora a bordo do [Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/). Isto lança as bases para muitos cenários de clientes em escala para recomendações do Advisor. Poucos cenários que não eram possíveis antes de fazer em escala e que agora podem ser alcançados usando o Resource Graph são:
* Dá capacidade para realizar consultas complexas para todas as suas subscrições no portal Azure
* Recomendações resumidas por tipos de categoria (como alta disponibilidade, desempenho) e tipos de impacto (altos, médios, baixos)
* Todas as recomendações para um tipo de recomendação particular
* Contagem de recursos impactados por categoria de recomendação

![Consultor no explorador de gráficos de recursos Azure](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Tipos de recursos consultivos no Gráfico Azure

Tipos de recursos advisor disponíveis no [Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/): Existem 3 tipos de recursos disponíveis para consulta ao abrigo dos recursos do Advisor. Aqui está a lista dos recursos que estão agora disponíveis para consulta no Graph de Recursos.
* Microsoft.Advisor/configurações
* Microsoft.Advisor/recomendações
* Microsoft.Advisor/supressões

Estes tipos de recursos estão listados sob uma nova tabela chamada AdvisorResources, que também pode consultar no Resource Graph Explorer no portal Azure.


## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre recomendações do Advisor, consulte:
* [Introdução ao Consultor Azure](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de Custos Consultivos](advisor-cost-recommendations.md)
* [Recomendações de Desempenho Do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional Consultiva](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)

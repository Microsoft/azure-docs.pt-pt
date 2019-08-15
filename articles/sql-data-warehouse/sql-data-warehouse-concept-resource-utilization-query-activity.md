---
title: Gerenciamento de SQL Data Warehouse do Azure e monitoramento – atividade de consulta, utilização de recursos | Microsoft Docs
description: Saiba quais recursos estão disponíveis para gerenciar e monitorar SQL Data Warehouse do Azure. Use o portal do Azure e as DMVs (exibições de gerenciamento dinâmico) para entender a atividade de consulta e a utilização de recursos do seu data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/09/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 7f7575daa91cef5cb5be6274a699323fafe67a68
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935143"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Monitorando a utilização de recursos e a atividade de consulta no Azure SQL Data Warehouse
O Azure SQL Data Warehouse fornece uma experiência de monitoramento avançada dentro do portal do Azure para insights de superfície para sua carga de trabalho de data warehouse. O portal do Azure é a ferramenta recomendada ao monitorar seu data warehouse, pois fornece períodos de retenção, alertas, recomendações e gráficos personalizáveis e painéis para métricas e logs configuráveis. O portal também permite que você integre com outros serviços de monitoramento do Azure, como o OMS (Operations Management Suite) e Azure Monitor (logs) para fornecer uma experiência de monitoramento holística para não apenas seus data warehouse, mas também toda a análise do Azure plataforma para uma experiência de monitoramento integrada. Esta documentação descreve quais recursos de monitoramento estão disponíveis para otimizar e gerenciar sua plataforma de análise com o SQL Data Warehouse. 

## <a name="resource-utilization"></a>Utilização de recursos 
As métricas a seguir estão disponíveis no portal do Azure para SQL Data Warehouse. Essas métricas são apresentadas por meio de [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).


| Nome da Métrica             | Descrição                                                  | Tipo de Agregação |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentagem de CPU          | Utilização da CPU em todos os nós para o data warehouse      | Máximo          |
| Percentagem de E/S de Dados      | Utilização de e/s em todos os nós para o data warehouse       | Máximo          |
| Percentagem de memória       | Utilização de memória (SQL Server) em todos os nós para o data warehouse | Máximo          |
| Conexões com êxito  | Número de conexões bem-sucedidas com os dados                 | Total            |
| Ligações com Falhas      | Número de conexões com falha para o data warehouse           | Total            |
| Bloqueado pelo firewall     | Número de logons no data warehouse que foi bloqueado     | Total            |
| Limite de DWU               | Objetivo de nível de serviço do data warehouse                | Máximo          |
| Porcentagem de DWU          | Máximo entre percentual de CPU e porcentagem de e/s de dados        | Máximo          |
| DWU usado                | Limite de DWU * DWU percentual                                   | Máximo          |
| Porcentagem de acesso ao cache    | (acertos de cache/erro de cache) * 100 em que acertos de cache é a soma de todos os segmentos columnstore acertos no cache SSD local e o erro de cache é a perda de segmentos columnstore no cache SSD local somado em todos os nós | Máximo          |
| Percentual de cache usado   | (cache usado/capacidade de cache) * 100 em que o cache usado é a soma de todos os bytes no cache SSD local em todos os nós e a capacidade de cache é a soma da capacidade de armazenamento do cache SSD local em todos os nós | Máximo          |
| Porcentagem de tempdb local | Utilização de tempdb local em todos os nós de computação-os valores são emitidos a cada cinco minutos | Máximo          |

> Itens a serem considerados ao exibir métricas e definir alertas:
>
> - Conexões com falha e bem-sucedidas são relatadas para um determinado data warehouse-não para o servidor lógico

## <a name="query-activity"></a>Atividade de consulta
Para uma experiência programática ao monitorar SQL Data Warehouse por meio de T-SQL, o serviço fornece um conjunto de DMVs (exibições de gerenciamento dinâmico). Essas exibições são úteis ao solucionar problemas e identificar ativamente afunilamentos de desempenho com sua carga de trabalho.

Para exibir a lista de DMVs que o SQL Data Warehouse fornece, consulte esta [documentação](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Log de métricas e diagnósticos
As métricas e os logs podem ser exportados para Azure Monitor, especificamente o componente de [logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) e podem ser acessados programaticamente por meio de [consultas de log](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). A latência de log para SQL Data Warehouse é de cerca de 10-15 minutos. Para obter mais detalhes sobre os fatores que impactam a latência, visite a documentação a seguir.


## <a name="next-steps"></a>Passos Seguintes
Os guias de instruções a seguir descrevem cenários comuns e casos de uso ao monitorar e gerenciar seus data warehouse:

- [Monitorar sua carga de trabalho de data warehouse com DMVs](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

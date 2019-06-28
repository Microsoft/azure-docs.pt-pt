---
title: Capacidade de gestão do armazém de dados SQL do Azure e monitorização - atividade, a utilização de recursos de consulta | Documentos da Microsoft
description: Saber que capacidades estão disponíveis para gerir e monitorizar o Azure SQL Data Warehouse. Utilize o portal do Azure e a vistas de gestão dinâmica (DMVs) para compreender a atividade de consulta e a utilização de recursos do seu armazém de dados.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 06/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 5038ae99a804b456c2cc388f07899278cc0f9a24
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312887"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Monitorizar atividade de consulta e de utilização de recursos no Azure SQL Data Warehouse
O Azure SQL Data Warehouse oferece uma Rica experiência de monitorização no portal do Azure para obter informações para a sua carga de trabalho do armazém de dados. O portal do Azure é a ferramenta recomendada quando o armazém de dados de monitorização, pois fornece períodos de retenção configurável, alertas, recomendações e personalizáveis gráficos e dashboards para métricas e registos. O portal também permite integrar com outros serviços de monitorização do Azure, como o Operations Management Suite (OMS) e o Azure Monitor (logs) para fornecer uma experiência holística de monitorização para não apenas seu armazém de dados, mas também suas análises do Azure inteiras plataforma para uma experiência de monitorização integrada. Esta documentação descreve as capacidades de monitorização estão disponíveis para otimizar e gerir a sua plataforma de análise com o SQL Data Warehouse. 

## <a name="resource-utilization"></a>Utilização de recursos 
As métricas seguintes estão disponíveis no portal do Azure para o SQL Data Warehouse. Estas métricas são apresentadas [do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).

> [!NOTE]
> Atualmente o nível de nó da CPU e métricas de e/s reflete propriamente o uso de armazém de dados. Estas métricas serão removidas no futuro próximo à medida que a equipe melhora a monitorização e resolução de problemas de experiência para o SQL Data Warehouse. 

| Nome da Métrica             | Descrição                                                  | Tipo de Agregação |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentagem de CPU          | Utilização da CPU de todos os nós para o armazém de dados      | Máximo          |
| Percentagem de ES de Dados      | Utilização de e/s em todos os nós para o armazém de dados       | Máximo          |
| Percentagem de memória       | Utilização da memória (SQL Server) em todos os nós para o armazém de dados | Máximo          |
| Ligações com êxito  | Número de conexões com êxito para os dados                 | Total            |
| Ligações com Falhas      | Número de ligações falhadas ao armazém de dados           | Total            |
| Bloqueado pela Firewall     | Número de inícios de sessão para o armazém de dados que foi bloqueado     | Total            |
| Limite de DWU               | Objetivo de nível de serviço do data warehouse                | Máximo          |
| Percentagem DWU          | Máximo entre a percentagem de CPU e percentagem de es de dados        | Máximo          |
| DWU utilizado                | Limite de DWU * percentagem DWU                                   | Máximo          |
| Percentagem de acertos na cache    | (colocar em cache pedidos com êxito na / falha de acerto em cache) * erros de 100 onde acertos na cache é a soma de todos os acessos de segmentos de columnstore na cache SSD local e os segmentos de columnstore de é de falha de acerto na cache na cache SSD local somado em todos os nós | Máximo          |
| Percentagem de cache utilizada   | (o cache utilizada / capacidade em cache) * 100 onde cache utilizada é a soma de todos os bytes no cache SSD local em todos os nós e a capacidade de cache é a soma da capacidade de local SSD de armazenamento em cache em todos os nós | Máximo          |
| Percentagem de local tempdb | Utilização de local tempdb em todos os nós de computação - valores são emitidos a cada cinco minutos | Máximo          |

## <a name="query-activity"></a>Atividade de consulta
Para uma experiência de programação ao monitorizar o SQL Data Warehouse através de T-SQL, o serviço fornece um conjunto de vistas de gestão dinâmica (DMVs). Estas vistas são úteis quando ativamente a solução de problemas e identificar afunilamentos de desempenho com a carga de trabalho.

Para ver a lista de DMVs que fornece o SQL Data Warehouse, consulte este [documentação](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Métricas e registo de diagnósticos
Métricas e registos podem ser exportados para o Azure Monitor, especificamente a [registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) componente e pode ser acessada programaticamente através de [consultas de registo](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). A latência de registo de armazém de dados SQL é cerca de 10 a 15 minutos. Para obter mais detalhes sobre os fatores que afetam o latência, visite a seguinte documentação.


## <a name="next-steps"></a>Passos Seguintes
Os guias de procedimentos seguintes descrevem cenários comuns e casos quando monitorizar e gerir o armazém de dados de utilização:

- [Monitorizar a sua carga de trabalho do armazém de dados com DMVs](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)


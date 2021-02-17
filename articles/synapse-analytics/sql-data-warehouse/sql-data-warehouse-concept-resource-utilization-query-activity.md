---
title: Gestão e monitorização - atividade de consulta, utilização de recursos
description: Saiba quais as capacidades disponíveis para gerir e monitorizar o Azure Synapse Analytics. Utilize o portal Azure e os Dynamic Management Views (DMVs) para compreender a atividade de consulta e a utilização de recursos do seu armazém de dados.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 96a076ef144af1d32e9ef3ec2903557b8edff006
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586712"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitorização da utilização de recursos e atividade de consulta na Azure Synapse Analytics

O Azure Synapse Analytics proporciona uma experiência de monitorização rica dentro do portal Azure para obter informações sobre a carga de trabalho do seu armazém de dados. O portal Azure é a ferramenta recomendada para monitorizar o seu armazém de dados, uma vez que fornece períodos de retenção, alertas, recomendações e gráficos e dashboards personalizáveis para métricas e registos. O portal permite-lhe também integrar-se com outros serviços de monitorização do Azure, como o Azure Monitor (logs) com a analytics log para fornecer uma experiência de monitorização holística não só para o seu armazém de dados, mas também para toda a sua plataforma de análise Azure para uma experiência de monitorização integrada. Esta documentação descreve quais as capacidades de monitorização disponíveis para otimizar e gerir a sua plataforma de análise com o Synapse SQL.

## <a name="resource-utilization"></a>Utilização dos recursos

As seguintes métricas estão disponíveis no portal Azure para Synapse SQL. Estas métricas são emergidas através do [Monitor Azure.](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics)

| Nome da Métrica             | Description                                                  | Tipo de Agregação |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentagem de CPU          | Utilização do CPU em todos os nós para o armazém de dados      | Avg, Min, Max    |
| Percentagem de ES de Dados      | IO Utilização em todos os nós para o armazém de dados       | Avg, Min, Max    |
| Percentagem de memória       | Utilização da memória (SQL Server) em todos os nós para o armazém de dados | Avg, Min, Max   |
| Consultas ativas          | Número de consultas ativas executando no sistema             | Soma              |
| Consultas em fila          | Número de consultas em fila à espera de começar a executar          | Soma              |
| Conexões bem sucedidas  | Número de ligações bem sucedidas (logins) na base de dados | Soma, Contagem       |
| Ligações com Falhas      | Número de ligações falhadas (logins) na base de dados | Soma, Contagem       |
| Bloqueado pela Firewall     | Número de logins no armazém de dados que foi bloqueado     | Soma, Contagem       |
| Limite dwu               | Objetivo de nível de serviço do armazém de dados                | Avg, Min, Max    |
| Percentagem de DWU          | Máximo entre a percentagem de CPU e a percentagem de IO de dados        | Avg, Min, Max    |
| DWU usado                | Limite DWU * percentagem de DWU                                   | Avg, Min, Max    |
| Percentagem de impacto de cache    | (cache hits / cache miss) * 100 onde cache hits é a soma de todos os segmentos de colunas atinge na cache SSD local e cache miss é o segmento de loja de colunas falha na cache SSD local resumida em todos os nós | Avg, Min, Max    |
| Percentagem utilizada em cache   | (cache utilizada / capacidade de cache) * 100 onde a cache utilizada é a soma de todos os bytes na cache SSD local em todos os nós e capacidade de cache é a soma da capacidade de armazenamento da cache SSD local em todos os nós | Avg, Min, Max    |
| Percentagem de temporários locais | Utilização temporária local em todos os nós de computação - valores são emitidos a cada cinco minutos | Avg, Min, Max    |

Coisas a ter em conta ao visualizar métricas e alertas de definição:

- A DWU utilizada representa apenas uma **representação de alto nível de utilização** em toda a piscina SQL e não se destina a ser um indicador abrangente de utilização. Para determinar se a escala para cima ou para baixo, considere todos os fatores que podem ser impactados pela DWU, tais como concuência, memória, temperatura e capacidade de cache adaptativa. Recomendamos [que faça a sua carga de trabalho em diferentes configurações do DWU](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) para determinar o que funciona melhor para cumprir os seus objetivos de negócio.
- As ligações falhadas e bem sucedidas são reportadas para um determinado armazém de dados - não para o próprio servidor.
- A percentagem de memória reflete a utilização mesmo que o armazém de dados esteja em estado de inatividade - não reflete o consumo ativo de memória da carga de trabalho. Utilize e acompanhe esta métrica juntamente com outras (tempdb, cache gen2) para tomar uma decisão holística sobre se a escala para a capacidade de cache adicional aumentará o desempenho da carga de trabalho para satisfazer os seus requisitos.

## <a name="query-activity"></a>Atividade de consulta

Para uma experiência programática ao monitorizar o Synapse SQL via T-SQL, o serviço fornece um conjunto de Vistas de Gestão Dinâmica (DMVs). Estas opiniões são úteis quando se soluciona ativamente e identifica estrangulamentos de desempenho com a sua carga de trabalho.

Para ver a lista de DMVs que se aplicam ao Sinaapse SQL, consulte esta [documentação](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Métricas e registos de diagnóstico 

Tanto as métricas como os registos podem ser exportados para o Azure Monitor, especificamente o componente [de registos do Azure Monitor](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e podem ser acedidos programáticamente através de [consultas de registo.](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) A latência do log para Synapse SQL é de cerca de 10-15 minutos. Para mais detalhes sobre os fatores que impactam a latência, visite a seguinte documentação.

## <a name="next-steps"></a>Passos seguintes

O seguinte guia de como guiar descreve cenários comuns e casos de utilização ao monitorizar e gerir o seu armazém de dados:

- [Monitorize a carga de trabalho do seu armazém de dados com DMVs](sql-data-warehouse-manage-monitor.md)
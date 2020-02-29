---
title: Gestão e monitorização - atividade de consulta, utilização de recursos
description: Saiba quais as capacidades disponíveis para gerir e monitorizar o Azure Synapse Analytics. Utilize o portal Azure e as Dinâmicas Vistas de Gestão (DMVs) para compreender a atividade de consulta e a utilização de recursos do seu armazém de dados.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 47f142a19ac470fb29e9542941cd94a6b29ce240
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195928"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitorização da utilização de recursos e atividade de consulta no Azure Synapse Analytics
A Azure Synapse Analytics proporciona uma rica experiência de monitorização dentro do portal Azure para visualizar a carga de trabalho do seu armazém de dados. O portal Azure é a ferramenta recomendada para monitorizar o seu armazém de dados, uma vez que fornece períodos de retenção configuráveis, alertas, recomendações e gráficos e dashboards personalizáveis para métricas e registos. O portal também permite integrar-se com outros serviços de monitorização azure, como a Suite de Gestão de Operações (OMS) e o Monitor Azure (registos) para proporcionar uma experiência de monitorização holística não só para o seu armazém de dados, mas também para toda a sua análise Azure plataforma para uma experiência integrada de monitorização. Esta documentação descreve quais as capacidades de monitorização disponíveis para otimizar e gerir a sua plataforma de análise com o SQL Analytics. 

## <a name="resource-utilization"></a>Utilização de recursos 
As seguintes métricas estão disponíveis no portal Azure para sQL Analytics. Estas métricas são surgidas através do [Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics)


| Nome da Métrica             | Descrição                                                  | Tipo de Agregação |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentagem de CPU          | Utilização da CPU em todos os nós para o armazém de dados      | Avg, Min, Max    |
| Percentagem de ES de Dados      | IO Utilização em todos os nós para o armazém de dados       | Avg, Min, Max    |
| Percentagem de memória       | Utilização de memória (SQL Server) em todos os nós para o armazém de dados | Avg, Min, Max   |
| Consultas Ativas          | Número de consultas ativas executadas no sistema             | Soma              |
| Consultas em fila          | Número de consultas em fila à espera de começar a executar          | Soma              |
| Conexões bem sucedidas  | Número de ligações bem sucedidas aos dados                 | Soma, Conde       |
| Ligações com Falhas      | Número de ligações falhadas ao armazém de dados           | Soma, Conde       |
| Bloqueado por Firewall     | Número de logins para o armazém de dados que foi bloqueado     | Soma, Conde       |
| Limite de DWU               | Objetivo de nível de serviço do armazém de dados                | Avg, Min, Max    |
| Percentagem de DWU          | Máximo entre percentagem de CPU e percentagem de Dados IO        | Avg, Min, Max    |
| DWU usado                | Limite de DWU * Percentagem de DWU                                   | Avg, Min, Max    |
| Percentagem de impacto cache    | (cache hits / cache miss) * 100 onde os hits de cache é a soma de todos os segmentos de lojas de colunas atinge na cache local SSD e cache miss é o segmento de loja de colunas que falha na cache SSD local resumida em todos os nós | Avg, Min, Max    |
| Cache usado percentagem   | (cache utilizada / cache capacidade) * 100 onde a cache utilizada é a soma de todos os bytes na cache SSD local em todos os nós e capacidade de cache é a soma da capacidade de armazenamento da cache SSD local em todos os nós | Avg, Min, Max    |
| Percentagem de temperatura local | Utilização temporária local em todos os nós de computação - valores são emitidos a cada cinco minutos | Avg, Min, Max    |

Coisas a considerar ao visualizar métricas e definir alertas:

- Ligações falhadas e bem sucedidas são reportadas para um determinado armazém de dados - não para o servidor lógico
- A percentagem de memória reflete a utilização mesmo que o armazém de dados esteja em estado de inatividade - não reflete o consumo ativo de memória da carga de trabalho. Utilize e rastreie esta métrica juntamente com outros (tempdb, cache gen2) para tomar uma decisão holística sobre se a escala para uma capacidade adicional de cache aumentará o desempenho da carga de trabalho para satisfazer os seus requisitos.


## <a name="query-activity"></a>Atividade de consulta
Para uma experiência programática ao monitorizar o SQL Analytics via T-SQL, o serviço fornece um conjunto de Pontos de Vista de Gestão Dinâmica (DMVs). Estas opiniões são úteis quando se desloque ativamente problemas e identifique estrangulamentos de desempenho com a sua carga de trabalho.

Para ver a lista de DMVs que a SQL Analytics fornece, consulte esta [documentação](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Métricas e registos de diagnóstico
Ambas as métricas e registos podem ser exportados para o Monitor Azure, especificamente o componente de [registos do Monitor Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) e podem ser acedidos programáticamente através de consultas de [registo](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). A latência de registo para SQL Analytics é de cerca de 10-15 minutos. Para mais detalhes sobre os fatores com impacto na latência, visite a seguinte documentação.


## <a name="next-steps"></a>Passos seguintes
Os seguintes guias como descrever descrevem cenários comuns e utilizam casos ao monitorizar e gerir o seu armazém de dados:

- [Monitorize a carga de trabalho do seu armazém de dados com DMVs](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

---
title: Gestão e monitorização - atividade de consulta, utilização de recursos
description: Saiba quais as capacidades disponíveis para gerir e monitorizar o Azure Synapse Analytics. Utilize o portal Azure e as Dinâmicas Vistas de Gestão (DMVs) para compreender a atividade de consulta e a utilização de recursos do seu armazém de dados.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7c09e9d9f93ead6f894c954f647ebe33918cf41d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653012"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitorização da utilização de recursos e atividade de consulta no Azure Synapse Analytics

A Azure Synapse Analytics proporciona uma rica experiência de monitorização dentro do portal Azure para visualizar informações sobre a carga de trabalho do seu armazém de dados. O portal Azure é a ferramenta recomendada para monitorizar o seu armazém de dados, uma vez que fornece períodos de retenção configuráveis, alertas, recomendações e gráficos e dashboards personalizáveis para métricas e registos. O portal também permite integrar-se com outros serviços de monitorização do Azure, como o Azure Monitor (logs) com a Log analytics, para proporcionar uma experiência de monitorização holística não só para o seu armazém de dados, mas também para toda a sua plataforma de análise Azure para uma experiência de monitorização integrada. Esta documentação descreve quais as capacidades de monitorização disponíveis para otimizar e gerir a sua plataforma de análise com o Synapse SQL.

## <a name="resource-utilization"></a>Utilização de recursos

As seguintes métricas estão disponíveis no portal Azure para Synapse SQL. Estas métricas são surgidas através do [Monitor Azure.](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics)

| Nome métrico             | Descrição                                                  | Tipo de Agregação |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentagem de CPU          | Utilização da CPU em todos os nós para o armazém de dados      | Avg, Min, Max    |
| Percentagem de ES de Dados      | IO Utilização em todos os nós para o armazém de dados       | Avg, Min, Max    |
| Percentagem de memória       | Utilização de memória (SQL Server) em todos os nós para o armazém de dados | Avg, Min, Max   |
| Consultas Ativas          | Número de consultas ativas executadas no sistema             | Soma              |
| Consultas em fila          | Número de consultas em fila à espera de começar a executar          | Soma              |
| Conexões bem sucedidas  | Número de ligações bem sucedidas (logins) na base de dados | Soma, Conde       |
| Ligações com Falhas      | Número de ligações falhadas (logins) na base de dados | Soma, Conde       |
| Bloqueado por Firewall     | Número de logins para o armazém de dados que foi bloqueado     | Soma, Conde       |
| Limite de DWU               | Objetivo de nível de serviço do armazém de dados                | Avg, Min, Max    |
| Percentagem de DWU          | Máximo entre percentagem de CPU e percentagem de Dados IO        | Avg, Min, Max    |
| DWU usado                | Limite de DWU * Percentagem de DWU                                   | Avg, Min, Max    |
| Percentagem de impacto cache    | (cache hits / cache miss) * 100 onde os hits de cache é a soma de todos os segmentos de lojas de colunas atinge na cache local SSD e cache miss é o segmento de loja de colunas que falha na cache SSD local resumida em todos os nós | Avg, Min, Max    |
| Cache usado percentagem   | (cache utilizada / cache capacidade) * 100 onde a cache utilizada é a soma de todos os bytes na cache SSD local em todos os nós e capacidade de cache é a soma da capacidade de armazenamento da cache SSD local em todos os nós | Avg, Min, Max    |
| Percentagem de temperatura local | Utilização temporária local em todos os nós de computação - valores são emitidos a cada cinco minutos | Avg, Min, Max    |
| Tamanho do armazenamento de dados (GB) | Tamanho total da base de dados. Isto inclui espaço usado, reservado e não atribuído. O espaço não atribuído é mantido para a base de dados para otimizar o desempenho da consulta e da carga. | Soma |
| Tamanho da recuperação de desastres (GB) | Tamanho total do geo-backup tomado a cada 24 horas | Soma |
| Tamanho de armazenamento instantâneo (GB) | Tamanho total de instantâneos tirados para fornecer pontos de restauro de base de dados. Isto inclui instantâneos automatizados e definidos pelo utilizador. | Soma |

Coisas a considerar ao visualizar métricas e definir alertas:

- A DWU utilizada representa apenas uma **representação de alto nível de utilização** em toda a piscina SQL e não se destina a ser um indicador abrangente de utilização. Para determinar se deve escalar para cima ou para baixo, considere todos os fatores que podem ser impactados pela DWU, tais como a conmoeda, a memória, a capacidade de cache tempdb e adaptativa. Recomendamos [executar a sua carga de trabalho em diferentes configurações de DWU](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) para determinar o que funciona melhor para cumprir os seus objetivos de negócio.
- Ligações falhadas e bem sucedidas são reportadas para um determinado armazém de dados - não para o servidor lógico
- A percentagem de memória reflete a utilização mesmo que o armazém de dados esteja em estado de inatividade - não reflete o consumo ativo de memória da carga de trabalho. Utilize e rastreie esta métrica juntamente com outros (tempdb, cache gen2) para tomar uma decisão holística sobre se a escala para uma capacidade adicional de cache aumentará o desempenho da carga de trabalho para satisfazer os seus requisitos.

## <a name="query-activity"></a>Atividade de consulta

Para uma experiência programática ao monitorizar o Synapse SQL via T-SQL, o serviço fornece um conjunto de Pontos de Vista de Gestão Dinâmica (DMVs). Estas opiniões são úteis quando se desloque ativamente problemas e identifique estrangulamentos de desempenho com a sua carga de trabalho.

Para ver a lista de DMVs que se aplicam ao Synapse SQL, consulte esta [documentação](../sql/reference-tsql-system-views.md#sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Métricas e registos de diagnóstico 

Ambas as métricas e registos podem ser exportados para o Monitor Azure, especificamente o componente de [registos do Monitor Azure](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e podem ser acedidos programáticamente através de consultas de [registo](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). A latência de log para Synapse SQL é de cerca de 10-15 minutos. Para mais detalhes sobre os fatores com impacto na latência, visite a seguinte documentação.

## <a name="next-steps"></a>Passos seguintes

O seguinte guia como orientar descreve cenários comuns e utiliza casos ao monitorizar e gerir o seu armazém de dados:

- [Monitorize a carga de trabalho do seu armazém de dados com DMVs](sql-data-warehouse-manage-monitor.md)

---
title: Recomendações Synapse SQL
description: Conheça as recomendações do SYnapse SQL e como são geradas
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 546945d70554adbb28f19a3153faa67495e55f04
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607759"
---
# <a name="synapse-sql-recommendations"></a>Recomendações Synapse SQL

Este artigo descreve as recomendações synapse SQL servidas através do Azure Advisor.  

A SQL Analytics fornece recomendações para garantir que a carga de trabalho do seu armazém de dados seja consistentemente otimizada para o desempenho. As recomendações estão fortemente integradas com o [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para lhe proporcionar as melhores práticas diretamente dentro do [portal Azure.](https://aka.ms/Azureadvisor) A SQL Analytics recolhe recomendações de telemetria e superfícies para a sua carga de trabalho ativa numa cadência diária. Os cenários de recomendação apoiados são descritos abaixo juntamente com a forma de aplicar as ações recomendadas.

Pode [consultar as suas recomendações](https://aka.ms/Azureadvisor) hoje! 

## <a name="data-skew"></a>Distorção de dados

O desvio de dados pode causar movimentos adicionais de dados ou estrangulamentos de recursos ao executar a sua carga de trabalho. A seguinte documentação descreve mostrar para identificar dados distorcidos e impedir que isso aconteça selecionando uma chave de distribuição ideal.

- [Identificar e remover o enviesamento](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Nenhuma estatística ou desatualizada

Ter estatísticas subóptimas pode afetar severamente o desempenho da consulta, uma vez que pode fazer com que o optimizador de consultas SQL gere planos de consulta sub-ideais. A seguinte documentação descreve as melhores práticas em torno da criação e atualização de estatísticas:

- [Criação e atualização de estatísticas de tabelas](sql-data-warehouse-tables-statistics.md)

Para ver a lista de tabelas impactadas por estas recomendações, execute o seguinte [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). O Advisor executa continuamente o mesmo script T-SQL para gerar estas recomendações.

## <a name="replicate-tables"></a>Replicar tabelas

Para recomendações de tabela replicadas, o Advisor deteta candidatos à tabela com base nas seguintes características físicas:

- Tamanho da mesa replicada
- Número de colunas
- Tipo de distribuição de tabelas
- Número de divisórias

O advisor aproveita continuamente a heurística baseada na carga de trabalho, como a frequência de acesso à mesa, as filas devolvidas em média, e os limiares em torno do tamanho e atividade dos armazéns de dados para garantir a geração de recomendações de alta qualidade.

A seguinte secção descreve heurísticabaseada baseada em carga de trabalho que pode encontrar no portal Azure para cada recomendação de tabela replicada:

- Scan avg- a percentagem média de filas que foram devolvidas da mesa para cada acesso à mesa nos últimos sete dias
- Leitura frequente, sem atualização - indica que a tabela não foi atualizada nos últimos sete dias enquanto mostra atividade de acesso
- Relação leitura/atualização - o rácio de frequência da tabela foi acedido em relação ao momento em que é atualizado nos últimos sete dias
- Atividade - mede o uso com base na atividade de acesso. Esta atividade compara a atividade de acesso à tabela em relação à atividade média de acesso à mesa em todo o armazém de dados nos últimos sete dias.

Atualmente, o Advisor só irá apresentar no máximo quatro candidatos de tabela replicados ao mesmo tempo com índices de colunas agrupadas priorizando a maior atividade.

> [!IMPORTANT]
> A recomendação do quadro replicado não é prova completa e não tem em conta as operações de movimento de dados. Estamos a trabalhar para adicionar isto como um heurístico, mas entretanto, deve sempre validar a sua carga de trabalho depois de aplicar a recomendação. Para saber mais sobre as tabelas replicadas, visite a seguinte [documentação.](design-guidance-for-replicated-tables.md#what-is-a-replicated-table)


## <a name="adaptive-gen2-cache-utilization"></a>Utilização adaptativa (Gen2)
Quando se tem um grande conjunto de trabalho, pode experimentar uma baixa percentagem de impacto de cache e uma alta utilização de cache. Para este cenário, deverá aumentar a capacidade de cache e refazer a sua carga de trabalho. Para mais informações visite a seguinte [documentação.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-how-to-monitor-cache) 

## <a name="tempdb-contention"></a>Contenção tempdb

O desempenho da consulta pode degradar-se quando há uma grande contenção temporária.  A contenção tempdb pode ocorrer através de tabelas temporárias definidas pelo utilizador ou quando há uma grande quantidade de movimento de dados. Para este cenário, pode escalar para mais alocação temporária e configurar classes de [recursos e gestão](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management) de carga de trabalho para fornecer mais memória às suas consultas. 

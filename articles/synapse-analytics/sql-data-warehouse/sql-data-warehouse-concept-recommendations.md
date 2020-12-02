---
title: Recomendações dedicadas ao sql pool Azure Advisor
description: Conheça as recomendações do Synapse SQL e como são geradas
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 667629b7f613b11f40528b039c7525339b7a62d0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462868"
---
# <a name="azure-advisor-recommendations-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Recomendações do Azure Advisor para piscina SQL dedicada em Azure Synapse Analytics

Este artigo descreve as recomendações dedicadas da piscina SQL disponíveis no Azure Advisor.  

O pool dedicado SQL fornece recomendações para garantir que a carga de trabalho do seu armazém de dados é consistentemente otimizada para o desempenho. As recomendações estão fortemente integradas com [o Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para lhe proporcionar as melhores práticas diretamente dentro do [portal Azure.](https://aka.ms/Azureadvisor) Piscina dedicada SQL recolhe recomendações de telemetria e superfícies para a sua carga de trabalho ativa numa cadência diária. Os cenários de recomendação apoiados são descritos a seguir, juntamente com a forma de aplicar as ações recomendadas.

Pode [consultar as suas recomendações](https://aka.ms/Azureadvisor) hoje! 

## <a name="data-skew"></a>Distorção de dados

A distorção de dados pode causar movimentos adicionais de dados ou estrangulamentos de recursos ao executar a sua carga de trabalho. A documentação que se segue descreve mostram mostrar que identificam os dados distorcidos e impedem que isso aconteça selecionando uma chave de distribuição ideal.

- [Identificar e remover o enviesamento](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Não estatísticas ou estatísticas desatualizadas

Ter estatísticas sub-ideais pode impactar severamente o desempenho da consulta, uma vez que pode fazer com que o otimizador de consulta SQL gere planos de consulta sub-ideais. A documentação que se segue descreve as melhores práticas em torno da criação e atualização de estatísticas:

- [Criação e atualização de estatísticas de quadros](sql-data-warehouse-tables-statistics.md)

Para ver a lista de tabelas impactadas por estas recomendações, execute o seguinte  [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). O Advisor executa continuamente o mesmo script T-SQL para gerar estas recomendações.

## <a name="replicate-tables"></a>Tabelas de replicação

Para recomendações de tabela replicadas, o Advisor deteta candidatos à mesa com base nas seguintes características físicas:

- Tamanho da mesa replicada
- Número de colunas
- Tipo de distribuição de mesa
- Número de divisórias

O advisor aproveita continuamente a heurística baseada na carga de trabalho, como a frequência de acesso à mesa, as linhas devolvidas em média, e os limiares em torno do tamanho e da atividade do armazém de dados para garantir que recomendações de alta qualidade são geradas.

A secção seguinte descreve heurística baseada na carga de trabalho que pode encontrar no portal Azure para cada recomendação de tabela replicada:

- Scan avg- a média por cento das filas que foram devolvidas da mesa para cada acesso à mesa nos últimos sete dias
- Leitura frequente, nenhuma atualização - indica que a tabela não foi atualizada nos últimos sete dias enquanto mostrava atividade de acesso
- Relação leitura/atualização - a relação entre a frequência com que a tabela foi acedida em relação à sua atualização nos últimos sete dias
- Atividade - mede o uso com base na atividade de acesso. Esta atividade compara a atividade de acesso à tabela em relação à atividade média de acesso à mesa em todo o armazém de dados nos últimos sete dias.

Atualmente, o Advisor só mostrará no máximo quatro candidatos de mesa replicados ao mesmo tempo, com índices de lojas de colunas agrupados priorizando a maior atividade.

> [!IMPORTANT]
> A recomendação da tabela replicada não é prova completa e não tem em conta as operações de movimento de dados. Estamos a trabalhar para adicionar isto como um heurístico, mas entretanto, deve sempre validar a sua carga de trabalho depois de aplicar a recomendação. Para saber mais sobre as tabelas replicadas, visite a seguinte [documentação.](design-guidance-for-replicated-tables.md#what-is-a-replicated-table)


## <a name="adaptive-gen2-cache-utilization"></a>Utilização adaptativa (Gen2) em cache
Quando se tem um grande conjunto de trabalho, pode experimentar uma baixa percentagem de cache e uma utilização de cache elevada. Para este cenário, deverá aumentar a capacidade de cache e refazer a sua carga de trabalho. Para mais informações visite a seguinte [documentação.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-how-to-monitor-cache) 

## <a name="tempdb-contention"></a>Contenção temporária

O desempenho da consulta pode degradar-se quando há uma alta contenção temporária.  A contenção temporária pode ocorrer através de tabelas temporárias definidas pelo utilizador ou quando há uma grande quantidade de movimento de dados. Para este cenário, pode escalar para mais alocação temporária e [configurar classes de recursos e gestão de carga de trabalho](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management) para fornecer mais memória às suas consultas. 

## <a name="data-loading-misconfiguration"></a>Confirmação errada de carregamento de dados

Deve sempre carregar dados de uma conta de armazenamento na mesma região que a sua piscina SQL dedicada para minimizar a latência. Utilize a [declaração COPY para ingestão de dados de produção elevada](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) e divida os seus ficheiros encenados na sua conta de armazenamento para maximizar a produção. Se não puder utilizar a declaração COPY, pode utilizar a API sqlBulkCopy ou o BCP com um tamanho de lote elevado para uma melhor produção. Para orientação adicional de carregamento de dados, visite a seguinte [documentação.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data) 

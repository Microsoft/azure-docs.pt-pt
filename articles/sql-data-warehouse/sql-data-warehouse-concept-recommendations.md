---
title: Recomendações de SQL Data Warehouse
description: Saiba mais sobre as recomendações de SQL Data Warehouse e como elas são geradas
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1a93339b99c6591ee9ed615c032d0eac58a372fe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827510"
---
# <a name="sql-data-warehouse-recommendations"></a>Recomendações de SQL Data Warehouse

Este artigo descreve as recomendações servidas por SQL Data Warehouse por meio do Azure Advisor.  

SQL Data Warehouse fornece recomendações para garantir que sua data warehouse seja otimizada consistentemente para o desempenho. As recomendações de data warehouse são totalmente integradas ao assistente [do Azure](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) para fornecer as práticas recomendadas diretamente dentro do [portal do Azure](https://aka.ms/Azureadvisor). SQL Data Warehouse analisa o estado atual do seu data warehouse, coleta a telemetria e as recomendações de superfícies para sua carga de trabalho ativa em uma cadência diária. Os cenários de recomendação data warehouse com suporte são descritos abaixo, juntamente com como aplicar as ações recomendadas.

Se você tiver algum comentário sobre o SQL Data Warehouse Advisor ou encontrar problemas, acesse [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com).   

Clique [aqui](https://aka.ms/Azureadvisor) para verificar suas recomendações hoje mesmo! Atualmente, esse recurso é aplicável somente a data warehouses Gen2. 

## <a name="data-skew"></a>Distorção de dados

A distorção de dados pode causar gargalos de recursos ou movimentação de dados adicionais ao executar sua carga de trabalho. A documentação a seguir descreve mostrar como identificar a distorção de dados e impedir que ele aconteça selecionando uma chave de distribuição ideal.

- [Identificar e remover a distorção](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Estatísticas não ou desatualizadas

Ter estatísticas de qualidade inferior pode afetar seriamente o desempenho da consulta, pois isso pode fazer com que o SQL Data Warehouse otimizador de consulta gere planos de consulta de qualidade inferior. A documentação a seguir descreve as práticas recomendadas em relação à criação e atualização de estatísticas:

- [Criando e atualizando estatísticas de tabela](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Para ver a lista de tabelas afetadas por essas recomendações, execute o [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)a seguir. O Advisor executa continuamente o mesmo script T-SQL para gerar essas recomendações.

## <a name="replicate-tables"></a>Replicar tabelas

Para recomendações de tabelas replicadas, o Advisor detecta candidatos à tabela com base nas seguintes características físicas:

- Tamanho da tabela replicada
- Número de colunas
- Tipo de distribuição de tabela
- Número de partições

O Advisor aproveita continuamente a heurística baseada em carga de trabalho, como frequência de acesso à tabela, linhas retornadas em média e limites em relação ao tamanho data warehouse e à atividade para garantir que as recomendações de alta qualidade sejam geradas. 

O seguinte descreve a heurística baseada em carga de trabalho que você pode encontrar na portal do Azure para cada recomendação de tabela replicada:

- Verificação média-a porcentagem média de linhas que foram retornadas da tabela para cada acesso de tabela nos últimos sete dias
- Leitura frequente, sem atualização-indica que a tabela não foi atualizada nos últimos sete dias ao mostrar a atividade de acesso
- Taxa de leitura/atualização-a taxa de quão frequente a tabela foi acessada em relação ao quando ela é atualizada nos últimos sete dias
- Atividade – mede o uso com base na atividade de acesso. Isso compara a atividade de acesso à tabela relativa à atividade média de acesso à tabela no data warehouse nos últimos sete dias. 

Atualmente, o Advisor mostrará apenas no máximo quatro candidatos à tabela replicada de uma só vez com índices columnstore clusterizados priorizando a atividade mais alta.

> [!IMPORTANT]
> A recomendação de tabela replicada não é uma prova completa e não leva em conta as operações de movimentação de dados. Estamos trabalhando para adicionar isso como uma heurística, mas enquanto isso, você sempre deve validar sua carga de trabalho depois de aplicar a recomendação. Entre em contato com sqldwadvisor@service.microsoft.com se você descobrir as recomendações da tabela replicada que faz com que sua carga de trabalho seja regressiva. Para saber mais sobre tabelas replicadas, visite a [documentação](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table)a seguir.

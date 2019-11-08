---
title: Monitorar e melhorar o desempenho
description: O banco de dados SQL do Azure fornece ferramentas de desempenho para ajudá-lo a identificar áreas que podem melhorar o desempenho atual da consulta.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: f7e5f698a822cd68d8319102e1b8bddbbfa8ad70
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821330"
---
# <a name="monitor-and-improve-performance"></a>Monitorar e melhorar o desempenho

O banco de dados SQL do Azure identifica possíveis problemas no banco de dados e recomenda ações que podem melhorar o desempenho da carga de trabalho fornecendo ações e recomendações de ajuste inteligente.

Para examinar o desempenho do banco de dados, use o bloco **desempenho** na página Visão geral ou navegue até a seção "suporte + solução de problemas":

   ![Exibir desempenho](./media/sql-database-performance/entries.png)

Na seção "suporte + solução de problemas", você pode usar as seguintes páginas:


1. [Visão geral do desempenho](#performance-overview) para monitorar o desempenho do seu banco de dados. 
2. [Recomendações de desempenho](#performance-recommendations) para encontrar recomendações de desempenho que podem melhorar o desempenho da carga de trabalho.
3. [Análise de desempenho de consultas](#query-performance-insight) localizar as principais consultas de consumo de recursos.
4. [Ajuste automático](#automatic-tuning) para permitir que o banco de dados SQL do Azure otimize automaticamente seu banco de dados.

## <a name="performance-overview"></a>Visão geral do desempenho

Essa exibição fornece um resumo do desempenho do banco de dados e ajuda você com ajuste de desempenho e solução de problemas. 

![Desempenho](./media/sql-database-performance/performance.png)

* O bloco **recomendações** fornece uma análise das recomendações de ajuste para seu banco de dados (três principais recomendações são mostradas se houver mais). Clicar nesse bloco leva você às **[recomendações de desempenho](#performance-recommendations)** . 
* O bloco **atividade de ajuste** fornece um resumo das ações de ajuste em andamento e concluídas para seu banco de dados, fornecendo uma exibição rápida do histórico de atividade de ajuste. Clicar nesse bloco leva você até a exibição do histórico de ajuste completo do seu banco de dados.
* O bloco de **ajuste automático** mostra a [configuração de ajuste automático](sql-database-automatic-tuning-enable.md) do banco de dados (opções de ajuste aplicadas automaticamente ao banco de dados). Clicar nesse bloco abre a caixa de diálogo configuração de automação.
* O bloco **consultas de banco de dados** mostra o resumo do desempenho da consulta para seu banco de dados (uso geral de DTU e consultas de consumo de recursos principais). Clicar nesse bloco leva você para **[análise de desempenho de consultas](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Recomendações de desempenho

Esta página fornece [recomendações de ajuste](sql-database-advisor.md) inteligente que podem melhorar o desempenho do seu banco de dados. Os seguintes tipos de recomendações são mostrados nesta página:

* Recomendações sobre quais índices criar ou descartar.
* Recomendações quando problemas de esquema são identificados no banco de dados.
* Recomendações quando as consultas podem se beneficiar de consultas parametrizadas.

![Desempenho](./media/sql-database-performance/recommendations.png)

Você também pode encontrar um histórico completo de ações de ajuste que foram aplicadas no passado.

Saiba como encontrar as recomendações de aplicar desempenho no artigo [Localizar e aplicar recomendações de desempenho](sql-database-advisor-portal.md) .

## <a name="automatic-tuning"></a>Ajuste automático

Os bancos de dados SQL do Azure podem ajustar automaticamente o desempenho do banco de dados aplicando [recomendações de desempenho](sql-database-advisor.md). Para saber mais, leia o [artigo de ajuste automático](sql-database-automatic-tuning.md). Para habilitá-lo, leia [como habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Query Performance Insight

[Análise de desempenho de consultas](sql-database-query-performance.md) permite que você gaste menos tempo Solucionando problemas de desempenho do banco de dados fornecendo:

* Informações mais aprofundadas sobre o consumo de seu recurso de bancos de dados (DTU). 
* As principais consultas de consumo de CPU, que podem ser potencialmente ajustadas para melhorar o desempenho. 
* A capacidade de detalhar os detalhes de uma consulta. 

  ![Painel de desempenho](./media/sql-database-query-performance/performance.png)

Encontre mais informações sobre esta página no artigo **[como usar análise de desempenho de consultas](sql-database-query-performance.md)** .

## <a name="additional-resources"></a>Recursos adicionais

* [Diretrizes de desempenho do banco de dados SQL](sql-database-performance-guidance.md)
* [Quando um pool elástico deve ser usado?](sql-database-elastic-pool-guidance.md)


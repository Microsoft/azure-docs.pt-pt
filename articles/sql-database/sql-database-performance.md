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
ms.date: 11/14/2019
ms.openlocfilehash: 474c2f4f00374ce785b81fe048e11cb353b3078a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151166"
---
# <a name="monitor-and-improve-performance"></a>Monitorar e melhorar o desempenho

O banco de dados SQL do Azure identifica possíveis problemas no banco de dados e recomenda ações que podem melhorar o desempenho da carga de trabalho fornecendo ações e recomendações de ajuste inteligente.

## <a name="performance-tuning-options"></a>Opções de ajuste de desempenho

As opções de ajuste de desempenho disponíveis no banco de dados SQL do Azure estão disponíveis no menu de navegação do banco de dados em "desempenho inteligente":

| Opção de ajuste de desempenho | Banco de dados individual e suporte a banco de dados em pool | Suporte a banco de dados de instância |
| :----------------------------- | ----- | ----- |
| **Visão geral de desempenho** -monitorar todas as atividades de desempenho do banco de dados. | Sim | Não | 
| **Recomendações de desempenho** -mostra as recomendações de desempenho que podem melhorar o desempenho da carga de trabalho. | Sim | Não | 
| **Análise de desempenho de consultas** -mostra o desempenho de principais consultas de consumo no banco de dados. | Sim | Não | 
| **Ajuste automático** -use o banco de dados SQL do Azure para otimizar automaticamente o desempenho do banco de dados. | Sim | Não | 

## <a name="performance-overview"></a>Visão geral do desempenho

Essa exibição fornece um resumo do desempenho do banco de dados e ajuda você com ajuste de desempenho e solução de problemas. 

![Visão geral do desempenho do banco de dados SQL do Azure](./media/sql-database-performance/performance-overview-annotated.png)

* O bloco **recomendações** fornece uma análise das recomendações de ajuste para seu banco de dados (três principais recomendações são mostradas se houver mais). Clicar nesse bloco leva você às **[recomendações de desempenho](#performance-recommendations)** . 
* O bloco **atividade de ajuste** fornece um resumo das ações de ajuste em andamento e concluídas para seu banco de dados, fornecendo uma exibição rápida do histórico de atividade de ajuste. Clicar nesse bloco leva você até a exibição do histórico de ajuste completo do seu banco de dados.
* O bloco de **ajuste automático** mostra a [configuração de ajuste automático](sql-database-automatic-tuning-enable.md) do banco de dados (opções de ajuste aplicadas automaticamente ao banco de dados). Clicar nesse bloco abre a caixa de diálogo configuração de automação.
* O bloco **consultas de banco de dados** mostra o resumo do desempenho da consulta para seu banco de dados (uso geral de DTU e consultas de consumo de recursos principais). Clicar nesse bloco leva você para **[análise de desempenho de consultas](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Recomendações de desempenho

Esta página fornece [recomendações de ajuste](sql-database-advisor.md) inteligente que podem melhorar o desempenho do seu banco de dados. Os seguintes tipos de recomendações são mostrados nesta página:

* Recomendações sobre quais índices criar ou descartar.
* Recomendações quando problemas de esquema são identificados no banco de dados.
* Recomendações quando as consultas podem se beneficiar de consultas parametrizadas.

![Recomendações de desempenho para o banco de dados SQL do Azure](./media/sql-database-performance/performance-recommendations-annotated.png)

Você também pode encontrar um histórico completo de ações de ajuste que foram aplicadas no passado.

Saiba como encontrar as recomendações de aplicar desempenho no artigo [Localizar e aplicar recomendações de desempenho](sql-database-advisor-portal.md) .

## <a name="query-performance-insight"></a>Query Performance Insight

[Análise de desempenho de consultas](sql-database-query-performance.md) permite que você gaste menos tempo Solucionando problemas de desempenho do banco de dados fornecendo:

* Informações mais aprofundadas sobre o consumo de seu recurso de bancos de dados (DTU). 
* As principais consultas de consumo de CPU, que podem ser potencialmente ajustadas para melhorar o desempenho. 
* A capacidade de detalhar os detalhes de uma consulta. 

  ![Informações de desempenho de consulta do banco de dados SQL do Azure](./media/sql-database-performance/query-performance-insights-annotated.png)

Encontre mais informações sobre esta página no artigo **[como usar análise de desempenho de consultas](sql-database-query-performance.md)** .

## <a name="automatic-tuning"></a>Ajuste automático

Os bancos de dados SQL do Azure podem ajustar automaticamente o desempenho do banco de dados aplicando [recomendações de desempenho](sql-database-advisor.md). Para habilitá-lo, leia [como habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).

  ![Ajuste automático para o banco de dados SQL do Azure](./media/sql-database-performance/automatic-tuning-annotated.png)

Para saber mais, leia o [artigo de ajuste automático](sql-database-automatic-tuning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Diretrizes de desempenho do banco de dados SQL](sql-database-performance-guidance.md)
* [Quando um pool elástico deve ser usado?](sql-database-elastic-pool-guidance.md)

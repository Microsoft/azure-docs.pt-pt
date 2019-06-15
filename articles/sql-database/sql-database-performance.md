---
title: Monitorizar e melhorar o desempenho - base de dados SQL do Azure | Documentos da Microsoft
description: A base de dados do SQL Azure fornece ferramentas de desempenho para o ajudar a identificar áreas que podem melhorar o desempenho de consulta atual.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: adbccd5f9cfd5ddd1912e304f800f3ebe04912c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584833"
---
# <a name="monitor-and-improve-performance"></a>Monitorizar e melhorar o desempenho

Base de dados SQL do Azure identifica potenciais problemas na sua base de dados e recomenda ações que podem melhorar o desempenho da carga de trabalho, fornecendo as ações de otimização inteligentes e recomendações.

Para rever o desempenho da sua base de dados, utilize o **desempenho** mosaico na página de descrição geral ou navegue para baixo até "Suporte + resolução de problemas" secção:

   ![Ver desempenho](./media/sql-database-performance/entries.png)

No "Suporte + resolução de problemas" secção, pode utilizar as seguintes páginas:


1. [Descrição geral do desempenho](#performance-overview) para monitorizar o desempenho da base de dados. 
2. [Recomendações de desempenho](#performance-recommendations) para localizar recomendações de desempenho que podem melhorar o desempenho da carga de trabalho.
3. [Query Performance Insight](#query-performance-insight) para localizar recursos principais consultas de consumo.
4. [A otimização automática](#automatic-tuning) para permitir que a base de dados do SQL Azure otimizamos automaticamente a sua base de dados.

## <a name="performance-overview"></a>Descrição geral do desempenho

Esta vista fornece um resumo de desempenho da sua base de dados e ajuda-o com o ajuste de desempenho e resolução de problemas. 

![Desempenho](./media/sql-database-performance/performance.png)

* O **recomendações** mosaico fornece uma repartição da otimização de recomendações para a sua base de dados (recomendações de três principais são apresentadas se existem mais). Clicar neste mosaico leva-o para  **[recomendações de desempenho](#performance-recommendations)** . 
* O **atividade da otimização** mosaico fornece um resumo em curso e concluídas, ajuste as ações de sua base de dados, dando-lhe uma vista rápida sobre o histórico de atividade da otimização. Clicar neste mosaico leva-o para a vista de histórico de ajuste completa da base de dados.
* O **Auto-ajuste** mosaico mostra o [auto-ajuste da configuração](sql-database-automatic-tuning-enable.md) da base de dados (Ajuste as opções que são aplicadas automaticamente à base de dados). Clicar neste mosaico abre a caixa de diálogo de configuração de automatização.
* O **consultas de base de dados** mosaico mostra o resumo de desempenho da consulta da base de dados (geral DTU utilização e de cima recurso consultas de consumo). Clicar neste mosaico leva-o para  **[Query Performance Insight](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Recomendações de desempenho

Esta página fornece inteligente [recomendações de otimização](sql-database-advisor.md) que pode melhorar o desempenho de seu banco de dados. Os seguintes tipos de recomendações são apresentados nesta página:

* Recomendações sobre quais índices para criar ou remover.
* Recomendações de quando os problemas de esquema são identificados na base de dados.
* Recomendações ao consultas podem se beneficiar consultas parametrizadas.

![Desempenho](./media/sql-database-performance/recommendations.png)

Também pode encontrar o histórico completo de ajuste de ações que foram aplicadas no passado.

Saiba como localizar um aplicar recomendações de desempenho na [localizar e aplicar recomendações de desempenho](sql-database-advisor-portal.md) artigo.

## <a name="automatic-tuning"></a>Ajuste automático

Bases de dados SQL do Azure automaticamente podem otimizar o desempenho da base de dados através da aplicação [recomendações de desempenho](sql-database-advisor.md). Para saber mais, leia [artigo de otimização automática](sql-database-automatic-tuning.md). Para ativá-la, leia [como ativar a otimização automática](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) permite-lhe gastar menos tempo de resolução de problemas de desempenho de base de dados ao fornecer:

* Informações mais aprofundadas sobre o consumo de recursos (DTUS) de bases de dados. 
* A principais consumo da CPU consultas, que potencialmente podem ser otimizadas para um melhor desempenho. 
* A capacidade de desagregar os detalhes de uma consulta. 

  ![dashboard de desempenho](./media/sql-database-query-performance/performance.png)

Encontrar mais informações sobre esta página no artigo  **[como utilizar o Query Performance Insight](sql-database-query-performance.md)** .

## <a name="additional-resources"></a>Recursos adicionais

* [Orientação de desempenho de base de dados SQL do Azure para bases de dados individuais](sql-database-performance-guidance.md)
* [Quando deve ser utilizado um conjunto elástico?](sql-database-elastic-pool-guidance.md)


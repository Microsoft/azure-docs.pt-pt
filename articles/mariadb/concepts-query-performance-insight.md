---
title: Informações de desempenho de consulta na base de dados do Azure para MariaDB
description: Este artigo descreve a funcionalidade de informações de desempenho de consulta na base de dados do Azure para MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: be9d5c4745cb03d9d3eaa324b7191d82b9d4a14e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079421"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Informações de desempenho de consulta na base de dados do Azure para MariaDB

**Aplica-se a:**  base de dados do Azure para MariaDB 10.2

> [!NOTE]
> O Query Performance Insight está em pré-visualização. Suporte para o Query Performance Insight no portal do Azure está a ser lançado e talvez ainda não estar disponível na sua região.

O Query Performance Insight ajuda-o a identificar rapidamente quais são suas consultas de maior duração, como são alterados ao longo do tempo e esperas de que estão a afetá-los.

## <a name="permissions"></a>Permissões

**Proprietário** ou **contribuinte** permissões necessárias para ver o texto das consultas no Query Performance Insight. ** Leitor** pode ver gráficos e tabelas, mas não texto da consulta.

## <a name="prerequisites"></a>Pré-requisitos

Para o Query Performance Insight para a função, dados tem de existir na [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Ver informações de desempenho

A vista [Query Performance Insight](concepts-query-performance-insight.md) no portal do Azure vai apresentar visualizações sobre informações importantes do Arquivo de Consultas.

Na página do portal da sua base de dados do Azure para MariaDB server, selecione **Query Performance Insight** sob o **desempenho inteligente** secção da barra de menus.

![O Query Performance Insight consultas de execução longa](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

O **consultas de execução longa** separador mostra as consultas de 5 principais por duração média por execução, agregados em intervalos de 15 minutos. Pode ver mais consultas ao selecionar a partir da **número de consultas** menu pendente. Ao fazer isso, as cores do gráfico podem ser alteradas para um ID de Consulta específico.

Pode clicar e arrastar no gráfico para restringir a uma janela de tempo específica. Em alternativa, utilize o zoom os ícones para ver um período de tempo menor ou maior, respetivamente.

Selecione o **estatísticas de espera** separador para ver as visualizações correspondentes no esperas no servidor.

Consultas apresentadas na vista de estatísticas de espera são agrupadas pelas consultas que apresentam as esperas maior durante o intervalo de tempo especificado.

![Estatísticas de espera o Query Performance Insight](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) na base de dados do Azure para MariaDB.
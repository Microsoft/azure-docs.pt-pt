---
title: Informações de desempenho de consulta na base de dados do Azure para MySQL
description: Este artigo descreve a funcionalidade de informações de desempenho de consulta na base de dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 8f142933ebf955cbe3aa119f42779109fb6ef7db
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67589076"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Informações de desempenho de consulta na base de dados do Azure para MySQL

**Aplica-se a:**  base de dados do Azure para MySQL 5.7

> [!NOTE]
> O Query Performance Insight está em pré-visualização.

O Query Performance Insight ajuda-o a identificar rapidamente quais são suas consultas de maior duração, como são alterados ao longo do tempo e esperas de que estão a afetá-los.

## <a name="common-scenarios"></a>Cenários comuns

### <a name="long-running-queries"></a>Consultas de execução longa

- Identificar as consultas de execução mais longa no passado X horas
- Identificação de consultas de N principais que estão a aguardar nos recursos
 
### <a name="wait-statistics"></a>Estatísticas de espera

- Natureza de espera de compreensão de consulta
- Compreender as tendências de esperas de recursos e onde existe contenção de recursos

## <a name="permissions"></a>Permissões

**Proprietário** ou **contribuinte** permissões necessárias para ver o texto das consultas no Query Performance Insight. ** Leitor** pode ver gráficos e tabelas, mas não texto da consulta.

## <a name="prerequisites"></a>Pré-requisitos

Para o Query Performance Insight para a função, dados tem de existir na [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Ver informações de desempenho

A vista [Query Performance Insight](concepts-query-performance-insight.md) no portal do Azure vai apresentar visualizações sobre informações importantes do Arquivo de Consultas.

Na página do portal da sua base de dados do Azure para o servidor MySQL, selecione **Query Performance Insight** sob o **desempenho inteligente** secção da barra de menus.

### <a name="long-running-queries"></a>Consultas de execução longa

O **consultas de execução longa** separador mostra as consultas de 5 principais por duração média por execução, agregados em intervalos de 15 minutos. Pode ver mais consultas ao selecionar a partir da **número de consultas** menu pendente. Ao fazer isso, as cores do gráfico podem ser alteradas para um ID de Consulta específico.

Pode clicar e arrastar no gráfico para restringir a uma janela de tempo específica. Em alternativa, utilize o zoom os ícones para ver um período de tempo menor ou maior, respetivamente.

![O Query Performance Insight consultas de execução longa](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Estatísticas de espera

> [!NOTE]
> As estatísticas de espera destinam-se para a resolução de problemas de desempenho de consulta. Recomenda-se para ser ativada apenas para fins de resolução de problemas.

As estatísticas de espera fornece uma visão dos eventos espera que ocorrem durante a execução de uma consulta específica. Saiba mais sobre os tipos de evento espera o [documentação de motor do MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Selecione o **estatísticas de espera** separador para ver as visualizações correspondentes no esperas no servidor.

Consultas apresentadas na vista de estatísticas de espera são agrupadas pelas consultas que apresentam as esperas maior durante o intervalo de tempo especificado.

![Estatísticas de espera o Query Performance Insight](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) na base de dados do Azure para MySQL.
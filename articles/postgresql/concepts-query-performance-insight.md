---
title: Análise de Desempenho de Consultas no banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve o recurso Análise de Desempenho de Consultas no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 748c6f08da535013724f68b8be424e50a2d49dfd
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764423"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**Aplica-se a:** Banco de dados do Azure para PostgreSQL-versões de servidor único 9,6, 10, 11

Análise de Desempenho de Consultas ajuda a identificar rapidamente o que são suas consultas em execução mais longas, como elas mudam com o passar do tempo e quais esperas estão afetando-as.

## <a name="permissions"></a>Permissões
As permissões **Proprietário**ou **Contribuidor** necessárias para ver o texto das consultas no Query Performance Insight. O **Leitor** pode ver gráficos e tabelas, mas não o texto da consulta.

## <a name="prerequisites"></a>Pré-requisitos
Para que Análise de Desempenho de Consultas funcionem, os dados devem existir no [repositório de consultas](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Exibindo informações de desempenho
A vista [Query Performance Insight](concepts-query-performance-insight.md) no portal do Azure vai apresentar visualizações sobre informações importantes do Arquivo de Consultas. 

Na página do portal do servidor do banco de dados do Azure para PostgreSQL, selecione análise de **desempenho de consultas** na seção **desempenho inteligente** da barra de menus.

![Análise de Desempenho de Consultas consultas de longa execução](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

A guia **consultas de longa execução** mostra as cinco principais consultas por duração média por execução, agregadas em intervalos de 15 minutos. Pode ver mais consultas ao selecionar no menu pendente **Número de Consultas**. Ao fazer isso, as cores do gráfico podem ser alteradas para um ID de Consulta específico.

Pode clicar e arrastar no gráfico para restringir a uma janela de tempo específica. Como alternativa, use os ícones ampliar e reduzir para exibir um período de tempo menor ou maior, respectivamente.

A tabela abaixo do gráfico fornece mais detalhes sobre as consultas de execução longa nessa janela de tempo.

Selecione o separador **Estatísticas de Espera** para ver as visualizações correspondentes sobre esperas no servidor.

![Estatísticas de esperas de Análise de Desempenho de Consultas](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>Considerações
* Análise de Desempenho de Consultas não está disponível para [réplicas de leitura](concepts-read-replicas.md).

## <a name="next-steps"></a>Passos Seguintes
- Saber mais sobre [monitorização e otimização](concepts-monitoring.md) na Base de Dados do Azure para PostgreSQL.



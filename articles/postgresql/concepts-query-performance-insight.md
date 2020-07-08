---
title: Insight de Desempenho de Consulta - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve a funcionalidade De Insight de Desempenho de Consulta na Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: dd5b4ec53d82421ddd9d680ca41e48eeecc43c2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74768389"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**Aplica-se a:** Azure Database para PostgreSQL - Versões single Server 9.6, 10, 11

A Consulta Performance Insight ajuda-o a identificar rapidamente quais são as suas consultas de execução mais longas, como mudam ao longo do tempo e o que as esperas estão a afetá-las.

## <a name="permissions"></a>Permissões
As permissões **Proprietário**ou **Contribuidor** necessárias para ver o texto das consultas no Query Performance Insight. O **Leitor** pode ver gráficos e tabelas, mas não o texto da consulta.

## <a name="prerequisites"></a>Pré-requisitos
Para que o Insight de Desempenho de Consulta funcione, os dados devem existir na [Loja de Consultas](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visualização de insights de desempenho
A vista [Query Performance Insight](concepts-query-performance-insight.md) no portal do Azure vai apresentar visualizações sobre informações importantes do Arquivo de Consultas. 

Na página do portal da sua Base de Dados Azure para o servidor PostgreSQL, selecione **Insight de desempenho de consulta** na secção desempenho **inteligente** da barra de menu.

![Consultas de insight de desempenho de consulta longas consultas](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

O separador **de consultas de longo prazo** mostra as cinco principais consultas pela duração média por execução, agregadas em intervalos de 15 minutos. Pode ver mais consultas ao selecionar no menu pendente **Número de Consultas**. Ao fazer isso, as cores do gráfico podem ser alteradas para um ID de Consulta específico.

Pode clicar e arrastar no gráfico para restringir a uma janela de tempo específica. Em alternativa, utilize os ícones de zoom para visualizar um período de tempo menor ou maior, respectivamente.

A tabela abaixo da tabela dá mais detalhes sobre as consultas de longa duração nessa janela de tempo.

Selecione o separador **Estatísticas de Espera** para ver as visualizações correspondentes sobre esperas no servidor.

![Consulta Performance Insight aguarda estatísticas](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>Considerações
* O Insight de Desempenho da Consulta não está disponível para [réplicas de leitura](concepts-read-replicas.md).

## <a name="next-steps"></a>Próximos passos
- Saber mais sobre [monitorização e otimização](concepts-monitoring.md) na Base de Dados do Azure para PostgreSQL.



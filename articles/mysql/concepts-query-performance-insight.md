---
title: Insight de Desempenho de Consulta - Base de Dados Azure para MySQL
description: Este artigo descreve a funcionalidade De Insight de Desempenho de Consulta na Base de Dados Azure para o MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 51b478e5184c79e11d95ff004a652b2e5298558f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83402617"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Query Performance Insight na Base de Dados do Azure para MySQL

**Aplica-se a:** Base de Dados Azure para MySQL 5.7, 8.0

A Consulta Performance Insight ajuda-o a identificar rapidamente quais são as suas consultas de execução mais longas, como mudam ao longo do tempo e o que as esperas estão a afetá-las.

## <a name="common-scenarios"></a>Cenários comuns

### <a name="long-running-queries"></a>Consultas de execução prolongada

- Identificando as consultas de execução mais longas nas últimas horas X
- Identificando as principais consultas N que estão à espera de recursos
 
### <a name="wait-statistics"></a>Estatísticas de espera

- Compreender a natureza da espera para uma consulta
- Compreender tendências para esperas de recursos e onde existe contenção de recursos

## <a name="permissions"></a>Permissões

As permissões **Proprietário**ou **Contribuidor** necessárias para ver o texto das consultas no Query Performance Insight. O **Leitor** pode ver gráficos e tabelas, mas não o texto da consulta.

## <a name="prerequisites"></a>Pré-requisitos

Para que o Insight de Desempenho de Consulta funcione, os dados devem existir na [Loja de Consultas](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visualização de insights de desempenho

A vista [Query Performance Insight](concepts-query-performance-insight.md) no portal do Azure vai apresentar visualizações sobre informações importantes do Arquivo de Consultas.

Na página do portal da sua Base de Dados Azure para o servidor MySQL, selecione **'Insight de Desempenho de Consulta'** na secção **desempenho inteligente** da barra de menus.

### <a name="long-running-queries"></a>Consultas de execução prolongada

O separador **de consultas de longa duração** mostra as 5 principais consultas pela duração média por execução, agregadas em intervalos de 15 minutos. Pode ver mais consultas selecionando a partir **do número de consultas.** Ao fazer isso, as cores do gráfico podem ser alteradas para um ID de Consulta específico.

Pode clicar e arrastar no gráfico para restringir a uma janela de tempo específica. Em alternativa, utilize os ícones de zoom para visualizar um período de tempo menor ou maior, respectivamente.

![Consultas de insight de desempenho de consulta longas consultas](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Estatísticas de espera

> [!NOTE]
> As estatísticas de espera destinam-se a resolver problemas de problemas de desempenho. Recomenda-se que seja ligado apenas para efeitos de resolução de problemas. <br>Se receber a mensagem de erro no portal Azure "*O problema encontrado para 'Microsoft.DBforMySQL'; não pode cumprir o pedido. Se este problema continuar ou for inesperado, contacte o suporte com esta informação."* ao visualizar estatísticas de espera, utilize um período de tempo menor.

As estatísticas de espera fornecem uma visão dos eventos de espera que ocorrem durante a execução de uma consulta específica. Saiba mais sobre os tipos de eventos de espera na documentação do [motor MySQL.](https://go.microsoft.com/fwlink/?linkid=2098206)

Selecione o separador **Estatísticas de Espera** para ver as visualizações correspondentes sobre esperas no servidor.

As consultas exibidas na vista das estatísticas de espera são agrupadas pelas consultas que exibem as maiores esperas durante o intervalo de tempo especificado.

![Consulta Performance Insight aguarda estatísticas](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [monitorização e afinação](concepts-monitoring.md) na Base de Dados Azure para o MySQL.
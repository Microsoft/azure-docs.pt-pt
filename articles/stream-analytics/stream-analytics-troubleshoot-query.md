---
title: Solucionar problemas Azure Stream Analytics consultas
description: Este artigo descreve técnicas para solucionar problemas de suas consultas em trabalhos do Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 5534a46ba99d1536d331b5852ef47588f03d73a4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980282"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Solucionar problemas Azure Stream Analytics consultas

Este artigo descreve problemas comuns com o desenvolvimento de Stream Analytics consultas e como solucioná-los.

## <a name="query-is-not-producing-expected-output"></a>A consulta não está produzindo a saída esperada
1.  Examine os erros testando localmente:
    - Na guia **consulta** , selecione **testar**. Use os dados de exemplo baixados para [testar a consulta](stream-analytics-test-query.md). Examine os erros e tente corrigi-los.   
    - Você também pode [testar sua consulta diretamente na entrada ao vivo](stream-analytics-live-data-local-testing.md) usando as ferramentas de Stream Analytics para o Visual Studio.

2.  Se você usar [**carimbo de data/hora por**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), verifique se os eventos têm carimbos de data/hora maiores do que o [horário de início do trabalho](stream-analytics-out-of-order-and-late-events.md).

3.  Elimine armadilhas comuns, como:
    - Uma cláusula [**Where**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) na consulta filtrou todos os eventos, impedindo que qualquer saída seja gerada.
    - Uma função [**Cast**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) falha, fazendo com que o trabalho falhe. Para evitar falhas de conversão de tipo, use [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) em vez disso.
    - Ao usar funções de janela, aguarde a duração da janela inteira para ver uma saída da consulta.
    - O carimbo de hora de eventos precede a hora de início do trabalho e, portanto, os eventos estão sendo removidos.

4.  Verifique se as políticas de ordenação de eventos estão configuradas como esperado. Vá para as **configurações** e selecione [**ordenação de eventos**](stream-analytics-out-of-order-and-late-events.md). A política *não* é aplicada quando você usa o botão **testar** para testar a consulta. Esse resultado é uma diferença entre o teste no navegador em vez da execução do trabalho em produção.

5. Depurar usando logs de diagnóstico e auditoria:
    - Use [logs de auditoria](../azure-resource-manager/management/view-activity-logs.md)e filtre para identificar e depurar erros.
    - Use [os logs de diagnóstico de trabalho](stream-analytics-job-diagnostic-logs.md) para identificar e depurar erros.

## <a name="job-is-consuming-too-many-streaming-units"></a>O trabalho está consumindo muitas unidades de streaming
Certifique-se de aproveitar a paralelização em Azure Stream Analytics. Você pode aprender a [dimensionar com a paralelização de consulta](stream-analytics-parallelization.md) de trabalhos de Stream Analytics Configurando partições de entrada e ajustando a definição de consulta de análise.

## <a name="debug-queries-progressively"></a>Depurar consultas progressivamente

No processamento de dados em tempo real, saber o que os dados parecem no meio da consulta pode ser útil. Como as entradas ou as etapas de um trabalho de Azure Stream Analytics podem ser lidas várias vezes, você pode gravar instruções SELECT INTO adicionais. Fazer isso gera dados intermediários no armazenamento e permite inspecionar a exatidão dos dados, assim como as *variáveis de inspeção* quando você depura um programa.

A consulta de exemplo a seguir em um trabalho de Azure Stream Analytics tem uma entrada de fluxo, duas entradas de dados de referência e uma saída para o armazenamento de tabelas do Azure. A consulta une dados do hub de eventos e dois blobs de referência para obter as informações de nome e categoria:

![Exemplo Stream Analytics consulta SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Observe que o trabalho está em execução, mas nenhum evento está sendo produzido na saída. No bloco **monitoramento** , mostrado aqui, você pode ver que a entrada está produzindo dados, mas não sabe qual etapa da **junção** fez com que todos os eventos sejam removidos.

![O bloco monitoramento de Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

Nessa situação, você pode adicionar algumas instruções SELECT INTO adicionais para "registrar" os resultados da junção intermediária e os dados que são lidos da entrada.

Neste exemplo, adicionamos duas novas "saídas temporárias". Eles podem ser qualquer coletor que você desejar. Aqui, usamos o armazenamento do Azure como exemplo:

![Adicionando instruções SELECT INTO extras para Stream Analytics consulta](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Em seguida, você pode reescrever a consulta da seguinte maneira:

![Consulta SELECT in Stream Analytics reescrita](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Agora, inicie o trabalho novamente e deixe-o em execução por alguns minutos. Em seguida, consulte temp1 e Temp2 com o Visual Studio Cloud Explorer para produzir as seguintes tabelas:


de **tabela temp1** ![selecionar na tabela temp1 Stream Analytics consulta](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

a **tabela temp2**
![seleção na tabela Temp2 Stream Analytics consulta](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Como você pode ver, temp1 e Temp2 têm dados e a coluna Name é populada corretamente em Temp2. No entanto, como ainda não há dados na saída, algo está errado:

![Selecione na tabela Saída1 sem dados Stream Analytics consulta](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Ao obter a amostragem dos dados, você pode ter quase certeza de que o problema está com a segunda junção. Você pode baixar os dados de referência do blob e dar uma olhada:

![SELECIONAR em consulta de Stream Analytics de tabela de referência](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Como você pode ver, o formato do GUID nesses dados de referência é diferente do formato da coluna [from] em Temp2. É por isso que os dados não chegam em Saída1 conforme o esperado.

Você pode corrigir o formato de dados, carregá-lo no blob de referência e tentar novamente:

![SELECIONAR na tabela temporária Stream Analytics consulta](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Desta vez, os dados na saída são formatados e preenchidos conforme o esperado.

![SELECIONAR na tabela final Stream Analytics consulta](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Obter ajuda

Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

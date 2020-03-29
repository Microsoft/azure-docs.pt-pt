---
title: Consultas de Troubleshoot Azure Stream Analytics
description: Este artigo descreve técnicas para resolver problemas com as suas consultas em trabalhos de Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: bf0740bbdd4754aeba43e64f1076a1bea33cffc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844429"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Consultas de Troubleshoot Azure Stream Analytics

Este artigo descreve questões comuns com o desenvolvimento de consultas de Stream Analytics e como os resolver.

## <a name="query-is-not-producing-expected-output"></a>A consulta não está a produzir a produção esperada
1.  Examinar erros testando localmente:
    - No portal Azure, no **separador Consulta,** selecione **Test**. Utilize os dados da amostra descarregados para [testar a consulta](stream-analytics-test-query.md). Examine quaisquer erros e tente corrigi-los.   
    - Também pode [testar a sua consulta local usando](stream-analytics-live-data-local-testing.md) ferramentas Azure Stream Analytics para Estúdio Visual ou Código de Estúdio [Visual](visual-studio-code-local-run-live-input.md). 

2.  Consultas de [depuração passo a passo local usando diagrama](debug-locally-using-job-diagram.md) de trabalho em ferramentas Azure Stream Analytics para Estúdio Visual. O diagrama de trabalho é para mostrar como os dados fluem de fontes de entrada (Event Hub, IoT Hub, etc.) através de múltiplos passos de consulta e, finalmente, saída para pias. Cada passo de consulta é mapeado para um conjunto de resultados temporário definido no script usando a declaração COM. Pode ver os dados, bem como as métricas em cada passo de consulta em cada passo intermédio definido para encontrar a origem do problema.
    ![Resultado da pré-visualização do diagrama de trabalho](./media/debug-locally-using-job-diagram/preview-result.png)

3.  Se utilizar o [**Timestamp By,**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)verifique se os eventos têm carimbos de tempo superiores à hora de início de [trabalho](stream-analytics-out-of-order-and-late-events.md).

4.  Eliminar armadilhas comuns, tais como:
    - Uma cláusula [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) na consulta filtrava todos os eventos, impedindo que qualquer saída fosse gerada.
    - Uma função [**CAST**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) falha, fazendo com que o trabalho falhe. Para evitar falhas de molde suito, use [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) em vez disso.
    - Quando utilizar funções de janela, aguarde toda a duração da janela para ver uma saída a partir da consulta.
    - A marca de tempo para os acontecimentos precede a hora de início do trabalho e, por conseguinte, os acontecimentos estão a ser eliminados.

5.  Certifique-se de que as políticas de ordenação de eventos estão configuradas como esperado. Vá às **Definições** e selecione [**Ordenar eventos**](stream-analytics-out-of-order-and-late-events.md). A política *não* é aplicada quando utiliza o botão **Teste** para testar a consulta. Este resultado é uma diferença entre testar no navegador versus executar o trabalho em produção. 

6. Depuração utilizando registos de auditoria e diagnóstico:
    - Utilize [registos](../azure-resource-manager/resource-group-audit.md)de auditoria e filtre para identificar e depurar erros.
    - Utilize [registos](stream-analytics-job-diagnostic-logs.md) de diagnóstico de trabalho para identificar e depurar erros.

## <a name="job-is-consuming-too-many-streaming-units"></a>O trabalho está a consumir demasiadas Unidades de Streaming
Certifique-se de tirar partido da paralelização no Azure Stream Analytics. Você pode aprender a [escalar com a paralelização](stream-analytics-parallelization.md) de consultas de trabalhos stream analytics, configurando divisórias de entrada e afinando a definição de consulta de análise.

## <a name="debug-queries-progressively"></a>Debug queries progressively (Depurar consultas progressivamente)

No processamento de dados em tempo real, saber como os dados são no meio da consulta pode ser útil. Como as inputs ou passos de um trabalho de Azure Stream Analytics podem ser lidos várias vezes, pode escrever extra SELECT INTO declarações. Ao fazê-lo, os dados intermédios são divulgados e permite-lhe inspecionar a correção dos dados, tal como *as variáveis* de relógio fazem quando depura um programa.

A seguinte consulta de exemplo num trabalho do Azure Stream Analytics tem uma entrada de fluxo, duas informações de referência e uma saída para o Armazenamento de Mesa Azure. A consulta junta dados do centro de eventos e duas bolhas de referência para obter o nome e a informação da categoria:

![Exemplo Stream Analytics SELECT EM consulta](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Note que o trabalho está em execução, mas não estão a ser produzidos eventos na produção. No azulejo **de Monitorização,** mostrado aqui, pode ver que a entrada está a produzir dados, mas não sabe qual o passo do **JOIN** que fez com que todos os eventos fossem abandonados.

![O azulejo de monitorização stream analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

Nesta situação, pode adicionar algumas declarações extra SELECT INTO para "registar" os resultados intermédios do JOIN e os dados lidos a partir da entrada.

Neste exemplo, adicionámos duas novas "saídas temporárias". Podem ser qualquer lavatório que quiser. Aqui usamos o Armazenamento Azure como exemplo:

![Adicionar extra SELECT EM declarações à consulta de Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Em seguida, pode reescrever a consulta assim:

![Reescrita SELECT INTO Stream Analytics consulta](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Agora começa o trabalho outra vez, e deixa-o correr por uns minutos. Em seguida, consulta temp1 e temp2 com Visual Studio Cloud Explorer para produzir as seguintes tabelas:

**tabela temporária1**
SELECT INTO temp1 tabela Stream Análise![consulta](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**tabela temporária2**
SELECT INTO temp2 tabela Stream Análise![consulta](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Como pode ver, os temp1 e os temp2 têm dados, e a coluna de nomes é povoada corretamente em temperatura2. No entanto, como ainda não há dados na produção, algo está errado:

![SELECIONE EM tabela de saída1 sem consulta de fluxo de dados Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Ao provar os dados, pode ter quase a certeza de que o problema está com o segundo JOIN. Pode descarregar os dados de referência da bolha e dar uma olhada:

![SELECIONE EM consulta de fluxo de mesa ref Análise](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Como pode ver, o formato do GUID nestes dados de referência é diferente do formato da coluna [a partir] em temp2. É por isso que os dados não chegaram à produção1 como esperado.

Pode corrigir o formato de dados, carregá-lo para a bolha de referência e tentar novamente:

![SELECIONE EM Tabela temporária Stream Analytics consulta](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Desta vez, os dados da saída são formatados e povoados como esperado.

![SELECIONE EM Consulta de stream de tabela final](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Obter ajuda

Para mais assistência, experimente o nosso [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

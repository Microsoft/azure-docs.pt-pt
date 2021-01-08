---
title: Introdução às funções de janela do Azure Stream Analytics
description: Este artigo descreve quatro funções de janela (tumbling, hopping, sliding, session) que são usadas em trabalhos Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: feea44741fbc2c81d781f0bba12b280abdb9f68a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020253"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introdução às funções de janela do Stream Analytics

Em cenários de streaming de tempo, realizar operações nos dados contidos nas janelas temporais é um padrão comum. O Stream Analytics tem suporte nativo para funções de janela, permitindo aos desenvolvedores autoriar trabalhos complexos de processamento de fluxo com o mínimo de esforço.

Existem cinco tipos de janelas temporais à escolha: [**Tumbling,**](/stream-analytics-query/tumbling-window-azure-stream-analytics) [**Hopping,**](/stream-analytics-query/hopping-window-azure-stream-analytics) [**Sliding,**](/stream-analytics-query/sliding-window-azure-stream-analytics) [**Session**](/stream-analytics-query/session-window-azure-stream-analytics)e Windows [**Snapshot.**](/stream-analytics-query/snapshot-window-azure-stream-analytics)  Utilize as funções de janela na cláusula [**GRUPO BY**](/stream-analytics-query/group-by-azure-stream-analytics) da sintaxe de consulta nos seus trabalhos stream Analytics. Também pode agregar eventos em várias janelas utilizando a [função **Windows().**](/stream-analytics-query/windows-azure-stream-analytics)

Todas as operações [de vitral](/stream-analytics-query/windowing-azure-stream-analytics) resultam no **final** da janela. Note que quando iniciar um trabalho de análise de fluxo, pode especificar a *hora de início de saída do Trabalho* e o sistema irá automaticamente recolher eventos anteriores nos fluxos de entrada para a saída da primeira janela no momento especificado; por exemplo, quando começar com a opção *Now,* começará a emitir dados imediatamente. A saída da janela será um único evento com base na função agregada utilizada. O evento de saída terá o carimbo de tempo da extremidade da janela e todas as funções de janela são definidas com um comprimento fixo. 

![Conceitos de funções de janela stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Janela caindo
As funções de janela de tropeçar são usadas para segmentar um fluxo de dados em segmentos de tempo distintos e executar uma função contra eles, como o exemplo abaixo. Os principais diferenciadores de uma Janela em cascata são que se repetem, não se sobrepõem e um evento não pode pertencer a mais do que uma janela em cascata.

![Janela de caindo stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Janela hopping
As funções de janela de salto avançam um período fixo no tempo. Pode ser fácil pensar neles como janelas caindo que podem sobrepor-se e ser emitidas mais frequentemente do que o tamanho da janela. Os eventos podem pertencer a mais de um conjunto de resultados da janela Hopping. Para fazer uma janela Hopping da mesma forma que uma janela de tropeçar, especifique o tamanho do lúpulo para ser o mesmo que o tamanho da janela. 

![Janela de salto stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Janela deslizante

Janelas deslizantes, ao contrário das janelas Tumbling ou Hopping, os eventos de saída apenas para pontos no tempo em que o conteúdo da janela realmente muda. Por outras palavras, quando um evento entra ou sai da janela. Então, cada janela tem pelo menos um evento. Semelhante às janelas Hopping, os eventos podem pertencer a mais de uma janela deslizante.

![Janela de deslizamento stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Janela de sessão
A janela de sessão funciona eventos de grupo que chegam em momentos semelhantes, filtrando períodos de tempo onde não há dados. Tem três parâmetros principais: tempo limite, duração máxima e chave de criação de partições (opcional).

![Janela de sessão stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Uma janela de sessão começa quando o primeiro evento ocorre. Se outro evento ocorrer dentro do tempo limite especificado do último evento ingerido, então a janela estende-se para incluir o novo evento. Caso contrário, se não ocorrerem eventos dentro do intervalo, a janela é fechada no intervalo.

Se os eventos continuarem a ocorrer dentro do intervalo especificado, a janela de sessão continuará a prolongar-se até que a duração máxima seja alcançada. Os intervalos máximos de verificação da duração devem ser do mesmo tamanho da duração máxima especificada. Por exemplo, se a duração máxima for de 10, então o controlo se a janela exceder a duração máxima acontecerá a t = 0, 10, 20, 30, etc.

Quando uma chave de partição é fornecida, os eventos são agrupados pela janela de chave e sessão é aplicada a cada grupo de forma independente. Esta partição é útil para casos em que você precisa de diferentes janelas de sessão para diferentes utilizadores ou dispositivos.

## <a name="snapshot-window"></a>Janela snapshot

As janelas instantâneas agrupam eventos que têm a mesma hora de tensão. Ao contrário de outros tipos de janela, que requerem uma função de janela específica (como [SessionWindow()](/stream-analytics-query/session-window-azure-stream-analytics), pode aplicar uma janela instantânea adicionando System.Timestamp() à cláusula GROUP BY.

![Janela snapshot stream Analytics](media/stream-analytics-window-functions/snapshot.png)

## <a name="next-steps"></a>Próximos passos
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)

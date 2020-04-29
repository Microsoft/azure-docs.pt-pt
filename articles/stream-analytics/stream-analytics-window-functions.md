---
title: Introdução às funções de janela sintetizar o Azure Stream Analytics
description: Este artigo descreve quatro funções de janela (tropeçar, saltar, deslizar, sessão) que são usadas em trabalhos de Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 872eec62e7a629d76533aa6c9906cbdb64c32236
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745557"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introdução às funções de janela stream Analytics

Em cenários de streaming de tempo, realizar operações nos dados contidos nas janelas temporais é um padrão comum. O Stream Analytics tem suporte nativo para funções de janela, permitindo aos desenvolvedores autores de trabalhos complexos de processamento de fluxo com o mínimo de esforço.

Há quatro tipos de janelas temporais para escolher: [**Tumbling,**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) [**Hopping,**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics) [**Sliding**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)e [**Session**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) windows.  Utiliza as funções da janela na cláusula [**GROUP BY**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) da sintaxe de consulta nos seus trabalhos de Stream Analytics. Também pode agregar eventos através de várias janelas utilizando a [função **Windows()** ](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics)

Todos os resultados de saída de operações [de janela](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) no **final** da janela. A saída da janela será um único evento com base na função agregada utilizada. O evento de saída terá o carimbo de tempo da extremidade da janela e todas as funções da janela são definidas com um comprimento fixo. 

![Conceitos de funções de janela stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Janela caindo
Funções de janela sumbling são usadas para segmentar um fluxo de dados em segmentos de tempo distintos e executar uma função contra eles, como o exemplo abaixo. Os principais diferenciadores de uma Janela em cascata são que se repetem, não se sobrepõem e um evento não pode pertencer a mais do que uma janela em cascata.

![Janela de tumbling Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Janela de pular
As funções de janela de salto avançam um período fixo no tempo. Pode ser mais fácil pensar que são Janelas em cascata que podem sobrepor-se, de forma a que os eventos possam pertencer a mais do que um conjunto de resultados de Janela de salto. Para fazer uma janela hopping igual a uma janela tumbling, especifique o tamanho do lúpulo para ser o mesmo que o tamanho da janela. 

![Janela de hopping Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Janela deslizante
Funções de janela deslizantes, ao contrário das janelas Tumbling ou Hopping, produzem uma saída **apenas** quando ocorre um evento. Cada janela terá pelo menos um evento e a janela avança continuamente por um ε (epsilon). Tal como as janelas de salto, os eventos podem pertencer a mais do que uma janela deslizante.

![Janela de deslizamento de Análise stream](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Janela de sessão
As funções da janela da sessão são eventos de grupo que chegam em momentos semelhantes, filtrando períodos de tempo onde não há dados. Tem três parâmetros principais: tempo limite, duração máxima e chave de criação de partições (opcional).

![Janela de sessão de Análise stream](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Uma janela de sessão começa quando o primeiro evento ocorre. Se outro evento ocorrer dentro do tempo limite especificado do último evento ingerido, então a janela estende-se para incluir o novo evento. Caso contrário, se não ocorrerem eventos dentro do intervalo, a janela está fechada no intervalo.

Se os eventos continuarem a ocorrer dentro do prazo de tempo especificado, a janela da sessão continuará a estender-se até que a duração máxima seja alcançada. Os intervalos de verificação da duração máxima devem ser do mesmo tamanho que a duração máxima especificada. Por exemplo, se a duração máxima for de 10, então os controlos se a janela exceder a duração máxima ocorrerão em t = 0, 10, 20, 30, etc.

Quando uma chave de partição é fornecida, os eventos são agrupados pela chave e a janela da sessão é aplicada a cada grupo de forma independente. Esta partilha é útil para casos em que necessita de diferentes janelas de sessão para diferentes utilizadores ou dispositivos.


## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


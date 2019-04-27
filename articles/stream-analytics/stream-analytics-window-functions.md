---
title: Introdução às funções do Azure Stream Analytics windowing
description: Este artigo descreve quatro modos (em cascata, saltos, deslizante, sessão) que são usados em tarefas do Azure Stream Analytics.
services: stream-analytics
author: lingliw
ms.author: v-lingwu
manager: digimobile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/21/19
ms.openlocfilehash: 04c19e7e51777db4c59bfab3d5a8a7598560556a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844769"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introdução às funções de janelas do Stream Analytics
Em cenários de transmissão em fluxo de tempo, a realização de operações nos dados contidos no windows temporais é um padrão comum. Stream Analytics tem suporte nativo para funções de janelas, permitindo que os desenvolvedores às tarefas de processamento de fluxo complexos de autor com um mínimo de esforço.

Existem quatro tipos de temporal windows à sua escolha: [**Em cascata**](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics), [ **saltos**](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics), [ **deslizante**](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics), e [ **sessão**  ](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) windows.  Utilizar as funções de janela no [ **Agrupar por** ](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) cláusula da sintaxe de consulta nas suas tarefas do Stream Analytics.

Todas as a [windowing](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics) operações produz resultados no **final** da janela. A saída da janela será o único evento com base na função de agregação utilizada. O evento de saída terá o carimbo de hora de fim da janela e todas as funções de janela são definidas com um comprimento fixo. 

![Conceitos de funções de janela do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Janela em cascata
Em cascata janela funções são usadas para segmentar um fluxo de dados em segmentos distintos de tempo e realizar uma função contra eles, como o exemplo abaixo. Os principais diferenciais de uma janela em cascata são que eles repetir, fazerem não se sobrepõem e um evento não podem pertencer a mais do que uma janela de em cascata.

![Janela em cascata do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Janela de salto
Saltos salto de funções de janela para a frente no tempo por um período fixo. Pode ser fácil de considerá-los como as janelas em cascata que podem sobrepor-se, pelo que os eventos podem pertencer a mais do que um conjunto de resultados de janela Hopping. Para criar uma janela de Hopping os mesmos como uma janela em cascata, especifique o tamanho de salto para ser o mesmo que o tamanho da janela. 

![Janela de salto do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Janela deslizante
Funções de janela deslizante, ao contrário em cascata ou saltos windows, produzem um resultado **apenas** quando ocorre um evento. Cada janela terá pelo menos um evento e a janela continuamente avança por um € (épsilon). Como saltos windows, os eventos podem pertencer a mais do que uma janela deslizante.

![Janela deslizante do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Janela de sessão
As funções de janela de sessão agrupar os eventos que chegam em alturas semelhante, filtram de períodos de tempo em que não existe nenhum dado. Ele tem três parâmetros de principais: tempo limite, duração máxima e a chave de particionamento (opcional).

![Janela de sessão do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Uma janela de sessão começa quando o primeiro evento ocorre. Se outro evento ocorre dentro do tempo limite especificado a partir do último evento ingerido, a janela se estende a incluir o novo evento. Caso contrário, se não existem eventos ocorrem no tempo limite, a janela é fechada no tempo limite.

Se os eventos manterem ocorra de acordo com o tempo limite especificado, manter alargar a janela de sessão até que a duração máxima é atingida. Os intervalos de verificação de duração máxima estão definidos para ser o mesmo tamanho que a duração máxima especificada. Por exemplo, se a duração máxima é de 10, em seguida, as verificações de se a janela de exceder o máximo duração irá acontecer em t = 0, 10, 20, 30, etc.

Quando uma chave de partição é fornecida, os eventos são agrupados em conjunto pela chave e a janela da sessão é aplicada a cada grupo de forma independente. Essa divisão é útil para casos em que precisa windows de sessão diferente para diferentes utilizadores ou dispositivos.

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Update_Description: update meta properties -->
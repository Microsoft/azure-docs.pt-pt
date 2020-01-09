---
title: Introdução às funções de janelas de Azure Stream Analytics
description: Este artigo descreve quatro funções de janela (em cascata, salto, deslizante, sessão) que são usadas em trabalhos de Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: a0547243ddf114d5c9f7034f182a5e76d8c3e016
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369427"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introdução às funções de janelas de Stream Analytics

Em cenários de streaming de tempo, a execução de operações nos dados contidos em janelas temporais é um padrão comum. Stream Analytics tem suporte nativo para funções de janelas, permitindo que os desenvolvedores às tarefas de processamento de fluxo complexos de autor com um mínimo de esforço.

Há quatro tipos de janelas temporais para escolher: [**em cascata**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), [**salto**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), [**deslizante**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)e janelas de [**sessão**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) .  Você usa as funções de janela na cláusula [**Group by**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) da sintaxe de consulta em seus trabalhos de Stream Analytics. Você também pode agregar eventos em várias janelas usando a [função **Windows ()** ](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics).

Todas as operações de [janela](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) de saída resultam no **final** da janela. A saída da janela será um evento único com base na função de agregação usada. O evento de saída terá o carimbo de data/hora do final da janela e todas as funções de janela serão definidas com um comprimento fixo. 

![Conceitos de funções de janela do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Janela em cascata
As funções de janela em cascata são usadas para segmentar um fluxo de dados em segmentos de tempo distintos e executar uma função em relação a eles, como o exemplo a seguir. Os principais diferenciadores de uma Janela em cascata são que se repetem, não se sobrepõem e um evento não pode pertencer a mais do que uma janela em cascata.

![Janela do Stream Analytics em cascata](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Janela de salto
As funções de janela de salto avançam um período fixo no tempo. Pode ser mais fácil pensar que são Janelas em cascata que podem sobrepor-se, de forma a que os eventos possam pertencer a mais do que um conjunto de resultados de Janela de salto. Para fazer uma janela de salto igual a uma janela em cascata, especifique o tamanho do salto como o mesmo que o tamanho da janela. 

![Stream Analytics janela de salto](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Janela deslizante
As funções de janela deslizantes, ao contrário das janelas em cascata ou de salto, produzem uma saída **somente** quando ocorre um evento. Cada janela terá pelo menos um evento e a janela avança continuamente por um € (épsilon). Tal como as janelas de salto, os eventos podem pertencer a mais do que uma janela deslizante.

![Stream Analytics janela deslizante](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Janela de sessão
As funções da janela de sessões agrupam eventos que chegam em momentos semelhantes, filtrando períodos de tempo em que não há dados. Tem três parâmetros principais: tempo limite, duração máxima e chave de criação de partições (opcional).

![Janela sessão Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Uma janela de sessão começa quando o primeiro evento ocorre. Se outro evento ocorrer dentro do tempo limite especificado do último evento ingerido, a janela se estenderá para incluir o novo evento. Caso contrário, se nenhum evento ocorrer dentro do tempo limite, a janela será fechada no tempo limite.

Se os eventos continuarem ocorrendo dentro do tempo limite especificado, a janela de sessão continuará sendo estendida até que a duração máxima seja atingida. Os intervalos de verificação de duração máxima são definidos para serem do mesmo tamanho que a duração máxima especificada. Por exemplo, se a duração máxima for 10, o verificará se a janela exceder a duração máxima ocorrerá em t = 0, 10, 20, 30, etc.

Quando uma chave de partição é fornecida, os eventos são agrupados em conjunto pela chave e a janela de sessão é aplicada a cada grupo de forma independente. Esse particionamento é útil para casos em que você precisa de janelas de sessão diferentes para usuários ou dispositivos diferentes.


## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


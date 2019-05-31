---
title: Como monitorizar e reduzir a limitação no Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como monitorizar, diagnosticar e mitigar problemas de desempenho que causam a latência e limitação no Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 6151af941b89198812f2a33a522b30ff0a8796a0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242064"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitorizar e mitigar limitação para reduzir a latência no Azure Time Series Insights

Quando a quantidade de dados de entrada excede a configuração do seu ambiente, poderá experienciar latência ou limitação no Azure Time Series Insights.

Pode evitar a latência e limitação ao configurar corretamente o seu ambiente para a quantidade de dados que pretende analisar.

Que é mais provável que a experiência de latência e limitação quando:

- Adicione uma origem de evento que contém os dados antigos que podem exceder a taxa de entrada alocado (Time Series Insights precisará recuperar-se).
- Adicione mais origens de eventos a um ambiente, resultando num pico de eventos adicionais (o que poderá exceder a capacidade do seu ambiente).
- Envie grandes quantidades de eventos históricos para uma origem de evento, resultando num atraso (Time Series Insights precisará recuperar-se).
- Junte-se os dados de referência com telemetria, resultando em tamanho maior de evento.  De uma perspectiva de limitação, um pacote de dados ingressed com um tamanho de pacote de 32 KB é tratado como 32 eventos, cada 1 KB em tamanho normal. O tamanho máximo do evento permitido é de 32 KB; pacotes de dados superiores a 32 KB são truncados.

## <a name="video"></a>Vídeo

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Saiba mais sobre o comportamento de entrada de dados do Time Series Insights e como planejá-la.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorizar a latência e limitação com alertas

Alertas podem ajudar a ajudar a diagnosticar e mitigar problemas de latência provocados pelo seu ambiente.

1. No portal do Azure, clique em **métricas**.

   [![Métricas](media/environment-mitigate-latency/add-metrics.png)](media/environment-mitigate-latency/add-metrics.png#lightbox)

1. Clique em **Adicionar alerta de métrica**.  

   [![Adicionar alerta de métrica](media/environment-mitigate-latency/add-metric-alert.png)](media/environment-mitigate-latency/add-metric-alert.png#lightbox)

A partir daí, pode configurar alertas com as métricas seguintes:

|Métrica  |Descrição  |
|---------|---------|
|**Bytes recebidos de entrada**     | Contagem de bytes não processados ler a partir de origens de eventos. Contagem de não processada normalmente inclui o nome da propriedade e o valor.  |  
|**Entrada recebeu mensagens inválidas**     | Contagem de mensagens inválidas de leitura de todas as origens de eventos de Hubs de eventos do Azure ou o IoT Hub do Azure.      |
|**Entrada recebeu mensagens**   | Contagem de mensagens de ler a partir de origens de eventos de todos os Hubs de eventos ou os Hubs IoT.        |
|**Entrada armazenados Bytes**     | Total de tamanho de eventos armazenados e disponíveis para consulta. Tamanho é calculado apenas no valor da propriedade.        |
|**Entrada armazenados eventos**     |   Contagem de eventos plana, armazenados e estão disponíveis para consulta.      |
|**Intervalo de tempo de mensagem recebida de entrada**    |  Diferença em segundos entre a hora em que a mensagem é colocado em fila de eventos de origem e o tempo que é processada na entrada.      |
|**Entrada recebidos desfasamento de contagem de mensagens**    |  Diferença entre o número de sequência de mensagem do último colocados em fila de eventos da origem de número de partição e a sequência de mensagem a ser processado na entrada.      |

![Latência](media/environment-mitigate-latency/latency.png)

* Se estiver a ser limitada, verá um valor para o *desfasamento de tempo de mensagem recebida entrada*, informando-o de quantos segundos por trás do TSI é, desde a hora real, a mensagem chega a origem do evento (excluindo o tempo de indexação de appx. 30 a 60 segundos).  *Atraso de contagem de mensagens recebidas da entrada* também deve ter um valor, permitindo-lhe determinar quantas mensagens atrás de.  A maneira mais fácil de se envolva é aumentar a capacidade do seu ambiente para um tamanho que irá permitir-lhe ultrapassar a diferença.  

  Por exemplo, se tiver um ambiente único de unidade de S1 e vê que existe um desfasamento de 5,000,000 mensagem, poderia aumentar o tamanho do seu ambiente para seis unidades, para, em torno de um dia para se envolva.  Poderia aumentar ainda mais catch cópia de segurança mais rapidamente. O período de catch-up é uma ocorrência comum quando são aprovisionados inicialmente um ambiente, especialmente quando ligar a uma origem de evento já contém eventos no mesmo ou quando em massa muitas de carregamento de dados históricos.

* Outra técnica é definir um **eventos de entrada armazenados** alerta > = um limiar ligeiramente abaixo da capacidade do seu ambiente total durante um período de duas horas.  Este alerta pode ajudá-lo a compreender se é constantemente atingiu o limite, o que indica uma elevada probabilidade de latência. 

  Por exemplo, se tiver três unidades de S1 aprovisionadas (ou eventos de 2100 por capacidade de minuto de entrada), pode definir uma **eventos de entrada armazenados** alerta de > = 1900 eventos durante 2 horas. Se forem constantemente que excedem este limite e por isso, acionar o alerta, provavelmente em-aprovisionadas.  

* Se suspeitar que está a ser limitada, pode comparar o seu **mensagens recebidas de entrada** com seu evento, origem do egressed mensagens.  Se a entrada para o Hub de eventos é maior do que seus **mensagens recebidas de entrada**, o Time Series Insights são provavelmente a ser limitado.

## <a name="improving-performance"></a>Melhorando o desempenho

Para reduzir a limitação ou a ter latência, a melhor forma para corrigi-lo é aumentar a capacidade do seu ambiente.

Pode evitar a latência e limitação ao configurar corretamente o seu ambiente para a quantidade de dados que pretende analisar. Para obter mais informações sobre como adicionar capacidade ao seu ambiente, consulte [dimensionar o seu ambiente](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter os passos de resolução de problemas adicionais, [diagnosticar e resolver problemas no seu ambiente do Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).

- Para obter mais ajuda, iniciar uma conversa sobre o [fórum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) ou [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Também pode contactar [suporte do Azure](https://azure.microsoft.com/support/options/) para opções de suporte assistido.

---
title: Como monitorizar e reduzir a aceleração - Azure Time Series Insights [ Microsoft Docs
description: Aprenda a monitorizar, diagnosticar e mitigar problemas de desempenho que causam latência e estrangulamento em Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 245a0b18187ff1c1b226e94b03374f2c071e51c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314832"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitore e mitigate a aceleração para reduzir a latência em Azure Time Series Insights

Quando a quantidade de dados de entrada exceder a configuração do seu ambiente, poderá experimentar latência ou estrangulamento em Insights da Série Temporal Azure.

Pode evitar a latência e o estrangulamento configurando corretamente o seu ambiente para a quantidade de dados que pretende analisar.

É mais provável que experimente latência e estrangulamento quando:

- Adicione uma fonte de evento que contenha dados antigos que possam exceder a sua taxa de ingresso atribuída (Time Series Insights terá de recuperar).
- Adicione mais fontes de eventos a um ambiente, resultando num pico de eventos adicionais (que podem exceder a capacidade do seu ambiente).
- Empurre grandes quantidades de eventos históricos para uma fonte de evento, resultando num atraso (Time Series Insights terá de recuperar).
- Junte dados de referência com telemetria, resultando em maior tamanho de evento. De uma perspetiva de estrangulamento, um pacote de dados ingressado com um pacote de 32 KB é tratado como 32 eventos, cada um do tamanho 1 KB. O tamanho máximo permitido do evento é de 32 KB; os pacotes de dados superiores a 32 KB são truncados.

## <a name="video"></a>Vídeo

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Saiba mais sobre o comportamento de ingresso de dados da Time Series Insights e como planeá-lo.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorizar a latência e a pulsação com alertas

Os alertas podem ajudá-lo a diagnosticar e mitigar problemas de latência que ocorrem no seu ambiente.

1. No portal Azure, selecione o seu ambiente Time Series Insights. Em seguida, selecione **Alertas**.

   [![Adicione um alerta ao seu ambiente time series insights](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Selecione **+ Nova regra de alerta**. Em seguida, será apresentado o painel de **regras Create.** **Selecione Adicionar** sob **CONDIÇÃO**.

   [![Adicione painel de alerta](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Em seguida, configure as condições exatas para a lógica do sinal.

   [![Configurar lógica de sinal](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   A partir daí, pode configurar alertas utilizando algumas das seguintes condições:

   |Métrica  |Descrição  |
   |---------|---------|
   |**Ingress recebido bytes**     | Contagem de bytes crus lidos a partir de fontes de eventos. A contagem bruta geralmente inclui o nome e o valor da propriedade.  |  
   |**Ingress recebeu mensagens inválidas**     | Contagem de mensagens inválidas lidas de todos os Hubs de Eventos Azure ou fontes de eventos Azure IoT Hub.      |
   |**Mensagens Recebidas de Ingress**   | Contagem de mensagens lidas de todos os Hubs de Eventos ou fontes de eventos ioT Hubs.        |
   |**Ingress Stored Bytes**     | Tamanho total dos eventos armazenados e disponíveis para consulta. O tamanho é calculado apenas sobre o valor da propriedade.        |
   |**Eventos Armazenados de Ingress**    |   Contagem de eventos achatados armazenados e disponíveis para consulta.      |
   |**Ingress recebeu o tempo de mensagem lag**   |  Diferença de segundos entre o tempo em que a mensagem é gravada na fonte do evento e o tempo que é processado em Ingress.      |
   |**Ingress Recebeu Contagem de Mensagens Lag**   |  Diferença entre o número de sequência da última mensagem enqueuada na partição da fonte do evento e o número de sequência da mensagem a ser processada em Ingress.      |

   Selecione **Done** (Concluído).

1. Depois de configurar a lógica de sinal desejada, reveja visualmente a regra de alerta escolhida.

   [![Vista de latência e gráfico](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Gestão de estrangulamento e ingresso

* Se estiver a ser estrangulado, será registado um valor para o *Tempo de Mensagem Recebida* ingresso, informando-o sobre quantos segundos atrás do seu ambiente TIme Series Insights estão a partir do momento em que a mensagem atinge a fonte do evento (excluindo o tempo de indexação do appx. 30-60 segundos).  

  *Ingress Received Message Count Lag* também deve ter um valor, permitindo-lhe determinar quantas mensagens estão por trás de si.  A maneira mais fácil de ser apanhado é aumentar a capacidade do seu ambiente para um tamanho que lhe permitirá superar a diferença.  

  Por exemplo, se o seu ambiente S1 estiver demonstrando um atraso de 5.000.000 mensagens, pode aumentar o tamanho do seu ambiente para seis unidades durante cerca de um dia para ser apanhado.  Pode aumentar ainda mais para recuperar mais rapidamente. O período de recuperação é uma ocorrência comum quando inicialmente se aprovisiona um ambiente, especialmente quando o liga a uma fonte de evento que já tem eventos ou quando carrega a granel muitos dados históricos.

* Outra técnica é definir um alerta **de Eventos Armazenados Ingress** >= um limiar ligeiramente inferior à sua capacidade total de ambiente por um período de 2 horas.  Este alerta pode ajudá-lo a entender se está constantemente na capacidade, o que indica uma alta probabilidade de latência. 

  Por exemplo, se tiver três unidades S1 aprovisionadas (ou 2100 eventos por capacidade de entrada de minutos), pode definir um alerta de **Eventos Armazenados ingressos** para >= 1900 eventos durante 2 horas. Se estiver constantemente a exceder este limiar e, portanto, desencadeando o seu alerta, é provável que esteja sub-provisionado.  

* Se suspeitar que está a ser estrangulado, pode comparar as suas **Mensagens Recebidas** ingressos com as mensagens de origem do evento.  Se entrar no seu Centro de Eventos for maior do que as suas **Mensagens Recebidas**de Ingress, é provável que os seus Insights da Série De Tempo estejam a ser estrangulados.

## <a name="improving-performance"></a>Melhorar o desempenho

Para reduzir a estrangulamento ou a latência, a melhor maneira de corrigi-la é aumentar a capacidade do seu ambiente.

Pode evitar a latência e o estrangulamento configurando corretamente o seu ambiente para a quantidade de dados que pretende analisar. Para obter mais informações sobre como adicionar capacidade ao seu ambiente, leia [Escala do seu ambiente.](time-series-insights-how-to-scale-your-environment.md)

## <a name="next-steps"></a>Passos seguintes

- Leia sobre [diagnosticar e resolver problemas no seu ambiente Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).

- Aprenda [a escalar o seu ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
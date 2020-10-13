---
title: Como monitorizar e reduzir o estrangulamento - Azure Time Series Insights Microsoft Docs
description: Aprenda a monitorizar, diagnosticar e mitigar problemas de desempenho que causam latência e estrangulamento em Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: e89189b22b144d9e92ee8315bc6fd9aabe699eec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91531654"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights-gen1"></a>Monitorize e mitige o estrangulamento para reduzir a latência em Azure Time Series Insights Gen1

> [!CAUTION]
> Este é um artigo da Gen1.

Quando a quantidade de dados de entrada exceder a configuração do seu ambiente, poderá experimentar latência ou estrangulamento em Azure Time Series Insights.

Pode evitar a latência e a aceleração configurando corretamente o seu ambiente para a quantidade de dados que pretende analisar.

É mais provável que experimente latência e estrangulamento quando:

- Adicione uma fonte de evento que contenha dados antigos que possam exceder a sua taxa de entrada atribuída (Azure Time Series Insights terá de recuperar).
- Adicione mais fontes de eventos a um ambiente, resultando num pico de eventos adicionais (que podem exceder a capacidade do seu ambiente).
- Empurre grandes quantidades de eventos históricos para uma fonte de evento, resultando num lag (Azure Time Series Insights terá de recuperar).
- Junte os dados de referência com a telemetria, resultando em maior tamanho do evento. O tamanho máximo permitido do pacote é de 32 KB; pacotes de dados maiores do que 32 KB são truncados.

## <a name="video"></a>Vídeo

### <a name="learn-about-azure-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Saiba mais sobre o comportamento da Azure Time Series Insights e como planeá-lo.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorize a latência e a aceleração com alertas

Os alertas podem ajudá-lo a diagnosticar e mitigar problemas de latência que ocorrem no seu ambiente.

1. No portal Azure, selecione o ambiente Azure Time Series Insights. Em seguida, selecione **Alertas**.

   [![Adicione um alerta ao seu ambiente Azure Time Series Insights](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Selecione **+ Nova regra de alerta**. O painel **de regras Criar** será então apresentado. **Selecione Adicionar** em **CONDIÇÕES**.

   [![Adicione painel de alerta](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Em seguida, configuure as condições exatas para a lógica do sinal.

   [![Configurar lógica de sinal](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   A partir daí, pode configurar alertas utilizando algumas das seguintes condições:

   |Métrica  |Descrição  |
   |---------|---------|
   |**Ingress Recebido Bytes**     | Contagem de bytes crus lidos de fontes de evento. A contagem bruta geralmente inclui o nome e o valor da propriedade.  |  
   |**Ingress recebeu mensagens inválidas**     | Contagem de mensagens inválidas lidas de todos os hubs de eventos Azure ou fontes de eventos Azure IoT Hub.      |
   |**Mensagens Recebidas Ingress**   | Contagem de mensagens lidas de todos os Centros de Eventos ou fontes de eventos IoT Hubs.        |
   |**Ingress Stored Bytes**     | Tamanho total dos eventos armazenados e disponíveis para consulta. O tamanho é calculado apenas sobre o valor da propriedade.        |
   |**Eventos Armazenados ingress**    |   Contagem de eventos achatados armazenados e disponíveis para consulta.      |
   |**Ingress recebeu atraso de tempo da mensagem**   |  Diferença de segundos entre o tempo em que a mensagem é encosa na fonte do evento e o tempo que é processado em Ingress.      |
   |**Ingress Recebeu Mensagem Contagem DeSfasamento**   |  Diferença entre o número de sequência da última mensagem encosa na partição de fonte de evento e o número de sequência de mensagem a ser processada em Ingress.      |

   Selecione **Concluído**.

1. Depois de configurar a lógica de sinal desejada, reveja a regra de alerta escolhida visualmente.

   [![Vista de latência e gráfico](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Gestão de estrangulamento e de entrada

- Se estiver a ser estrangulado, será registado um valor para o *Ingress Received Message Time Lag* informando-o sobre quantos segundos atrás do ambiente Azure Time Series Insights é a partir do momento em que a mensagem chega à fonte do evento (excluindo o tempo de indexação do appx. 30-60 segundos).  

  *Ingress Received Message Count Lag* também deve ter um valor, permitindo-lhe determinar quantas mensagens estão atrás de si.  A maneira mais fácil de ser apanhado é aumentar a capacidade do seu ambiente para um tamanho que lhe permitirá superar a diferença.  

  Por exemplo, se o seu ambiente S1 estiver a demonstrar um atraso de 5.000.000 mensagens, poderá aumentar o tamanho do seu ambiente para seis unidades durante cerca de um dia para ser apanhado.  Pode aumentar ainda mais para recuperar mais rápido. O período de recuperação é uma ocorrência comum quando inicialmente fornece um ambiente, especialmente quando o conecta a uma fonte de eventos que já tem eventos nele ou quando carrega muitos dados históricos.

- Outra técnica é definir um alerta **de Eventos Armazenados de Ingress** >= um limiar ligeiramente abaixo da capacidade total do ambiente por um período de 2 horas.  Este alerta pode ajudá-lo a entender se está constantemente na capacidade, o que indica uma alta probabilidade de latência.

  Por exemplo, se tiver três unidades S1 a formos (ou 2100 eventos por minuto de capacidade de entrada), pode definir um alerta **de Eventos Armazenados ingress** para >= 1900 eventos durante 2 horas. Se estiver constantemente a exceder este limiar e, portanto, desencadeando o seu alerta, é provável que esteja sub-a provisionado.  

- Se suspeitar que está a ser estrangulado, pode comparar as suas **Mensagens Recebidas de Entrada** com as mensagens emitidas pela fonte do evento.  Se a entrada no seu Centro de Eventos for maior do que as **mensagens recebidas de Ingress,** as suas Inzure Time Series Insights estão provavelmente a ser estranguladas.

## <a name="improving-performance"></a>Melhorar o desempenho

Para reduzir a aceleração ou a gravidez, a melhor maneira de corrigi-la é aumentar a capacidade do seu ambiente.

Pode evitar a latência e a aceleração configurando corretamente o seu ambiente para a quantidade de dados que pretende analisar. Para obter mais informações sobre como adicionar capacidade ao seu ambiente, leia [Escalar o seu ambiente](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Passos seguintes

- Leia sobre [diagnosticar e resolver problemas no ambiente Azure Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).

- Saiba [como escalar o ambiente Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

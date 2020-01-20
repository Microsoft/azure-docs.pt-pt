---
title: Como monitorar e reduzir a limitação-Azure Time Series Insights | Microsoft Docs
description: Saiba como monitorar, diagnosticar e atenuar problemas de desempenho que causam latência e limitação no Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: b301bc6c1674cad26288556957ba6214df74f18d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278678"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitorar e mitigar a limitação para reduzir a latência no Azure Time Series Insights

Quando a quantidade de dados de entrada excede a configuração do seu ambiente, você pode enfrentar latência ou limitação em Azure Time Series Insights.

Você pode evitar a latência e a limitação configurando corretamente seu ambiente para a quantidade de dados que deseja analisar.

É mais provável que haja latência e limitação quando você:

- Adicione uma origem de evento que contenha dados antigos que possam exceder sua taxa de entrada alocada (Time Series Insights precisará ser atualizada).
- Adicione mais fontes de eventos a um ambiente, resultando em um pico de eventos adicionais (o que pode exceder a capacidade do seu ambiente).
- Envie grandes quantidades de eventos históricos para uma origem de evento, resultando em um atraso (Time Series Insights precisará ser atualizado).
- Unir dados de referência com telemetria, resultando em maior tamanho de evento. Do ponto de vista da limitação, um pacote de dados de entrada com um tamanho de pacote de 32 KB é tratado como 32 eventos, cada um com o tamanho de 1 KB. O tamanho máximo de evento permitido é 32 KB; pacotes de dados maiores que 32 KB são truncados.

## <a name="video"></a>Vídeo

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Saiba mais sobre o comportamento de entrada de dados Time Series Insights e como planejar para ele.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorar a latência e a limitação com alertas

Os alertas podem ajudá-lo a diagnosticar e atenuar problemas de latência que ocorrem em seu ambiente.

1. Na portal do Azure, selecione seu ambiente de Time Series Insights. Em seguida, selecione **alertas**.

   [![adicionar um alerta ao seu ambiente de Time Series Insights](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Selecione **+ Nova regra de alerta**. O painel **criar regra** será exibido. Selecione **Adicionar** em **condição**.

   [![adicionar painel de alerta](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Em seguida, configure as condições exatas para a lógica de sinal.

   [![configurar a lógica de sinal](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   A partir daí, você pode configurar alertas usando algumas das seguintes condições:

   |Métrica  |Descrição  |
   |---------|---------|
   |**Bytes de entrada recebidos**     | Contagem de bytes brutos lidos de origens de evento. A contagem bruta geralmente inclui o nome e o valor da propriedade.  |  
   |**A entrada recebeu mensagens inválidas**     | Contagem de mensagens inválidas lidas de todos os hubs de eventos do Azure ou origens de evento do Hub IoT do Azure.      |
   |**Mensagens de entrada recebidas**   | Contagem de mensagens lidas de todos os hubs de eventos ou origens de eventos de hubs IoT.        |
   |**Bytes de entrada armazenados**     | Tamanho total de eventos armazenados e disponíveis para consulta. O tamanho é calculado somente no valor da propriedade.        |
   |**Eventos de entrada armazenados**     |   Contagem de eventos mesclados armazenados e disponíveis para consulta.      |
   |**Intervalo de tempo de mensagem de entrada recebida**    |  Diferença em segundos entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada.      |
   |**Atraso na contagem de mensagens de entrada recebidas**    |  Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência de mensagens que estão sendo processadas na entrada.      |

   Selecione **Done** (Concluído).

1. Depois de configurar a lógica de sinal desejada, examine visualmente a regra de alerta escolhida.

   [exibição de latência ![e gráfico](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Limitação e gerenciamento de entrada

* Se você estiver sendo limitado, um valor para o *intervalo de tempo de mensagens recebidas de entrada* será registrado informando a você sobre quantos segundos por trás do ambiente de análise de séries temporais são do tempo real em que a mensagem atinge a origem do evento (excluindo o tempo de indexação do Appx. 30-60 segundos).  

  O *retardo de contagem de mensagens recebidas de entrada* também deve ter um valor, permitindo que você determine quantas mensagens atrás você está.  A maneira mais fácil de se familiarizar é aumentar a capacidade do seu ambiente para um tamanho que lhe permitirá superar a diferença.  

  Por exemplo, se o seu ambiente S1 estiver demonstrando o atraso de 5 milhões mensagens, você poderá aumentar o tamanho do seu ambiente para seis unidades por cerca de um dia para ser pego.  Você pode aumentar ainda mais para acompanhar mais rapidamente. O período de atualização é uma ocorrência comum ao provisionar inicialmente um ambiente, especialmente quando você o conecta a uma fonte de eventos que já tem eventos ou quando você carrega em massa muitos dados históricos.

* Outra técnica é definir um alerta de **eventos armazenados de entrada** > = um limite um pouco abaixo da capacidade total do ambiente por um período de 2 horas.  Esse alerta pode ajudá-lo a entender se você está constantemente na capacidade, o que indica uma alta probabilidade de latência. 

  Por exemplo, se você tiver três unidades S1 provisionadas (ou 2100 eventos de entrada por minuto), poderá definir um alerta de **eventos armazenados de entrada** para > = 1900 eventos por 2 horas. Se você estiver constantemente excedendo esse limite e, portanto, disparando o alerta, provavelmente estará subvisionado.  

* Se você suspeitar que está sendo limitado, poderá comparar as **mensagens de entrada recebidas** com as mensagens de saída da origem do evento.  Se a entrada no Hub de eventos for maior do que as **mensagens de entrada recebidas**, suas Time Series insights provavelmente estão sendo limitadas.

## <a name="improving-performance"></a>Melhorar o desempenho

Para reduzir a limitação ou experimentar a latência, a melhor maneira de corrigi-la é aumentar a capacidade do seu ambiente.

Você pode evitar a latência e a limitação configurando corretamente seu ambiente para a quantidade de dados que deseja analisar. Para obter mais informações sobre como adicionar capacidade ao seu ambiente, leia [dimensionar seu ambiente](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Passos seguintes

- Leia sobre como [diagnosticar e resolver problemas em seu ambiente de time Series insights](time-series-insights-diagnose-and-solve-problems.md).

- Saiba [como dimensionar seu ambiente de time Series insights](time-series-insights-how-to-scale-your-environment.md).
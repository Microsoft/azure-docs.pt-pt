---
title: Planeie o seu ambiente GA - Azure Time Series Insights [ Microsoft Docs
description: As melhores práticas para preparar, configurar e implementar o seu ambiente Azure Time Series Insights GA.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 85910ee5467ecc9f4fe3c1a8bc13110b6f218e5c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272717"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planeie o seu ambiente Ga da Série De Tempo Azure Insights

Este artigo descreve como planear o seu ambiente de disponibilidade geral da Série De Tempo Azure Insights (GA) com base na sua taxa de ingresso esperada e nos seus requisitos de retenção de dados.

## <a name="video"></a>Vídeo

Veja este vídeo para saber mais sobre a retenção de **dados em Azure Time Series Insights e como planeá-lo:**<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Melhores práticas

Para começar com o Azure Time Series Insights, é melhor saber quantos dados espera pressionar ao minuto e quanto tempo precisa para armazenar os seus dados.  

Para obter mais informações sobre capacidade e retenção para ambas as Séries De Tempo Insights SKUs, leia os preços da [Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Para planear melhor o seu ambiente Time Series Insights para o sucesso a longo prazo, considere os seguintes atributos:

- [Capacidade de armazenamento](#storage-capacity)
- [Período de retenção de dados](#data-retention)
- [Capacidade ingresso](#ingress-capacity)
- [Moldar os seus eventos](#shape-your-events)
- [Garantindo que tem dados de referência no lugar](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Capacidade de armazenamento

Por padrão, a Time Series Insights retém dados baseados na quantidade de armazenamento que você fornecer (unidades &#215; a quantidade de armazenamento por unidade) e ingresso.

## <a name="data-retention"></a>Retenção de dados

Pode alterar a definição de tempo de **retenção** de Dados no seu ambiente Azure Time Series Insights. Pode permitir até 400 dias de retenção. 

A Azure Time Series Insights tem dois modos:

* Um modo otimiza para os dados mais atualizados. Impõe uma política de **expurgação** de dados antigos, deixando dados recentes disponíveis com a instância. Este modo está ligado, por defeito. 
* O outro otimiza os dados para permanecer abaixo dos limites de retenção configurados. **A pausa** impede que novos dados sejam ingressados quando são selecionados, uma vez que o limite de **armazenamento excedeu**o comportamento .

Pode ajustar a retenção e alternar entre os dois modos na página de configuração do ambiente no portal Azure.

> [!IMPORTANT]
> Pode configurar um máximo de 400 dias de retenção de dados no seu ambiente Azure Time Series Insights GA.

### <a name="configure-data-retention"></a>Configurar a retenção de dados

1. No [portal Azure,](https://portal.azure.com)selecione o seu ambiente Time Series Insights.

1. No painel ambiente **Time Series Insights,** em **Definições,** selecione **configuração de armazenamento**.

1. Na caixa de tempo de retenção de **Dados (em dias),** insira um valor entre 1 e 400.

   [![Configurar a retenção](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Para saber mais sobre como implementar uma política adequada de retenção de dados, leia [Como configurar a retenção.](./time-series-insights-how-to-configure-retention.md)

## <a name="ingress-capacity"></a>Capacidade ingresso

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Planeamento ambiental

A segunda área em que se concentrar para planear o seu ambiente Time Series Insights é a capacidade de ingresso. O armazenamento diário de ingresso e a capacidade do evento são medidos por minuto, em blocos de 1 KB. O tamanho máximo permitido do pacote é de 32 KB. Os pacotes de dados superiores a 32 KB são truncados.

Pode aumentar a capacidade de um S1 ou S2 SKU para 10 unidades num único ambiente. Não se pode migrar de um ambiente S1 para um S2. Não se pode migrar de um ambiente S2 para um S1.

Para a capacidade de ingresso, determine primeiro a entrada total que necessita por mês. Em seguida, determine quais são as suas necessidades por minuto. 

O estrangulamento e a latência desempenham um papel na capacidade por minuto. Se tiver um pico na entrada de dados que dure menos de 24 horas, a Time Series Insights pode "recuperar" a uma taxa de entrada de duas vezes as taxas listadas na tabela anterior.

Por exemplo, se tiver um único S1 SKU, ingressa dados a uma taxa de 720 eventos por minuto, e a taxa de dados aumenta por menos de uma hora a uma taxa de 1.440 eventos ou menos, não há latência visível no seu ambiente. No entanto, se exceder 1.440 eventos por minuto por mais de uma hora, provavelmente sentirá latência em dados visualizados e disponíveis para consulta no seu ambiente.

Pode não saber antecipadamente quantos dados espera pressionar. Neste caso, pode encontrar telemetria de dados para [O Hub Azure IoT](../iot-hub/iot-hub-metrics.md) e Hubs de [Eventos Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) na subscrição do portal Azure. A telemetria pode ajudá-lo a determinar como fornecer o seu ambiente. Utilize o painel **Métricas** no portal Azure para que a respetiva fonte do evento veja a sua telemetria. Se compreender as suas métricas de origem de eventos, pode planear e fornecer mais eficazmente o seu ambiente Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcular requisitos de ingresso

Para calcular os seus requisitos de ingresso:

- Verifique se a sua capacidade de ingresso está acima da sua taxa média por minuto e que o seu ambiente é grande o suficiente para lidar com a sua entrada antecipada equivalente a duas vezes a sua capacidade durante menos de uma hora.

- Se ocorrerem picos de ingresso que durem mais de 1 hora, utilize a taxa de pico como a sua média. Providenciar um ambiente com capacidade para lidar com a taxa de pico.

### <a name="mitigate-throttling-and-latency"></a>Atenuar a estrangulamento e a latência

Para obter informações sobre como evitar estrangulamentos e latências, leia [a latência e](time-series-insights-environment-mitigate-latency.md)a aceleração do Mitigate .

## <a name="shape-your-events"></a>Moldar os seus eventos

É importante garantir que a forma como envia eventos para time series Insights suporta o tamanho do ambiente que você está a fornecer. (Inversamente, você pode mapear o tamanho do ambiente para quantos eventos Time Series Insights lê e o tamanho de cada evento.) Também é importante pensar nos atributos que você pode querer usar para cortar e filtrar quando você consulta os seus dados.

> [!TIP]
> Reveja a documentação de formação jSON em [Eventos de Envio](time-series-insights-send-events.md).

## <a name="ensure-that-you-have-reference-data"></a>Certifique-se de que tem dados de referência

Um conjunto de *dados de referência* é uma coleção de itens que aumentam os eventos a partir da sua fonte de evento. O motor de entrada Time Series Insights junta-se a cada evento a partir da fonte do seu evento com a linha de dados correspondente no seu conjunto de dados de referência. O evento aumentado está então disponível para consulta. A adesão baseia-se nas colunas **Chave Primária** que são definidas no seu conjunto de dados de referência.

> [!NOTE]
> Os dados de referência não são acompanhados retroativamente. Apenas os dados de ingresso atuais e futuros são combinados e unidos ao conjunto de dados de referência depois de configurado e carregado. Se planeia enviar uma grande quantidade de dados históricos para Time Series Insights e não enviar ou criar dados de referência em Time Series Insights, poderá ter de refazer o seu trabalho (dica: não divertido).  

Para saber mais sobre como criar, carregar e gerir os seus dados de referência em Time Series Insights, leia a nossa [documentação de conjunto](time-series-insights-add-reference-data-set.md)de dados de referência .

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passos seguintes

- Inicie-se com a criação [de um novo ambiente Time Series Insights no portal Azure.](time-series-insights-get-started.md)

- Saiba como adicionar uma fonte de [evento saque](time-series-insights-how-to-add-an-event-source-eventhub.md) a Time Series Insights.

- Leia sobre como configurar uma fonte de [evento ioT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).

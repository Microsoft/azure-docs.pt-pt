---
title: Planeie o seu ambiente Gen1 - Azure Time Series Insights | Microsoft Docs
description: As melhores práticas para preparar, configurar e implementar o seu ambiente Azure Time Series Insights Gen1.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 5e0f1ea42aa2ba888b89dd652d3397a3a2163a3e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95016212"
---
# <a name="plan-your-azure-time-series-insights-gen1-environment"></a>Planeie o seu ambiente Azure Time Series Insights Gen1

> [!CAUTION]
> Este é um artigo da Gen1.

Este artigo descreve como planear o seu ambiente Azure Time Series Insights Gen1 com base na sua taxa de entrada esperada e nos seus requisitos de retenção de dados.

## <a name="video"></a>Vídeo

**Veja este vídeo para saber mais sobre a retenção de dados em Azure Time Series Insights e como planeá-lo:**<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Melhores práticas

Para começar com a Azure Time Series Insights, é melhor saber quantos dados espera empurrar ao minuto e quanto tempo precisa para armazenar os seus dados.  

Para obter mais informações sobre capacidade e retenção para ambos os Azure Time Series Insights SKUs, leia [os preços da Azure Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Para melhor planear o seu ambiente Azure Time Series Insights para o sucesso a longo prazo, considere os seguintes atributos:

- [Capacidade de armazenamento](#storage-capacity)
- [Período de retenção de dados](#data-retention)
- [Capacidade de entrada](#ingress-capacity)
- [Moldar os seus eventos](#shape-your-events)
- [Garantindo que tem dados de referência no lugar](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Capacidade de armazenamento

Por predefinição, a Azure Time Series Insights retém dados com base na quantidade de armazenamento que fornece (unidades &#215; quantidade de armazenamento por unidade) e entrada.

## <a name="data-retention"></a>Retenção de dados

Pode alterar a definição do **tempo de retenção** de dados no ambiente Azure Time Series Insights. Pode ativar até 400 dias de retenção.

Azure Time Series Insights tem dois modos:

- Um modo otimiza para os dados mais atualizados. Aplica uma política de **purga de dados antigos** deixando dados recentes disponíveis com o caso. Este modo está ligado, por defeito.
- O outro otimiza os dados para permanecer abaixo dos limites de retenção configurados. **A pausa** impede que novos dados sejam ingressados quando são selecionados, uma vez que o **limite de armazenamento excede o comportamento**.

Pode ajustar a retenção e alternar entre os dois modos na página de configuração do ambiente no portal Azure.

> [!IMPORTANT]
> Pode configurar um máximo de 400 dias de retenção de dados no seu ambiente Azure Time Series Insights Gen1.

### <a name="configure-data-retention"></a>Configurar a retenção de dados

1. No [portal Azure,](https://portal.azure.com)selecione o ambiente De Insights de Séries Tempotadas.

1. No painel **de ambiente Time Series Insights,** em **Definições**, selecione **Configuração de Armazenamento**.

1. Na caixa de **tempo de retenção de dados (em dias),** insira um valor entre 1 e 400.

   [![Configure a retenção](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Para saber mais sobre como implementar uma política adequada de retenção de dados, leia [Como configurar a retenção.](./time-series-insights-how-to-configure-retention.md)

## <a name="ingress-capacity"></a>Capacidade de entrada

[!INCLUDE [Azure Time Series Insights Gen1 limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Planeamento ambiental

A segunda área em que se concentrar para planear o seu ambiente Azure Time Series Insights é a capacidade de entrada. O armazenamento diário e a capacidade do evento são medidos por minuto, em blocos de 1 KB. O tamanho máximo permitido do pacote é de 32 KB. Os pacotes de dados maiores do que 32 KB são truncados.

Pode aumentar a capacidade de um S1 ou S2 SKU para 10 unidades num único ambiente. Não se pode migrar de um ambiente S1 para um S2. Não se pode migrar de um ambiente S2 para um S1.

Para a capacidade de entrada, primeiro determine a entrada total que necessita numa base mensal. Em seguida, determine quais são as suas necessidades por minuto.

A agitação e a latência desempenham um papel na capacidade por minuto. Se tiver um pico na sua entrada de dados que dure menos de 24 horas, a Azure Time Series Insights pode "recuperar" a uma taxa de entrada de duas vezes superior às taxas listadas na tabela anterior.

Por exemplo, se tiver um SKU único, ingressa dados a uma taxa de 720 eventos por minuto, e a taxa de dados aumenta por menos de uma hora a uma taxa de 1.440 eventos ou menos, não há latência percetível no seu ambiente. No entanto, se exceder 1.440 eventos por minuto por mais de uma hora, provavelmente sentirá latência em dados que estão visualizados e disponíveis para consulta no seu ambiente.

Pode não saber com antecedência quantos dados espera empurrar. Neste caso, pode encontrar telemetria de dados para [Azure IoT Hub](../iot-hub/monitor-iot-hub.md) e [Azure Event Hubs](/archive/blogs/cloud_solution_architect/using-the-azure-rest-apis-to-retrieve-event-hub-metrics) na sua subscrição do portal Azure. A telemetria pode ajudá-lo a determinar como providenciar o seu ambiente. Utilize o painel **Métricas** no portal Azure para que a respetiva fonte de evento veja a sua telemetria. Se compreender as métricas da fonte do evento, pode planear e fornecer mais eficazmente o seu ambiente Azure Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcular requisitos de entrada

Para calcular os seus requisitos de entrada:

- Verifique se a sua capacidade de entrada está acima da sua taxa média por minuto e que o seu ambiente é grande o suficiente para lidar com a sua entrada antecipada equivalente a duas vezes a sua capacidade por menos de uma hora.

- Se ocorrerem picos de entrada que duram mais de 1 hora, use a taxa de pico como a sua média. Aprovisione um ambiente com capacidade para lidar com a taxa de pico.

### <a name="mitigate-throttling-and-latency"></a>Mitigar a estrangulamento e a latência

Para obter informações sobre como prevenir o estrangulamento e a latência, leia [a latência e a aceleração de Mitigação](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Moldar os seus eventos

É importante garantir que a forma como envia eventos para a Azure Time Series Insights suporta o tamanho do ambiente que está a fornecer. (Inversamente, você pode mapear o tamanho do ambiente para quantos eventos Azure Time Series Insights lê e o tamanho de cada evento.) Também é importante pensar nos atributos que pode querer usar para cortar e filtrar quando consulta os seus dados.

> [!TIP]
> Reveja a documentação de formação JSON em [eventos de envio.](time-series-insights-send-events.md)

## <a name="ensure-that-you-have-reference-data"></a>Certifique-se de que tem dados de referência

Um *conjunto de dados* de referência é uma recolha de itens que aumentam os eventos a partir da sua fonte de evento. O motor de entrada da Azure Time Series Insights junta-se a cada evento a partir da sua fonte de evento com a correspondente linha de dados no seu conjunto de dados de referência. O evento aumentado está então disponível para consulta. A junção baseia-se nas colunas **Da Chave Primária** que são definidas no seu conjunto de dados de referência.

> [!NOTE]
> Os dados de referência não são unidos retroativamente. Apenas os dados atuais e futuros de entrada são combinados e unidos ao conjunto de dados de referência após a configuração e upload. Se planeia enviar uma grande quantidade de dados históricos para a Azure Time Series Insights e não fazer o primeiro upload ou criar dados de referência no Azure Time Series Insights, poderá ter de refazer o seu trabalho (dica: não é divertido).  

Para saber mais sobre como criar, carregar e gerir os seus dados de referência em Azure Time Series Insights, leia a nossa [documentação do conjunto de dados de referência](time-series-insights-add-reference-data-set.md).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passos seguintes

- Iniciou-se com a criação [de um novo ambiente Azure Time Series Insights no portal Azure](time-series-insights-get-started.md).

- Saiba como [adicionar uma fonte de eventos do Event Hubs](./how-to-ingest-data-event-hub.md) ao Azure Time Series Insights.

- Leia sobre como [configurar uma fonte de eventos IoT Hub](./how-to-ingest-data-iot-hub.md).
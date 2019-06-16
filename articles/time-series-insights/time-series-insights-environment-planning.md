---
title: Planear a escala do seu ambiente do Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como a seguir as melhores práticas quando planear um ambiente do Azure Time Series Insights. Áreas que são abrangidas incluem a capacidade de armazenamento, retenção de dados, capacidade de entrada, monitorização e negócios continuidade e recuperação após desastre (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 9d9f9b7fe7aafa927f023e1d4e30e079a26f7fab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431042"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planejar seu ambiente de disponibilidade geral do Azure Time Series Insights

Este artigo descreve como planejar seu ambiente de disponibilidade geral (GA) do Azure Time Series Insights com base na sua taxa de entrada esperada e os requisitos de retenção de dados.

## <a name="video"></a>Vídeo

**Assista a este vídeo para saber mais sobre a retenção de dados no Azure Time Series Insights e como planejá-la**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Melhores práticas

Para começar a utilizar com o Time Series Insights, é melhor se souber a quantidade de dados esperado para enviar por minuto e o tempo que precisa armazenar os dados.  

Para obter mais informações sobre a retenção e capacidade para ambos os SKUs de informações de série de tempo, consulte [preços de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Para melhor planejar seu ambiente de Time Series Insights para o sucesso de longo prazo, considere os seguintes atributos:

- <a href="#storage-capacity">Capacidade de armazenamento</a>
- <a href="#data-retention">Período de retenção de dados</a>
- <a href="#ingress-capacity">Capacidade de entrada</a>
- <a href="#shape-your-events">Formatação de seus eventos</a>
- <a href="#ensure-that-you-have-reference-data">Assegurando que tem dados de referência no local</a>

## <a name="storage-capacity"></a>Capacidade de armazenamento

Por predefinição, o Time Series Insights retém os dados com base na quantidade de armazenamento que Aprovisiona (unidades &#215; a quantidade de armazenamento por unidade) e de entrada.

## <a name="data-retention"></a>Retenção de dados

Pode alterar o **período de retenção de dados** definição no seu ambiente do Time Series Insights. Pode ativar até 400 dias de retenção. 

O Time Series Insights tem dois modos. Otimiza um modo para garantir que o seu ambiente tem os dados mais atualizados. Esse modo é, por predefinição. 

O outro modo otimiza para garantir que os limites de retenção são cumpridos. No modo de segundo, entrada está em pausa se a capacidade de armazenamento geral do ambiente for cumprida. 

Pode ajustar a retenção e alternar entre os dois modos na página de configuração do ambiente no portal do Azure.

Pode configurar um máximo de 400 dias de retenção de dados no seu ambiente do Time Series Insights.

### <a name="configure-data-retention"></a>Configurar a retenção de dados

1. Na [portal do Azure](https://portal.azure.com), selecione o seu ambiente do Time Series Insights.

1. Na **ambiente do Time Series Insights** painel, em **definições**, selecione **configurar**.

1. Na **período de retenção de dados (em dias)** , introduza um valor entre 1 e 400.

   [![Configurar retenção](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Para saber mais sobre como implementar uma política de retenção de dados apropriados, veja [como configurar a retenção](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Capacidade de entrada

A segunda área para se concentrar para planejar seu ambiente do Time Series Insights é a capacidade de entrada. Capacidade de entrada é um derivativo da alocação de minuto.

De uma perspectiva de limitação, um pacote de dados ingressed que tem um tamanho de pacote de 32 KB é tratado como 32 eventos, cada 1 KB de tamanho. O tamanho máximo do evento permitido é de 32 KB. Pacotes de dados superiores a 32 KB são truncados.

A tabela seguinte resume a capacidade de entrada por unidade para cada SKU do Time Series Insights:

|SKU  |Contagem de eventos por mês  |Tamanho de eventos por mês  |Contagem de eventos por minuto  |Tamanho de eventos por minuto  |
|---------|---------|---------|---------|---------|
|S1     |   30 milhões     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milhões    |   300 GB   | 7,200   | 7\.200 KB  |

Pode aumentar a capacidade de um S1 ou S2 SKU de 10 unidades num único ambiente. Não é possível migrar a partir de um ambiente de S1 para S2. Não é possível migrar a partir de um ambiente de S2 para um S1.

Para a capacidade de entrada, determine primeiro a entrada total que exigir numa base por mês. Em seguida, determine o que sua por minuto às suas necessidades. 

Limitação e latência desempenham um papel na capacidade por minuto. Se tiver um pico na sua entrada de dados que dura menos de 24 horas, o Time Series Insights pode "acompanhar" a uma taxa de entrada de duas vezes com as tarifas listadas na tabela anterior.

Por exemplo, se tiver um único SKU S1, o que os dados de entrada a uma taxa de 720 eventos por minuto, e a taxa de dados picos para menos de uma hora a uma taxa de 1.440 eventos ou menos, não existe nenhuma latência percebida no seu ambiente. No entanto, se o ter mais de 1.440 eventos por minuto durante mais de uma hora, é provável que experimentará latência nos dados visualizados e disponível para consulta no seu ambiente.

Poderá não saber com antecedência a quantidade de dados esperado enviar por push. Neste caso, pode encontrar a telemetria de dados para [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) e [Event Hubs do Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) na sua subscrição do portal do Azure. A telemetria pode ajudá-lo a determinar como aprovisionar o seu ambiente. Utilize o **métricas** painel no portal do Azure, a respetiva origem do evento ver a respetiva telemetria. Se compreender suas métricas de origem do evento, pode planear com mais eficiência e aprovisionar o seu ambiente do Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcular os requisitos de entrada

Para calcular os requisitos de entrada:

- Certifique-se de que sua capacidade de entrada for superior a taxa média de minuto e que o seu ambiente é grande o suficiente para lidar com a sua entrada antecipada equivalente para duas vezes a capacidade para menos de uma hora.

- Se a picos de entrada ocorrerem utilizados pela última vez durante mais de 1 hora, a taxa de pico como sua média. Aprovisione um ambiente com capacidade para lidar com a taxa de pico.

### <a name="mitigate-throttling-and-latency"></a>Mitigar limitação e latência

Para obter informações sobre como impedir a limitação e latência, consulte [minimizar a latência e limitação](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Moldar seus eventos

É importante certificar-se de que forma que envia eventos para o Time Series Insights suporta o tamanho do ambiente do que se estiver a aprovisionar. (Por outro lado, pode mapear o tamanho do ambiente para o número de eventos lê do Time Series Insights e o tamanho de cada evento.) Também é importante pensar sobre os atributos que pode querer utilizar para segmentar, filtrar por ao consultar os seus dados.

> [!TIP]
> Reveja o JSON shaping documentação na [envio de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Certifique-se de que tem dados de referência

R *conjunto de dados de referência* é uma coleção de itens que aumentam os eventos da origem de evento. O mecanismo de entrada do Time Series Insights une cada evento da origem de evento com a linha de dados correspondente no seu conjunto de dados de referência. O evento aumentado fica então disponível para consulta. A associação se baseia a **chave primária** colunas definidas no seu conjunto de dados de referência.

> [!NOTE]
> Dados de referência não se encontra associados retroativamente. Apenas os dados de entrada atuais e futuras são correspondidos e associados ao conjunto de dados de referência, depois que ele é configurado e carregados. Se pretender enviar uma grande quantidade de dados históricos para Time Series Insights e não o primeiro carregamento ou criar dados de referência no Time Series Insights, poderá ter de Refazer seu trabalho (dica: não divertidos).  

Para obter mais informações sobre como criar, carregar e gerir os seus dados de referência no Time Series Insights, consulte nosso [documentação de conjunto de dados de referência](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passos Seguintes

- Introdução à criação [um novo ambiente do Time Series Insights no portal do Azure](time-series-insights-get-started.md).

- Saiba como [adicionar uma origem de eventos de Hubs de eventos](time-series-insights-how-to-add-an-event-source-eventhub.md) para Time Series Insights.

- Saiba mais sobre como [configurar uma origem de evento do IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).

---
title: Limitações de produção de streaming - Azure Time Series Insights Gen2 | Microsoft Docs
description: Conheça os limites de produção ingres em Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: d86cc6af34036f5bd638b4fc78abdb54d71e6859
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306522"
---
# <a name="streaming-ingestion-throughput-limits"></a>Limites de produção de ingestão de streaming

Azure Time Series Insights Gen2 streaming de dados limitações de ingres são descritos abaixo.

> [!TIP]
> Leia [O Plano do seu ambiente Azure Time Series Insights Gen2](./how-to-plan-your-environment.md#review-azure-time-series-insights-gen2-limits) para uma lista completa de todos os limites.

## <a name="per-environment-limitations"></a>Por limitações ambientais

Em geral, as taxas de entrada são vistas como o fator do número de dispositivos que estão na sua organização, frequência de emissões de eventos e o tamanho de cada evento:

* **Número de dispositivos** × **frequência de emissão de eventos** × **tamanho de cada evento**.

Por padrão, a Azure Time Series Insights Gen2 pode ingerir dados de entrada a uma taxa de **até 1 megabyte por segundo (MBps) por Azure Time Series Insights Gen2 ambiente**. Existem limitações adicionais [por partição do hub.](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)

> [!TIP]
>
> * O apoio ao ambiente para a ingestão de velocidades até 2 MBps pode ser fornecido a pedido.
> * Contacte-nos se necessitar de uma produção mais elevada, enviando um bilhete de apoio através do portal Azure.

* **Exemplo 1:**

    A Contoso Shipping tem 100.000 dispositivos que emitem um evento três vezes por minuto. O tamanho de um evento é de 200 bytes. Estão a usar um Hub IoT com quatro divisórias como fonte de eventos da Azure Time Series Insights Gen2.

  * A taxa de ingestão para o seu ambiente Azure Time Series Insights Gen2 seria: **100.000 dispositivos * 200 bytes/event * (3/60 event/seg) = 1 MBps**.
    * Assumindo divisórias equilibradas, a taxa de ingestão por partição seria de 0,25 MBps.
    * A taxa de ingestão da Contoso Shipping estaria dentro das limitações de escala.

* **Exemplo 2:**

    A Contoso Fleet Analytics tem 10.000 dispositivos que emitem um evento a cada segundo. Eles estão a usar um Event Hub com uma contagem de partição de 2 como fonte de eventos Azure Time Series Insights Gen2. O tamanho de um evento é de 200 bytes.

  * A taxa de ingestão ambiental seria: **10.000 dispositivos * 200 bytes/event * 1 evento/seg = 2 MBps**.
    * Assumindo divisórias equilibradas, a sua taxa por partição seria de 1 MBps.
    * A taxa de ingestão da Frota Contoso é sobre o ambiente e os limites de partição. Podem submeter um pedido ao Azure Time Series Insights Gen2 através do portal Azure para aumentar a taxa de ingestão para o seu ambiente, e criar um Centro de Eventos com mais divisórias para estar dentro dos limites.

## <a name="hub-partitions-and-per-partition-limits"></a>Divisórias centrais e por limite de partição

Ao planear o seu ambiente Azure Time Series Insights Gen2, é importante considerar a configuração da(s) fonte(s) do evento que estará a ligar-se ao Azure Time Series Insights Gen2. Tanto o Azure IoT Hub como o Event Hubs utilizam divisórias para permitir a escala horizontal para o processamento de eventos.

Uma *partição* é uma sequência ordenada de eventos realizados num centro. A contagem de divisórias é definida durante a fase de criação do hub e não pode ser alterada.

Para o Event Hubs a dividir as melhores práticas, reveja [quantas divisórias preciso?](../event-hubs/event-hubs-faq.yml#how-many-partitions-do-i-need-)

> [!NOTE]
> A maioria dos hubs IoT usados com Azure Time Series Insights Gen2 só precisam de quatro divisórias.

Quer esteja a criar um novo hub para o seu ambiente Azure Time Series Insights Gen2 ou usando um existente, terá de calcular a sua taxa de ingestão por partição para determinar se está dentro dos limites.

Azure Time Series Insights Gen2 tem atualmente um limite geral **por partição de 0,5 MBps**.

### <a name="iot-hub-specific-considerations"></a>Considerações específicas do IoT Hub

Quando um dispositivo é criado no IoT Hub, é permanentemente atribuído a uma partição. Ao fazê-lo, o IoT Hub é capaz de garantir a encomenda de eventos (uma vez que a atribuição nunca muda).

Uma atribuição de partição fixa também impacta as instâncias da Azure Time Series Insights Gen2 que estão a ingerir dados enviados do IoT Hub a jusante. Quando as mensagens de vários dispositivos são reencaminhadas para o hub utilizando o mesmo ID do dispositivo de gateway, podem chegar à mesma partição ao mesmo tempo que podem exceder os limites de escala por partição.

**Impacto:**

* Se uma única partição experimentar uma taxa sustentada de ingestão acima do limite, é possível que a Azure Time Series Insights Gen2 não sincronize toda a telemetria do dispositivo antes do período de retenção de dados do IoT Hub ter sido ultrapassado. Como resultado, os dados enviados podem ser perdidos se os limites de ingestão forem consistentemente ultrapassados.

Para mitigar esta circunstância, recomendamos as seguintes boas práticas:

* Calcule as suas taxas de ingestão por ambiente e por partição antes de implementar a sua solução.
* Certifique-se de que os seus dispositivos IoT Hub estão equilibrados na medida do possível.

> [!IMPORTANT]
> Para ambientes que utilizem o IoT Hub como fonte de evento, calcule a taxa de ingestão utilizando o número de dispositivos hub em uso para se certificar de que a taxa fica abaixo dos 0,5 MBps por limitação de partição.
>
> * Mesmo que vários eventos cheguem simultaneamente, o limite não será ultrapassado.

  ![Diagrama de partição do hub IoT](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Consulte os seguintes recursos para saber mais sobre a otimização da produção e partições do hub:

* [Escala de hub IoT](../iot-hub/iot-hub-scaling.md)
* [Escala de hub de evento](../event-hubs/event-hubs-scalability.md#throughput-units)
* [Partições do Hub de Eventos](../event-hubs/event-hubs-features.md#partitions)

## <a name="next-steps"></a>Passos seguintes

* Ler sobre [armazenamento de](./concepts-storage.md) dados

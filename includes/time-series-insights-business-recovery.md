---
title: incluir ficheiro
description: incluir ficheiro
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: e87a82e985ed1d1794f9da00546f167ef01e1779
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65815630"
---
## <a name="business-disaster-recovery"></a>Recuperação após desastre de negócio

Esta secção descreve os recursos do Azure Time Series Insights que mantém as aplicações e serviços em execução, mesmo se ocorrer um desastre (**negócio recuperação após desastre**).

### <a name="high-availability"></a>Elevada disponibilidade

Como um serviço do Azure, o Time Series Insights fornece certas **elevada disponibilidade** funcionalidades com redundâncias ao nível da região do Azure. Por exemplo, o Microsoft Azure suporta capacidades de recuperação após desastre através do Azure **entre regiões disponibilidade** funcionalidade.

As funcionalidades adicionais de elevada disponibilidade fornecidas através do Azure (e também está disponível para qualquer instância do Time Series Insights) incluem:

1. **Ativação pós-falha**: O Azure disponibiliza [georreplicação e carregar balanceamento](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
1. **Restauro de dados** e **recuperação armazenamento**: O Azure disponibiliza [várias opções para preservar e recuperar dados](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
1. **Recuperação de sites** funcionalidades através de [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Certifique-se ativar estas funcionalidades do Azure fornecer global, entre regiões, de elevada disponibilidade para os dispositivos e utilizadores.

> [!NOTE]
> Se o Azure está configurado para habilitar **entre regiões disponibilidade**, nenhuma configuração adicional de entre regiões de disponibilidade é necessária no Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>Hubs de eventos e de IoT

Alguns serviços do Azure IoT também incluem recursos de recuperação após desastre de negócio incorporada:

1. [Recuperação de desastres de elevada disponibilidade do IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr) incluindo redundância de intra-região.
1. [Políticas do Hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
1. [Redundância de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

Integrar o Time Series Insights com estes outros serviços fornece após desastre adicionais oportunidades de recuperação. Por exemplo, a telemetria enviada ao seu Hub de eventos pode ser persistente para uma cópia de segurança da base de dados do armazenamento de Blobs do Azure.

### <a name="time-series-insights"></a>Time Series Insights

Existem várias formas de manter os dados, aplicações e serviços em execução, mesmo que eles são interrompidos do Time Series Insights. Também pode determinar que uma cópia completa, duplicada e cópia de segurança do seu ambiente do Azure Time Series é necessária:

1. Como um Time Series Insights-específico **instância de ativação pós-falha** para redirecionar dados e para o tráfego.
1. Para fins de preservação de auditoria e dados.

Em geral, a melhor forma de duplicar um ambiente do Time Series Insights é criar um ambiente do Time Series Insights segundo numa região do Azure de cópia de segurança. Eventos também são enviados para este ambiente secundário da origem de evento primário. Certifique-se para utilizar um grupo de consumidores dedicado e, em segundo e seguir business desastre recuperação orientações essa origem (fornecidas acima).

Especificamente, para criar um ambiente duplicado:

1. Criar um ambiente numa segunda região ([criar um novo ambiente do Time Series Insights no portal do Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)).
1. Crie um segundo grupo de consumidores dedicado para a origem do evento.
1. Ligue-se essa origem de evento para o novo ambiente certificar-se de que designar o grupo de consumidores dedicado, segundo.
1. Reveja o Time Series Insights [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) e [hub de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) documentação.

Por fim:

* Se a região primária é afetada durante um incidente de desastres, redirecionar operações para o ambiente do Time Series Insights cópia de segurança.
* Utilize a sua segunda região para criar cópias de segurança e recuperar todos os dados de telemetria e consulta de Time Series Insights.

> [!IMPORTANT]
> * Tenha em atenção que poderá encontrar um atraso na eventualidade de uma ativação pós-falha.
> * Ativação pós-falha também poderá provocar um pico momentânea no processamento de mensagens, como operações são reencaminhadas.
> * Para obter mais informações, consulte [minimizar a latência no Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).
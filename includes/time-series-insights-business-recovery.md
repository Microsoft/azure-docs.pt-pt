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
ms.openlocfilehash: e3e7044148e262e6977ae3be96f7cb61218114a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388754"
---
## <a name="business-disaster-recovery"></a>Recuperação após desastre de negócio

Esta secção descreve as funcionalidades do Azure Time Series Insights que mantém as aplicações e serviços em execução, mesmo se ocorrer um desastre. Esse conceito é conhecido como recuperação após desastre de negócio.

### <a name="high-availability"></a>Elevada disponibilidade

Como um serviço do Azure, o Time Series Insights fornece algumas funcionalidades de elevada disponibilidade utilizando o redundâncias ao nível da região do Azure. Por exemplo, Microsoft Azure suporta as capacidades de recuperação de desastres por meio do recurso de disponibilidade entre regiões do Azure.

Recursos adicionais de elevada disponibilidade fornecidos através do Azure e que também estão disponíveis para qualquer instância do Time Series Insights, incluem:

* **Ativação pós-falha**: O Azure disponibiliza [georreplicação e balanceamento de carga](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
* **Restauro de dados** e **recuperação armazenamento**: O Azure disponibiliza [várias opções para preservar e recuperar dados](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
* **Recuperação de sites**: Funcionalidades que estão disponíveis por meio [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Certifique-se ativar estas funcionalidades do Azure fornecer elevada disponibilidade global e em várias regiões para seus dispositivos e utilizadores.

> [!NOTE]
> Se o Azure está configurado para ativar a disponibilidade em várias regiões, nenhuma configuração adicional de entre regiões de disponibilidade é necessária no Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>Hubs de eventos e de IoT

Alguns serviços do Azure IoT também incluem recursos de recuperação após desastre de negócio incorporada:

* [Recuperação de desastres de elevada disponibilidade do IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), que inclui a redundância de intra-região.
* [Políticas de Hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
* [Redundância de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

Integrar o Time Series Insights com estes outros serviços fornece após desastre adicionais oportunidades de recuperação. Por exemplo, a telemetria enviada ao seu hub de eventos pode ser persistente para uma cópia de segurança base de dados de armazenamento de Blobs do Azure.

### <a name="time-series-insights"></a>Time Series Insights

Existem várias formas de manter os dados, aplicações e serviços em execução, mesmo que eles são interrompidos do Time Series Insights. Também pode determinar que uma cópia de segurança completa do seu ambiente do Azure Time Series é necessária:

* Como uma instância de ativação pós-falha do Time Series Insights específicas para redirecionar dados e de tráfego para.
* Para fins de preservação de auditoria e dados.

Em geral, a melhor forma de duplicar um ambiente do Time Series Insights é criar um ambiente do Time Series Insights segundo numa região do Azure de cópia de segurança. Eventos também são enviados para este ambiente secundário da origem de evento primário. Certifique-se de que, para utilizar um grupo de consumidores dedicado em segundo lugar e seguir business desastre recuperação orientações essa origem, conforme anteriormente fornecidas.

Especificamente, para criar um ambiente duplicado:

1. Crie um ambiente numa segunda região. Para obter instruções, consulte [criar um novo ambiente do Time Series Insights no portal do Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Crie um segundo grupo de consumidores dedicado para a origem do evento.
1. Ligue-se essa origem de evento para o novo ambiente. Certifique-se designar o segundo grupo de consumidores dedicado.
1. Reveja o Time Series Insights [IoT hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) e [hub de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) documentação.

Por fim:

1. Se a região primária é afetada durante um incidente de desastres, redirecionar operações para o ambiente do Time Series Insights cópia de segurança.
1. Utilize a sua segunda região para criar cópias de segurança e recuperar todos os dados de telemetria e consulta de Time Series Insights.

> [!IMPORTANT]
> * Tenha em atenção que poderá encontrar um atraso na eventualidade de uma ativação pós-falha.
> * Ativação pós-falha também poderá provocar um pico momentânea no processamento de mensagens, como operações são reencaminhadas.
> * Para obter mais informações, consulte [minimizar a latência no Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

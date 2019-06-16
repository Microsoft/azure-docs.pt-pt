---
ms.topic: include
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: 8a3c630b54ff95a9b1200e2421c787a514a0aa52
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431033"
---
## <a name="business-disaster-recovery"></a>Recuperação após desastre de negócio

Esta secção descreve os recursos do Azure Time Series Insights que mantém as aplicações e serviços em execução, mesmo se ocorrer um desastre (conhecido como *negócio recuperação após desastre*).

### <a name="high-availability"></a>Elevada disponibilidade

Como um serviço do Azure, o Time Series Insights fornece certas *elevada disponibilidade* funcionalidades com redundâncias ao nível da região do Azure. Por exemplo, o Azure suporta capacidades de recuperação após desastre através do Azure *entre regiões disponibilidade* funcionalidade.

As funcionalidades adicionais de elevada disponibilidade fornecidas através do Azure (e também está disponível para qualquer instância do Time Series Insights) incluem:

- **Ativação pós-falha**: O Azure disponibiliza [georreplicação e balanceamento de carga](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Restauro de dados** e **recuperação armazenamento**: O Azure disponibiliza [várias opções para preservar e recuperar dados](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Recuperação de sites**: O Azure fornece recursos de recuperação de site por meio [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Certifique-se de que ativar as funcionalidades relevantes do Azure fornecer elevada disponibilidade global e em várias regiões para seus dispositivos e utilizadores.

> [!NOTE]
> Se o Azure está configurado para ativar a disponibilidade em várias regiões, não é necessária nenhuma configuração adicional de entre regiões de disponibilidade no Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>Hubs de eventos e de IoT

Alguns serviços do Azure IoT também incluem recursos de recuperação após desastre de negócio incorporada:

- [Recuperação de desastres de elevada disponibilidade do IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), incluindo redundância de intra-região
- [Políticas de Hubs de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Redundância de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Integrar o Time Series Insights com os outros serviços fornece após desastre adicionais oportunidades de recuperação. Por exemplo, a telemetria enviada ao seu hub de eventos pode ser persistente para uma cópia de segurança base de dados de armazenamento de Blobs do Azure.

### <a name="time-series-insights"></a>Time Series Insights

Existem várias formas de manter os seus dados do Time Series Insights, aplicações e serviços em execução, mesmo que eles são interrompidos. 

No entanto, poderá determinar que uma cópia de segurança completa do seu ambiente do Azure Time Series também é necessária, para os seguintes fins:

- Como um *instância de ativação pós-falha* especificamente para o Time Series Insights redirecionar dados e de tráfego para
- Para preservar dados e informações de auditoria

Em geral, a melhor forma de duplicar um ambiente do Time Series Insights é criar um ambiente do Time Series Insights segundo numa região do Azure de cópia de segurança. Eventos também são enviados para este ambiente secundário da origem de evento primário. Certifique-se de que utiliza um grupo de consumidores dedicado, segundo. Siga as diretrizes de recuperação após desastre do negócio essa origem, conforme descrito anteriormente.

Para criar um ambiente duplicado:

1. Crie um ambiente numa segunda região. Para obter mais informações, consulte [criar um novo ambiente do Time Series Insights no portal do Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Crie um segundo grupo de consumidores dedicado para a origem do evento.
1. Ligue-se essa origem de evento para o novo ambiente. Certifique-se de que designar o grupo de consumidores dedicado, segundo.
1. Reveja o Time Series Insights [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) e [dos Hubs de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) documentação.

Se ocorrer um evento:

1. Se a região primária é afetada durante um incidente de desastres, redirecionar operações para o ambiente do Time Series Insights cópia de segurança.
1. Utilize a sua segunda região para criar cópias de segurança e recuperar todos os dados de telemetria e consulta de Time Series Insights.

> [!IMPORTANT]
> Se ocorrer uma ativação pós-falha:
> 
> * Também pode ocorrer um atraso.
> * Poderão ocorrer um pico momentânea no processamento de mensagens, como operações são reencaminhadas.
> 
> Para obter mais informações, consulte [minimizar a latência no Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).


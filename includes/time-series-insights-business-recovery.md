---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013784"
---
## <a name="business-disaster-recovery"></a>Recuperação de desastres empresariais

Esta secção descreve funcionalidades de Insights da Série De Tempo Azure que mantêm as aplicações e serviços em funcionamento, mesmo que ocorra um desastre (conhecido como recuperação de *desastres de negócios).*

### <a name="high-availability"></a>Elevada disponibilidade

Como serviço Azure, a Time Series Insights fornece certas funcionalidades de *alta disponibilidade* utilizando redundâncias a nível da região de Azure. Por exemplo, o Azure suporta capacidades de recuperação de desastres através da funcionalidade de disponibilidade de *regiões transversais* do Azure.

As funcionalidades adicionais de alta disponibilidade fornecidas através do Azure (e também disponíveis para qualquer instância de Time Series Insights) incluem:

- **Failover**: O Azure fornece [geo-replicação e equilíbrio de carga](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Recuperação** de dados e recuperação de **armazenamento:** O Azure oferece [várias opções para preservar e recuperar dados.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)
- **Recuperação**do site Azure : Azure fornece funcionalidades de recuperação do local através da [Recuperação do Site Azure.](https://docs.microsoft.com/azure/site-recovery/)
- **Backup Azure**: [O Backup Azure](https://docs.microsoft.com/azure/backup/backup-architecture) suporta tanto no local como na nuvem backup de VMs Azure.

Certifique-se de que permite que as funcionalidades do Azure relevantes ofereçam uma elevada disponibilidade global e transversal aos seus dispositivos e utilizadores.

> [!NOTE]
> Se o Azure estiver configurado para permitir a disponibilidade transversal, não é necessária uma configuração adicional de disponibilidade transversal em Insights da Série De Tempo Azure.

### <a name="iot-and-event-hubs"></a>IoT e centros de eventos

Alguns serviços Azure IoT também incluem funcionalidades de recuperação de desastres de negócios incorporados:

- [Azure IoT Hub recuperação de desastres de alta disponibilidade,](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)que inclui redundância intrarregião
- [Políticas de Hubs de Eventos Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Redundância de armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Integrar insights da Time Series com os outros serviços proporciona oportunidades adicionais de recuperação de desastres. Por exemplo, a telemetria enviada para o seu centro de eventos pode ser persistiu numa base de dados de armazenamento Azure Blob de reserva.

### <a name="time-series-insights"></a>Time Series Insights

Existem várias formas de manter os seus dados, aplicações e serviços da Série Time Insights em funcionamento, mesmo que sejam interrompidos. 

No entanto, poderá determinar que também é necessária uma cópia de cópia de reserva completa do seu ambiente da Série De Tempo Azure, para os seguintes fins:

- Como um *caso de failover* especificamente para Time Series Insights para redirecionar dados e tráfego para
- Para preservar dados e informações de auditoria

Em geral, a melhor maneira de duplicar um ambiente time series insights é criar um segundo ambiente time series Insights em uma região de backup Azure. Os eventos também são enviados para este ambiente secundário a partir da sua principal fonte de evento. Certifique-se de que utiliza um segundo grupo de consumidores dedicado. Siga as diretrizes de recuperação de desastres comerciais da fonte, como descrito anteriormente.

Para criar um ambiente duplicado:

1. Criar um ambiente numa segunda região. Para mais informações, leia Criar um novo ambiente time [series insights no portal Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Crie um segundo grupo de consumidores dedicado para a sua fonte de evento.
1. Ligue a fonte do evento ao novo ambiente. Certifique-se de que designa o segundo grupo de consumidores dedicado.
1. Reveja a documentação do Time Series Insights [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) e [do Event Hubs.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)

Se ocorrer um evento:

1. Se a sua região primária for afetada durante um incidente de desastre, reencaminhe as operações para o ambiente de backup Time Series Insights.
1. Use a sua segunda região para apoiar e recuperar todos os dados de telemetria e consulta da Time Series Insights.

> [!IMPORTANT]
> Se ocorrer uma falha:
> 
> * Pode também ocorrer um atraso.
> * Pode ocorrer um pico momentâneo no processamento de mensagens, à medida que as operações são redirecionadas.
> 
> Para mais informações, leia [a latência de Mitigação em Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).


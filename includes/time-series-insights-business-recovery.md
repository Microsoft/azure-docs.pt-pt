---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 07/09/2020
ms.openlocfilehash: f25c335c568c112c05f81df51d69e83aeff423e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026663"
---
## <a name="business-disaster-recovery"></a>Recuperação de desastres de negócios

Esta secção descreve características do Azure Time Series Insights que mantêm aplicações e serviços em funcionamento, mesmo que ocorra um desastre (conhecido como *recuperação de desastres comerciais).*

### <a name="high-availability"></a>Elevada disponibilidade

Como um serviço Azure, a Azure Time Series Insights fornece certas funcionalidades *de alta disponibilidade* utilizando redundâncias ao nível da região de Azure. Por exemplo, o Azure suporta capacidades de recuperação de desastres através da funcionalidade *de disponibilidade de regiões transversais* da Azure.

As funcionalidades adicionais de alta disponibilidade fornecidas através do Azure (e também disponíveis para qualquer instância Azure Time Series Insights) incluem:

- **Failover**: A azul fornece [geo-replicação e equilíbrio de carga](/azure/architecture/resiliency/recovery-loss-azure-region).
- **Recuperação** e **recuperação de dados**: A Azure oferece [várias opções para preservar e recuperar dados.](/azure/architecture/resiliency/recovery-data-corruption)
- **Recuperação do sítio Azure**: O Azure fornece funcionalidades de recuperação do local através da [Recuperação do Sítio Azure.](../articles/site-recovery/index.yml)
- **Azure Backup**: [Azure Backup](../articles/backup/backup-architecture.md) suporta tanto no local como na nuvem backup de VMs Azure.

Certifique-se de que ativa as funcionalidades Azure relevantes para fornecer uma alta disponibilidade global e transversal para os seus dispositivos e utilizadores.

> [!NOTE]
> Se o Azure estiver configurado para permitir a disponibilidade entre regiões, não é necessária nenhuma configuração adicional de disponibilidade entre regiões no Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT e centros de eventos

Alguns serviços da Azure IoT também incluem funcionalidades de recuperação de desastres comerciais incorporadas:

- [Azure IoT Hub recuperação de desastres de alta disponibilidade,](../articles/iot-hub/iot-hub-ha-dr.md)que inclui redundância intrarregião
- [Políticas de Azure Event Hubs](../articles/event-hubs/event-hubs-geo-dr.md)
- [Redundância do Armazenamento do Azure](../articles/storage/common/storage-redundancy.md)

A integração da Azure Time Series Insights com os outros serviços proporciona oportunidades adicionais de recuperação de desastres. Por exemplo, a telemetria enviada para o seu centro de eventos pode ser persistido a uma base de dados de armazenamento Azure Blob de reserva.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

Existem várias formas de manter os dados, apps e serviços da Azure Time Series, mesmo que sejam perturbados. 

No entanto, pode determinar que também é necessária uma cópia de backup completa do seu ambiente Azure Time Series, para as seguintes finalidades:

- Como um *exemplo de failover* especificamente para Azure Time Series Insights para redirecionar dados e tráfego para
- Para preservar dados e auditoria de informação

Em geral, a melhor maneira de duplicar um ambiente Azure Time Series Insights é criar um segundo ambiente Azure Time Series Insights numa região de Azure. Os eventos também são enviados para este ambiente secundário a partir da sua fonte principal de eventos. Certifique-se de que utiliza um segundo grupo de consumidores dedicado. Siga as diretrizes de recuperação de desastres comerciais da fonte, como descrito anteriormente.

Para criar um ambiente duplicado:

1. Criar um ambiente numa segunda região. Para mais informações, leia [Criar um novo ambiente Azure Time Series Insights no portal Azure](../articles/time-series-insights/time-series-insights-get-started.md).
1. Crie um segundo grupo de consumidores dedicado para a sua fonte de eventos.
1. Ligue a fonte do evento ao novo ambiente. Certifique-se de que designa o segundo grupo de consumidores dedicado.
1. Reveja a documentação do Azure Time Series Insights [IoT Hub](../articles/time-series-insights/how-to-ingest-data-iot-hub.md) e [do Event Hubs.](../articles/time-series-insights/concepts-access-policies.md)

Se ocorrer um evento:

1. Se a sua região primária for afetada durante um incidente de desastre, reencaminhe as operações para o ambiente de backup Azure Time Series Insights.
1. Use a sua segunda região para fazer cópias de segurança e recuperar todos os dados de telemetria e consulta da Azure Time Series Insights.

> [!IMPORTANT]
> Se ocorrer uma falha:
> 
> * Um atraso também pode ocorrer.
> * Pode ocorrer um pico momentâneo no processamento de mensagens, uma vez que as operações são reencaminhadas.
> 
> Para obter mais informações, leia [Mitigate latência em Azure Time Series Insights](../articles/time-series-insights/time-series-insights-environment-mitigate-latency.md).
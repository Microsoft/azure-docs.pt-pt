---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/09/2019
ms.openlocfilehash: 038d25a01c02799f48b8430fb6ac3facc1cd3bd6
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990836"
---
## <a name="business-disaster-recovery"></a>Recuperação de desastres de negócios

Esta seção descreve os recursos de Azure Time Series Insights que mantêm aplicativos e serviços em execução, mesmo se ocorrer um desastre (conhecido como *recuperação de desastres de negócios*).

### <a name="high-availability"></a>Elevada disponibilidade

Como um serviço do Azure, o Time Series Insights fornece determinados recursos de *alta disponibilidade* usando redundâncias no nível de região do Azure. Por exemplo, o Azure dá suporte a recursos de recuperação de desastre por meio do recurso de *disponibilidade entre regiões* do Azure.

Recursos adicionais de alta disponibilidade fornecidos por meio do Azure (e também disponíveis para qualquer instância de Time Series Insights) incluem:

- **Failover**: o Azure fornece [replicação geográfica e balanceamento de carga](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Restauração de dados** e **recuperação de armazenamento**: o Azure fornece [várias opções para preservar e recuperar dados](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Azure site Recovery**: o Azure fornece recursos de recuperação de site por meio de [Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/).
- **Backup do Azure**: o [backup do Azure](https://docs.microsoft.com/azure/backup/backup-architecture) dá suporte ao backup local e na nuvem de VMs do Azure.

Certifique-se de habilitar os recursos relevantes do Azure para fornecer alta disponibilidade global entre regiões para seus dispositivos e usuários.

> [!NOTE]
> Se o Azure estiver configurado para habilitar a disponibilidade entre regiões, nenhuma configuração de disponibilidade de região entre regiões adicional será necessária no Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT e hubs de eventos

Alguns serviços do Azure IoT também incluem recursos internos de recuperação de desastres de negócios:

- [Recuperação de desastre de alta disponibilidade do Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), que inclui redundância de região interna
- [Políticas de hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Redundância de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

A integração do Time Series Insights com os outros serviços fornece outras oportunidades de recuperação de desastre. Por exemplo, a telemetria enviada ao Hub de eventos pode ser persistida em um banco de dados de armazenamento de BLOBs do Azure de backup.

### <a name="time-series-insights"></a>Time Series Insights

Há várias maneiras de manter seus Time Series Insights dados, aplicativos e serviços em execução, mesmo que eles sejam interrompidos. 

No entanto, você pode determinar que uma cópia de backup completa do seu ambiente de série temporal do Azure também é necessária, para as seguintes finalidades:

- Como uma *instância de failover* especificamente para Time Series insights redirecionar dados e tráfego para
- Para preservar dados e informações de auditoria

Em geral, a melhor maneira de duplicar um ambiente de Time Series Insights é criar um segundo ambiente de Time Series Insights em uma região de backup do Azure. Os eventos também são enviados para esse ambiente secundário a partir da origem do evento principal. Certifique-se de usar um segundo grupo de consumidores dedicado. Siga as diretrizes de recuperação de desastre comercial da fonte, conforme descrito anteriormente.

Para criar um ambiente duplicado:

1. Crie um ambiente em uma segunda região. Para obter mais informações, consulte [criar um novo ambiente de time Series insights no portal do Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Crie um segundo grupo de consumidores dedicado para a origem do evento.
1. Conecte essa origem do evento ao novo ambiente. Certifique-se de designar o segundo grupo de consumidores dedicado.
1. Examine a documentação do [Hub IOT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) e dos [hubs de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) do time Series insights.

Se ocorrer um evento:

1. Se sua região primária for afetada durante um incidente de desastre, redirecione as operações para o ambiente de Time Series Insights de backup.
1. Use sua segunda região para fazer backup e recuperar todos os Time Series Insights telemetria e dados de consulta.

> [!IMPORTANT]
> Se ocorrer um failover:
> 
> * Um atraso também pode ocorrer.
> * Um pico momentâneo no processamento de mensagens pode ocorrer, pois as operações são redirecionadas.
> 
> Para obter mais informações, consulte [mitigar latência em time Series insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).


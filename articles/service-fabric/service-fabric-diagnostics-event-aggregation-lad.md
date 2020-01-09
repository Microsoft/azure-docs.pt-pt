---
title: Agregação de eventos com Linux Diagnóstico do Azure
description: Saiba mais sobre como agregar e coletar eventos usando o LAD para monitoramento e diagnóstico de clusters de Service Fabric do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: fdb78498d33416ef21b2e2b0f498e7afa6a58d99
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609966"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Agregação e coleta de eventos usando o Linux Diagnóstico do Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando você estiver executando um cluster de Service Fabric do Azure, é uma boa ideia coletar os logs de todos os nós em um local central. Ter os logs em um local central ajuda você a analisar e solucionar problemas no cluster ou problemas nos aplicativos e serviços em execução nesse cluster.

Uma maneira de carregar e coletar logs é usar a extensão LAD (Linux Diagnóstico do Azure), que carrega logs no armazenamento do Azure e também tem a opção de enviar logs para Aplicativo Azure insights ou hubs de eventos. Você também pode usar um processo externo para ler os eventos do armazenamento e colocá-los em um produto da plataforma de análise, como [logs de Azure monitor](../log-analytics/log-analytics-service-fabric.md) ou outra solução de análise de log.

## <a name="log-and-event-sources"></a>Origens de log e eventos

### <a name="service-fabric-platform-events"></a>Service Fabric eventos da plataforma
Service Fabric emite alguns logs prontos para uso por meio de [LTTng](https://lttng.org), incluindo eventos operacionais ou eventos de tempo de execução. Esses logs são armazenados no local que o modelo do Resource Manager do cluster especifica. Para obter ou definir os detalhes da conta de armazenamento, procure a marca **AzureTableWinFabETWQueryable** e procure **StoreConnectionString**.

### <a name="application-events"></a>Eventos de aplicativo
 Eventos emitidos por meio do código de seus aplicativos e serviços, conforme especificado por você ao instrumentar seu software. Você pode usar qualquer solução de registro em log que grava arquivos de log baseados em texto, por exemplo, LTTng. Para obter mais informações, consulte a documentação do LTTng sobre como rastrear seu aplicativo.

[Monitorar e diagnosticar serviços em uma configuração de desenvolvimento de computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Implantar a extensão de diagnóstico
A primeira etapa na coleta de logs é implantar a extensão de diagnóstico em cada uma das VMs no Cluster Service Fabric. A extensão de diagnóstico coleta logs em cada VM e os carrega na conta de armazenamento que você especificar. 

Para implantar a extensão de diagnóstico nas VMs no cluster como parte da criação do cluster, defina **diagnóstico** como **ativado**. Depois de criar o cluster, você não pode alterar essa configuração usando o portal, portanto, você precisará fazer as alterações apropriadas no modelo do Resource Manager.

Isso configura o agente do LAD para monitorar os arquivos de log especificados. Sempre que uma nova linha é anexada ao arquivo, ela cria uma entrada syslog que é enviada para o armazenamento (tabela) que você especificou.


## <a name="next-steps"></a>Passos seguintes

1. Para entender mais detalhadamente os eventos que devem ser examinados durante a solução de problemas, consulte a [documentação do LTTng](https://lttng.org/docs) e o [uso do Lad](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux).
2. [Configurar o agente de log Analytics](service-fabric-diagnostics-event-analysis-oms.md) para ajudar a coletar métricas, monitorar contêineres implantados em seu cluster e visualizar seus logs 

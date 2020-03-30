---
title: Agregação de eventos com diagnósticos Linux Azure
description: Aprenda sobre agregação e recolha de eventos usando LAD para monitorização e diagnóstico de clusters Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: fdb78498d33416ef21b2e2b0f498e7afa6a58d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609966"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Agregação e coleção de eventos usando o Linux Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando se gere um cluster azure service fabric, é uma boa ideia recolher os registos de todos os nós num local central. Ter os registos num local central ajuda-o a analisar e resolver problemas no seu cluster, ou problemas nas aplicações e serviços que estão a decorrer nesse cluster.

Uma forma de carregar e recolher registos é utilizar a extensão Linux Azure Diagnostics (LAD), que envia registos para o Armazenamento Azure, e também tem a opção de enviar registos para Insights de Aplicação Azure ou Centros de Eventos. Também pode utilizar um processo externo para ler os eventos a partir do armazenamento e colocá-los num produto de plataforma de análise, como [registos do Monitor Azure](../log-analytics/log-analytics-service-fabric.md) ou outra solução de log-parsing.

## <a name="log-and-event-sources"></a>Fontes de registo e eventos

### <a name="service-fabric-platform-events"></a>Eventos da plataforma Service Fabric
O Serviço Fabric emite alguns registos fora da caixa via [LTTng,](https://lttng.org)incluindo eventos operacionais ou eventos de tempo de funcionamento. Estes registos são armazenados no local que o modelo de Gestor de Recursos do cluster especifica. Para obter ou definir os detalhes da conta de armazenamento, procure a etiqueta **AzureTableWinFabETWQueryable** e procure **storeConnectionString**.

### <a name="application-events"></a>Eventos de aplicação
 Os eventos emitidos do código das suas aplicações e serviços, conforme especificado por si, ao instrumentar o seu software. Pode utilizar qualquer solução de registo que escreva ficheiros de registo baseados em texto-- por exemplo, LTTng. Para mais informações, consulte a documentação ltTng sobre o rastreio da sua aplicação.

[Monitorize e diagnostice serviços numa instalação de desenvolvimento de máquinas local.](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## <a name="deploy-the-diagnostics-extension"></a>Implementar a extensão de Diagnóstico
O primeiro passo na recolha de registos é implementar a extensão de Diagnóstico em cada um dos VMs no cluster Service Fabric. A extensão de Diagnóstico recolhe registos em cada VM e envia-os para a conta de armazenamento que especifica. 

Para implantar a extensão de Diagnóstico sintetizador estoirar os VMs no cluster como parte da criação do cluster, deset **Diagnósticos** para **On**. Depois de criar o cluster, não pode alterar esta definição utilizando o portal, pelo que terá de fazer as alterações adequadas no modelo de Gestor de Recursos.

Isto confunde o agente LAD para monitorizar ficheiros de registo especificados. Sempre que uma nova linha é anexada ao ficheiro, cria uma entrada sintetizada que é enviada para o armazenamento (tabela) que especificou.


## <a name="next-steps"></a>Passos seguintes

1. Para compreender mais detalhadamente que eventos deve examinar enquanto problemas de resolução de problemas, consulte [documentação LTTng](https://lttng.org/docs) e [Use LAD](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux).
2. [Configurar o agente Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) para ajudar a recolher métricas, monitorizar os contentores implantados no seu cluster e visualizar os seus registos 

---
title: Agregação de eventos de recursos de infraestrutura de serviço do Azure com o diagnóstico do Linux do Azure | Documentos da Microsoft
description: Saiba mais sobre a agregação e a recolha de eventos usando LAD para monitorização e diagnóstico de clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: a41599194d6dd6230c80aa078375b798c3002adf
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824016"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Agregação de eventos e coleções com o diagnóstico do Linux do Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando estiver a executar um cluster do Azure Service Fabric, é uma boa idéia para recolher os registos de todos os nós numa localização central. Ter os registos numa localização central ajuda a analisar e resolver problemas no seu cluster, ou problemas em aplicações e serviços em execução nesse cluster.

Uma forma de carregar e recolher registos é utilizar a extensão de diagnóstico de Azure Linux (LAD), que carrega os registos para o armazenamento do Azure e, também tem a opção para enviar registos para o Azure Application Insights ou Hubs de eventos. Também pode utilizar um processo externo para ler os eventos de armazenamento e colocá-los num produto de plataforma de análise, como [do Log Analytics](../log-analytics/log-analytics-service-fabric.md) ou outra solução de análise de registos.

## <a name="log-and-event-sources"></a>Origens de registos e eventos

### <a name="service-fabric-platform-events"></a>Eventos de plataforma do Service Fabric
Service Fabric emite alguns registos de out-of-the-box via [LTTng](http://lttng.org), incluindo eventos operacionais ou eventos de tempo de execução. Estes registos são armazenados na localização que especifica o modelo do Resource Manager do cluster. Para obter ou definir os detalhes da conta de armazenamento, procure a marca **AzureTableWinFabETWQueryable** e procure **StoreConnectionString**.

### <a name="application-events"></a>Eventos da aplicação
 Eventos emitidos a partir do código de seus aplicativos e dos serviços conforme especificado por si quando instrumentar o seu software. Pode usar qualquer solução de registo que escreve os ficheiros de registo baseados em texto, por exemplo, LTTng. Para obter mais informações, consulte a documentação de LTTng no rastreamento de seu aplicativo.

[Monitorizar e diagnosticar serviços numa configuração de desenvolvimento do computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Implementar a extensão de diagnóstico
Recolher registos a primeira etapa é implantar a extensão de diagnóstico em cada uma das VMs no cluster do Service Fabric. A extensão de diagnóstico recolhe registos em cada VM e carrega-os para a conta de armazenamento que especificar. 

Para implementar a extensão de diagnóstico para as VMs no cluster como parte da criação do cluster, defina **diagnóstico** ao **no**. Depois de criar o cluster, é possível alterar esta definição com o portal, precisará fazer as alterações apropriadas no modelo do Resource Manager.

Esta ação configura o agente LAD para monitorizar os ficheiros de registo especificado. Sempre que uma nova linha é anexada ao arquivo, ele cria uma entrada de syslog que é enviada para o armazenamento (tabela) que especificou.


## <a name="next-steps"></a>Passos Seguintes

1. Para entender mais detalhadamente quais eventos, deve examinar durante a resolução de problemas, consulte [documentação LTTng](http://lttng.org/docs) e [LAD usando](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux).
2. [Configurar o agente do Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) para ajudar a recolher métricas, monitorizar contentores implementados no seu cluster e visualizar os seus registos 

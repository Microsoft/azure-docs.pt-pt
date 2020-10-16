---
title: Agregação de eventos com Diagnósticos Linux Azure
description: Saiba mais sobre agregação e recolha de eventos utilizando o LAD para monitorização e diagnóstico de clusters de tecidos de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: 453258bb5768a2faa8f4e42cce86d02125283026
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86259198"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Agregação e recolha de eventos usando Diagnósticos Linux Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando você está executando um cluster de tecido de serviço Azure, é uma boa ideia recolher os registos de todos os nós em um local central. Ter os registos numa localização central ajuda-o a analisar e resolver problemas no seu cluster, ou problemas nas aplicações e serviços que estão a decorrer nesse cluster.

Uma forma de carregar e recolher registos é utilizar a extensão Linux Azure Diagnostics (LAD), que faz o upload de registos para o Azure Storage, e também tem a opção de enviar registos para Azure Application Insights ou Event Hubs. Também pode utilizar um processo externo para ler os eventos a partir do armazenamento e colocá-los num produto da plataforma de análise, como [registos Azure Monitor](./service-fabric-diagnostics-oms-setup.md) ou outra solução de registo.

## <a name="log-and-event-sources"></a>Fontes de registo e evento

### <a name="service-fabric-platform-events"></a>Eventos da plataforma de tecido de serviço
O Service Fabric emite alguns registos fora da caixa via [LTTng,](https://lttng.org)incluindo eventos operacionais ou eventos de tempo de execução. Estes registos são armazenados no local que o modelo de Gestor de Recursos do cluster especifica. Para obter ou definir os detalhes da conta de armazenamento, procure a etiqueta **AzureTableWinFabETWQueryable** e procure **storeConnectionString**.

### <a name="application-events"></a>Eventos de aplicação
 Os eventos emitidos a partir do código das suas aplicações e serviços, conforme especificado por si ao instrumentar o seu software. Pode utilizar qualquer solução de registo que escreva ficheiros de registo baseados em texto- por exemplo, LTTng. Para mais informações, consulte a documentação LTTng sobre o rastreio da sua aplicação.

[Monitorizar e diagnosticar serviços numa configuração local de desenvolvimento de máquinas.](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## <a name="deploy-the-diagnostics-extension"></a>Implementar a extensão de Diagnóstico
O primeiro passo na recolha de registos é a implementação da extensão de Diagnóstico em cada um dos VMs no cluster de Tecido de Serviço. A extensão De Diagnóstico recolhe registos em cada VM e envia-os para a conta de armazenamento que especifica. 

Para implementar a extensão de Diagnóstico aos VMs no cluster como parte da criação de **clusters,** dedipa o Diagnóstico para **On**. Depois de criar o cluster, não pode alterar esta definição utilizando o portal, pelo que terá de escruputurar as alterações adequadas no modelo de Gestor de Recursos.

Isto configura o agente LAD para monitorizar ficheiros de registo especificados. Sempre que uma nova linha é anexada ao ficheiro, cria uma entrada de syslog que é enviada para o armazenamento (tabela) que especificou.


## <a name="next-steps"></a>Passos seguintes

1. Para compreender mais detalhadamente quais os eventos que deve examinar enquanto resolver problemas, consulte a [documentação LTTng](https://lttng.org/docs) e [a utilização do LAD](../virtual-machines/extensions/diagnostics-linux.md).
2. [Configurar o agente Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) para ajudar a recolher métricas, monitorizar os contentores implantados no seu cluster e visualizar os seus registos 

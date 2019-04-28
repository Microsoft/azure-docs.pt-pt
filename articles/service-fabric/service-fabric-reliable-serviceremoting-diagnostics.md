---
title: Azure ServiceFabric de diagnóstico e monitorização | Documentos da Microsoft
description: Este artigo descreve as funcionalidades de monitorização de desempenho em tempo de execução do ServiceRemoting fiáveis do Service Fabric, como contadores de desempenho emitidos por ela.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.openlocfilehash: e4c698dc5aa8a07d09835adeac39db6ee35f720c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123183"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnóstico e monitorização de desempenho para Reliable Service Remoting
O tempo de execução fiável ServiceRemoting emite [contadores de desempenho](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Estes fornecem informações sobre como o ServiceRemoting está a funcionar e ajudar a resolver problemas e monitorização do desempenho.


## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução fiável ServiceRemoting define as categorias de contador de desempenho seguintes:

| Category | Descrição |
| --- | --- |
| Serviço do Service Fabric |Contadores específicos do Azure Service Fabric Service Remoting, por exemplo, média de tempo que demora a processar o pedido |
| Método de Serviço do Service Fabric |Contadores específicos de métodos implementados pelo serviço de comunicação remota do Service Fabric, por exemplo, a frequência com que um método de serviço é invocado |

Cada uma das categorias anteriores tem um ou mais contadores.

O [Monitor de desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) aplicativo que está disponível por predefinição no sistema operacional Windows pode ser usado para recolher e visualizar dados de contador de desempenho. [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para recolher dados de contador de desempenho e carregá-lo para tabelas do Azure.

### <a name="performance-counter-instance-names"></a>Nomes de instâncias do contador de desempenho
Um cluster com um grande número de serviços de ServiceRemoting ou partições têm um grande número de instâncias de contadores de desempenho. Os nomes das instâncias do contador de desempenho podem ajudar a identificar a partição específica e o método de serviço (se aplicável) que a instância do contador de desempenho está associada.

#### <a name="service-fabric-service-category"></a>Categoria de serviço do Service Fabric
Para a categoria `Service Fabric Service`, os nomes de instância do contador estão no seguinte formato:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia de caracteres de ID de partição o Service Fabric que está associada à instância do contador de desempenho. O ID de partição é um GUID e sua representação de cadeia de caracteres é gerada através da [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) método com o especificador de formato "D".

*ServiceReplicaOrInstanceId* é a representação de cadeia de caracteres do ID de réplica/instância de recursos de infraestrutura de serviço que a instância do contador de desempenho está associada.

*ServiceRuntimeInternalID* é a representação de cadeia de caracteres de um inteiro de 64 bits que é gerado pelo runtime do Fabric Service para uso interno. Isso está incluído no nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não devem tentar interpretar essa parte do nome de instância do contador de desempenho.

Segue-se um exemplo de um nome de instância do contador de um contador que pertence o `Service Fabric Service` categoria:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

No exemplo anterior, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação de cadeia de caracteres de ID de partição Service Fabric, `635650083799324046` é a representação de cadeia de caracteres de réplica/InstanceId e `5008379932` é usar o ID de 64 bits que é gerado para o tempo de execução interno.

#### <a name="service-fabric-service-method-category"></a>Categoria de método de serviço do Service Fabric
Para a categoria `Service Fabric Service Method`, os nomes de instância do contador estão no seguinte formato:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* é o nome do método de serviço que a instância do contador de desempenho está associada. O formato do nome do método é determinado com base em algumas lógicas no tempo de execução do serviço de recursos de infraestrutura que equilibra a legibilidade do nome com restrições ao comprimento máximo dos nomes de instância do contador de desempenho no Windows.

*ServiceRuntimeMethodId* é a representação de cadeia de caracteres de um inteiro de 32 bits que é gerado pelo runtime do Fabric Service para uso interno. Isso está incluído no nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não devem tentar interpretar essa parte do nome de instância do contador de desempenho.

*ServiceFabricPartitionID* é a representação de cadeia de caracteres de ID de partição o Service Fabric que está associada à instância do contador de desempenho. O ID de partição é um GUID e sua representação de cadeia de caracteres é gerada através da [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) método com o especificador de formato "D".

*ServiceReplicaOrInstanceId* é a representação de cadeia de caracteres do ID de réplica/instância de recursos de infraestrutura de serviço que a instância do contador de desempenho está associada.

*ServiceRuntimeInternalID* é a representação de cadeia de caracteres de um inteiro de 64 bits que é gerado pelo runtime do Fabric Service para uso interno. Isso está incluído no nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não devem tentar interpretar essa parte do nome de instância do contador de desempenho.

Segue-se um exemplo de um nome de instância do contador de um contador que pertence o `Service Fabric Service Method` categoria:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

No exemplo anterior, `ivoicemailboxservice.leavemessageasync` corresponde ao nome do método `2` é o ID de 32 bits gerado para utilização interna do tempo de execução, `89383d32-e57e-4a9b-a6ad-57c6792aa521` é a representação de cadeia de caracteres de ID de partição Service Fabric,`635650083804480486` é a representação de cadeia de caracteres de o ID de réplica/instância de recursos de infraestrutura do serviço e `5008380` é usar o ID de 64 bits gerado para o tempo de execução interno.

## <a name="list-of-performance-counters"></a>Lista de contadores de desempenho
### <a name="service-method-performance-counters"></a>Contadores de desempenho do método de serviço

O tempo de execução de Reliable Service publica os seguintes contadores de desempenho relacionados com a execução de métodos de serviço.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Método de Serviço do Service Fabric |Invocações/Segundo |Número de vezes que o método de serviço é invocado por segundo |
| Método de Serviço do Service Fabric |Média em milissegundos por invocação |Tempo decorrido para executar o método de serviço em milissegundos |
| Método de Serviço do Service Fabric |Exceções geradas/Segundo |Número de vezes que o método de serviço emitiu uma exceção por segundo |

### <a name="service-request-processing-performance-counters"></a>Contadores de desempenho de processamento de pedido de serviço
Quando um cliente invoca um método por meio de um objeto de proxy de serviço, que resulta numa mensagem de solicitação a ser enviada através da rede para o serviço de comunicação remota. O serviço processa a mensagem de solicitação e envia uma resposta de volta ao cliente. O tempo de execução fiável ServiceRemoting publica os seguintes contadores de desempenho relacionados com o processamento de pedidos de serviço.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Serviço do Service Fabric |N.º de pedidos pendentes |Número de pedidos em processamento no serviço |
| Serviço do Service Fabric |Média em milissegundos por pedido |Tempo decorrido (em milissegundos) pelo serviço para processar um pedido |
| Serviço do Service Fabric |Média em milissegundos para a desserialização do pedido |Tempo decorrido (em milissegundos) para anular a serialização de mensagem de pedido de serviço quando ele é recebido no serviço |
| Serviço do Service Fabric |Média em milissegundos para a serialização da resposta |Tempo decorrido (em milissegundos) para serializar a mensagem de resposta do serviço no serviço antes da resposta é enviada ao cliente |

## <a name="next-steps"></a>Passos Seguintes
* [Código de exemplo](https://azure.microsoft.com/en-us/resources/samples/?service=service-fabric&sort=0)
* [Fornecedores de EventSource na PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)

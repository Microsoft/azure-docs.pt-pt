---
title: Diagnóstico e monitoramento do Azure infabric | Microsoft Docs
description: Este artigo descreve os recursos de monitoramento de desempenho no Service Fabric tempo de execução confiável do Reliable, como contadores de desempenho emitidos por ele.
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
ms.author: chackdan
ms.openlocfilehash: 4e9aa2bbb99cac2ffc2b57ccb9299bf4ee7a729e
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876267"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnóstico e monitoramento de desempenho para comunicação remota do serviço confiável
O tempo de execução confiável do reremoting emite [contadores de desempenho](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Eles fornecem informações sobre como a imcomunicação remota está operando e ajudam na solução de problemas e no monitoramento de desempenho.


## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução confiável do reremoting define as seguintes categorias de contador de desempenho:

| Category | Descrição |
| --- | --- |
| Serviço de Service Fabric |Contadores específicos para comunicação remota do serviço de Service Fabric do Azure, por exemplo, tempo médio necessário para processar a solicitação |
| Método de serviço Service Fabric |Contadores específicos para métodos implementados por Service Fabric serviço de comunicação remota, por exemplo, com que frequência um método de serviço é invocado |

Cada uma das categorias anteriores tem um ou mais contadores.

O aplicativo de [Monitor de desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) que está disponível por padrão no sistema operacional Windows pode ser usado para coletar e exibir dados do contador de desempenho. [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para coletar dados do contador de desempenho e carregá-los em tabelas do Azure.

### <a name="performance-counter-instance-names"></a>Nomes de instância do contador de desempenho
Um cluster que tem um grande número de serviços ou partições de serviço de comunicação tem um grande número de instâncias de contador de desempenho. Os nomes de instância do contador de desempenho podem ajudar a identificar a partição específica e o método de serviço (se aplicável) a que a instância do contador de desempenho está associada.

#### <a name="service-fabric-service-category"></a>Categoria de serviço Service Fabric
Para a categoria `Service Fabric Service`, os nomes de instância do contador estão no seguinte formato:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia de caracteres da ID de partição de Service Fabric à qual a instância do contador de desempenho está associada. A ID de partição é um GUID, e sua representação de cadeia de caracteres [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) é gerada por meio do método com o especificador de formato "D".

*ServiceReplicaOrInstanceId* é a representação de cadeia de caracteres do Service Fabric ID de réplica/instância à qual a instância do contador de desempenho está associada.

*ServiceRuntimeInternalID* é a representação de cadeia de caracteres de um inteiro de 64 bits que é gerado pelo tempo de execução do serviço de malha para seu uso interno. Isso é incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

Veja a seguir um exemplo de um nome de instância de contador para um contador que pertence `Service Fabric Service` à categoria:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

No exemplo anterior, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação de cadeia de caracteres da ID de partição de Service Fabric, `635650083799324046` é representação de cadeia de `5008379932` caracteres de réplica/InstanceId e é a ID de 64 bits que é gerada para uso interno do tempo de execução.

#### <a name="service-fabric-service-method-category"></a>Categoria do método de serviço Service Fabric
Para a categoria `Service Fabric Service Method`, os nomes de instância do contador estão no seguinte formato:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* é o nome do método de serviço ao qual a instância do contador de desempenho está associada. O formato do nome do método é determinado com base em alguma lógica no tempo de execução do serviço de malha que equilibra a legibilidade do nome com restrições no tamanho máximo dos nomes de instância do contador de desempenho no Windows.

*ServiceRuntimeMethodId* é a representação de cadeia de caracteres de um inteiro de 32 bits que é gerado pelo tempo de execução do serviço de malha para seu uso interno. Isso é incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

*ServiceFabricPartitionID* é a representação de cadeia de caracteres da ID de partição de Service Fabric à qual a instância do contador de desempenho está associada. A ID de partição é um GUID, e sua representação de cadeia de caracteres [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) é gerada por meio do método com o especificador de formato "D".

*ServiceReplicaOrInstanceId* é a representação de cadeia de caracteres do Service Fabric ID de réplica/instância à qual a instância do contador de desempenho está associada.

*ServiceRuntimeInternalID* é a representação de cadeia de caracteres de um inteiro de 64 bits que é gerado pelo tempo de execução do serviço de malha para seu uso interno. Isso é incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

Veja a seguir um exemplo de um nome de instância de contador para um contador que pertence `Service Fabric Service Method` à categoria:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

`ivoicemailboxservice.leavemessageasync` No exemplo anterior, é o nome do método, `2` é a ID de 32 bits gerada para uso interno do tempo de execução, `89383d32-e57e-4a9b-a6ad-57c6792aa521` é a representação de cadeia de caracteres da ID`635650083804480486` de partição de Service Fabric, é a representação de cadeia de caracteres de o Service Fabric a ID de réplica/ `5008380` instância e é a ID de 64 bits gerada para uso interno do tempo de execução.

## <a name="list-of-performance-counters"></a>Lista de contadores de desempenho
### <a name="service-method-performance-counters"></a>Contadores de desempenho do método de serviço

O tempo de execução do serviço confiável publica os seguintes contadores de desempenho relacionados à execução de métodos de serviço.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Método de serviço Service Fabric |Invocações/s |Número de vezes que o método de serviço é invocado por segundo |
| Método de serviço Service Fabric |Média de milissegundos por invocação |Tempo necessário para executar o método de serviço em milissegundos |
| Método de serviço Service Fabric |Exceções geradas/s |Número de vezes que o método de serviço lançou uma exceção por segundo |

### <a name="service-request-processing-performance-counters"></a>Contadores de desempenho de processamento de solicitação de serviço
Quando um cliente chama um método por meio de um objeto de proxy de serviço, ele resulta em uma mensagem de solicitação sendo enviada pela rede para o serviço de comunicação remota. O serviço processa a mensagem de solicitação e envia uma resposta de volta ao cliente. O tempo de execução confiável do reremoting publica os seguintes contadores de desempenho relacionados ao processamento da solicitação de serviço.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Serviço de Service Fabric |n º de solicitações pendentes |Número de solicitações sendo processadas no serviço |
| Serviço de Service Fabric |Média de milissegundos por solicitação |Tempo decorrido (em milissegundos) pelo serviço para processar uma solicitação |
| Serviço de Service Fabric |Média de milissegundos para desserialização de solicitação |Tempo decorrido (em milissegundos) para desserializar a mensagem de solicitação de serviço quando ela é recebida no serviço |
| Serviço de Service Fabric |Média de milissegundos para serialização de resposta |Tempo decorrido (em milissegundos) para serializar a mensagem de resposta do serviço no serviço antes que a resposta seja enviada ao cliente |

## <a name="next-steps"></a>Passos Seguintes
* [Código de exemplo](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [Provedores de EventSource no PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)

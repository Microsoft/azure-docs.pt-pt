---
title: Diagnóstico e monitorização do Azure ServiceFabric
description: Este artigo descreve as funcionalidades de monitorização de desempenho no tempo de funcionamento do Service Reliable ServiceRemoting do Tecido de Serviço, como contadores de desempenho emitidos por ele.
author: suchiagicha
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: pepogors
ms.openlocfilehash: 9c7d466d6e8fd36b4445966b92ee753becf96c64
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791766"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnóstico e monitorização de desempenho para remoting de serviços fiáveis
O tempo de execução Reliable ServiceRemoting emite  [contadores de desempenho](/dotnet/api/system.diagnostics.performancecounter). Estes fornecem informações sobre como o ServiceRemoting está a funcionar e ajudam na resolução de problemas e na monitorização do desempenho.


## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução Reliable ServiceRemoting define as seguintes categorias de contador de desempenho:

| Categoria | Descrição |
| --- | --- |
| Serviço de Tecido de Serviço |Contadores específicos do Azure Service Fabric Service Remoting, por exemplo, tempo médio tomado para processar pedido |
| Método de serviço de serviço de tecido |Contadores específicos aos métodos implementados pelo Service Fabric Remoting Service, por exemplo, quantas vezes um método de serviço é invocado |

Cada uma das categorias anteriores tem um ou mais contadores.

A aplicação [Windows Performance Monitor](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) que está disponível por padrão no sistema operativo Windows pode ser usada para recolher e visualizar dados de contador de desempenho. [O Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para recolher dados de contador de desempenho e enviá-lo para as tabelas Azure.

### <a name="performance-counter-instance-names"></a>Nomes de contra-instância de desempenho
Um cluster que tem um grande número de serviços de ServiceRemoting ou divisórias tem um grande número de contra-casos de desempenho. Os nomes do contador de desempenho podem ajudar a identificar o método específico de partição e serviço (se aplicável) a que a instância do contador de desempenho está associada.

#### <a name="service-fabric-service-category"></a>Categoria serviço de tecido de serviço
Para a `Service Fabric Service` categoria, os nomes de contraexemplo estão no seguinte formato:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia do ID de partição do Tecido de Serviço a que o contador de desempenho está associado. O ID de partição é um GUID, e a sua representação de cordas é gerada através do método com o [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) especificador de formato "D".

*ServiceReplicaOrInstanceId* é a representação de cadeia da réplica de tecido de serviço/ID de instância a que a instância do contador de desempenho está associada.

*ServiceRuntimeInternalID* é a representação de uma corda de um inteiro de 64 bits que é gerado pelo tempo de execução do Serviço de Tecido para o seu uso interno. Isto está incluído no nome do contraexemplo de desempenho para garantir a sua singularidade e evitar conflitos com outros nomes de contra-instância de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome do contador de desempenho.

Segue-se um exemplo de um nome de contra-instância para um contador que pertence à `Service Fabric Service` categoria:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

No exemplo anterior, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação de cadeia do ID de partição de tecido de serviço, `635650083799324046` é a representação de cadeia de Replica/InstanceId e `5008379932` é o ID de 64 bits que é gerado para o uso interno do tempo de execução.

#### <a name="service-fabric-service-method-category"></a>Categoria de Método de Serviço de Serviço de Tecido de Serviço
Para a `Service Fabric Service Method` categoria, os nomes de contraexemplo estão no seguinte formato:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* é o nome do método de serviço a que o contador de desempenho está associado. O formato do nome do método é determinado com base em alguma lógica no tempo de funcionamento do Serviço de Tecido que equilibra a legibilidade do nome com restrições no comprimento máximo dos nomes de instância de contador de desempenho no Windows.

*ServiceRuntimeMethodId* é a representação de uma corda de um inteiro de 32 bits que é gerado pelo tempo de execução do Serviço de Tecido para o seu uso interno. Isto está incluído no nome do contraexemplo de desempenho para garantir a sua singularidade e evitar conflitos com outros nomes de contra-instância de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome do contador de desempenho.

*ServiceFabricPartitionID* é a representação de cadeia do ID de partição do Tecido de Serviço a que o contador de desempenho está associado. O ID de partição é um GUID, e a sua representação de cordas é gerada através do método com o [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) especificador de formato "D".

*ServiceReplicaOrInstanceId* é a representação de cadeia da réplica de tecido de serviço/ID de instância a que a instância do contador de desempenho está associada.

*ServiceRuntimeInternalID* é a representação de uma corda de um inteiro de 64 bits que é gerado pelo tempo de execução do Serviço de Tecido para o seu uso interno. Isto está incluído no nome do contraexemplo de desempenho para garantir a sua singularidade e evitar conflitos com outros nomes de contra-instância de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome do contador de desempenho.

Segue-se um exemplo de um nome de contra-instância para um contador que pertence à `Service Fabric Service Method` categoria:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

No exemplo anterior, `ivoicemailboxservice.leavemessageasync` é o nome do método, é o `2` ID de 32 bits gerado para uso interno do tempo `89383d32-e57e-4a9b-a6ad-57c6792aa521` de execução, é a representação de cadeia do ID de partição de tecido de serviço, `635650083804480486` é a representação de cadeia do ID de réplica de tecido de serviço/iD de instância e `5008380` é o ID de 64 bits gerado para o uso interno do tempo de execução.

## <a name="list-of-performance-counters"></a>Lista de contadores de desempenho
### <a name="service-method-performance-counters"></a>Contadores de desempenho do método de serviço

O tempo de execução do Serviço Fiável publica os seguintes contadores de desempenho relacionados com a execução de métodos de serviço.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Método de serviço de serviço de tecido |Invocações/Sec |Número de vezes que o método de serviço é invocado por segundo |
| Método de serviço de serviço de tecido |Milissegundos médios por invocação |Tempo necessário para executar o método de serviço em milissegundos |
| Método de serviço de serviço de tecido |Exceções lançadas/Sec |Número de vezes que o método de serviço atirou uma exceção por segundo |

### <a name="service-request-processing-performance-counters"></a>Pedido de serviço processamento de contadores de desempenho
Quando um cliente invoca um método através de um objeto de procuração de serviço, resulta em uma mensagem de pedido enviada através da rede para o serviço de remoting. O serviço processa a mensagem de pedido e envia uma resposta ao cliente. O tempo de execução Reliable ServiceRemoting publica os seguintes contadores de desempenho relacionados com o processamento de pedidos de serviço.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Serviço de Tecido de Serviço |# de pedidos pendentes |Número de pedidos a ser processados no serviço |
| Serviço de Tecido de Serviço |Milissegundos médios por pedido |Tempo tomado (em milissegundos) pelo serviço para processar um pedido |
| Serviço de Tecido de Serviço |Milissegundos médios para pedido de deserialização |Tempo demorado (em milissegundos) para deserializar a mensagem de pedido de serviço quando é recebida no serviço |
| Serviço de Tecido de Serviço |Milissegundos médios para serialização de resposta |Tempo demorado (em milissegundos) para serializar a mensagem de resposta de serviço no serviço antes de a resposta ser enviada ao cliente |

## <a name="next-steps"></a>Próximos passos
* [Código de amostra](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [Fornecedores de EventSource em PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)

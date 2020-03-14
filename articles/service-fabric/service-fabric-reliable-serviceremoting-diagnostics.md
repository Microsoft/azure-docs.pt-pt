---
title: Diagnósticos e monitorização de Serviços Azure
description: Este artigo descreve as funcionalidades de monitorização do desempenho no tempo de funcionamento do Serviço De Assistência Fiável de ServiçoRemo, como contadores de desempenho emitidos por ele.
author: suchiagicha
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: pepogors
ms.openlocfilehash: 31095a619fc4d756fa4ef9c29691d1d511d59ece
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282279"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnósticos e monitorização de desempenho para Remoting de Serviço Fiável
O tempo de execução fiável do serviço Remoting emite [contadores de desempenho](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Estes fornecem informações sobre como o ServiceRemoting está a funcionar e ajudam na resolução de problemas e monitorização do desempenho.


## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução fiável do Serviço Remoting define as seguintes categorias de contra-desempenho:

| Categoria | Descrição |
| --- | --- |
| Serviço de Tecido de Serviço |Contadores específicos do Serviço de Tecido sinuoso do Serviço Azure, por exemplo, tempo médio tomado para processar pedido |
| Método de Serviço de Serviço de Tecido |Contadores específicos dos métodos implementados pelo Serviço de Remo de Tecidos de Serviço, por exemplo, quantas vezes um método de serviço é invocado |

Cada uma das categorias anteriores tem um ou mais balcões.

A aplicação [Do Windows Performance Monitor,](https://technet.microsoft.com/library/cc749249.aspx) que está disponível por padrão no sistema operativo Windows, pode ser utilizada para recolher e visualizar dados de contra-desempenho. [O Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para recolher dados de contador de desempenho e carregá-lo para as tabelas Azure.

### <a name="performance-counter-instance-names"></a>Nomes de contra-instância de desempenho
Um cluster que tem um grande número de serviços de Remoting de Serviçoou divisórias tem um grande número de contra-instâncias de desempenho. Os nomes de contra-instância de desempenho podem ajudar na identificação do método específico de partição e de serviço (se aplicável) com o qual a contra-instância de desempenho está associada.

#### <a name="service-fabric-service-category"></a>Categoria serviço de tecido de serviço
Para a categoria `Service Fabric Service`, os nomes de contra-instância estão no seguinte formato:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia do ID de partição de tecido de serviço a que a contra-instância de desempenho está associada. O ID de partição é um GUID, e a sua representação de cordas é gerada através do método [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

*ServiceReplicaOrInstanceId* é a representação de cadeia da Réplica/ID de instância de tecido de serviço a que a contra-instância de desempenho está associada.

*ServiceRuntimeInternalID* é a representação de cordas de um inteiro de 64 bits que é gerado pelo tempo de funcionação do Serviço de Tecidopara a sua utilização interna. Isto está incluído no nome de contador de desempenho para garantir a sua singularidade e evitar conflitos com outros nomes de contra-instância de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome da contra-instância de desempenho.

Segue-se um exemplo de um nome de contra-instância para um contador que pertence à categoria `Service Fabric Service`:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

No exemplo anterior, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação de cordas do ID de divisória de tecido de serviço, `635650083799324046` é representação de cordas de Replica/InstanceId e `5008379932` é o ID de 64 bits que é gerado para o uso interno do tempo de execução.

#### <a name="service-fabric-service-method-category"></a>Categoria de Método de Serviço de Tecido de Serviço
Para a categoria `Service Fabric Service Method`, os nomes de contra-instância estão no seguinte formato:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* é o nome do método de serviço a que a contra-instância de desempenho está associada. O formato do nome do método é determinado com base em alguma lógica no tempo de execução do Serviço de Tecidos que equilibra a legibilidade do nome com restrições no comprimento máximo dos nomes de contadores de desempenho no Windows.

*ServiceRuntimeMethodId* é a representação de uma prótese de 32 bits que é gerada pelo tempo de funcionação do Serviço de Tecidos para a sua utilização interna. Isto está incluído no nome de contador de desempenho para garantir a sua singularidade e evitar conflitos com outros nomes de contra-instância de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome da contra-instância de desempenho.

*ServiceFabricPartitionID* é a representação de cadeia do ID de partição de tecido de serviço a que a contra-instância de desempenho está associada. O ID de partição é um GUID, e a sua representação de cordas é gerada através do método [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

*ServiceReplicaOrInstanceId* é a representação de cadeia da Réplica/ID de instância de tecido de serviço a que a contra-instância de desempenho está associada.

*ServiceRuntimeInternalID* é a representação de cordas de um inteiro de 64 bits que é gerado pelo tempo de funcionação do Serviço de Tecidopara a sua utilização interna. Isto está incluído no nome de contador de desempenho para garantir a sua singularidade e evitar conflitos com outros nomes de contra-instância de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome da contra-instância de desempenho.

Segue-se um exemplo de um nome de contra-instância para um contador que pertence à categoria `Service Fabric Service Method`:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

No exemplo anterior, `ivoicemailboxservice.leavemessageasync` é o nome do método, `2` é o ID de 32 bits gerado para o uso interno do tempo de execução, `89383d32-e57e-4a9b-a6ad-57c6792aa521` é a representação de cordas do ID de divisória de tecido de serviço,`635650083804480486` é a representação de cadeia do Id de réplica/instância de tecido de serviço e `5008380` é o ID de 64 bits gerado para o uso interno do tempo de execução.

## <a name="list-of-performance-counters"></a>Lista de contadores de desempenho
### <a name="service-method-performance-counters"></a>Contadores de desempenho do método de serviço

O tempo de execução do Serviço Fiável publica os seguintes contadores de desempenho relacionados com a execução de métodos de serviço.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Método de Serviço de Serviço de Tecido |Invocações/Sec |Número de vezes que o método de serviço é invocado por segundo |
| Método de Serviço de Serviço de Tecido |Milissegundos médios por invocação |Tempo deexecução o método de serviço em milissegundos |
| Método de Serviço de Serviço de Tecido |Exceções lançadas/Sec |Número de vezes que o método de serviço lançou uma exceção por segundo |

### <a name="service-request-processing-performance-counters"></a>Contadores de desempenho de processamento de pedidos de serviço
Quando um cliente invoca um método através de um objeto de procuração de serviço, resulta na envio de uma mensagem de pedido através da rede para o serviço de remo. O serviço processa a mensagem de pedido e envia uma resposta ao cliente. O tempo de execução do Serviço Fiável Remoting publica os seguintes contadores de desempenho relacionados com o processamento de pedidos de serviço.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Serviço de Tecido de Serviço |# de pedidos pendentes |Número de pedidos a serem processados no serviço |
| Serviço de Tecido de Serviço |Milissegundos médios por pedido |Tempo tomado (em milissegundos) pelo serviço para processar um pedido |
| Serviço de Tecido de Serviço |Milissegundos médios para desserialização de pedidos |Tempo descontado (em milissegundos) para desserializar mensagem de pedido de serviço quando é recebida no serviço |
| Serviço de Tecido de Serviço |Milissegundos médios para a serialização da resposta |Tempo demorou (em milissegundos) a serializar a mensagem de resposta de serviço no serviço antes de a resposta ser enviada ao cliente |

## <a name="next-steps"></a>Passos seguintes
* [Código de exemplo](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [Fornecedores de EventSource em PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)

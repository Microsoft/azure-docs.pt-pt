---
title: Eventos CLI-sfctl de tecido de serviço Azure
description: Descreve os comandos de eventos CLI sfctl de tecido de serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76906100"
---
# <a name="sfctl-events"></a>eventos sfctl
Recupere os eventos da loja de eventos (se o serviço EventStore já estiver instalado).

O serviço de sistema EventStore pode ser adicionado através de uma atualização config a qualquer cluster SFRP em execução >=6.4. Verifique o seguinte url \: https \: //docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| lista de todas as aplicações | Obtém todos os eventos relacionados com aplicações. |
| all-nosdes-lista | Obtém todos os eventos relacionados com os Nodes. |
| all-partitions-list | Obtém todos os eventos relacionados com partições. |
| lista de todos os serviços | Obtém todos os eventos relacionados com serviços. |
| lista de aplicações | Obtém eventos relacionados com a aplicação. |
| lista de clusters | Obtém todos os eventos relacionados com o Cluster. |
| lista de nó | Obtém um evento relacionado com nó. |
| partição-todas as réplicas-lista | Obtém todos os eventos relacionados com réplicas para uma partição. |
| lista de divisórias | Obtém um evento relacionado com partição. |
| partição-réplica-lista | Obtém um evento relacionado com a réplica da partição. |
| lista de serviços | Obtém um evento relacionado com o serviço. |

## <a name="sfctl-events-all-applications-list"></a>eventos sfctl todos os pedidos-lista
Obtém todos os eventos relacionados com aplicações.

A resposta é a lista de objetos ApplicationEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --tempo de início-utc [Obrigatório] | A hora de início de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma vírgula separada que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovada. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações correlatedEvents se for aprovada. caso contrário, os Eventos CorrelationEvents são processados e o campo HasCorrelatedEvents em cada FabricEvent é povoado. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-events-all-nodes-list"></a>eventos sfctl all-nodes-list
Obtém todos os eventos relacionados com os Nodes.

A resposta é a lista de objetos NodeEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --tempo de início-utc [Obrigatório] | A hora de início de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma vírgula separada que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovada. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações correlatedEvents se for aprovada. caso contrário, os Eventos CorrelationEvents são processados e o campo HasCorrelatedEvents em cada FabricEvent é povoado. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-events-all-partitions-list"></a>eventos sfctl all-partitions-list
Obtém todos os eventos relacionados com partições.

A resposta é a lista de objetos PartitionEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --tempo de início-utc [Obrigatório] | A hora de início de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma vírgula separada que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovada. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações correlatedEvents se for aprovada. caso contrário, os Eventos CorrelationEvents são processados e o campo HasCorrelatedEvents em cada FabricEvent é povoado. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-events-all-services-list"></a>eventos sfctl todos os serviços-lista
Obtém todos os eventos relacionados com serviços.

A resposta é a lista de objetos ServiceEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --tempo de início-utc [Obrigatório] | A hora de início de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma vírgula separada que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovada. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações correlatedEvents se for aprovada. caso contrário, os Eventos CorrelationEvents são processados e o campo HasCorrelatedEvents em cada FabricEvent é povoado. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-events-application-list"></a>sfctl eventos lista de aplicações
Obtém eventos relacionados com a aplicação.

A resposta é a lista de objetos ApplicationEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --tempo de início-utc [Obrigatório] | A hora de início de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma vírgula separada que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovada. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações correlatedEvents se for aprovada. caso contrário, os Eventos CorrelationEvents são processados e o campo HasCorrelatedEvents em cada FabricEvent é povoado. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-events-cluster-list"></a>sfctl eventos cluster-list
Obtém todos os eventos relacionados com o Cluster.

A resposta é a lista de objetos ClusterEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --tempo de início-utc [Obrigatório] | A hora de início de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma vírgula separada que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovada. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações correlatedEvents se for aprovada. caso contrário, os Eventos CorrelationEvents são processados e o campo HasCorrelatedEvents em cada FabricEvent é povoado. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-events-node-list"></a>sfctl eventos node-list
Obtém um evento relacionado com nó.

A resposta é a lista de objetos NodeEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --tempo de início-utc [Obrigatório] | A hora de início de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma vírgula separada que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovada. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações correlatedEvents se for aprovada. caso contrário, os Eventos CorrelationEvents são processados e o campo HasCorrelatedEvents em cada FabricEvent é povoado. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl eventos partition-all-replicas-lista
Obtém todos os eventos relacionados com réplicas para uma partição.

A resposta é a lista de objetos ReplicaEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --tempo de início-utc [Obrigatório] | A hora de início de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma vírgula separada que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovada. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações correlatedEvents se for aprovada. caso contrário, os Eventos CorrelationEvents são processados e o campo HasCorrelatedEvents em cada FabricEvent é povoado. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-events-partition-list"></a>sfctl eventos partition-list
Obtém um evento relacionado com partição.

A resposta é a lista de objetos PartitionEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --tempo de início-utc [Obrigatório] | A hora de início de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma vírgula separada que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovada. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações correlatedEvents se for aprovada. caso contrário, os Eventos CorrelationEvents são processados e o campo HasCorrelatedEvents em cada FabricEvent é povoado. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl eventos partition-replica-lista
Obtém um evento relacionado com a réplica da partição.

A resposta é a lista de objetos ReplicaEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
| --tempo de início-utc [Obrigatório] | A hora de início de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma vírgula separada que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovada. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações correlatedEvents se for aprovada. caso contrário, os Eventos CorrelationEvents são processados e o campo HasCorrelatedEvents em cada FabricEvent é povoado. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-events-service-list"></a>sfctl eventos lista de serviços
Obtém um evento relacionado com o serviço.

A resposta é a lista de objetos ServiceEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --tempo de início-utc [Obrigatório] | A hora de início de uma consulta de procura em ISO UTC yyyy-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma vírgula separada que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovada. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações correlatedEvents se for aprovada. caso contrário, os Eventos CorrelationEvents são processados e o campo HasCorrelatedEvents em cada FabricEvent é povoado. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


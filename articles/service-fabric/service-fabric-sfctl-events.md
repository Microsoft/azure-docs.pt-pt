---
title: CLI do Azure Service Fabric-eventos sfctl | Microsoft Docs
description: Descreve os comandos Service Fabric CLI sfctl Events.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 858fd1971a22b1db2d243838558c3792d3a60cc9
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901979"
---
# <a name="sfctl-events"></a>eventos de sfctl
Recupere eventos do repositório de eventos (se o serviço EventStore já estiver instalado).

O serviço do sistema EventStore pode ser adicionado por meio de uma atualização de configuração para qualquer cluster SFRP que esteja executando > = 6.4. Verifique a URL a seguir\: https\://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| todos os aplicativos – lista | Obtém todos os eventos relacionados a aplicativos. |
| todos os nós-lista | Obtém todos os eventos relacionados a nós. |
| todas as partições-lista | Obtém todos os eventos relacionados a partições. |
| todos os serviços-lista | Obtém todos os eventos relacionados a serviços. |
| lista de aplicativos | Obtém eventos relacionados ao aplicativo. |
| lista de clusters | Obtém todos os eventos relacionados ao cluster. |
| lista de nós | Obtém eventos relacionados a nós. |
| partição-todas as réplicas-lista | Obtém todos os eventos relacionados a réplicas para uma partição. |
| lista de partições | Obtém eventos relacionados à partição. |
| partição-réplica-List | Obtém eventos relacionados à réplica de partição. |
| lista de serviços | Obtém eventos relacionados ao serviço. |

## <a name="sfctl-events-all-applications-list"></a>sfctl eventos All-Applications-List
Obtém todos os eventos relacionados a aplicativos.

A resposta é uma lista de objetos ApplicationEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl eventos todos-nós-lista
Obtém todos os eventos relacionados a nós.

A resposta é uma lista de objetos NodeEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl eventos All-Partitions-List
Obtém todos os eventos relacionados a partições.

A resposta é uma lista de objetos PartitionEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-events-all-services-list"></a>sfctl eventos All-lista de serviços
Obtém todos os eventos relacionados a serviços.

A resposta é uma lista de objetos irregulares.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-events-application-list"></a>aplicativo de eventos sfctl – lista
Obtém eventos relacionados ao aplicativo.

A resposta é uma lista de objetos ApplicationEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-events-cluster-list"></a>lista de clusters de eventos sfctl
Obtém todos os eventos relacionados ao cluster.

A resposta é uma lista de objetos ClusterEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-events-node-list"></a>lista de nós de eventos do sfctl
Obtém eventos relacionados a nós.

A resposta é uma lista de objetos NodeEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --node-Name [obrigatório] | O nome do nó. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl de eventos partição-todas as réplicas-lista
Obtém todos os eventos relacionados a réplicas para uma partição.

A resposta é uma lista de objetos ReplicaEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --Partition-ID [obrigatório] | A identidade da partição. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-events-partition-list"></a>partição de eventos sfctl – lista
Obtém eventos relacionados à partição.

A resposta é uma lista de objetos PartitionEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --Partition-ID [obrigatório] | A identidade da partição. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl de eventos de partição – lista de réplicas
Obtém eventos relacionados à réplica de partição.

A resposta é uma lista de objetos ReplicaEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --Partition-ID [obrigatório] | A identidade da partição. |
| --ID da réplica [obrigatório] | O identificador da réplica. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-events-service-list"></a>serviço de eventos sfctl – lista
Obtém eventos relacionados ao serviço.

A resposta é uma lista de objetos irregulares.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --Service-ID [obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa em ISO UTC AAAA-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


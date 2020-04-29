---
title: Azure Service Fabric CLI- eventos sfctl
description: Descreve os comandos de eventos cli de tecido de serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906100"
---
# <a name="sfctl-events"></a>eventos sfctl
Recupere os eventos da loja de eventos (se o serviço EventStore já estiver instalado).

O sistema EventStore pode ser adicionado através de um upgrade de config a qualquer cluster SFRP que execute >=6.4. Por favor,\: verifique\:o seguinte url https //docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| lista de aplicações | Obtém todos os eventos relacionados com aplicações. |
| lista de todos os nós | Obtém todos os eventos relacionados com nós. |
| lista de divisórias | Obtém todos os eventos relacionados com divisórias. |
| lista de todos os serviços | Obtém todos os eventos relacionados com os Serviços. |
| lista de aplicações | Obtém um evento relacionado com aplicações. |
| cluster-list | Obtém todos os eventos relacionados com o Cluster. |
| lista de nó | Tem um evento relacionado com o Nó. |
| divisão-all-replicas-list | Obtém todos os eventos relacionados com réplicas para uma partição. |
| lista de divisórias | Obtém um evento relacionado com a partição. |
| lista de réplicas de divisórias | Obtém uma divisão de eventos relacionados com réplicas. |
| lista de serviços | Obtém um evento relacionado com o serviço. |

## <a name="sfctl-events-all-applications-list"></a>sfctl eventos lista de todas as aplicações
Obtém todos os eventos relacionados com aplicações.

A resposta é a lista de objetos ApplicationEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim do tempo-utc [Obrigatório] | O tempo final de uma consulta de procura ção na ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --início-tempo-utc [Obrigatório] | O tempo de início de uma consulta de procuração na ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia separada de vírem que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| -excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for verdade. |
| -skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se for verdadeiramente passado. caso contrário, os Eventos Correlationevents são processados e o campo HasCorrelatedEvents em cada FabricEvent fica povoado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl eventos all-nodes-list
Obtém todos os eventos relacionados com nós.

A resposta é a lista de objetos NodeEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim do tempo-utc [Obrigatório] | O tempo final de uma consulta de procura ção na ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --início-tempo-utc [Obrigatório] | O tempo de início de uma consulta de procuração na ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia separada de vírem que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| -excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for verdade. |
| -skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se for verdadeiramente passado. caso contrário, os Eventos Correlationevents são processados e o campo HasCorrelatedEvents em cada FabricEvent fica povoado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl eventos lista de todas as divisórias
Obtém todos os eventos relacionados com divisórias.

A resposta é a lista de objetos PartitionEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim do tempo-utc [Obrigatório] | O tempo final de uma consulta de procura ção na ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --início-tempo-utc [Obrigatório] | O tempo de início de uma consulta de procuração na ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia separada de vírem que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| -excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for verdade. |
| -skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se for verdadeiramente passado. caso contrário, os Eventos Correlationevents são processados e o campo HasCorrelatedEvents em cada FabricEvent fica povoado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-events-all-services-list"></a>sfctl eventos all-services-list
Obtém todos os eventos relacionados com os Serviços.

A resposta é a lista de objetos ServiceEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim do tempo-utc [Obrigatório] | O tempo final de uma consulta de procura ção na ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --início-tempo-utc [Obrigatório] | O tempo de início de uma consulta de procuração na ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia separada de vírem que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| -excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for verdade. |
| -skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se for verdadeiramente passado. caso contrário, os Eventos Correlationevents são processados e o campo HasCorrelatedEvents em cada FabricEvent fica povoado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-events-application-list"></a>sfctl eventos application-list
Obtém um evento relacionado com aplicações.

A resposta é a lista de objetos ApplicationEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --fim do tempo-utc [Obrigatório] | O tempo final de uma consulta de procura ção na ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --início-tempo-utc [Obrigatório] | O tempo de início de uma consulta de procuração na ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia separada de vírem que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| -excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for verdade. |
| -skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se for verdadeiramente passado. caso contrário, os Eventos Correlationevents são processados e o campo HasCorrelatedEvents em cada FabricEvent fica povoado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-events-cluster-list"></a>sfctl eventos cluster-list
Obtém todos os eventos relacionados com o Cluster.

A resposta é a lista de objetos ClusterEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim do tempo-utc [Obrigatório] | O tempo final de uma consulta de procura ção na ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --início-tempo-utc [Obrigatório] | O tempo de início de uma consulta de procuração na ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia separada de vírem que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| -excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for verdade. |
| -skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se for verdadeiramente passado. caso contrário, os Eventos Correlationevents são processados e o campo HasCorrelatedEvents em cada FabricEvent fica povoado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-events-node-list"></a>sfctl eventos lista de nó
Tem um evento relacionado com o Nó.

A resposta é a lista de objetos NodeEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim do tempo-utc [Obrigatório] | O tempo final de uma consulta de procura ção na ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --início-tempo-utc [Obrigatório] | O tempo de início de uma consulta de procuração na ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia separada de vírem que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| -excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for verdade. |
| -skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se for verdadeiramente passado. caso contrário, os Eventos Correlationevents são processados e o campo HasCorrelatedEvents em cada FabricEvent fica povoado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl eventos partition-all-replicas-list
Obtém todos os eventos relacionados com réplicas para uma partição.

A resposta é a lista de objetos ReplicaEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim do tempo-utc [Obrigatório] | O tempo final de uma consulta de procura ção na ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --início-tempo-utc [Obrigatório] | O tempo de início de uma consulta de procuração na ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia separada de vírem que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| -excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for verdade. |
| -skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se for verdadeiramente passado. caso contrário, os Eventos Correlationevents são processados e o campo HasCorrelatedEvents em cada FabricEvent fica povoado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-events-partition-list"></a>sfctl eventos partition-list
Obtém um evento relacionado com a partição.

A resposta é a lista de objetos PartitionEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim do tempo-utc [Obrigatório] | O tempo final de uma consulta de procura ção na ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --início-tempo-utc [Obrigatório] | O tempo de início de uma consulta de procuração na ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia separada de vírem que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| -excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for verdade. |
| -skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se for verdadeiramente passado. caso contrário, os Eventos Correlationevents são processados e o campo HasCorrelatedEvents em cada FabricEvent fica povoado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl eventos divisória-réplica-lista
Obtém uma divisão de eventos relacionados com réplicas.

A resposta é a lista de objetos ReplicaEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim do tempo-utc [Obrigatório] | O tempo final de uma consulta de procura ção na ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
| --início-tempo-utc [Obrigatório] | O tempo de início de uma consulta de procuração na ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia separada de vírem que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| -excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for verdade. |
| -skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se for verdadeiramente passado. caso contrário, os Eventos Correlationevents são processados e o campo HasCorrelatedEvents em cada FabricEvent fica povoado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-events-service-list"></a>sfctl eventos lista de serviços
Obtém um evento relacionado com o serviço.

A resposta é a lista de objetos ServiceEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim do tempo-utc [Obrigatório] | O tempo final de uma consulta de procura ção na ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo\:do serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:do serviço for "tecido /myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --início-tempo-utc [Obrigatório] | O tempo de início de uma consulta de procuração na ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia separada de vírem que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| -excluir-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for verdade. |
| -skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se for verdadeiramente passado. caso contrário, os Eventos Correlationevents são processados e o campo HasCorrelatedEvents em cada FabricEvent fica povoado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |


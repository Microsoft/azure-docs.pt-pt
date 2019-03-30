---
title: O Azure Service Fabric CLI sfctl partição | Documentos da Microsoft
description: Descreve os comandos do CLI do Service Fabric sfctl partição.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: f7c9bcc51757100cb1fc957dee12213bc8bf2eec
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666771"
---
# <a name="sfctl-partition"></a>sfctl partition
Consultar e gerir partições de qualquer serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| perda de dados | Esta API irá induza perda de dados para a partição especificada. |
| data-loss-status | Obtém o progresso de uma operação de perda de dados de partição a utilizar com a API de StartDataLoss. |
| saúde | Obtém o estado de funcionamento a partição especificada do Service Fabric. |
| informações | Obtém as informações sobre uma partição do Service Fabric. |
| list | Obtém a lista de partições de um serviço do Service Fabric. |
| carregar | Obtém as informações de carga da partição do Service Fabric especificada. |
| a reposição de carga | Repõe a carga atual de uma partição do Service Fabric. |
| quorum-loss | Induces perda de quórum para uma partição de determinado serviço com estado. |
| quorum-loss-status | Obtém o progresso de uma operação de perda de quórum numa partição a utilizar com a API de StartQuorumLoss. |
| recuperar | Indica ao cluster do Service Fabric que ele deve tentar recuperar uma partição específica que está atualmente parada em perda de quórum. |
| recuperar-all | Indica ao cluster do Service Fabric que ele deve tentar recuperar quaisquer serviços (incluindo os serviços do sistema) que estão atualmente bloqueados em perda de quórum. |
| report-health | Envia um relatório de estado de funcionamento na partição do Service Fabric. |
| restart | Esta API irá reiniciar algumas ou todas as réplicas ou instâncias da partição especificada. |
| restart-status | Obtém o progresso de uma operação de PartitionRestart StartPartitionRestart uma introdução. |
| svc-name | Obtém o nome do serviço do Service Fabric para uma partição. |

## <a name="sfctl-partition-data-loss"></a>perda de dados do partição sfctl
Esta API irá induza perda de dados para a partição especificada.

Aciona uma chamada à API do OnDataLossAsync da partição.  Esta API irá induza perda de dados para a partição especificada. Aciona uma chamada à API do OnDataLoss da partição. Perda de dados real irá depender do DataLossMode especificado.  <br> -PartialDataLoss - apenas um quórum de réplicas são removidos e OnDataLoss seja acionada para a partição mas perda de dados real depende da presença da replicação em andamento.  <br> -FullDataLoss - todas as réplicas são removidas, por conseguinte, todos os dados são perdidos e OnDataLoss é acionado. Esta API só deve ser chamada com um serviço com estado como destino. Não é aconselhada a chamar esta API com um serviço de sistema como o destino.

> [!NOTE] 
> Depois desta API for chamada, ele não pode ser revertido. Chamar CancelOperation apenas interromperá a execução e limpar o estado do sistema interno. Não irá restaurar dados se o comando progrediu suficiente para causar perda de dados. Chame a API de GetDataLossProgress com o mesmo OperationId para devolver informações sobre a operação iniciada com esta API.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – modo de perda de dados [necessário] | Esta enumeração é passada para a API de StartDataLoss para indicar que tipo de perda de dados para induza. |
| – id de operação [necessário] | Um GUID que identifica uma chamada desta API.  Isso é passado para a API de GetProgress correspondente. |
| – id de partição [necessário] | A identidade da partição. |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-data-loss-status"></a>o sfctl partição dados-perda-status
Obtém o progresso de uma operação de perda de dados de partição a utilizar com a API de StartDataLoss.

Obtém o progresso de uma operação de perda de dados ao StartDataLoss, usando o OperationId.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de operação [necessário] | Um GUID que identifica uma chamada desta API.  Isso é passado para a API de GetProgress correspondente. |
| – id de partição [necessário] | A identidade da partição. |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-health"></a>Estado de funcionamento de partição de sfctl
Obtém o estado de funcionamento a partição especificada do Service Fabric.

Utilize EventsHealthStateFilter para filtrar a recolha de eventos de estado de funcionamento comunicadas falhas no serviço com base no estado de funcionamento. Utilize ReplicasHealthStateFilter para filtrar a coleção de objetos de ReplicaHealthState na partição. Se especificar uma partição que não existe no arquivo de estado de funcionamento, este pedido devolve um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário] | A identidade da partição. |
| --events-health-state-filter | Permite a filtragem da coleção de objetos de HealthEvent devolvidos com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregado. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores, obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, todos os eventos com o valor HealthState OK (2) e (4) de aviso são devolvidos.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| --exclude-health-statistics | Indica se as estatísticas de estado de funcionamento devem ser devolvidas como parte do resultado da consulta. FALSE por padrão. As estatísticas mostram o número de subordinados entidades no estado de funcionamento Ok, aviso e erro. |
| --replicas-health-state-filter | Permite a filtragem da coleção de objetos de ReplicaHealthState na partição. O valor pode ser obtido de membros ou operações bit a bit em membros de HealthStateFilter. Apenas as réplicas que correspondem ao filtro vão ser devolvidas. Todas as réplicas serão utilizadas para avaliar o estado de funcionamento agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, todos os eventos com o valor HealthState OK (2) e de aviso (4) vão ser devolvidos. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-info"></a>informações de partição de sfctl
Obtém as informações sobre uma partição do Service Fabric.

Obtém as informações sobre a partição especificada. A resposta inclui o ID de partição, informações de esquema de particionamento, chaves suportadas pela partição, estado, integridade e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário] | A identidade da partição. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-list"></a>lista de partição de sfctl
Obtém a lista de partições de um serviço do Service Fabric.

A resposta inclui o ID de partição, informações de esquema de particionamento, chaves suportadas pela partição, estado, integridade e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| --continuation-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API, quando os resultados do sistema não se encaixam numa única resposta. Quando esse valor é passado para a próxima chamada de API, a API devolve o próximo conjunto de resultados. Se não existirem mais resultados, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificada com URL. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-load"></a>carga de partição de sfctl
Obtém as informações de carga da partição do Service Fabric especificada.

Devolve informações sobre a carga de uma partição especificada. A resposta inclui uma lista de relatórios de carga para uma partição do Service Fabric. Cada relatório inclui o nome da métrica de carga, o valor e a última hora relatada em UTC.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário] | A identidade da partição. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-load-reset"></a>partição de sfctl a reposição de carga
Repõe a carga atual de uma partição do Service Fabric.

Repõe a carga atual de uma partição do Service Fabric para a carga de padrão para o serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário] | A identidade da partição. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-quorum-loss"></a>perda de quórum do partição sfctl
Induces perda de quórum para uma partição de determinado serviço com estado.

Esta API é útil para uma situação de perda de quórum temporária no seu serviço. Chame a API de GetQuorumLossProgress com o mesmo OperationId para devolver informações sobre a operação iniciada com esta API. Só pode ser chamado com monitoração de estado persistente (HasPersistedState = = true) dos serviços.  Não utilize esta API nos serviços sem estado ou com monitoração de estado na memória apenas os serviços.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de operação [necessário] | Um GUID que identifica uma chamada desta API.  Isso é passado para a API de GetProgress correspondente. |
| – id de partição [necessário] | A identidade da partição. |
| – duração de perda de quórum [necessária] | A quantidade de tempo para que a partição será mantida na perda de quórum.  Isso deve ser especificado em segundos. |
| – modo de perda de quórum [necessário] | Esta enumeração é passada para a API de StartQuorumLoss para indicar que tipo de perda de quórum induza. |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-quorum-loss-status"></a>o sfctl partição quórum-perda-status
Obtém o progresso de uma operação de perda de quórum numa partição a utilizar com a API de StartQuorumLoss.

Obtém o progresso de uma operação de perda de quórum ao StartQuorumLoss, usando o OperationId fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de operação [necessário] | Um GUID que identifica uma chamada desta API.  Isso é passado para a API de GetProgress correspondente. |
| – id de partição [necessário] | A identidade da partição. |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-recover"></a>recuperar a partição de sfctl
Indica ao cluster do Service Fabric que ele deve tentar recuperar uma partição específica que está atualmente parada em perda de quórum.

Esta operação só deve ser efetuada se se sabe que não não possível recuperar as réplicas que estão indisponíveis. Utilização incorreta desta API pode causar a potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário] | A identidade da partição. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-recover-all"></a>o sfctl partição de recuperação-all
Indica ao cluster do Service Fabric que ele deve tentar recuperar quaisquer serviços (incluindo os serviços do sistema) que estão atualmente bloqueados em perda de quórum.

Esta operação só deve ser efetuada se se sabe que não não possível recuperar as réplicas que estão indisponíveis. Utilização incorreta desta API pode causar a potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-report-health"></a>o sfctl partição relatório de estado de funcionamento
Envia um relatório de estado de funcionamento na partição do Service Fabric.

Estado de funcionamento de relatórios da partição especificada do Service Fabric. O relatório tem de conter as informações sobre a origem do relatório de estado de funcionamento e em que é comunicado de propriedade. O relatório é enviado para um partição, que encaminha para o arquivo de estado de funcionamento de gateway do Service Fabric. O relatório pode ser aceites pelo gateway, mas rejeitado pelo arquivo de estado de funcionamento após a validação extra. Por exemplo, o arquivo de estado de funcionamento poderá rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoletos. Para ver se o relatório foi aplicado no arquivo de estado de funcionamento, verifique que o relatório é apresentada na seção de eventos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – [necessária] de propriedade de estado de funcionamento | A propriedade das informações de estado de funcionamento. <br><br> Uma entidade pode ter relatórios de estado de funcionamento para diferentes propriedades. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir que a flexibilidade de gerador de relatórios categorizar a condição de estado que aciona o relatório. Por exemplo, um gerador de relatórios com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que ele pode informar a propriedade de "AvailableDisk" nesse nó. O mesmo gerador de relatórios pode monitorizar a conectividade de nó, para que ele pode reportar uma propriedade "Conectividade" no mesmo nó. No arquivo de estado de funcionamento, esses relatórios são tratados como eventos de estado de funcionamento separado para o nó especificado. Em conjunto com o SourceId, a propriedade identifica exclusivamente as informações de estado de funcionamento. |
| -Estado de funcionamento [necessário] | Os valores possíveis incluem\: "Inválido", "Ok", "Aviso", "Error", "Desconhecido". |
| – id de partição [necessário] | A identidade da partição. |
| – id de origem [necessário] | O nome de origem que identifica o componente de sistema/watchdog/cliente gerado as informações de estado de funcionamento. |
| --description | A descrição das informações de estado de funcionamento. <br><br> Ele representa o texto livre usado para adicionar informações legíveis humanas sobre o relatório. O comprimento máximo da cadeia para a descrição é 4096 carateres. Se a cadeia fornecida é mais longa, ele será automaticamente truncado. Quando truncados, os últimos carateres da descrição contêm um marcador "[truncado]" e o tamanho total da cadeia de caracteres é 4096 carateres. A presença do marcador indica aos utilizadores esse truncamento ocorreu. Observe que, quando truncados, a descrição tem menos de 4096 carateres a partir da cadeia original. |
| – imediata | Um sinalizador que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de estado de funcionamento é enviado para um aplicativo, que encaminha para o arquivo de estado de funcionamento de gateway do Service Fabric. Se Immediate estiver definido como true, o relatório será enviado imediatamente do Gateway de HTTP para o armazenamento de estado de funcionamento, independentemente das definições de cliente de recursos de infraestrutura que está a utilizar a aplicação de Gateway HTTP. Isto é útil para os relatórios críticos que devem ser enviados logo que possível. Dependendo do tempo e outras condições, enviar o relatório poderá ainda falhar, por exemplo, se o HTTP Gateway foi fechado ou a mensagem não aceder ao Gateway. Se Immediate estiver definido como false, o relatório é enviado com base nas definições de cliente do Estado de funcionamento do HTTP Gateway. Por conseguinte, irá ser loteado, de acordo com a configuração de HealthReportSendInterval. Esta é a definição recomendada porque permite que o cliente do Estado de funcionamento otimizar as mensagens para o arquivo de estado de funcionamento, bem como o processamento de relatórios de estado de funcionamento de relatórios de estado de funcionamento. Por predefinição, os relatórios não são enviados imediatamente. |
| --remove-when-expired | Valor que indica se o relatório é removido do arquivo de estado de funcionamento, quando este expirar. <br><br> Se definido como true, o relatório for removido do arquivo de estado de funcionamento, depois de expirar. Se definido como false, o relatório é tratado como um erro quando a expirou. O valor desta propriedade é false por padrão. Quando os clientes reportem periodicamente, eles devem definir RemoveWhenExpired false (predefinição). Dessa forma, é o gerador de relatórios tem problemas (por exemplo, o deadlock) e não é possível reportar a entidade é avaliada em erro quando expira o relatório de estado de funcionamento. Este processo sinaliza a entidade como sendo num Estado de funcionamento de erro. |
| --sequence-number | O número de sequência para este relatório de estado de funcionamento como uma cadeia numérica. <br><br> O número de sequência de relatório é utilizado pelo arquivo de estado de funcionamento para detetar relatórios obsoletos. Se não for especificado, um número de sequência é gerado automaticamente pelo cliente do Estado de funcionamento quando é adicionado um relatório. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |
| --ttl | A duração para o qual este relatório de estado de funcionamento é válido. Este campo utiliza o formato ISO8601 para especificar a duração. <br><br> Quando os clientes reportem periodicamente, eles devem enviar relatórios com freqüência mais alta do que o tempo de duração. Se os clientes comunicam na transição, eles podem definir o tempo de duração para infinito. Quando expira o TTL, o evento de estado de funcionamento que contém as informações de estado de funcionamento é seja removido do arquivo de estado de funcionamento, se RemoveWhenExpired for true, ou avaliadas no erro, se RemoveWhenExpired false. Se não for especificado, tempo de duração a predefinição é o valor de infinito. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-restart"></a>reinício de partição de sfctl
Esta API irá reiniciar algumas ou todas as réplicas ou instâncias da partição especificada.

Esta API é útil para testar ativação pós-falha. Se utilizado como destino uma partição de serviço sem estado, RestartPartitionMode tem de ser AllReplicasOrInstances. Chame a API de GetPartitionRestartProgress com o mesmo OperationId para obter o progresso.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de operação [necessário] | Um GUID que identifica uma chamada desta API.  Isso é passado para a API de GetProgress correspondente. |
| – id de partição [necessário] | A identidade da partição. |
| – modo de partição de reinício [necessário] | Descreva as partições para reiniciar. |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-restart-status"></a>reinício de partição de sfctl-status
Obtém o progresso de uma operação de PartitionRestart StartPartitionRestart uma introdução.

Obtém o progresso de um PartitionRestart ao StartPartitionRestart com OperationId fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de operação [necessário] | Um GUID que identifica uma chamada desta API.  Isso é passado para a API de GetProgress correspondente. |
| – id de partição [necessário] | A identidade da partição. |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-partition-svc-name"></a>o sfctl svc-nome da partição
Obtém o nome do serviço do Service Fabric para uma partição.

Obtém o nome do serviço para a partição especificada. Um erro 404 é devolvido se o ID de partição não existe no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário] | A identidade da partição. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |


## <a name="next-steps"></a>Passos Seguintes
- [Configuração](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como utilizar a CLI do Service Fabric utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).

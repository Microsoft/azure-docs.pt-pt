---
title: CLI do Azure Service Fabric-partição sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para o gerenciamento de partições para um serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: c038ef3266a727bf6984a5bd88ca540a589380db
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905852"
---
# <a name="sfctl-partition"></a>sfctl partition
Consultar e gerenciar partições para qualquer serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| perda de dados | Essa API irá induzir a perda de dados para a partição especificada. |
| data-loss-status | Obtém o progresso de uma operação de perda de dados de partição iniciada usando a API StartDataLoss. |
| saúde | Obtém a integridade da partição de Service Fabric especificada. |
| info | Obtém as informações sobre uma partição de Service Fabric. |
| list | Obtém a lista de partições de um serviço de Service Fabric. |
| carregar | Obtém as informações de carga da partição de Service Fabric especificada. |
| redefinir carga | Redefine a carga atual de uma partição de Service Fabric. |
| quorum-perda | Induzi a perda de quorum para uma determinada partição de serviço com estado. |
| quorum-loss-status | Obtém o progresso de uma operação de perda de quorum em uma partição iniciada usando a API StartQuorumLoss. |
| recuperar | Indica ao Cluster Service Fabric que ele deve tentar recuperar uma partição específica que está bloqueada no momento na perda de quorum. |
| recuperar-tudo | Indica ao Cluster Service Fabric que ele deve tentar recuperar quaisquer serviços (incluindo serviços do sistema) que estão atualmente presos na perda de quorum. |
| report-health | Envia um relatório de integridade sobre a partição de Service Fabric. |
| reiniciar | Essa API irá reiniciar algumas ou todas as réplicas ou instâncias da partição especificada. |
| restart-status | Obtém o progresso de uma operação PartitionRestart iniciada usando StartPartitionRestart. |
| svc-name | Obtém o nome do serviço de Service Fabric para uma partição. |

## <a name="sfctl-partition-data-loss"></a>dados da partição sfctl-perda
Essa API irá induzir a perda de dados para a partição especificada.

Ele irá disparar uma chamada para a API OnDataLossAsync da partição.  Essa API irá induzir a perda de dados para a partição especificada. Ele irá disparar uma chamada para a API OnDataLoss da partição. A perda de dados real dependerá do dataperdamode especificado.
- DataLoss parcial: Apenas um quórum de réplicas é removido e o OnDataLoss é desencadeado para a partição, mas a perda real de dados depende da presença de replicação a bordo.  
- FullDataLoss: Todas as réplicas são removidas, pelo que todos os dados são perdidos e o OnDataLoss é desencadeado. Essa API só deve ser chamada com um serviço com estado como o destino. Chamar essa API com um serviço de sistema como o destino não é recomendado.

> [!NOTE]   
> Depois que essa API for chamada, ela não poderá ser revertida. Chamar CancelOperation só interromperá a execução e limpará o estado interno do sistema. Ele não restaurará dados se o comando tiver progredido o suficiente para causar perda de dados. Chame a API GetDataLossProgress com a mesma operationId para retornar informações sobre a operação iniciada com essa API.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --modo perda de dados [Obrigatório] | Essa enumeração é passada para a API StartDataLoss para indicar o tipo de perda de dados a ser induzido. |
| -operação-id [Obrigatório] | A GUID that identifies a call of this API.  This is passed into the corresponding GetProgress API. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl de partição de dados-perda-status
Obtém o progresso de uma operação de perda de dados de partição iniciada usando a API StartDataLoss.

Obtém o progresso de uma operação de perda de dados iniciada com StartDataLoss, usando operationId.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --operation-id [Required] | A GUID that identifies a call of this API.  This is passed into the corresponding GetProgress API. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-health"></a>integridade da partição do sfctl
Obtém a integridade da partição de Service Fabric especificada.

Use EventsHealthStateFilter para filtrar a coleção de eventos de integridade relatados no serviço com base no estado de integridade. Use ReplicasHealthStateFilter para filtrar a coleção de objetos ReplicaHealthState na partição. Se você especificar uma partição que não existe no repositório de integridade, essa solicitação retornará um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador e, portanto, o valor pode ser uma combinação desses valores, obtida usando o operador ' OR ' bit-up. Por exemplo, se o valor fornecido for 6, todos os eventos com o valor OK (2) e Warning (4) serão retornados.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --Exclude-Statistics-Health | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. false por padrão. As estatísticas mostram o número de entidades filhas no estado de integridade Ok, aviso e erro. |
| --replicas-health-state-filter | Permite filtrar a coleção de objetos ReplicaHealthState na partição. O valor pode ser obtido a partir de membros ou operações bits em membros de HealthStateFilter. Somente as réplicas que correspondem ao filtro serão retornadas. Todas as réplicas serão usadas para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador, portanto, o valor pode ser uma combinação desses valores obtidos usando o operador ' OR Por exemplo, se o valor fornecido for 6, todos os eventos com o valor OK (2) e Warning (4) serão retornados. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-info"></a>informações de partição do sfctl
Obtém as informações sobre uma partição de Service Fabric.

Obtém as informações sobre a partição especificada. A resposta inclui a ID da partição, informações de esquema de particionamento, chaves suportadas pela partição, status, integridade e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-list"></a>lista de partições do sfctl
Obtém a lista de partições de um serviço de Service Fabric.

A resposta inclui a ID da partição, informações de esquema de particionamento, chaves suportadas pela partição, status, integridade e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se ajustam em uma única resposta. Quando esse valor é passado para a próxima chamada à API, a API retorna o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-load"></a>carga de partição sfctl
Obtém as informações de carga da partição de Service Fabric especificada.

Retorna informações sobre a carga de uma partição especificada. A resposta inclui uma lista de relatórios de carga para uma partição de Service Fabric. Cada relatório inclui o nome da métrica de carga, o valor e a última hora relatada em UTC.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-load-reset"></a>carga de partição sfctl-redefinir
Redefine a carga atual de uma partição de Service Fabric.

Redefine a carga atual de uma partição de Service Fabric para a carga padrão do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-quorum-loss"></a>quorum de partição sfctl-perda
Induzi a perda de quorum para uma determinada partição de serviço com estado.

Essa API é útil para uma situação de perda de quorum temporária em seu serviço. Chame a API GetQuorumLossProgress com a mesma operationId para retornar informações sobre a operação iniciada com essa API. Isso só pode ser chamado nos serviços com estado persistente (HasPersistedState = = true).  Não use essa API em serviços sem estado ou em serviços com estado somente na memória.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -operação-id [Obrigatório] | A GUID that identifies a call of this API.  This is passed into the corresponding GetProgress API. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --quórum-perda-duração [Obrigatório] | A quantidade de tempo para a qual a partição será mantida na perda de quorum.  Isso deve ser especificado em segundos. |
| --quórum-perda-modo [Obrigatório] | Essa enumeração é passada para a API StartQuorumLoss para indicar o tipo de perda de quorum a ser induzido. |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-quorum-loss-status"></a>quorum de partição sfctl-perda-status
Obtém o progresso de uma operação de perda de quorum em uma partição iniciada usando a API StartQuorumLoss.

Obtém o progresso de uma operação de perda de quorum iniciada com StartQuorumLoss, usando operationId fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --operation-id [Required] | A GUID that identifies a call of this API.  This is passed into the corresponding GetProgress API. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-recover"></a>recuperação de partição sfctl
Indica ao Cluster Service Fabric que ele deve tentar recuperar uma partição específica que está bloqueada no momento na perda de quorum.

Esta operação só deve ser executada se for conhecida que as réplicas que estão inativas não podem ser recuperadas. O uso incorreto dessa API pode causar potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-recover-all"></a>sfctl Partition Recover-todos
Indica ao Cluster Service Fabric que ele deve tentar recuperar quaisquer serviços (incluindo serviços do sistema) que estão atualmente presos na perda de quorum.

Esta operação só deve ser executada se for conhecida que as réplicas que estão inativas não podem ser recuperadas. O uso incorreto dessa API pode causar potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-report-health"></a>relatório de partição sfctl-integridade
Envia um relatório de integridade sobre a partição de Service Fabric.

Relata o estado de integridade da partição de Service Fabric especificada. O relatório deve conter as informações sobre a origem do relatório de integridade e a propriedade na qual ele é relatado. O relatório é enviado para uma partição Service Fabric gateway, que encaminha para o repositório de integridade. O relatório pode ser aceito pelo gateway, mas rejeitado pelo repositório de integridade após a validação extra. Por exemplo, o repositório de integridade pode rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoleto. Para ver se o relatório foi aplicado no repositório de integridade, verifique se o relatório aparece na seção eventos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --propriedade de saúde [Necessária] | A propriedade das informações de integridade. <br><br> Uma entidade pode ter relatórios de integridade para propriedades diferentes. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir a flexibilidade do reporter para categorizar a condição de estado que dispara o relatório. Por exemplo, um reporter com SourceID "LocalWatchdog" pode monitorar o estado do disco disponível em um nó, para que ele possa relatar a propriedade "AvailableDisk" nesse nó. O mesmo Reporter pode monitorar a conectividade do nó, para que possa relatar uma propriedade "Connectivity" no mesmo nó. No repositório de integridade, esses relatórios são tratados como eventos de integridade separados para o nó especificado. Junto com SourceID, a propriedade identifica exclusivamente as informações de integridade. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem\: "Invalid", "OK", "Warning", "Error", "Unknown". |
| --partição-id [Obrigatório] | A identidade da partição. |
| --source-id [Obrigatório] | O nome de origem que identifica o componente de cliente/Watchdog/sistema que gerou as informações de integridade. |
| --Descrição | A descrição das informações de integridade. <br><br> Ele representa o texto livre usado para adicionar informações legíveis ao relatório. O comprimento máximo da cadeia de caracteres para a descrição é de 4096 caracteres. Se a cadeia de caracteres fornecida for maior, ela será truncada automaticamente. Quando truncados, os últimos caracteres da descrição contêm um marcador "[Truncado]", e o tamanho total da corda é de 4096 caracteres. A presença do marcador indica para os usuários que o truncamento ocorreu. Observe que, quando truncado, a descrição tem menos de 4096 caracteres a partir da cadeia de caracteres original. |
| --imediato | Um sinalizador que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de integridade é enviado para um aplicativo Service Fabric gateway, que encaminha para o repositório de integridade. Se Immediate for definido como true, o relatório será enviado imediatamente do gateway HTTP para o repositório de integridade, independentemente das configurações do cliente de malha que o aplicativo de gateway HTTP está usando. Isso é útil para relatórios críticos que devem ser enviados assim que possível. Dependendo do tempo e de outras condições, o envio do relatório ainda poderá falhar, por exemplo, se o gateway HTTP estiver fechado ou se a mensagem não alcançar o gateway. Se Immediate for definido como false, o relatório será enviado com base nas configurações do cliente de integridade do gateway HTTP. Portanto, ele será em lote de acordo com a configuração do HealthReportSendInterval. Essa é a configuração recomendada porque permite que o cliente de integridade Otimize as mensagens de relatório de integridade para o repositório de integridade, bem como o processamento do relatório de integridade. Por padrão, os relatórios não são enviados imediatamente. |
| --remove-when-expired | Valor que indica se o relatório será removido do repositório de integridade quando ele expirar. <br><br> Se definido como true, o relatório será removido do repositório de integridade depois de expirar. Se definido como false, o relatório será tratado como um erro quando expirado. O valor dessa propriedade é false por padrão. Quando os clientes relatam periodicamente, eles devem definir RemoveWhenExpired false (padrão). Dessa forma, o relator tem problemas (por exemplo, deadlock) e não pode relatar, a entidade é avaliada com erro quando o relatório de integridade expira. Isso sinaliza a entidade como estando em estado de integridade de erro. |
| --sequence-number | O número de sequência deste relatório de integridade como uma cadeia de caracteres numérica. <br><br> O número de sequência do relatório é usado pelo repositório de integridade para detectar relatórios obsoletos. Se não for especificado, um número de sequência será gerado automaticamente pelo cliente de integridade quando um relatório for adicionado. |
| --timeout -t | Default\: 60. |
| --ttl | A duração para a qual este relatório de integridade é válido. Esse campo usa o formato ISO8601 para especificar a duração. <br><br> Quando os clientes relatam periodicamente, eles devem enviar relatórios com mais frequência do que a vida útil. Se os clientes reportarem a transição, eles poderão definir a vida útil como infinito. Quando a vida útil expira, o evento de integridade que contém as informações de integridade é removido do repositório de integridade, se RemoveWhenExpired for verdadeiro ou avaliado com erro, se RemoveWhenExpired false. Se não for especificado, o padrão de vida útil para valor infinito. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-restart"></a>reinicialização de partição sfctl
Essa API irá reiniciar algumas ou todas as réplicas ou instâncias da partição especificada.

Essa API é útil para testar o failover. Se usado para direcionar uma partição de serviço sem estado, RestartPartitionMode deve ser Allreplicasorinstance. Chame a API GetPartitionRestartProgress usando a mesma operationId para obter o progresso.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -operação-id [Obrigatório] | A GUID that identifies a call of this API.  This is passed into the corresponding GetProgress API. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --reiniciar o modo de partição [Obrigatório] | Descreva quais partições reiniciar. |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-restart-status"></a>reinicialização de partição sfctl-status
Obtém o progresso de uma operação PartitionRestart iniciada usando StartPartitionRestart.

Obtém o progresso de um PartitionRestart iniciado com StartPartitionRestart usando o operationId fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --operation-id [Required] | A GUID that identifies a call of this API.  This is passed into the corresponding GetProgress API. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-partition-svc-name"></a>sfctl Partition svc-Name
Obtém o nome do serviço de Service Fabric para uma partição.

Obtém o nome do serviço para a partição especificada. Um erro 404 será retornado se a ID da partição não existir no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |


## <a name="next-steps"></a>Passos seguintes
- [Setup](service-fabric-cli.md) the Service Fabric CLI.
- Learn how to use the Service Fabric CLI using the [sample scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).

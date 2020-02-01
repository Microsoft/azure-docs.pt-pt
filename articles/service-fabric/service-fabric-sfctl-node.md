---
title: CLI do Azure Service Fabric-nó sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para gerenciar nós de cluster.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 5881e6485003abd4fd23a7f6d06a428e768c00fa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905870"
---
# <a name="sfctl-node"></a>sfctl node
Gerencie os nós que formam um cluster.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| adiciona-configuração-parâmetro-overrides | Adiciona a lista de substituições de configuração no nó especificado. |
| desativar | Desative um nó de Cluster Service Fabric com a intenção de desativação especificada. |
| permitir | Ative um nó de Cluster Service Fabric que esteja desativado no momento. |
| obter-configuração-overrides | Obtém a lista de substituições de configuração no nó especificado. |
| saúde | Obtém a integridade de um nó de Service Fabric. |
| info | Obtém as informações sobre um nó específico no Cluster Service Fabric. |
| list | Obtém a lista de nós no Cluster Service Fabric. |
| carregar | Obtém as informações de carga de um nó de Service Fabric. |
| remover-configuração-overrides | Remove as sobreposições de configuração no nó especificado. |
| remove-state | Notifica Service Fabric que o estado persistente em um nó foi removido permanentemente ou perdido. |
| report-health | Envia um relatório de integridade no nó Service Fabric. |
| reiniciar | Reinicia um nó de Cluster Service Fabric. |
| transição | Inicia ou para um nó de cluster. |
| transição-status | Gets the progress of an operation started using StartNodeTransition. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>sfctl nó adicionar-configuração-parâmetro-sobreposições
Adiciona a lista de substituições de configuração no nó especificado.

Este api permite adicionar todas as substituições de configuração existentes no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --config-parâmetro-lista [Obrigatório] | Descrição para adicionar lista de substituições de configuração. |
| --nome do nó [Obrigatório] | The name of the node. |
| --Force | Força adicionando configuração sobreposições em nódos os nódosos especificados. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-node-disable"></a>desabilitação do nó do sfctl
Desative um nó de Cluster Service Fabric com a intenção de desativação especificada.

Desative um nó de Cluster Service Fabric com a intenção de desativação especificada. Depois que a desativação estiver em andamento, a intenção de desativação poderá ser aumentada, mas não diminuída (por exemplo, um nó desativado com a intenção de pausa poderá ser desativado ainda mais com reinicialização, mas não o contrário. Os nós podem ser reativados usando a operação ativar um nó a qualquer momento depois de serem desativados. Se a desativação não for concluída, a desativação será cancelada. Um nó que fica inativo e volta ao ser desativado ainda precisará ser reativado antes que os serviços sejam colocados nesse nó.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | The name of the node. |
| --deactivation-intent | Descreve a intenção ou o motivo da desativação do nó. Os valores possíveis são os seguintes. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-node-enable"></a>habilitação do nó sfctl
Ative um nó de Cluster Service Fabric que esteja desativado no momento.

Ativa um nó de Cluster Service Fabric que está desativado no momento. Depois de ativado, o nó se tornará novamente um destino viável para colocar novas réplicas e todas as réplicas desativadas restantes no nó serão reativadas.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | The name of the node. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-node-get-configuration-overrides"></a>sfctl nó get-configuração-overrides
Obtém a lista de substituições de configuração no nó especificado.

Este api permite obter todas as configurações existentes sobreposições no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | The name of the node. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-node-health"></a>integridade do nó do sfctl
Obtém a integridade de um nó de Service Fabric.

Obtém a integridade de um nó de Service Fabric. Use EventsHealthStateFilter para filtrar a coleção de eventos de integridade relatados no nó com base no estado de integridade. Se o nó especificado por nome não existir no repositório de integridade, isso retornará um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | The name of the node. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador e, portanto, o valor pode ser uma combinação desses valores, obtida usando o operador ' OR ' bit-up. Por exemplo, se o valor fornecido for 6, todos os eventos com o valor OK (2) e Warning (4) serão retornados.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-node-info"></a>informações do nó sfctl
Obtém as informações sobre um nó específico no Cluster Service Fabric.

A resposta inclui o nome, o status, a ID, a integridade, o tempo de atividade e outros detalhes sobre o nó.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | The name of the node. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-node-list"></a>lista de nós do sfctl
Obtém a lista de nós no Cluster Service Fabric.

A resposta inclui o nome, o status, a ID, a integridade, o tempo de atividade e outros detalhes sobre os nós.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se ajustam em uma única resposta. Quando esse valor é passado para a próxima chamada à API, a API retorna o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser menores que os resultados máximos especificados se não couberem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero ou não for especificado, a consulta paginável incluirá o máximo possível de resultados que couberem na mensagem de retorno. |
| --node-status-filter | Permite filtrar os nós com base no NodeStatus. Somente os nós que correspondem ao valor de filtro especificado serão retornados. O valor do filtro pode ser um dos seguintes.  Padrão\: padrão. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-node-load"></a>carregamento de nó sfctl
Obtém as informações de carga de um nó de Service Fabric.

Recupera as informações de carga de um nó Service Fabric para todas as métricas que têm carga ou capacidade definida.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | The name of the node. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-node-remove-configuration-overrides"></a>sfctl nó remover-configuração-overrides
Remove as sobreposições de configuração no nó especificado.

Este api permite remover todas as substituições de configuração existentes no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | The name of the node. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-node-remove-state"></a>remover estado do nó sfctl
Notifica Service Fabric que o estado persistente em um nó foi removido permanentemente ou perdido.

Isso implica que não é possível recuperar o estado persistente desse nó. Isso geralmente acontece se um disco rígido tiver sido limpo, ou se um disco rígido falhar. O nó precisa estar inoperante para que essa operação seja bem-sucedida. Essa operação permite Service Fabric saber que as réplicas nesse nó não existem mais e que Service Fabric deve parar de esperar que essas réplicas sejam revertidas. Não execute este cmdlet se o estado no nó não tiver sido removido e o nó puder ser revertido com seu estado intacto. A partir do Service Fabric 6,5, para usar esta API para nós de semente, altere os nós de semente para nós regulares (não semente) e, em seguida, invoque essa API para remover o estado do nó. Se o cluster estiver em execução no Azure, depois que o nó semente ficar inativo, Service Fabric tentará alterá-lo para um nó não semente automaticamente. Para fazer isso acontecer, verifique se o número de nós não semente no tipo de nó primário não é menor que o número de nós de semente inativos. Se necessário, adicione mais nós ao tipo de nó primário para conseguir isso. Para o cluster autónomo, se não se espera que o nó de sementes down volte com o seu estado intacto, por favor retire o nó do cluster, consulte https\://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | The name of the node. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-node-report-health"></a>relatório de nó de sfctl – integridade
Envia um relatório de integridade no nó Service Fabric.

Relata o estado de integridade do nó de Service Fabric especificado. O relatório deve conter as informações sobre a origem do relatório de integridade e a propriedade na qual ele é relatado. O relatório é enviado para um nó Service Fabric gateway, que encaminha para o repositório de integridade. O relatório pode ser aceito pelo gateway, mas rejeitado pelo repositório de integridade após a validação extra. Por exemplo, o repositório de integridade pode rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoleto. Para ver se o relatório foi aplicado no repositório de integridade, verifique se o relatório aparece na seção HealthEvents.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --propriedade de saúde [Necessária] | A propriedade das informações de integridade. <br><br> Uma entidade pode ter relatórios de integridade para propriedades diferentes. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir a flexibilidade do reporter para categorizar a condição de estado que dispara o relatório. Por exemplo, um reporter com SourceID "LocalWatchdog" pode monitorar o estado do disco disponível em um nó, para que ele possa relatar a propriedade "AvailableDisk" nesse nó. O mesmo Reporter pode monitorar a conectividade do nó, para que possa relatar uma propriedade "Connectivity" no mesmo nó. No repositório de integridade, esses relatórios são tratados como eventos de integridade separados para o nó especificado. Junto com SourceID, a propriedade identifica exclusivamente as informações de integridade. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem\: "Invalid", "OK", "Warning", "Error", "Unknown". |
| --nome do nó [Obrigatório] | The name of the node. |
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

## <a name="sfctl-node-restart"></a>reinicialização de nó sfctl
Reinicia um nó de Cluster Service Fabric.

Reinicia um nó de Cluster Service Fabric que já foi iniciado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | The name of the node. |
| --create-fabric-dump | Especifique true para criar um despejo do processo do nó de malha. Isso diferencia maiúsculas de minúsculas.  Padrão\: false. |
| --node-instance-id | A ID da instância do nó de destino. Se a ID da instância for especificada, o nó será reiniciado somente se corresponder à instância atual do nó. Um valor padrão de "0" corresponderia A qualquer ID de instância. A ID da instância pode ser obtida usando a consulta obter nó.  Padrão\: 0. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-node-transition"></a>transição de nó sfctl
Inicia ou para um nó de cluster.

Inicia ou para um nó de cluster.  Um nó de cluster é um processo, não a própria instância do sistema operacional.  Para iniciar um nó, passe "Start" para o parâmetro NodeTransitionType. Para interromper um nó, passe "Stop" para o parâmetro NodeTransitionType. Essa API inicia a operação-quando a API retorna o nó pode ainda não ter concluído a transição. Chame GetNodeTransitionProgress com a mesma operationId para obter o progresso da operação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nó-instância-id [Obrigatório] | A ID de instância de nó do nó de destino. Isso pode ser determinado por meio da API do GetNodeInfo. |
| --nome do nó [Obrigatório] | The name of the node. |
| --nó-tipo de transição [Obrigatório] | Indica o tipo de transição a ser executado.  NodeTransitionType. Start iniciará um nó parado. NodeTransitionType. Stop irá parar um nó que está ativo. |
| --operation-id             [Required] | A GUID that identifies a call of this API.  This is passed into the corresponding GetProgress API. |
| --stop-duration-in-seconds [Required] | The duration, in seconds, to keep the node stopped.  The minimum value is 600, the maximum is 14400.  After this time expires, the node will automatically come back up. |
| --timeout -t | The server timeout for performing the operation in seconds. This timeout specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.  Default\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argumento|Descrição|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## <a name="sfctl-node-transition-status"></a>sfctl node transition-status
Gets the progress of an operation started using StartNodeTransition.

Gets the progress of an operation started with StartNodeTransition using the provided OperationId.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --node-name    [Required] | The name of the node. |
| --operation-id [Required] | A GUID that identifies a call of this API.  This is passed into the corresponding GetProgress API. |
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
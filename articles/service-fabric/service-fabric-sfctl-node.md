---
title: O Azure Service Fabric CLI sfctl nós | Documentos da Microsoft
description: Descreve os comandos do CLI do Service Fabric sfctl nó.
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
ms.openlocfilehash: 1e5b5876fa6277d1bad0989c543de667f75a066c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258737"
---
# <a name="sfctl-node"></a>sfctl node
Gerir os nós que formam um cluster.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| Desativar | Desative um nó de cluster do Service Fabric com a intenção de Desativação especificada. |
| Ativar | Ative um nó de cluster do Service Fabric que está atualmente desativado. |
| health | Obtém o estado de funcionamento de um nó do Service Fabric. |
| info | Obtém as informações sobre um nó específico no cluster do Service Fabric. |
| list | Obtém a lista de nós no cluster do Service Fabric. |
| load | Obtém as informações de carga de um nó do Service Fabric. |
| remove-state | Notifica o Service Fabric que o estado persistente num nó foi permanentemente removido ou perdido. |
| report-health | Envia um relatório de estado de funcionamento no nó do Service Fabric. |
| restart | Reinicia um nó de cluster do Service Fabric. |
| transição | Inicia ou para um nó de cluster. |
| Estado de transição | Obtém o progresso de uma operação à utilização do StartNodeTransition. |

## <a name="sfctl-node-disable"></a>desativar o nó sfctl
Desative um nó de cluster do Service Fabric com a intenção de Desativação especificada.

Desative um nó de cluster do Service Fabric com a intenção de Desativação especificada. Após a desativação está em curso, a intenção de Desativação pode ser aumentada, mas não diminui (por exemplo, um nó que é desativado com a intenção de pausa pode ser ainda mais desativado com reinício, mas não o contrário. Nós podem ser reativados com a ativar uma operação de nó qualquer altura depois de eles são desativados. Se a desativação não estiver concluída, esta ação irá cancelar a desativação. Um nó fica inativo e é reativado enquanto desativado ainda precisarão ser reativados antes dos serviços serão colocados nesse nó.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário] | O nome do nó. |
| --deactivation-intent | Descreve a intenção ou a razão para desativar o nó. Seguem-se os valores possíveis. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-node-enable"></a>Ativar do nó de sfctl
Ative um nó de cluster do Service Fabric que está atualmente desativado.

Ativa um nó de cluster do Service Fabric que está atualmente desativado. Depois de ativado, o nó novamente se tornará um destino viável para colocar as réplicas novas e as réplicas desativadas restantes no nó vão ser reativadas.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário] | O nome do nó. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-node-health"></a>Estado de funcionamento de nó de sfctl
Obtém o estado de funcionamento de um nó do Service Fabric.

Obtém o estado de funcionamento de um nó do Service Fabric. Utilize EventsHealthStateFilter para filtrar a recolha de eventos de estado de funcionamento comunicado no nó com base no estado de funcionamento. Se o nó que especificou, o nome não existe no arquivo de estado de funcionamento, esta ação devolve um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário] | O nome do nó. |
| --events-health-state-filter | Permite a filtragem da coleção de objetos de HealthEvent devolvidos com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregado. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores, obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, todos os eventos com o valor HealthState OK (2) e (4) de aviso são devolvidos.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-node-info"></a>informações do nó de sfctl
Obtém as informações sobre um nó específico no cluster do Service Fabric.

A resposta inclui o nome, estado, ID, estado de funcionamento, tempo de atividade e outros detalhes acerca do nó.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário] | O nome do nó. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-node-list"></a>lista de nós de sfctl
Obtém a lista de nós no cluster do Service Fabric.

A resposta inclui o nome, estado, ID, estado de funcionamento, tempo de atividade e outros detalhes sobre os nós.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --continuation-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API, quando os resultados do sistema não se encaixam numa única resposta. Quando esse valor é passado para a próxima chamada de API, a API devolve o próximo conjunto de resultados. Se não existirem mais resultados, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificada com URL. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos pode ser menor do que os resultados máximos especificados se eles não se encaixam na mensagem de acordo com as restrições de tamanho de mensagem máximo definido na configuração. Se este parâmetro for igual a zero ou não especificado, a consulta paginada inclui resultados tantos possível que se enquadram na mensagem de retorna. |
| --node-status-filter | Permite a filtragem de nós com base no NodeStatus. Apenas os nós que correspondem o valor do filtro especificado serão retornados. O valor do filtro pode ser um dos seguintes.  Predefinição\: padrão. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-node-load"></a>carga de nó de sfctl
Obtém as informações de carga de um nó do Service Fabric.

Obtém as informações de carga de um nó do Service Fabric para todas as métricas que tenham a carga ou capacidade definido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário] | O nome do nó. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-node-remove-state"></a>o sfctl remove-estado do nó
Notifica o Service Fabric que o estado persistente num nó foi permanentemente removido ou perdido.

Isso implica que não é possível recuperar o estado persistente desse nó. Isso geralmente acontece se tiver sido eliminado um disco rígido, limpo ou no caso de falha de um disco rígido. O nó tem de ser para baixo para esta operação seja concluída com êxito. Esta operação permite que o Service Fabric, sabe que as réplicas nesse nó já não existem e que o Service Fabric deve pare de esperar por essas réplicas seja aberto. Não execute este cmdlet se o estado no nó não foi removido e o nó pode se levante com seu estado intacto.

A partir do Service Fabric 6.5, para utilizar este cmdlet para nós de semente, altere os nós de semente para regulares (não seed) nós e, em seguida, invocar este cmdlet para remover o estado do nó. Se o cluster está em execução no Azure, depois do nó seed ficar inativo, Service Fabric irá tentar altere-o automaticamente para um nó não semente. Para que isso aconteça, certifique-se de que o número de nós não seed no tipo de nó primário é nem menos que o número de para baixo de nós de semente. Se necessário, adicione mais nós para o tipo de nó primário para atingir esse objetivo. Para cluster autónomo, se o nó de seed inferior não é esperado seja aberto com o seu estado intacto, tente remover o nó do cluster, consulte [remover nós de cluster autónomo do Service Fabric](/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes) 

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário] | O nome do nó. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-node-report-health"></a>o sfctl nó relatório de estado de funcionamento
Envia um relatório de estado de funcionamento no nó do Service Fabric.

Estado de funcionamento de relatórios de nó especificado do Service Fabric. O relatório tem de conter as informações sobre a origem do relatório de estado de funcionamento e em que é comunicado de propriedade. O relatório é enviado para um nó de gateway do Service Fabric, que encaminha para o arquivo de estado de funcionamento. O relatório pode ser aceites pelo gateway, mas rejeitado pelo arquivo de estado de funcionamento após a validação extra. Por exemplo, o arquivo de estado de funcionamento poderá rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoletos. Para ver se o relatório foi aplicado no arquivo de estado de funcionamento, verifique que o relatório é apresentado na secção HealthEvents.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – [necessária] de propriedade de estado de funcionamento | A propriedade das informações de estado de funcionamento. <br><br> Uma entidade pode ter relatórios de estado de funcionamento para diferentes propriedades. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir que a flexibilidade de gerador de relatórios categorizar a condição de estado que aciona o relatório. Por exemplo, um gerador de relatórios com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que ele pode informar a propriedade de "AvailableDisk" nesse nó. O mesmo gerador de relatórios pode monitorizar a conectividade de nó, para que ele pode reportar uma propriedade "Conectividade" no mesmo nó. No arquivo de estado de funcionamento, esses relatórios são tratados como eventos de estado de funcionamento separado para o nó especificado. Em conjunto com o SourceId, a propriedade identifica exclusivamente as informações de estado de funcionamento. |
| -Estado de funcionamento [necessário] | Os valores possíveis incluem\: "Inválido", "Ok", "Aviso", "Error", "Desconhecido". |
| --nome do nó [necessário] | O nome do nó. |
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

## <a name="sfctl-node-restart"></a>reinício do nó sfctl
Reinicia um nó de cluster do Service Fabric.

Reinicia um nó de cluster do Service Fabric que já foi iniciado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário] | O nome do nó. |
| --create-fabric-dump | Especifica verdadeiro para criar um despejo do processo de nó do fabric. Diferencia maiúsculas de minúsculas.  Predefinido\: FALSO. |
| --node-instance-id | O ID de instância do nó de destino. Se o ID de instância é especificado o nó é reiniciado apenas se ele corresponder a com a instância atual do nó. Um valor predefinido de "0" corresponderia a qualquer ID de instância. O ID de instância pode ser obtido com o get nó query.  Predefinido\: 0. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-node-transition"></a>o sfctl de transição de nó
Inicia ou para um nó de cluster.

Inicia ou para um nó de cluster.  Um nó de cluster é um processo, não a instância de SO em si.  Para iniciar um nó, introduzir "Start" para o parâmetro NodeTransitionType. Para parar um nó, introduzir "Stop" para o parâmetro NodeTransitionType. Esta API inicia a operação - quando a API devolve que o nó não pode concluir a transição ainda. Chame GetNodeTransitionProgress com o mesmo OperationId para obter o progresso da operação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de instância de nó [necessário] | O ID de instância de nó do nó de destino. Isso pode ser determinado através da API de GetNodeInfo. |
| --nome do nó [necessário] | O nome do nó. |
| – tipo de transição de nó [necessário] | Indica o tipo de transição para executar.  NodeTransitionType.Start iniciará um nó de parado. NodeTransitionType.Stop irá parar um nó que está a funcionar. |
| – id de operação [necessário] | Um GUID que identifica uma chamada desta API.  Isso é passado para a API de GetProgress correspondente. |
| -stop-duração-em-segundos [necessários] | A duração, em segundos manter o nó parado.  O valor mínimo é de 600, o máximo é 14400.  Após este período expira, o nó irá surgir automaticamente novamente. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-node-transition-status"></a>Estado de transição do nó sfctl
Obtém o progresso de uma operação à utilização do StartNodeTransition.

Obtém o progresso de uma operação ao StartNodeTransition com OperationId fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário] | O nome do nó. |
| – id de operação [necessário] | Um GUID que identifica uma chamada desta API.  Isso é passado para a API de GetProgress correspondente. |
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
---
title: Nó CLI-sfctl de tecido de serviço Azure
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para gerir nós de cluster.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f2cf1011db37892f71bdd06f409cec1a76463507
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257150"
---
# <a name="sfctl-node"></a>sfctl node
Gerir os nós que formam um aglomerado.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| complemento de configuração-parâmetro-sobreposições | Adiciona a lista de substituições de configuração no nó especificado. |
| desativar | Desativar um nó de cluster de tecido de serviço com a intenção de desativação especificada. |
| ativar | Ative um nó de cluster de tecido de serviço que está atualmente desativado. |
| obter-configuração-overrides | Obtém a lista de substituições de configuração no nó especificado. |
| Saúde | Obtém a saúde de um nó de tecido de serviço. |
| informações | Obtém a informação sobre um nó específico no cluster de Tecido de Serviço. |
| lista | Obtém a lista de nós no cluster de Tecido de Serviço. |
| carregar | Obtém a informação de carga de um nó de tecido de serviço. |
| remover substituições de configuração | Remove as substituições de configuração no nó especificado. |
| remover estado | Notifica o Tecido de Serviço de que o estado persistido num nó foi permanentemente removido ou perdido. |
| relatório-saúde | Envia um relatório de saúde sobre o nó de tecido de serviço. |
| restart | Reinicia um nó de cluster de tecido de serviço. |
| transição | Começa ou para um nó de aglomerado. |
| estado de transição | O progresso de uma operação começou a usar o StartNodeTransition. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>sfctl nó suplemento-configuração-substituições de parâmetros
Adiciona a lista de substituições de configuração no nó especificado.

Esta api permite adicionar todas as substituições de configuração existentes no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --config-parâmetro-lista de substituição [Obrigatório] | Descrição para adicionar lista de substituições de configuração. |
| --nome de nó [Obrigatório] | O nome do nó. |
| ...-força | A configuração de adição de força sobrepõe-se aos nós especificados. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-disable"></a>nódoa sfctl desativar
Desativar um nó de cluster de tecido de serviço com a intenção de desativação especificada.

Desativar um nó de cluster de tecido de serviço com a intenção de desativação especificada. Uma vez que a desativação esteja em curso, a intenção de desativação pode ser aumentada, mas não diminuída (por exemplo, um nó que é desativado com a intenção de pausa pode ser desativado ainda mais com o Restart, mas não o contrário. Os nós podem ser reativados utilizando a operação de nó ativar sempre que forem desativados. Se a desativação não estiver completa, isto cancelará a desativação. Um nó que desce e volta a subir enquanto desativado ainda terá de ser reativado antes de os serviços serem colocados nesse nó.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --desactivação-intenção | Descreve a intenção ou razão para desativar o nó. Os valores possíveis estão a seguir-se. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-enable"></a>nódoa sfctl ativar
Ative um nó de cluster de tecido de serviço que está atualmente desativado.

Ativa um nó de cluster de tecido de serviço que está atualmente desativado. Uma vez ativado, o nó tornar-se-á novamente um alvo viável para a colocação de novas réplicas, e quaisquer réplicas desativadas restantes no nó serão reativadas.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-get-configuration-overrides"></a>nó sfctl get-configuração-overrides
Obtém a lista de substituições de configuração no nó especificado.

Esta api permite obter todas as configurações existentes sobreposições no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-health"></a>sfctl saúde nól
Obtém a saúde de um nó de tecido de serviço.

Obtém a saúde de um nó de tecido de serviço. Use EventosHealthStateFilter para filtrar a recolha de eventos de saúde relatados no nó com base no estado de saúde. Se o nó que especifica pelo nome não existir na loja de saúde, este devolva um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --eventos-saúde-estado-filtro | Permite filtrar a recolha de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, Se o valor fornecido for 6, todos os eventos com o valor healthState de OK (2) e Aviso (4) são devolvidos.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-info"></a>informação sobre nó sfctl
Obtém a informação sobre um nó específico no cluster de Tecido de Serviço.

A resposta inclui o nome, estado, identificação, saúde, uptime, e outros detalhes sobre o nó.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-list"></a>lista de nó sfctl
Obtém a lista de nós no cluster de Tecido de Serviço.

A resposta inclui o nome, estado, identificação, saúde, uptime, e outros detalhes sobre os nós.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificado por URL. |
| --resultados máximos | O número máximo de resultados a devolver como parte das consultas de página. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições máximas do tamanho da mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta paged inclui o maior número possível de resultados que se encaixam na mensagem de retorno. |
| --nó-filtro de estado-estado | Permite filtrar os nós com base no NodeStatus. Apenas os nós que correspondem ao valor do filtro especificado serão devolvidos. O valor do filtro pode ser um dos seguintes.  \:Predefinição. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-load"></a>carga de nó sfctl
Obtém a informação de carga de um nó de tecido de serviço.

Recupera a informação de carga de um nó de tecido de serviço para todas as métricas que têm carga ou capacidade definidas.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-remove-configuration-overrides"></a>nó sfctl remove-configuração-overrides
Remove as substituições de configuração no nó especificado.

Esta api permite remover todas as substituições de configuração existentes no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-remove-state"></a>nó sfctl remover estado
Notifica o Tecido de Serviço de que o estado persistido num nó foi permanentemente removido ou perdido.

Isto implica que não é possível recuperar o estado persistido desse nó. Isto geralmente acontece se um disco rígido tiver sido limpo, ou se um disco rígido falhar. O nó tem de estar em baixo para que esta operação tenha sucesso. Esta operação permite ao Service Fabric saber que as réplicas nesse nó já não existem, e que o Tecido de Serviço deve parar de esperar que essas réplicas voltem a aparecer. Não executar este cmdlet se o estado do nó não tiver sido removido e o nó puder voltar a estar intacto. A partir do Tecido de Serviço 6.5, para utilizar esta API para nós de sementes, altere os nós de sementes para nós regulares (sem sementes) e, em seguida, invoque esta API para remover o estado do nó. Se o cluster estiver a funcionar no Azure, depois de o nó de sementes descer, o Service Fabric tentará alterá-lo automaticamente para um nó de não semente. Para que isto aconteça, certifique-se de que o número de nós não-sementes no tipo de nó primário não é inferior ao número de nós de sementes para baixo. Se necessário, adicione mais nós ao tipo de nó primário para o conseguir. Para o aglomerado autónomo, se não se espera que o nó de sementes para baixo volte a estar intacto, retire o nó do cluster, consulte https \: //docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-report-health"></a>sfctl node relatório-saúde
Envia um relatório de saúde sobre o nó de tecido de serviço.

Relatórios de estado de saúde do nó de tecido de serviço especificado. O relatório deve conter as informações sobre a origem do relatório de saúde e sobre os bens em que é comunicado. O relatório é enviado para um nó de gateway de tecido de serviço, que remete para a loja de saúde. O relatório pode ser aceite pela porta de entrada, mas rejeitado pela loja de saúde após validação extra. Por exemplo, a loja de saúde pode rejeitar o relatório por causa de um parâmetro inválido, como um número de sequência velha. Para ver se o relatório foi aplicado na loja de saúde, verifique se o relatório consta da secção HealthEvents.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --saúde-propriedade [Necessária] | A propriedade da informação de saúde. <br><br> Uma entidade pode ter relatórios de saúde para diferentes propriedades. A propriedade é uma corda e não uma enumeração fixa para permitir ao repórter flexibilidade para categorizar a condição do estado que desencadeia o relatório. Por exemplo, um repórter com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que possa reportar a propriedade "AvailableDisk" nesse nó. O mesmo repórter pode monitorizar a conectividade do nó, para que possa reportar uma propriedade "Conectividade" no mesmo nó. Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para o nó especificado. Juntamente com o SourceId, a propriedade identifica exclusivamente a informação de saúde. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem \: 'Inválido', 'Ok', 'Aviso', 'Erro', 'Desconhecido'. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --source-id [Obrigatório] | O nome de origem que identifica o componente cliente/cão de guarda/sistema que gerou a informação de saúde. |
| --descrição | A descrição da informação de saúde. <br><br> Representa um texto gratuito utilizado para adicionar informações legíveis humanas sobre o relatório. O comprimento máximo da corda para a descrição é de 4096 caracteres. Se a corda fornecida for mais comprida, será automaticamente truncada. Quando truncados, os últimos caracteres da descrição contêm um marcador "[Truncated]", e o tamanho total da corda é de 4096 caracteres. A presença do marcador indica aos utilizadores que a truncação ocorreu. Note que, quando truncado, a descrição tem menos de 4096 caracteres da corda original. |
| --imediata | Uma bandeira que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de saúde é enviado para uma aplicação de gateway de tecido de serviço, que reencaminha para a loja de saúde. Se o Imediato for definido como verdadeiro, o relatório é enviado imediatamente de HTTP Gateway para a loja de saúde, independentemente das definições do cliente de tecido que a Aplicação HTTP Gateway está a usar. Isto é útil para relatórios críticos que devem ser enviados o mais rapidamente possível. Dependendo do tempo e de outras condições, o envio do relatório pode ainda falhar, por exemplo, se o GATEWAY HTTP estiver fechado ou a mensagem não chegar ao Gateway. Se o Immediate for definido como falso, o relatório é enviado com base nas definições do cliente de saúde a partir do GATEWAY HTTP. Portanto, será loteado de acordo com a configuração HealthReportSendInterval. Esta é a configuração recomendada porque permite ao cliente de saúde otimizar as mensagens de relato de saúde para a loja de saúde, bem como o processamento de relatórios de saúde. Por predefinição, os relatórios não são enviados imediatamente. |
| --remover-quando expirado | Valor que indica se o relatório é removido da loja de saúde quando expira. <br><br> Se for verdadeiro, o relatório é removido da loja de saúde depois de expirar. Se for definido como falso, o relatório é tratado como um erro quando expirado. O valor desta propriedade é falso por defeito. Quando os clientes reportam periodicamente, devem definir RemoveWhenExpired falso (padrão). Desta forma, o repórter tem problemas (por exemplo, impasse) e não pode reportar, a entidade é avaliada por erro quando o relatório de saúde expira. Isto sinaliza a entidade como estando no estado de saúde error. |
| --número de sequência | O número da sequência deste relatório de saúde como uma corda numérica. <br><br> O número da sequência do relatório é usado pela loja de saúde para detetar relatórios antigos. Se não for especificado, um número de sequência é gerado automaticamente pelo cliente de saúde quando um relatório é adicionado. |
| --timeout -t | Padrão \: 60. |
| --ttl | A duração para a qual este relatório de saúde é válido. Este campo utiliza o formato ISO8601 para especificar a duração. <br><br> Quando os clientes reportam periodicamente, devem enviar relatórios com maior frequência do que o tempo de vida. Se os clientes reportarem sobre a transição, podem definir o tempo para viver em infinito. Quando o tempo de vida expirar, o evento de saúde que contém as informações de saúde é removido da loja de saúde, se removeWhenExpired for verdadeiro, ou avaliado por erro, se RemoveWhenExpired falso. Se não for especificado, o tempo de viver não tem valor infinito. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-restart"></a>reinício do nó sfctl
Reinicia um nó de cluster de tecido de serviço.

Reinicia um nó de cluster de tecido de serviço que já foi iniciado.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --criar-tecido-despejo | Especifique True para criar uma descarga do processo do nó de tecido. Isto é sensível a casos.  Falso \: predefinido. |
| --nó-instância-id | A identificação do nó do alvo. Se o ID de instância for especificado, o nó só é reiniciado se corresponder à instância atual do nó. Um valor padrão de "0" corresponderia a qualquer identificação de instância. O ID de caso pode ser obtido usando obter consulta de nó.  Predefinido \: 0. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-transition"></a>transição do nó sfctl
Começa ou para um nó de aglomerado.

Começa ou para um nó de aglomerado.  Um nó de cluster é um processo, não o próprio caso so.  Para iniciar um nó, passe em "Iniciar" para o parâmetro NodeTransitionType. Para parar um nó, passe em "Stop" para o parâmetro NodeTransitionType. Esta API inicia a operação - quando a API retorna o nó pode ainda não ter terminado a transição. Ligue para GetNodeTransitionProgress com a mesma OperaçãoId para obter o progresso da operação.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nó-instância-id [Obrigatório] | A identificação do nó do nó-alvo. Isto pode ser determinado através da API GetNodeInfo. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --nó-tipo de transição [Obrigatório] | Indica o tipo de transição a realizar.  NodeTransitionType.Start iniciará um nó parado. NodeTransitionType.Stop irá parar um nó que está para cima. |
| --operation-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| ---duração do stop-em-segundos [Obrigatório] | A duração, em segundos, para manter o nó parado.  O valor mínimo é de 600, o máximo é de 14400.  Após o termo deste tempo, o nó voltará automaticamente para cima. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-node-transition-status"></a>sfctl nó transição-status
O progresso de uma operação começou a usar o StartNodeTransition.

Inicia-se o progresso de uma operação com o StartNodeTransition utilizando a OperaçãoId fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --operation-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI do Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .

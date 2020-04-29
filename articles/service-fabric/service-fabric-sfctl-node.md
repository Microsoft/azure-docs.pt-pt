---
title: Azure Service Fabric CLI-sfctl nó
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para gerir nós de cluster.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 5881e6485003abd4fd23a7f6d06a428e768c00fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905870"
---
# <a name="sfctl-node"></a>sfctl node
Gerencie os nós que formam um aglomerado.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| adiciona-configuração-parâmetro-overrides | Adiciona a lista de substituições de configuração no nó especificado. |
| desativar | Desativar um nó de cluster de tecido de serviço com a intenção de desativação especificada. |
| ativar | Ativar um nó de cluster de tecido de serviço que está atualmente desativado. |
| obter-configuração-overrides | Obtém a lista de substituições de configuração no nó especificado. |
| saúde | Obtém a saúde de um nó de tecido de serviço. |
| informações | Obtém a informação sobre um nó específico no cluster Service Fabric. |
| list | Obtém a lista de nós no cluster Service Fabric. |
| carregar | Obtém a informação de carga de um nó de tecido de serviço. |
| remover-configuração-overrides | Remove as sobreposições de configuração no nó especificado. |
| remover estado | Notifica o Serviço Fabric de que o estado persponificado num nó foi permanentemente removido ou perdido. |
| relatório-saúde | Envia um relatório de saúde sobre o nó de Tecido de Serviço. |
| restart | Reinicia um nó de cluster de tecido de serviço. |
| transição | Começa ou para um nó de aglomerado. |
| situação de transição | Obtém o progresso de uma operação iniciada com a StartNodeTransition. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>sfctl nó adicionar-configuração-parâmetro-sobreposições
Adiciona a lista de substituições de configuração no nó especificado.

Este api permite adicionar todas as substituições de configuração existentes no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --config-parâmetro-lista [Obrigatório] | Descrição para adicionar lista de substituições de configuração. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --força | Força adicionando configuração sobreposições em nódos os nódosos especificados. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-disable"></a>nó sfctl desativar
Desativar um nó de cluster de tecido de serviço com a intenção de desativação especificada.

Desativar um nó de cluster de tecido de serviço com a intenção de desativação especificada. Uma vez em curso a desativação, a intenção de desativação pode ser aumentada, mas não diminuída (por exemplo, um nó que é desativado com a intenção de Pausa pode ser desativado ainda mais com o Restart, mas não o contrário. Os nódosos podem ser reativados utilizando o funcionamento do nó Ativar um nó a qualquer momento depois de desativados. Se a desativação não estiver concluída, isto cancelará a desativação. Um nó que desce e volta a subir enquanto desativado ainda terá de ser reativado antes que os serviços sejam colocados naquele nó.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| --desactivação-intenção | Descreve a intenção ou a razão para desativar o nó. Os valores possíveis estão a seguir-se. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-enable"></a>nó sfctl permitir
Ativar um nó de cluster de tecido de serviço que está atualmente desativado.

Ativa um nó de cluster de tecido de serviço que está atualmente desativado. Uma vez ativado, o nó tornar-se-á novamente um alvo viável para a colocação de novas réplicas, e quaisquer réplicas desativadas restantes no nó serão reativadas.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-get-configuration-overrides"></a>sfctl nó get-configuração-overrides
Obtém a lista de substituições de configuração no nó especificado.

Este api permite obter todas as configurações existentes sobreposições no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-health"></a>sfctl saúde do nó
Obtém a saúde de um nó de tecido de serviço.

Obtém a saúde de um nó de tecido de serviço. Use EventsHealthStateFilter para filtrar a recolha de eventos de saúde relatados no nó com base no estado de saúde. Se o nó que especifica pelo nome não existir na loja de saúde, isto devolve um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| -eventos-saúde-filtro estado-estado | Permite filtrar a coleção de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Só os eventos que combinam com o filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for de 6, todos os eventos com o valor do Estado de Saúde de OK (2) e Aviso (4) são devolvidos.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-info"></a>informação do nó sfctl
Obtém a informação sobre um nó específico no cluster Service Fabric.

A resposta inclui o nome, estado, identificação, saúde, uptime, e outros detalhes sobre o nó.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-list"></a>lista de nó sfctl
Obtém a lista de nós no cluster Service Fabric.

A resposta inclui o nome, estado, identificação, saúde, uptime, e outros detalhes sobre os nós.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --continuação-token | O parâmetro simbólico de continuação é utilizado para obter o próximo conjunto de resultados. Um símbolo de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se encaixam numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o sinal de continuação não contém um valor. O valor deste parâmetro não deve ser codificado. |
| -resultados máximos | O número máximo de resultados a devolver como parte das consultas páginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta páginada inclui o maior número possível de resultados que se encaixem na mensagem de devolução. |
| --nó-status-filter | Permite filtrar os nódosos com base no Nó. Apenas os nódosos que correspondem ao valor do filtro especificado serão devolvidos. O valor do filtro pode ser um dos seguintes.  Padrão\: predefinido. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-load"></a>carga do nó sfctl
Obtém a informação de carga de um nó de tecido de serviço.

Recupera a informação de carga de um nó de tecido de serviço para todas as métricas que tenham carga ou capacidade definidas.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-remove-configuration-overrides"></a>sfctl nó remover-configuração-overrides
Remove as sobreposições de configuração no nó especificado.

Este api permite remover todas as substituições de configuração existentes no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-remove-state"></a>sfctl nó remover estado
Notifica o Serviço Fabric de que o estado persponificado num nó foi permanentemente removido ou perdido.

Isto implica que não é possível recuperar o estado persistente desse nó. Isto geralmente acontece se um disco rígido tiver sido limpo, ou se um disco rígido se despenhar. O nó tem de ser para baixo para que esta operação tenha sucesso. Esta operação permite que a Service Fabric saiba que as réplicas desse nó já não existem, e que o Tecido de Serviço deve parar de esperar que essas réplicas voltem a subir. Não corra este cmdlet se o estado no nó não tiver sido removido e o nó puder voltar com o seu estado intacto. A partir do Tecido de Serviço 6.5, para utilizar esta API para nós de sementes, por favor mude os nós de semente para nós regulares (não sementes) e, em seguida, invoque esta API para remover o estado do nó. Se o cluster estiver a funcionar em Azure, depois do nó de semente descer, o Tecido de Serviço tentará mudá-lo automaticamente para um nó de sementes. Para que isso aconteça, certifique-se de que o número de nós não sementes no nó primário não é inferior ao número de nós de sementes down. Se necessário, adicione mais nós ao tipo de nó primário para o conseguir. Para o cluster autónomo, se não se espera que o nó de sementes down volte com o\:seu estado intacto, por favor retire o nó do cluster, consulte https //docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-report-health"></a>sfctl substantivo relatório-saúde
Envia um relatório de saúde sobre o nó de Tecido de Serviço.

Informa o estado de saúde do nó de tecido de serviço especificado. O relatório deve conter as informações sobre a origem do relatório de saúde e dos bens em que é comunicado. O relatório é enviado para um nó de gateway de Serviço Fabric, que reencaminha para a loja de saúde. O relatório pode ser aceite pela porta de entrada, mas rejeitado pela loja de saúde após validação extra. Por exemplo, a loja de saúde pode rejeitar o relatório por causa de um parâmetro inválido, como um número de sequência velha. Para ver se o relatório foi aplicado na loja de saúde, verifique se o relatório aparece na secção HealthEvents.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --propriedade de saúde [Necessária] | A propriedade da informação de saúde. <br><br> Uma entidade pode ter relatórios de saúde para diferentes propriedades. A propriedade é uma corda e não uma enumeração fixa para permitir que a flexibilidade do repórter categorize a condição do Estado que desencadeia o relatório. Por exemplo, um repórter com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que possa reportar a propriedade "AvailableDisk" nesse nó. O mesmo repórter pode monitorizar a conectividade do nó, para que possa reportar uma propriedade "Conectividade" no mesmo nó. Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para o nó especificado. Juntamente com o SourceId, a propriedade identifica exclusivamente a informação de saúde. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem\: 'Inválido', 'Ok', 'Aviso', 'Erro', 'Desconhecido'. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --source-id [Obrigatório] | O nome de origem que identifica o componente cliente/cão de guarda/sistema que gerou a informação de saúde. |
| --descrição | A descrição da informação de saúde. <br><br> Representa texto gratuito utilizado para adicionar informações legíveis humanas sobre o relatório. O comprimento máximo da corda para a descrição é de 4096 caracteres. Se a corda fornecida for mais comprida, será automaticamente truncada. Quando truncados, os últimos caracteres da descrição contêm um marcador "[Truncado]", e o tamanho total da corda é de 4096 caracteres. A presença do marcador indica aos utilizadores que ocorreu a truncação. Note que quando truncado, a descrição tem menos de 4096 caracteres da cadeia original. |
| -imediatamente | Uma bandeira que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de saúde é enviado para uma aplicação de gateway de tecido de serviço, que reencaminha para a loja de saúde. Se o Immediate for definido como verdadeiro, o relatório é enviado imediatamente de HTTP Gateway para a loja de saúde, independentemente das definições do cliente de tecido que a Aplicação HTTP Gateway está a usar. Isto é útil para relatórios críticos que devem ser enviados o mais rapidamente possível. Dependendo do tempo e de outras condições, o envio do relatório pode ainda falhar, por exemplo, se o Gateway HTTP estiver fechado ou a mensagem não chegar ao Gateway. Se imediatamente for definido como falso, o relatório é enviado com base nas definições do cliente de saúde a partir do HTTP Gateway. Portanto, será loteizado de acordo com a configuração HealthReportSendInterval. Esta é a configuração recomendada porque permite ao cliente de saúde otimizar mensagens de relatórios de saúde para a loja de saúde, bem como processamento de relatórios de saúde. Por defeito, os relatórios não são enviados imediatamente. |
| -remover-quando expirado | Valor que indica se o relatório é removido da loja de saúde quando expira. <br><br> Se for verdade, o relatório é removido da loja de saúde após o seu termo. Se for definido como falso, o relatório é tratado como um erro quando expirado. O valor desta propriedade é falso por defeito. Quando os clientes reportam periodicamente, devem definir RemoverQuando Expirado falso (predefinido). Desta forma, é que o repórter tem problemas (por exemplo, impasse) e não pode reportar, a entidade é avaliada por engano quando o relatório de saúde expira. Isto sinaliza a entidade como estando em estado de saúde error. |
| --número de sequência | O número da sequência deste relatório de saúde como uma corda numérica. <br><br> O número da sequência do relatório é usado pela loja de saúde para detetar relatórios antigos. Se não especificado, um número de sequência é gerado automaticamente pelo cliente de saúde quando um relatório é adicionado. |
| --timeout -t | Padrão\: 60. |
| -ttl | A duração da qual este relatório de saúde é válido. Este campo utiliza o formato ISO8601 para especificar a duração. <br><br> Quando os clientes reportam periodicamente, devem enviar relatórios com maior frequência do que o tempo de vida. Se os clientes reportarem sobre a transição, podem definir o tempo para viver em infinito. Quando expira a hora de viver, o evento de saúde que contém as informações de saúde é removido da loja de saúde, se removeWhenExpired for verdadeiro, ou avaliado por erro, se removerQuando Expirado falso. Se não for especificado, o tempo para viver não corresponde ao valor infinito. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-restart"></a>reinício do nó sfctl
Reinicia um nó de cluster de tecido de serviço.

Reinicia um nó de cluster de tecido de serviço que já está iniciado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| --create-fabric-dump | Especifique true para criar uma descarga do processo do nó de tecido. Isto é sensível ao caso.  Falso\: padrão. |
| -- nó-instância-id | A identificação do nó alvo. Se for especificado o ID da instância, o nó só é reiniciado se corresponder à instância atual do nó. Um valor padrão de "0" corresponderia a qualquer identificação de instância. A identificação da instância pode ser obtida usando consulta de nó.  Padrão\: 0. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-transition"></a>transição do nó sfctl
Começa ou para um nó de aglomerado.

Começa ou para um nó de aglomerado.  Um nó de cluster é um processo, não a própria instância de SO.  Para iniciar um nó, passe em "Iniciar" para o parâmetro NodeTransitionType. Para parar um nó, passe em "Stop" para o parâmetro NodeTransitionType. Esta API inicia a operação - quando a API devolve o nó pode ainda não ter terminado a transição. Ligue para GetNodeTransitionProgress com o mesmo OperationId para obter o progresso da operação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nó-instância-id [Obrigatório] | A identificação do nó do nó alvo. Isto pode ser determinado através da API GetNodeInfo. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nó-tipo de transição [Obrigatório] | Indica o tipo de transição para realizar.  NodeTransitionType.Start iniciará um nó parado. NodeTransitionType.Stop irá parar um nó que está para cima. |
| -operação-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| -stop-duration-in-segundos [Obrigatório] | A duração, em segundos, para manter o nó parado.  O valor mínimo é de 600, o máximo é de 14400.  Após este tempo expirar, o nó voltará automaticamente. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-node-transition-status"></a>sfctl nó de transição estado
Obtém o progresso de uma operação iniciada com a StartNodeTransition.

O progresso de uma operação começou com o StartNodeTransition utilizando o OperationId fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| -operação-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).
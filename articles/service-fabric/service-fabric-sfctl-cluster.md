---
title: Aglomerado CLI-sfctl de tecido de serviço Azure
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para a gestão de clusters.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 05132e4c7e644e681e2e4cfdedaa2b04a066ebeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86259965"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Selecione, gerencie e opere os clusters de tecido de serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| versões de código | Obtém uma lista de versões de código de tecido que são alistadas num cluster de Tecido de Serviço. |
| config-versões | Obtém uma lista de versões de tecido config que são alistadas num cluster de Tecido de Serviço. |
| Saúde | Obtém a saúde de um cluster de tecido de serviço. |
| manifest | Obtenha o manifesto de cluster de tecido de serviço. |
| operação-cancelar | Cancela uma operação de avaria induzida pelo utilizador. |
| lista de operações | Obtém uma lista de operações de avaria induzidas pelo utilizador filtradas por entrada fornecida. |
| provisão | Disposição dos pacotes de código ou configuração de um cluster de Tecido de Serviço. |
| sistema de recuperação | Indica ao cluster de Tecido de Serviço que deve tentar recuperar os serviços do sistema que estão atualmente presos em perda de quórum. |
| relatório-saúde | Envia um relatório de saúde sobre o aglomerado de tecidos de serviço. |
| selecionar | Liga-se a um ponto final de cluster de tecido de serviço. |
| show-conexão | Mostre a que conjunto de tecidos de serviço este caso sfctl está ligado. |
| não provisão | Desprovisionar os pacotes de código ou configuração de um cluster de Tecido de Serviço. |
| atualizar | Inicie a atualização da versão de código ou configuração de um cluster de Tecido de Serviço. |
| resumo de upgrade | Faça com que a atualização do cluster passe para o próximo domínio de atualização. |
| upgrade-rollback | Reverta o upgrade de um cluster de tecido de serviço. |
| estado de atualização | Obtém o progresso da atual atualização do cluster. |
| atualização-atualização | Atualize os parâmetros de atualização de uma atualização do cluster de tecido de serviço. |

## <a name="sfctl-cluster-code-versions"></a>sfctl cluster versões de código
Obtém uma lista de versões de código de tecido que são alistadas num cluster de Tecido de Serviço.

Obtém uma lista de informações sobre versões de código de tecido que são avisadas no cluster. O parâmetro CodeVersion pode ser usado para filtrar opcionalmente a saída apenas para essa versão em particular.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --versão código | A versão do produto da Service Fabric. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-config-versions"></a>sfctl cluster config-vers
Obtém uma lista de versões de tecido config que são alistadas num cluster de Tecido de Serviço.

Obtém uma lista de informações sobre versões de tecido config que são a provisionadas no cluster. O parâmetro ConfigVersion pode ser usado para filtrar opcionalmente a saída apenas para essa versão em particular.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --versão config- | A versão config do Service Fabric. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-health"></a>sfctl saúde cluster
Obtém a saúde de um cluster de tecido de serviço.

Use EventosHealthStateFilter para filtrar a recolha de eventos de saúde relatados no cluster com base no estado de saúde. Da mesma forma, utilize o NodesHealthStateFilter e aplicaçõesHealthStateFilter para filtrar a coleção de nós e aplicações devolvidas com base no seu estado de saúde agregado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --aplicações-health-state-filter | Permite a filtragem da aplicação objetos do estado de saúde devolvidos em resultado de consulta de saúde do cluster com base no seu estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro obtido dos membros ou as operações bitwise em membros da enumeração HealthStateFilter. Apenas são devolvidas as aplicações que correspondam ao filtro. Todas as aplicações são usadas para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumerações baseadas em bandeiras, pelo que o valor pode ser uma combinação destes valores obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for 6, então o estado de saúde das aplicações com o valor do Estado de Saúde de OK (2) e Aviso (4) são devolvidos.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --eventos-saúde-estado-filtro | Permite filtrar a recolha de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, Se o valor fornecido for 6, todos os eventos com o valor healthState de OK (2) e Aviso (4) são devolvidos.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --excluir-estatísticas da saúde | Indica se as estatísticas de saúde devem ser devolvidas como parte do resultado da consulta. Falso por defeito. As estatísticas mostram o número de crianças entidades no estado de saúde Ok, Aviso e Erro. |
| --incluir sistema-aplicação-estatísticas de saúde | Indica se as estatísticas de saúde devem incluir as estatísticas de saúde de aplicação de \: tecido/sistema. Falso por defeito. Se o IncludeSystemApplicationHealthStatistics for definido como verdadeiro, as estatísticas de saúde incluem as entidades que pertencem à \: aplicação fabric/System. Caso contrário, o resultado da consulta inclui estatísticas de saúde apenas para aplicações de utilizador. As estatísticas de saúde devem ser incluídas no resultado da consulta para que este parâmetro seja aplicado. |
| --nódes-saúde-estado-filtro | Permite a filtragem dos objetos do estado de saúde do nó devolvidos em resultado de consulta de saúde do cluster com base no seu estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas os nós que correspondem ao filtro são devolvidos. Todos os nós são usados para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumerações baseadas em bandeiras, pelo que o valor pode ser uma combinação destes valores obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for 6, então o estado de saúde dos nós com o valor healthState de OK (2) e Aviso (4) são devolvidos.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-manifest"></a>manifesto de cluster sfctl
Obtenha o manifesto de cluster de tecido de serviço.

Obtenha o manifesto de cluster de tecido de serviço. O manifesto de cluster contém propriedades do cluster que incluem diferentes tipos de nós no cluster, configurações de segurança, falha e topologias de domínio de upgrade, etc. Estas propriedades são especificadas como parte do ficheiro ON ClusterConfig.JSenquanto implanta um cluster autónomo. No entanto, a maior parte da informação no manifesto do cluster é gerada internamente pelo tecido de serviço durante a implantação do cluster em outros cenários de implantação (por exemplo, quando se utiliza o portal Azure). O conteúdo do manifesto do cluster é apenas para fins informativos e não se espera que os utilizadores assumam uma dependência do formato do conteúdo do ficheiro ou da sua interpretação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-operation-cancel"></a>sfctl cluster operação-cancelar
Cancela uma operação de avaria induzida pelo utilizador.

As seguintes APIs iniciam operações de avaria que podem ser canceladas utilizando o CancelOperation \: StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Se a força for falsa, a operação induzida pelo utilizador especificada será graciosamente interrompida e limpa.  Se a força for verdadeira, o comando será abortado, e algum estado interno pode ser deixado para trás.  Especificar a força como verdadeira deve ser usada com cuidado. Chamar esta API com força definida para verdade não é permitida até que esta API já tenha sido chamada no mesmo comando de teste com força definida para falsa primeiro, ou a menos que o comando de teste já tenha uma OperaçãoState da OperaçãoState.RollingBack. Operação De Esclarecimento \: Estado.RollingBack significa que o sistema será/está a limpar o estado interno do sistema causado pela execução do comando.  Não restabelecerá os dados se o comando de teste causar a perda de dados.  Por exemplo, se ligar para o StartDataLoss, então chame esta API, o sistema apenas limpará o estado interno de executar o comando. Não restaurará os dados da partição do alvo, se o comando progrediu o suficiente para causar a perda de dados. Nota importante \:  se esta API for invocada com força=verdade, o estado interno pode ser deixado para trás.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --operation-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| ...-força | Indica se deve revirar graciosamente e limpar o estado interno do sistema modificado executando a operação induzida pelo utilizador. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-operation-list"></a>sfctl cluster operação-lista
Obtém uma lista de operações de avaria induzidas pelo utilizador filtradas por entrada fornecida.

Obtém a lista de operações de avaria induzidas pelo utilizador filtradas por entrada fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --estado-filtro | Usado para filtrar no OperationState's para operações induzidas pelo utilizador. - 65535 - selecione All - 1 - selecione Running - 2 - selecione RollingBack - 8 - selecione Completed - 16 - selecione Faulted - 32 - selecione Cancelado - 64 - selecione ForceCancelled.  Padrão \: 65535. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |
| --tipo-filtro | Utilizado para filtrar o OperationType para operações induzidas pelo utilizador. - 65535 - selecione todos - 1 - selecione PartitionDataLoss. - 2 - selecione PartitionQuorumLoss. - 4 - selecione PartitionRestart. - 8 - selecione NodeTransition.  Padrão \: 65535. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-provision"></a>sfctl fornecimento de cluster
Disposição dos pacotes de código ou configuração de um cluster de Tecido de Serviço.

Validar e prever os pacotes de código ou configuração de um cluster de Tecido de Serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --cluster-manifesto-arquivo-caminho | O caminho manifesto do arquivo do cluster. |
| --código-arquivo-caminho | O caminho do ficheiro do pacote de código de cluster. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-recover-system"></a>sfctl cluster recuperação-sistema
Indica ao cluster de Tecido de Serviço que deve tentar recuperar os serviços do sistema que estão atualmente presos em perda de quórum.

Indica ao cluster de Tecido de Serviço que deve tentar recuperar os serviços do sistema que estão atualmente presos em perda de quórum. Esta operação só deve ser efetuada se se souber que as réplicas que estão em baixo não podem ser recuperadas. A utilização incorreta desta API pode causar uma potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-report-health"></a>sfctl cluster relatório-saúde
Envia um relatório de saúde sobre o aglomerado de tecidos de serviço.

Envia um relatório de saúde sobre um aglomerado de tecidos de serviço. O relatório deve conter as informações sobre a origem do relatório de saúde e sobre os bens em que é comunicado. O relatório é enviado para um nó de gateway de tecido de serviço, que remete para a loja de saúde. O relatório pode ser aceite pela porta de entrada, mas rejeitado pela loja de saúde após validação extra. Por exemplo, a loja de saúde pode rejeitar o relatório por causa de um parâmetro inválido, como um número de sequência velha. Para ver se o relatório foi aplicado na loja de saúde, gere a GetClusterHealth e verifique se o relatório aparece na secção HealthEvents.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --saúde-propriedade [Necessária] | A propriedade da informação de saúde. <br><br> Uma entidade pode ter relatórios de saúde para diferentes propriedades. A propriedade é uma corda e não uma enumeração fixa para permitir ao repórter flexibilidade para categorizar a condição do estado que desencadeia o relatório. Por exemplo, um repórter com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que possa reportar a propriedade "AvailableDisk" nesse nó. O mesmo repórter pode monitorizar a conectividade do nó, para que possa reportar uma propriedade "Conectividade" no mesmo nó. Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para o nó especificado. Juntamente com o SourceId, a propriedade identifica exclusivamente a informação de saúde. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem \: 'Inválido', 'Ok', 'Aviso', 'Erro', 'Desconhecido'. |
| --source-id [Obrigatório] | O nome de origem que identifica o componente cliente/cão de guarda/sistema que gerou a informação de saúde. |
| --descrição | A descrição da informação de saúde. <br><br> Representa um texto gratuito utilizado para adicionar informações legíveis humanas sobre o relatório. O comprimento máximo da corda para a descrição é de 4096 caracteres. Se a corda fornecida for mais comprida, será automaticamente truncada. Quando truncados, os últimos caracteres da descrição contêm um marcador "[Truncated]", e o tamanho total da corda é de 4096 caracteres. A presença do marcador indica aos utilizadores que a truncação ocorreu. Note que, quando truncado, a descrição tem menos de 4096 caracteres da corda original. |
| --imediata | Uma bandeira que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de saúde é enviado para uma aplicação de gateway de tecido de serviço, que reencaminha para a loja de saúde. Se o Imediato for definido como verdadeiro, o relatório é enviado imediatamente de HTTP Gateway para a loja de saúde, independentemente das definições do cliente de tecido que a Aplicação HTTP Gateway está a usar. Isto é útil para relatórios críticos que devem ser enviados o mais rapidamente possível. Dependendo do tempo e de outras condições, o envio do relatório pode ainda falhar, por exemplo, se o GATEWAY HTTP estiver fechado ou a mensagem não chegar ao Gateway. Se o Immediate for definido como falso, o relatório é enviado com base nas definições do cliente de saúde a partir do GATEWAY HTTP. Portanto, será loteado de acordo com a configuração HealthReportSendInterval. Esta é a configuração recomendada porque permite ao cliente de saúde otimizar as mensagens de relato de saúde para a loja de saúde, bem como o processamento de relatórios de saúde. Por predefinição, os relatórios não são enviados imediatamente. |
| --remover-quando expirado | Valor que indica se o relatório é removido da loja de saúde quando expira. <br><br> Se for verdadeiro, o relatório é removido da loja de saúde depois de expirar. Se for definido como falso, o relatório é tratado como um erro quando expirado. O valor desta propriedade é falso por defeito. Quando os clientes reportam periodicamente, devem definir RemoveWhenExpired falso (padrão). Desta forma, o repórter tem problemas (por exemplo, impasse) e não pode reportar, a entidade é avaliada por erro quando o relatório de saúde expira. Isto sinaliza a entidade como estando no estado de saúde error. |
| --número de sequência | O número da sequência deste relatório de saúde como uma corda numérica. <br><br> O número da sequência do relatório é usado pela loja de saúde para detetar relatórios antigos. Se não for especificado, um número de sequência é gerado automaticamente pelo cliente de saúde quando um relatório é adicionado. |
| --timeout -t | Padrão \: 60. |
| --ttl | A duração para a qual este relatório de saúde é válido. Este campo utiliza o formato ISO8601 para especificar a duração. <br><br> Quando os clientes reportam periodicamente, devem enviar relatórios com maior frequência do que o tempo de vida. Se os clientes reportarem sobre a transição, podem definir o tempo para viver em infinito. Quando o tempo de vida expirar, o evento de saúde que contém as informações de saúde é removido da loja de saúde, se removeWhenExpired for verdadeiro, ou avaliado por erro, se RemoveWhenExpired falso. Se não for especificado, o tempo de viver não tem valor infinito. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-select"></a>sfctl cluster selecionado
Liga-se a um ponto final de cluster de tecido de serviço.

Se ligar para fixar o cluster, especifique um caminho absoluto para um cert (.crt) e ficheiro chave (.key) ou um único ficheiro com ambos (.pem). Não especifique os dois. Opcionalmente, se ligar a um cluster seguro, também especificar um caminho absoluto para um ficheiro ou diretório de ca bundle de confiança.  Não há ligação a um cluster sem executar este comando primeiro, incluindo uma ligação com a localidade. No entanto, não é necessário um ponto final explícito para a ligação a um cluster local.  Se utilizar um certificado auto-assinado, ou outro certificado não assinado por um ca bem conhecido, passe no parâmetro -ca para garantir que a validação passa. Se não estiver num cluster de produção, para contornar a validação do lado do cliente (útil para a auto-assinada AC assinada ou não conhecida), utilize a opção --não verificar. Embora possível, não é recomendado para clusters de produção. Um erro de verificação do certificado pode resultar de outra forma.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --aad | Utilize o Diretório Ativo Azure para autenticação. |
| --ca | Caminho absoluto para o diretório ca certs para tratar como ficheiro válido ou agregado ca. Se utilizar um diretório de certificados ca, `c_rehash <directory>` fornecido pela OpenSSL deve ser executado primeiro para calcular os hashes certificados e criar as ligações simbólicas apropriadas. Isto é utilizado para verificar se o certificado devolvido pelo cluster é válido. |
| --cert | Caminho absoluto para um arquivo de certificado de cliente. |
| --ponto final | URL de ponto final do cluster, incluindo prefixo de porta e HTTPS. Tipicamente, o ponto final será parecido com https \: //<o seu url>\: 19080. Se não for dado nenhum ponto final, irá por defeito para http \: //localhost \: 19080.  Padrão \: http \: //localhost \: 19080. |
| ---chave | Caminho absoluto para o ficheiro chave do certificado do cliente. |
| --não verificar | Desativar a verificação dos certificados ao utilizar HTTPS, note \: que esta é uma opção insegura e não deve ser utilizada para ambientes de produção. |
| --pem | Caminho absoluto para certificado de cliente, como um ficheiro .pem. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-connection
Mostre a que conjunto de tecidos de serviço este caso sfctl está ligado.

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-unprovision"></a>sfctl cluster não provisão
Desprovisionar os pacotes de código ou configuração de um cluster de Tecido de Serviço.

É suportado para não obter código e configuração separadamente.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --versão código | A versão do pacote de código de cluster. |
| --versão config- | A versão manifesto do cluster. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-upgrade"></a>upgrade de cluster sfctl
Inicie a atualização da versão de código ou configuração de um cluster de Tecido de Serviço.

Valide os parâmetros de atualização fornecidos e comece a atualizar o código ou a versão de configuração de um cluster de Tecido de Serviço se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-health-map | JSON codificava o dicionário de pares de nomes de aplicação e a percentagem máxima não saudável antes de levantar o erro. |
| --app-tipo-mapa de saúde | JSON codificava o dicionário de pares de nomes do tipo de aplicação e a percentagem máxima não saudável antes de elevar o erro. |
| --versão código | A versão do código de cluster. |
| --versão config- | A versão de configuração do cluster. |
| --avaliação delta-saúde | Permite a avaliação da saúde delta em vez de uma avaliação de saúde absoluta após a conclusão de cada domínio de upgrade. |
| --delta-não-saudável-nódoas | A percentagem máxima permitida de degradação da saúde dos nós permitida durante as atualizações do cluster.  Padrão \: 10. <br><br> O delta é medido entre o estado dos nós no início da atualização e o estado dos nós no momento da avaliação de saúde. A verificação é realizada após cada conclusão de upgrade de domínio de upgrade para garantir que o estado global do cluster está dentro dos limites tolerados. |
| ...-falha de ação | Os valores possíveis incluem \: 'Inválido', 'Rollback', 'Manual'. |
| ...-força-reiniciar | Os processos são reiniciados com força durante a atualização, mesmo quando a versão código não foi alterada. <br><br> A atualização apenas altera a configuração ou dados. |
| --verificação da saúde-redação | O período de tempo entre tentativas de efetuar controlos de saúde se a aplicação ou o agrupamento não forem saudáveis. |
| --saúde-check-estável | O tempo que a aplicação ou cluster deve permanecer saudável antes da atualização avançar para o próximo domínio de atualização. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --saúde-check-espera | O tempo de espera após completar um domínio de upgrade antes de iniciar o processo de verificação de saúde. |
| --replica-set-check-timeout | O tempo máximo para bloquear o processamento de um domínio de upgrade e evitar a perda de disponibilidade quando há problemas inesperados. <br><br> Quando este tempo de tempo expirar, o processamento do domínio de atualização irá prosseguir independentemente dos problemas de perda de disponibilidade. O tempo limite é reiniciado no início de cada domínio de atualização. Os valores válidos são entre 0 e 42949672925 inclusive. |
| --modo de atualização rolante | Os valores possíveis incluem \: 'Inválido', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitorado'.  Padrão \: não monitorizado. |
| --timeout -t | Padrão \: 60. |
| --aplicações pouco saudáveis | A percentagem máxima permitida de aplicações não saudáveis antes de reportar um erro. <br><br> Por exemplo, para permitir que 10% das aplicações não sejam saudáveis, este valor seria de 10. A percentagem representa a percentagem máxima tolerada de aplicações que podem não ser saudáveis antes de o cluster ser considerado errado. Se a percentagem for respeitada mas houver pelo menos uma aplicação pouco saudável, a saúde é avaliada como Advertência. Isto é calculado dividindo o número de aplicações não saudáveis sobre o número total de instâncias de aplicação no cluster, excluindo aplicações de tipos de aplicação que estão incluídos no ApplicationTypeHealthPolicyMap. O cálculo reúne-se para tolerar uma falha em um pequeno número de aplicações. |
| --nó-insalubres | A percentagem máxima permitida de nós não saudáveis antes de reportar um erro. <br><br> Por exemplo, para permitir que 10% dos nós não sejam saudáveis, este valor seria 10. A percentagem representa a percentagem máxima tolerada de nós que podem não ser saudáveis antes de o cluster ser considerado errado. Se a percentagem for respeitada mas houver pelo menos um nó pouco saudável, a saúde é avaliada como Advertência. A percentagem é calculada dividindo o número de nós não saudáveis sobre o número total de nós no cluster. A computação arredonda para tolerar uma falha em um pequeno número de nós. Em grandes aglomerados, alguns nós estarão sempre em baixo ou fora para reparações, pelo que esta percentagem deve ser configurada para tolerar isso. |
| --upgrade-domínio-delta-unhealthy-nodes | A percentagem máxima permitida de upgrade de nós de domínio de saúde permitida durante as atualizações do cluster.  \:Predefinição 15. <br><br> O delta é medido entre o estado dos nós de domínio de upgrade no início da atualização e o estado dos nós de domínio de upgrade no momento da avaliação de saúde. A verificação é realizada após cada conclusão de atualização de domínio de atualização para todos os domínios de upgrade concluídos para garantir que o estado dos domínios de atualização está dentro dos limites tolerados. |
| --upgrade-tempo limite de domínio | O tempo que cada domínio de atualização tem de completar antes da execução do FailureAction. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --upgrade-tempo limite | O tempo que a atualização global tem de ser concluída antes da execução do FailureAction. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| ...-aviso-como-erro | Indica se os avisos são tratados com a mesma gravidade que os erros. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl cluster upgrade-resume
Faça com que a atualização do cluster passe para o próximo domínio de atualização.

Faça com que o código de cluster ou a atualização de configuração passe para o domínio de atualização seguinte, se apropriado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --upgrade-domínio [Obrigatório] | O próximo domínio de upgrade para este upgrade de cluster. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl cluster upgrade-rollback
Reverta o upgrade de um cluster de tecido de serviço.

Reverta o código ou a atualização de configuração de um cluster de Tecido de Serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-upgrade-status"></a>sfctl cluster upgrade-status
Obtém o progresso da atual atualização do cluster.

Obtém o progresso atual da atualização do cluster em curso. Se não houver nenhuma atualização em curso, obtenha o último estado da atualização anterior do cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-cluster-upgrade-update"></a>atualização de cluster sfctl
Atualize os parâmetros de atualização de uma atualização do cluster de tecido de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-health-map | JSON codificava o dicionário de pares de nomes de aplicação e a percentagem máxima não saudável antes de levantar o erro. |
| --app-tipo-mapa de saúde | JSON codificava o dicionário de pares de nomes do tipo de aplicação e a percentagem máxima não saudável antes de elevar o erro. |
| --avaliação delta-saúde | Permite a avaliação da saúde delta em vez de uma avaliação de saúde absoluta após a conclusão de cada domínio de upgrade. |
| --delta-não-saudável-nódoas | A percentagem máxima permitida de degradação da saúde dos nós permitida durante as atualizações do cluster.  Padrão \: 10. <br><br> O delta é medido entre o estado dos nós no início da atualização e o estado dos nós no momento da avaliação de saúde. A verificação é realizada após cada conclusão de upgrade de domínio de upgrade para garantir que o estado global do cluster está dentro dos limites tolerados. |
| ...-falha de ação | Os valores possíveis incluem \: 'Inválido', 'Rollback', 'Manual'. |
| ...-força-reiniciar | Os processos são reiniciados com força durante a atualização, mesmo quando a versão código não foi alterada. <br><br> A atualização apenas altera a configuração ou dados. |
| --verificação da saúde-redação | O período de tempo entre tentativas de efetuar controlos de saúde se a aplicação ou o agrupamento não forem saudáveis. |
| --saúde-check-estável | O tempo que a aplicação ou cluster deve permanecer saudável antes da atualização avançar para o próximo domínio de atualização. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --saúde-check-espera | O tempo de espera após completar um domínio de upgrade antes de iniciar o processo de verificação de saúde. |
| --replica-set-check-timeout | O tempo máximo para bloquear o processamento de um domínio de upgrade e evitar a perda de disponibilidade quando há problemas inesperados. <br><br> Quando este tempo de tempo expirar, o processamento do domínio de atualização irá prosseguir independentemente dos problemas de perda de disponibilidade. O tempo limite é reiniciado no início de cada domínio de atualização. Os valores válidos são entre 0 e 42949672925 inclusive. |
| --modo de atualização rolante | Os valores possíveis incluem \: 'Inválido', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitorado'.  Padrão \: não monitorizado. |
| --timeout -t | Padrão \: 60. |
| --aplicações pouco saudáveis | A percentagem máxima permitida de aplicações não saudáveis antes de reportar um erro. <br><br> Por exemplo, para permitir que 10% das aplicações não sejam saudáveis, este valor seria de 10. A percentagem representa a percentagem máxima tolerada de aplicações que podem não ser saudáveis antes de o cluster ser considerado errado. Se a percentagem for respeitada mas houver pelo menos uma aplicação pouco saudável, a saúde é avaliada como Advertência. Isto é calculado dividindo o número de aplicações não saudáveis sobre o número total de instâncias de aplicação no cluster, excluindo aplicações de tipos de aplicação que estão incluídos no ApplicationTypeHealthPolicyMap. O cálculo reúne-se para tolerar uma falha em um pequeno número de aplicações. |
| --nó-insalubres | A percentagem máxima permitida de nós não saudáveis antes de reportar um erro. <br><br> Por exemplo, para permitir que 10% dos nós não sejam saudáveis, este valor seria 10. A percentagem representa a percentagem máxima tolerada de nós que podem não ser saudáveis antes de o cluster ser considerado errado. Se a percentagem for respeitada mas houver pelo menos um nó pouco saudável, a saúde é avaliada como Advertência. A percentagem é calculada dividindo o número de nós não saudáveis sobre o número total de nós no cluster. A computação arredonda para tolerar uma falha em um pequeno número de nós. Em grandes aglomerados, alguns nós estarão sempre em baixo ou fora para reparações, pelo que esta percentagem deve ser configurada para tolerar isso. |
| --upgrade-domínio-delta-unhealthy-nodes | A percentagem máxima permitida de upgrade de nós de domínio de saúde permitida durante as atualizações do cluster.  \:Predefinição 15. <br><br> O delta é medido entre o estado dos nós de domínio de upgrade no início da atualização e o estado dos nós de domínio de upgrade no momento da avaliação de saúde. A verificação é realizada após cada conclusão de atualização de domínio de atualização para todos os domínios de upgrade concluídos para garantir que o estado dos domínios de atualização está dentro dos limites tolerados. |
| --upgrade-tempo limite de domínio | O tempo que cada domínio de atualização tem de completar antes da execução do FailureAction. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --upgrade-tipo | Os valores possíveis incluem \: 'Inválido', 'Rolling', 'Rolling_ForceRestart'.  \:Rolamento padrão. |
| --upgrade-tempo limite | O tempo que a atualização global tem de ser concluída antes da execução do FailureAction. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| ...-aviso-como-erro | Indica se os avisos são tratados com a mesma gravidade que os erros. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI do Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .

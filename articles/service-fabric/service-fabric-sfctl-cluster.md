---
title: Azure Service Fabric CLI-sfctl cluster
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para gestão de clusters.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 007ad6f59f0ce304db579f4faa1bb95611a93a37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906147"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Selecione, gere e opere os clusters de Tecido de Serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| versões de código | Obtém uma lista de versões de código de tecido que são aprovisionadas num cluster de Tecido de Serviço. |
| config-versões | Obtém uma lista de versões de config de tecido que são aprovisionadas num cluster de Tecido de Serviço. |
| saúde | Obtém a saúde de um cluster de Tecido de Serviço. |
| manifest | Obtenha o manifesto de cluster de tecido de serviço. |
| operação-cancelar | Cancela uma operação de avaria induzida pelo utilizador. |
| lista de operações | Obtém uma lista das operações de avaria induzidas pelo utilizador filtradas pela entrada fornecida. |
| disposição | Fornecê-lo-ia os pacotes de código ou configuração de um cluster de tecido de serviço. |
| recuperar sistema | Indica ao cluster Service Fabric que deve tentar recuperar os serviços do sistema que estão atualmente presos em perda de quórum. |
| relatório-saúde | Envia um relatório de saúde sobre o cluster de tecido de serviço. |
| selecionar | Liga-se a um ponto final do cluster de tecido de serviço. |
| show-connection | Mostre a que cluster de tecido de serviço esta instância de sfctl está ligada. |
| unprovision | Desfornecer os pacotes de código ou configuração de um cluster de tecido de serviço. |
| atualizar | Comece a atualizar o código ou versão de configuração de um cluster de Tecido de Serviço. |
| upgrade-currículo | Faça a atualização do cluster passar para o próximo domínio de atualização. |
| upgrade-rollback | Recue a atualização de um cluster de tecido de serviço. |
| estado de atualização | Obtém o progresso da atual atualização do cluster. |
| atualização | Atualize os parâmetros de atualização de uma atualização do cluster Service Fabric. |

## <a name="sfctl-cluster-code-versions"></a>sfctl cluster código-versões
Obtém uma lista de versões de código de tecido que são aprovisionadas num cluster de Tecido de Serviço.

Obtém uma lista de informações sobre versões de código de tecido que são aprovisionadas no cluster. O parâmetro CodeVersion pode ser utilizado para filtrar opcionalmente a saída apenas para essa versão em particular.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --versão código | A versão do produto do Tecido de Serviço. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-config-versions"></a>sfctl cluster config-versões
Obtém uma lista de versões de config de tecido que são aprovisionadas num cluster de Tecido de Serviço.

Obtém uma lista de informações sobre versões de config de tecido que são aprovisionadas no cluster. O parâmetro ConfigVersion pode ser utilizado para filtrar opcionalmente a saída apenas para essa versão em particular.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --config-versão | A versão config do Tecido de Serviço. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-health"></a>sfctl saúde cluster
Obtém a saúde de um cluster de Tecido de Serviço.

Use EventsHealthStateFilter para filtrar a coleção de eventos de saúde relatados no cluster com base no estado de saúde. Da mesma forma, utilize o NodesHealthStateFilter e applicationsHealthStateFilter para filtrar a recolha de nós e aplicações devolvidas com base no seu estado de saúde agregado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -aplicações-filtro estado-saúde-estado | Permite a filtragem dos objetos do estado de saúde da aplicação devolvidos em resultado de uma consulta de saúde de cluster com base no seu estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro obtido a partir de membros ou operações bitwise em membros da enumeração HealthStateFilter. Apenas as aplicações que correspondem ao filtro são devolvidas. Todas as aplicações são usadas para avaliar o estado de saúde agregado. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada na bandeira, pelo que o valor pode ser uma combinação destes valores obtidos utilizando o operador "OR" bitwise. Por exemplo, se o valor fornecido for de 6, então o estado de saúde das aplicações com o valor do Estado de Saúde de OK (2) e Aviso (4) são devolvidos.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| -eventos-saúde-filtro estado-estado | Permite filtrar a coleção de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Só os eventos que combinam com o filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for de 6, todos os eventos com o valor do Estado de Saúde de OK (2) e Aviso (4) são devolvidos.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| -excluir-estatísticas de saúde | Indica se as estatísticas de saúde devem ser devolvidas como parte do resultado da consulta. Falso por defeito. As estatísticas mostram o número de entidades infantis no estado de saúde Ok, Warning e Error. |
| --incluir-sistema-aplicação-estatísticas de saúde | Indica se as estatísticas de\:saúde devem incluir as estatísticas de saúde de aplicação de tecido/sistema. Falso por defeito. Se incluir SistemaApplicationHealthStatistics é definido para verdade, as estatísticas de\:saúde incluem as entidades que pertencem à aplicação tecido/Sistema. Caso contrário, o resultado da consulta inclui estatísticas de saúde apenas para aplicações de utilizadores. As estatísticas de saúde devem ser incluídas no resultado da consulta para que este parâmetro seja aplicado. |
| --nódosos-filtro-estado-saúde | Permite a filtragem dos objetos do estado de saúde do nó devolvidos em resultado de uma consulta de saúde de cluster com base no seu estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Só os nódosos que combinam com o filtro são devolvidos. Todos os nós são usados para avaliar o estado de saúde agregado. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada na bandeira, pelo que o valor pode ser uma combinação destes valores obtidos utilizando o operador "OR" bitwise. Por exemplo, se o valor fornecido for de 6, então o estado de saúde dos nós com o valor do Estado de Saúde de OK (2) e Aviso (4) são devolvidos.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-manifest"></a>manifesto de aglomerado sfctl
Obtenha o manifesto de cluster de tecido de serviço.

Obtenha o manifesto de cluster de tecido de serviço. O manifesto do cluster contém propriedades do cluster que incluem diferentes tipos de nó no cluster, configurações de segurança, falhas e topoologias de domínio de upgrade, etc. Estas propriedades são especificadas como parte do ficheiro ClusterConfig.JSON enquanto implementam um cluster autónomo. No entanto, a maior parte da informação no manifesto de cluster é gerada internamente pelo tecido de serviço durante a implantação do cluster em outros cenários de implantação (por exemplo, quando se utiliza o portal Azure). O conteúdo do manifesto de cluster é apenas para fins informantes e não se espera que os utilizadores assumam uma dependência do formato do conteúdo do ficheiro ou da sua interpretação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-operation-cancel"></a>sfctl cluster operação-cancelar
Cancela uma operação de avaria induzida pelo utilizador.

As seguintes operações de falha de início\: de APIs que podem ser canceladas utilizando cancelOperação StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Se a força for falsa, então a operação especificada induzida pelo utilizador será graciosamente interrompida e limpa.  Se a força for verdadeira, o comando será abortado, e algum estado interno pode ser deixado para trás.  Especificar a força como verdadeira deve ser utilizada com cuidado. Chamar a esta API com força definida para verdade não é permitido até que esta API já tenha sido chamada no mesmo comando de teste com força definida para falso primeiro, ou a menos que o comando de teste já tenha uma Operação State of OperationState.RollingBack. Esclarecimento\: OperaçãoState.RollingBack significa que o sistema será/está a limpar o estado do sistema interno causado pela execução do comando.  Não restaurará os dados se o comando de teste causar a perda de dados.  Por exemplo, se ligar para o StartDataLoss e depois chamar a esta API, o sistema apenas limpará o estado interno de executar o comando. Não restaurará os dados da partilha do alvo, se o comando progrediu o suficiente para causar a perda de dados. Nota\: importante se esta API for invocada com força==verdade, o estado interno pode ser deixado para trás.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -operação-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --força | Indica se deve reverter graciosamente e limpar o estado interno do sistema modificado executando a operação induzida pelo utilizador. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-operation-list"></a>sfctl cluster operation-list
Obtém uma lista das operações de avaria induzidas pelo utilizador filtradas pela entrada fornecida.

Obtém a lista de operações de avaria induzidas pelo utilizador filtradas pela entrada fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --filtro do estado | Utilizado para filtrar no OperationState's para operações induzidas pelo utilizador. - 65535 - selecione All - 1 - selecione Running - 2 - selecione RollingBack - 8 - selecione Completeed - 16 - selecione Faulted - 32 - selecione Cancelled - 64 - selecione ForceCancelled.  Padrão\: 65535. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |
| --tipo-filtro | Utilizado para filtrar no OperationType para operações induzidas pelo utilizador. - 65535 - selecione todos - 1 - selecione PartitionDataLoss. - 2 - selecione PartitionQuorumLoss. - 4 - selecione PartitionRestart. - 8 - selecione NodeTransition.  Padrão\: 65535. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-provision"></a>disposição de cluster sfctl
Fornecê-lo-ia os pacotes de código ou configuração de um cluster de tecido de serviço.

Validar e fornecer os pacotes de código ou configuração de um cluster de tecido de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --cluster-manifesto-arquivo-caminho | O caminho de arquivo manifesto do cluster. |
| --código-arquivo-caminho | O caminho do ficheiro do pacote de código de cluster. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-recover-system"></a>sistema de recuperação de cluster sfctl
Indica ao cluster Service Fabric que deve tentar recuperar os serviços do sistema que estão atualmente presos em perda de quórum.

Indica ao cluster Service Fabric que deve tentar recuperar os serviços do sistema que estão atualmente presos em perda de quórum. Esta operação só deve ser executada se se souber que as réplicas que estão em baixo não podem ser recuperadas. A utilização incorreta desta API pode causar uma potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-report-health"></a>sfctl cluster relatório-saúde
Envia um relatório de saúde sobre o cluster de tecido de serviço.

Envia um relatório de saúde sobre um cluster de tecido de serviço. O relatório deve conter as informações sobre a origem do relatório de saúde e dos bens em que é comunicado. O relatório é enviado para um nó de gateway de Serviço Fabric, que reencaminha para a loja de saúde. O relatório pode ser aceite pela porta de entrada, mas rejeitado pela loja de saúde após validação extra. Por exemplo, a loja de saúde pode rejeitar o relatório por causa de um parâmetro inválido, como um número de sequência velha. Para ver se o relatório foi aplicado na loja de saúde, faça a GetClusterHealth e verifique se o relatório aparece na secção HealthEvents.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --propriedade de saúde [Necessária] | A propriedade da informação de saúde. <br><br> Uma entidade pode ter relatórios de saúde para diferentes propriedades. A propriedade é uma corda e não uma enumeração fixa para permitir que a flexibilidade do repórter categorize a condição do Estado que desencadeia o relatório. Por exemplo, um repórter com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que possa reportar a propriedade "AvailableDisk" nesse nó. O mesmo repórter pode monitorizar a conectividade do nó, para que possa reportar uma propriedade "Conectividade" no mesmo nó. Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para o nó especificado. Juntamente com o SourceId, a propriedade identifica exclusivamente a informação de saúde. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem\: 'Inválido', 'Ok', 'Aviso', 'Erro', 'Desconhecido'. |
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

## <a name="sfctl-cluster-select"></a>sfctl cluster selecionar
Liga-se a um ponto final do cluster de tecido de serviço.

Se ligar para o cluster seguro, especifique um caminho absoluto para um cert (.crt) e ficheiro chave (.chave) ou um único ficheiro com ambos (.pem). Não especifique os dois. Opcionalmente, se ligar a um cluster seguro, também especifica um caminho absoluto para um ficheiro de pacote ca ou diretório de certificados ca confiáveis.  Não há nenhuma ligação a um aglomerado sem executar este comando primeiro, incluindo uma ligação com o hospedeiro local. No entanto, não é necessário um ponto final explícito para a ligação a um cluster local.  Se utilizar um certificado auto-assinado, ou outro certificado não assinado por um CA bem conhecido, passe no parâmetro --ca para garantir que a validação passa. Se não estiver num cluster de produção, para contornar a validação do lado do cliente (útil para auto-assinado ou não conhecido ca assinado), use a opção --sem verificação. Embora possível, não é recomendado para aglomerados de produção. Um erro de verificação de certificado pode resultar de outra forma.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -aad | Utilize o Diretório Ativo Azure para autenticação. |
| --ca | Caminho absoluto para o diretório ca certs para tratar como ficheiro de pacote válido ou CA. Se utilizar um diretório de `c_rehash <directory>` certs CA, fornecido pela OpenSSL deve ser executado primeiro para calcular as hashes do certificado e criar as ligações simbólicas apropriadas. Isto é utilizado para verificar se o certificado devolvido pelo cluster é válido. |
| --cert | Caminho absoluto para um ficheiro de certificado de cliente. |
| -ponto final | URL final do cluster, incluindo porto e prefixo HTTP ou HTTPS. Tipicamente, o ponto final será\:algo como https \://<seu url>19080. Se não for dado nenhum ponto\:final,\:será indefinido para http//localhost 19080.  \: Predefinido\:http //localhost\:19080. |
| --chave | Caminho absoluto para o ficheiro chave do certificado de cliente. |
| -- não verificar | Desative a verificação dos\: certificados ao utilizar HTTPS, note que esta é uma opção insegura e não deve ser utilizada para ambientes de produção. |
| --pem | Caminho absoluto para o certificado de cliente, como um ficheiro .pem. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-connection
Mostre a que cluster de tecido de serviço esta instância de sfctl está ligada.

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-unprovision"></a>unprovisionamento de cluster sfctl
Desfornecer os pacotes de código ou configuração de um cluster de tecido de serviço.

É suportado ao código de unprovisionamento e configuração separadamente.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --versão código | A versão do pacote de código de cluster. |
| --config-versão | A versão manifesto do cluster. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-upgrade"></a>upgrade de cluster sfctl
Comece a atualizar o código ou versão de configuração de um cluster de Tecido de Serviço.

Valide os parâmetros de atualização fornecidos e comece a atualizar o código ou a versão de configuração de um cluster de tecido de serviço se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -app-health-map | JSON codificadicionário codificado de pares de nome de aplicação e percentagem máxima insalubre antes de aumentar o erro. |
| -app-tipo-mapa de saúde | JSON codificadicionário codificado de pares de nome tipo de aplicação e percentagem máxima insalubre antes de aumentar o erro. |
| --versão código | A versão do código de cluster. |
| --config-versão | A versão de configuração do cluster. |
| --delta-avaliação de saúde | Permite a avaliação da saúde delta em vez de uma avaliação absoluta de saúde após a conclusão de cada domínio de upgrade. |
| --delta-insalubre-nódosos | A percentagem máxima permitida de degradação da saúde dos nós permitida durante as atualizações do cluster.  Padrão\: 10. <br><br> O delta é medido entre o estado dos nós no início da atualização e o estado dos nós no momento da avaliação da saúde. O cheque é realizado após cada atualização de upgrade de domínio concluído para garantir que o estado global do cluster está dentro dos limites tolerados. |
| --falha-acção | Os valores possíveis incluem\: 'Inválido', 'Rollback', 'Manual'. |
| --reinício da força | Os processos são reiniciados com força durante a atualização, mesmo quando a versão do código não mudou. <br><br> A atualização apenas altera a configuração ou os dados. |
| --saúde-check-retry | O período de tempo entre as tentativas de realização de verificações de saúde se a aplicação ou o cluster não forem saudáveis. |
| --saúde-check-estável | O tempo que a aplicação ou o cluster devem manter-se saudáveis antes da atualização passar para o próximo domínio de atualização. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --saúde-check-wait | O tempo de espera após completar um domínio de atualização antes de iniciar o processo de verificação de saúde. |
| --replica-set-check-out | O máximo de tempo para bloquear o processamento de um domínio de upgrade e evitar a perda de disponibilidade quando há problemas inesperados. <br><br> Quando este prazo expirar, o processamento do domínio de atualização prosseguirá independentemente dos problemas de perda de disponibilidade. O tempo é reiniciado no início de cada domínio de atualização. Os valores válidos estão entre 0 e 42949672925 inclusive. |
| --modo de atualização de rolling-upgrade | Os valores possíveis incluem\: 'Inválido', 'UnmonitoredAuto', 'Unmonitormanual', 'Monitored'.  Padrão\: Não monitorizadoAuto. |
| --timeout -t | Padrão\: 60. |
| --aplicações pouco saudáveis | A percentagem máxima permitida de aplicações não saudáveis antes de reportar um erro. <br><br> Por exemplo, para permitir que 10% das aplicações não sejam saudáveis, este valor seria de 10. A percentagem representa a percentagem máxima tolerada de aplicações que podem ser insalubres antes de o cluster ser considerado por engano. Se a percentagem for respeitada mas houver pelo menos uma aplicação pouco saudável, a saúde é avaliada como Aviso. Isto é calculado dividindo o número de aplicações não saudáveis sobre o número total de instâncias de aplicação no cluster, excluindo aplicações de tipos de aplicações que estão incluídas no ApplicationTypeHealthPolicyMap. O cálculo reúne-se para tolerar uma falha em um pequeno número de aplicações. |
| ---não-saudáveis-nódosos | A percentagem máxima permitida de nódosos não saudáveis antes de reportar um erro. <br><br> Por exemplo, para permitir que 10% dos nós não sejam saudáveis, este valor seria de 10. A percentagem representa a percentagem máxima tolerada de nós que pode ser insalubre antes de o cluster ser considerado por engano. Se a percentagem for respeitada mas houver pelo menos um nó insalubre, a saúde é avaliada como Aviso. A percentagem é calculada dividindo o número de nós não saudáveis sobre o número total de nós no cluster. A computação reúne-se para tolerar uma falha em pequenos números de nódosos. Em grandes aglomerados, alguns nós estarão sempre para baixo ou para reparações, por isso esta percentagem deve ser configurada para tolerar isso. |
| -upgrade-domínio-delta-insalubre-nódosos | A percentagem máxima permitida de nós de domínio de atualização é permitida na degradação da saúde permitida durante as atualizações do cluster.  Padrão\: 15. <br><br> O delta é medido entre o estado dos nós de domínio de upgrade no início da atualização e o estado dos nós de domínio de upgrade no momento da avaliação da saúde. A verificação é realizada após a conclusão de cada atualização de upgrade de domínio para todos os domínios de atualização concluídos para garantir que o estado dos domínios de atualização está dentro dos limites tolerados. |
| --upgrade-tempo de domínio | O tempo que cada domínio de atualização tem de completar antes da execução da Ação de Falha. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --upgrade-timeout | O tempo que a atualização global tem de completar antes da execução da FailAction. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --aviso-como-erro | Indica se os avisos são tratados com a mesma gravidade que os erros. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl cluster upgrade-currículo
Faça a atualização do cluster passar para o próximo domínio de atualização.

Faça o código de cluster ou a atualização de configuração passar para o próximo domínio de atualização, se for caso disso.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --domínio de atualização [Obrigatório] | O próximo domínio de atualização para este upgrade do cluster. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl cluster upgrade-rollback
Recue a atualização de um cluster de tecido de serviço.

Recue o código ou a atualização de configuração de um cluster de tecido de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-upgrade-status"></a>sfctl cluster upgrade-status
Obtém o progresso da atual atualização do cluster.

Obtém o progresso atual da atualização do cluster em curso. Se não houver atualização atualmente em curso, obtenha o último estado da atualização do cluster anterior.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-cluster-upgrade-update"></a>sfctl cluster upgrade-upgrade
Atualize os parâmetros de atualização de uma atualização do cluster Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -app-health-map | JSON codificadicionário codificado de pares de nome de aplicação e percentagem máxima insalubre antes de aumentar o erro. |
| -app-tipo-mapa de saúde | JSON codificadicionário codificado de pares de nome tipo de aplicação e percentagem máxima insalubre antes de aumentar o erro. |
| --delta-avaliação de saúde | Permite a avaliação da saúde delta em vez de uma avaliação absoluta de saúde após a conclusão de cada domínio de upgrade. |
| --delta-insalubre-nódosos | A percentagem máxima permitida de degradação da saúde dos nós permitida durante as atualizações do cluster.  Padrão\: 10. <br><br> O delta é medido entre o estado dos nós no início da atualização e o estado dos nós no momento da avaliação da saúde. O cheque é realizado após cada atualização de upgrade de domínio concluído para garantir que o estado global do cluster está dentro dos limites tolerados. |
| --falha-acção | Os valores possíveis incluem\: 'Inválido', 'Rollback', 'Manual'. |
| --reinício da força | Os processos são reiniciados com força durante a atualização, mesmo quando a versão do código não mudou. <br><br> A atualização apenas altera a configuração ou os dados. |
| --saúde-check-retry | O período de tempo entre as tentativas de realização de verificações de saúde se a aplicação ou o cluster não forem saudáveis. |
| --saúde-check-estável | O tempo que a aplicação ou o cluster devem manter-se saudáveis antes da atualização passar para o próximo domínio de atualização. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --saúde-check-wait | O tempo de espera após completar um domínio de atualização antes de iniciar o processo de verificação de saúde. |
| --replica-set-check-out | O máximo de tempo para bloquear o processamento de um domínio de upgrade e evitar a perda de disponibilidade quando há problemas inesperados. <br><br> Quando este prazo expirar, o processamento do domínio de atualização prosseguirá independentemente dos problemas de perda de disponibilidade. O tempo é reiniciado no início de cada domínio de atualização. Os valores válidos estão entre 0 e 42949672925 inclusive. |
| --modo de atualização de rolling-upgrade | Os valores possíveis incluem\: 'Inválido', 'UnmonitoredAuto', 'Unmonitormanual', 'Monitored'.  Padrão\: Não monitorizadoAuto. |
| --timeout -t | Padrão\: 60. |
| --aplicações pouco saudáveis | A percentagem máxima permitida de aplicações não saudáveis antes de reportar um erro. <br><br> Por exemplo, para permitir que 10% das aplicações não sejam saudáveis, este valor seria de 10. A percentagem representa a percentagem máxima tolerada de aplicações que podem ser insalubres antes de o cluster ser considerado por engano. Se a percentagem for respeitada mas houver pelo menos uma aplicação pouco saudável, a saúde é avaliada como Aviso. Isto é calculado dividindo o número de aplicações não saudáveis sobre o número total de instâncias de aplicação no cluster, excluindo aplicações de tipos de aplicações que estão incluídas no ApplicationTypeHealthPolicyMap. O cálculo reúne-se para tolerar uma falha em um pequeno número de aplicações. |
| ---não-saudáveis-nódosos | A percentagem máxima permitida de nódosos não saudáveis antes de reportar um erro. <br><br> Por exemplo, para permitir que 10% dos nós não sejam saudáveis, este valor seria de 10. A percentagem representa a percentagem máxima tolerada de nós que pode ser insalubre antes de o cluster ser considerado por engano. Se a percentagem for respeitada mas houver pelo menos um nó insalubre, a saúde é avaliada como Aviso. A percentagem é calculada dividindo o número de nós não saudáveis sobre o número total de nós no cluster. A computação reúne-se para tolerar uma falha em pequenos números de nódosos. Em grandes aglomerados, alguns nós estarão sempre para baixo ou para reparações, por isso esta percentagem deve ser configurada para tolerar isso. |
| -upgrade-domínio-delta-insalubre-nódosos | A percentagem máxima permitida de nós de domínio de atualização é permitida na degradação da saúde permitida durante as atualizações do cluster.  Padrão\: 15. <br><br> O delta é medido entre o estado dos nós de domínio de upgrade no início da atualização e o estado dos nós de domínio de upgrade no momento da avaliação da saúde. A verificação é realizada após a conclusão de cada atualização de upgrade de domínio para todos os domínios de atualização concluídos para garantir que o estado dos domínios de atualização está dentro dos limites tolerados. |
| --upgrade-tempo de domínio | O tempo que cada domínio de atualização tem de completar antes da execução da Ação de Falha. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --upgrade-tipo | Os valores possíveis incluem\: "Inválido", "Rolling", "Rolling_ForceRestart".  Rolling\: padrão. |
| --upgrade-timeout | O tempo que a atualização global tem de completar antes da execução da FailAction. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --aviso-como-erro | Indica se os avisos são tratados com a mesma gravidade que os erros. |

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
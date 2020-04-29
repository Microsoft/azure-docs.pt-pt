---
title: Azure Service Fabric CLI-sfctl replica
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para gerir réplicas.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f6ad0b4c08ac8d710340fe654a068d0a3804e58f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905816"
---
# <a name="sfctl-replica"></a>sfctl replica
Gerencie as réplicas que pertencem às divisórias de serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| implantado | Obtém os detalhes da réplica implantada num nó de Tecido de Serviço. |
| lista implantada | Obtém a lista de réplicas implantadas num nó de Tecido de Serviço. |
| saúde | Obtém a saúde de uma réplica de serviço de serviço de serviço de serviço de serviço ou de serviço apátrida. |
| informações | Obtém a informação sobre uma réplica de uma divisória de Tecido de Serviço. |
| list | Obtém a informação sobre réplicas de uma partição de serviço de serviço de tecido de serviço. |
| remover | Remove uma réplica de serviço em funcionamento num nó. |
| relatório-saúde | Envia um relatório de saúde sobre a réplica do Tecido de Serviço. |
| restart | Reinicia uma réplica de serviço de um serviço persistente em funcionamento num nó. |

## <a name="sfctl-replica-deployed"></a>réplica sfctl implantada
Obtém os detalhes da réplica implantada num nó de Tecido de Serviço.

Obtém os detalhes da réplica num nó de Tecido de Serviço. A informação inclui tipo de serviço, nome de serviço, operação de serviço atual, hora de início da operação de serviço atual, ID de partição, ID de réplica/instância, carga reportada e outras informações.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-replica-deployed-list"></a>sfctl réplica implantada lista
Obtém a lista de réplicas implantadas num nó de Tecido de Serviço.

Obtém a lista que contém a informação sobre réplicas implantadas num nó de Tecido de Serviço. As informações incluem id de partição, id réplica, estado da réplica, nome do serviço, nome do tipo de serviço, e outras informações. Utilize parâmetros de consulta PartitionId ou ServiceManifestName para devolver informações sobre as réplicas implementadas que correspondam aos valores especificados para esses parâmetros.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --partição-id | A identidade da partição. |
| --nome manifesto de serviço | O nome de um manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-replica-health"></a>sfctl replica saúde
Obtém a saúde de uma réplica de serviço de serviço de serviço de serviço de serviço ou de serviço apátrida.

Obtém a saúde de uma réplica de Tecido de Serviço. Use EventsHealthStateFilter para filtrar a coleção de eventos de saúde relatados na réplica com base no estado de saúde.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
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

## <a name="sfctl-replica-info"></a>sfctl réplica informação
Obtém a informação sobre uma réplica de uma divisória de Tecido de Serviço.

A resposta inclui o ID, papel, estado, saúde, nome do nó, uptime, e outros detalhes sobre a réplica.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-replica-list"></a>lista de réplicas sfctl
Obtém a informação sobre réplicas de uma partição de serviço de serviço de tecido de serviço.

O ponto final getReplicas devolve informações sobre as réplicas da partição especificada. A resposta inclui o ID, papel, estado, saúde, nome do nó, uptime, e outros detalhes sobre a réplica.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --continuação-token | O parâmetro simbólico de continuação é utilizado para obter o próximo conjunto de resultados. Um símbolo de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se encaixam numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o sinal de continuação não contém um valor. O valor deste parâmetro não deve ser codificado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-replica-remove"></a>réplica sfctl remover
Remove uma réplica de serviço em funcionamento num nó.

Esta API simula uma falha de réplica de tecido de serviço removendo uma réplica de um cluster de Tecido de Serviço. A remoção fecha a réplica, transita a réplica para o papel Nenhum, e depois remove toda a informação do estado da réplica do cluster. Esta API testa a rota de remoção do estado de réplica, e simula o caminho permanente da falha do relatório através das APIs do cliente. Aviso - Não são efetuadas verificações de segurança quando esta API é utilizada. A utilização incorreta desta API pode levar à perda de dados para serviços estatais. Além disso, a forçaRemover a bandeira impacta todas as outras réplicas alojadas no mesmo processo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
| --remoção da força | Remova uma aplicação ou serviço de tecido de serviço com força sem passar pela sequência de paragem graciosa. Este parâmetro pode ser utilizado para eliminar com força uma aplicação ou serviço para o qual a eliminação é cronometragem devido a problemas no código de serviço que impedem o encerramento gracioso das réplicas. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-replica-report-health"></a>sfctl réplica relatório-saúde
Envia um relatório de saúde sobre a réplica do Tecido de Serviço.

Informa o estado de saúde da réplica especificada do Tecido de Serviço. O relatório deve conter as informações sobre a origem do relatório de saúde e dos bens em que é comunicado. O relatório é enviado para uma réplica de gateway de tecido de serviço, que reencaminha para a loja de saúde. O relatório pode ser aceite pela porta de entrada, mas rejeitado pela loja de saúde após validação extra. Por exemplo, a loja de saúde pode rejeitar o relatório por causa de um parâmetro inválido, como um número de sequência velha. Para ver se o relatório foi aplicado na loja de saúde, faça a réplica da saúde e verifique se o relatório aparece na secção HealthEvents.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --propriedade de saúde [Necessária] | A propriedade da informação de saúde. <br><br> Uma entidade pode ter relatórios de saúde para diferentes propriedades. A propriedade é uma corda e não uma enumeração fixa para permitir que a flexibilidade do repórter categorize a condição do Estado que desencadeia o relatório. Por exemplo, um repórter com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que possa reportar a propriedade "AvailableDisk" nesse nó. O mesmo repórter pode monitorizar a conectividade do nó, para que possa reportar uma propriedade "Conectividade" no mesmo nó. Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para o nó especificado. Juntamente com o SourceId, a propriedade identifica exclusivamente a informação de saúde. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem\: 'Inválido', 'Ok', 'Aviso', 'Erro', 'Desconhecido'. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | A identidade da partição. |
| --source-id [Obrigatório] | O nome de origem que identifica o componente cliente/cão de guarda/sistema que gerou a informação de saúde. |
| --descrição | A descrição da informação de saúde. <br><br> Representa texto gratuito utilizado para adicionar informações legíveis humanas sobre o relatório. O comprimento máximo da corda para a descrição é de 4096 caracteres. Se a corda fornecida for mais comprida, será automaticamente truncada. Quando truncados, os últimos caracteres da descrição contêm um marcador "[Truncado]", e o tamanho total da corda é de 4096 caracteres. A presença do marcador indica aos utilizadores que ocorreu a truncação. Note que quando truncado, a descrição tem menos de 4096 caracteres da cadeia original. |
| -imediatamente | Uma bandeira que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de saúde é enviado para uma aplicação de gateway de tecido de serviço, que reencaminha para a loja de saúde. Se o Immediate for definido como verdadeiro, o relatório é enviado imediatamente de HTTP Gateway para a loja de saúde, independentemente das definições do cliente de tecido que a Aplicação HTTP Gateway está a usar. Isto é útil para relatórios críticos que devem ser enviados o mais rapidamente possível. Dependendo do tempo e de outras condições, o envio do relatório pode ainda falhar, por exemplo, se o Gateway HTTP estiver fechado ou a mensagem não chegar ao Gateway. Se imediatamente for definido como falso, o relatório é enviado com base nas definições do cliente de saúde a partir do HTTP Gateway. Portanto, será loteizado de acordo com a configuração HealthReportSendInterval. Esta é a configuração recomendada porque permite ao cliente de saúde otimizar mensagens de relatórios de saúde para a loja de saúde, bem como processamento de relatórios de saúde. Por defeito, os relatórios não são enviados imediatamente. |
| -remover-quando expirado | Valor que indica se o relatório é removido da loja de saúde quando expira. <br><br> Se for verdade, o relatório é removido da loja de saúde após o seu termo. Se for definido como falso, o relatório é tratado como um erro quando expirado. O valor desta propriedade é falso por defeito. Quando os clientes reportam periodicamente, devem definir RemoverQuando Expirado falso (predefinido). Desta forma, é que o repórter tem problemas (por exemplo, impasse) e não pode reportar, a entidade é avaliada por engano quando o relatório de saúde expira. Isto sinaliza a entidade como estando em estado de saúde error. |
| --número de sequência | O número da sequência deste relatório de saúde como uma corda numérica. <br><br> O número da sequência do relatório é usado pela loja de saúde para detetar relatórios antigos. Se não especificado, um número de sequência é gerado automaticamente pelo cliente de saúde quando um relatório é adicionado. |
| -tipo de serviço | O tipo de réplica de serviço (apátrida ou apátrida) para a qual a saúde está a ser reportada. Seguem-se\: os valores possíveis 'Apátridas', 'Apátridas'.  Estado\: padrão. |
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

## <a name="sfctl-replica-restart"></a>sfctl réplica reiniciar
Reinicia uma réplica de serviço de um serviço persistente em funcionamento num nó.

Reinicia uma réplica de serviço de um serviço persistente em funcionamento num nó. Aviso - Não são efetuadas verificações de segurança quando esta API é utilizada. O uso incorreto desta API pode levar à perda de disponibilidade de serviços estatais.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
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

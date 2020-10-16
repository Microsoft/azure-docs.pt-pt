---
title: Réplica CLI-sfctl de tecido de serviço Azure
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para a gestão de réplicas.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3ed1ecca686471e052f07e945738ad1a49e68464
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260307"
---
# <a name="sfctl-replica"></a>sfctl replica
Gerir as réplicas que pertencem a divisórias de serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| implantado | Obtém os detalhes da réplica implantados num nó de Tecido de Serviço. |
| lista implantada | Obtém a lista de réplicas implantadas num nó de Tecido de Serviço. |
| Saúde | Obtém a saúde de uma réplica de serviço stateful Fabric ou de serviço apátrida. |
| informações | Obtém a informação sobre uma réplica de uma divisória de Tecido de Serviço. |
| lista | Obtém a informação sobre réplicas de uma divisória de serviço de tecido de serviço. |
| remover | Remove uma réplica de serviço em execução num nó. |
| relatório-saúde | Envia um relatório de saúde sobre a réplica do Tecido de Serviço. |
| restart | Reinicia uma réplica de serviço de um serviço persistido que funciona num nó. |

## <a name="sfctl-replica-deployed"></a>réplica sfctl implantada
Obtém os detalhes da réplica implantados num nó de Tecido de Serviço.

Obtém os detalhes da réplica implantados num nó de Tecido de Serviço. A informação inclui o tipo de serviço, nome de serviço, operação de serviço atual, operação de serviço atual hora de início da data, ID de partição, ID de réplica/instância, carga reportada e outras informações.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-replica-deployed-list"></a>sfctl réplica implantada lista
Obtém a lista de réplicas implantadas num nó de Tecido de Serviço.

Obtém a lista que contém as informações sobre réplicas implantadas num nó de Tecido de Serviço. As informações incluem iD de partição, ID de réplica, estado da réplica, nome do serviço, nome do tipo de serviço e outras informações. Utilize os parâmetros de consulta PartitionId ou ServiceManifestName para devolver informações sobre as réplicas implantadas que correspondam aos valores especificados para esses parâmetros.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --partição-id | A identidade da partição. |
| --serviço-manifesto-nome | O nome de um manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-replica-health"></a>sfctl saúde réplica
Obtém a saúde de uma réplica de serviço stateful Fabric ou de serviço apátrida.

Obtém a saúde de uma réplica de tecido de serviço. Use EventosHealthStateFilter para filtrar a coleção de eventos de saúde relatados na réplica com base no estado de saúde.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
| --eventos-saúde-estado-filtro | Permite filtrar a recolha de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, Se o valor fornecido for 6, todos os eventos com o valor healthState de OK (2) e Aviso (4) são devolvidos.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-replica-info"></a>sfctl réplica informação
Obtém a informação sobre uma réplica de uma divisória de Tecido de Serviço.

A resposta inclui o ID, função, estado, saúde, nome do nó, uptime, e outros detalhes sobre a réplica.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-replica-list"></a>lista de réplicas sfctl
Obtém a informação sobre réplicas de uma divisória de serviço de tecido de serviço.

O ponto final getReplicas devolve informações sobre as réplicas da partição especificada. A resposta inclui o ID, função, estado, saúde, nome do nó, uptime, e outros detalhes sobre a réplica.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificado por URL. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-replica-remove"></a>réplica sfctl remover
Remove uma réplica de serviço em execução num nó.

Esta API simula uma falha de réplica de tecido de serviço removendo uma réplica de um cluster de Tecido de Serviço. A remoção fecha a réplica, transita a réplica para o papel Nenhum, e depois remove toda a informação do estado da réplica do cluster. Esta API testa a via de remoção do estado réplica e simula o caminho permanente da falha do relatório através das APIs do cliente. Aviso - Não existem verificações de segurança quando esta API é utilizada. A utilização incorreta desta API pode levar à perda de dados para serviços estatais. Além disso, a bandeira forceRemove impacta todas as outras réplicas hospedadas no mesmo processo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
| ...-força-remoção | Remova uma aplicação ou serviço de tecido de serviço com força sem passar pela sequência graciosa de paragem. Este parâmetro pode ser usado para eliminar com força uma aplicação ou serviço para o qual a eliminação está fora devido a problemas no código de serviço que impede o encerramento gracioso de réplicas. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-replica-report-health"></a>sfctl réplica relatório-saúde
Envia um relatório de saúde sobre a réplica do Tecido de Serviço.

Relatórios de estado de saúde da réplica de tecido de serviço especificado. O relatório deve conter as informações sobre a origem do relatório de saúde e sobre os bens em que é comunicado. O relatório é enviado para uma réplica de porta de entrada de tecido de serviço, que reencaminha para a loja de saúde. O relatório pode ser aceite pela porta de entrada, mas rejeitado pela loja de saúde após validação extra. Por exemplo, a loja de saúde pode rejeitar o relatório por causa de um parâmetro inválido, como um número de sequência velha. Para ver se o relatório foi aplicado na loja de saúde, gere a saúde da réplica e verifica se o relatório aparece na secção HealthEvents.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --saúde-propriedade [Necessária] | A propriedade da informação de saúde. <br><br> Uma entidade pode ter relatórios de saúde para diferentes propriedades. A propriedade é uma corda e não uma enumeração fixa para permitir ao repórter flexibilidade para categorizar a condição do estado que desencadeia o relatório. Por exemplo, um repórter com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que possa reportar a propriedade "AvailableDisk" nesse nó. O mesmo repórter pode monitorizar a conectividade do nó, para que possa reportar uma propriedade "Conectividade" no mesmo nó. Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para o nó especificado. Juntamente com o SourceId, a propriedade identifica exclusivamente a informação de saúde. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem \: 'Inválido', 'Ok', 'Aviso', 'Erro', 'Desconhecido'. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | A identidade da partição. |
| --source-id [Obrigatório] | O nome de origem que identifica o componente cliente/cão de guarda/sistema que gerou a informação de saúde. |
| --descrição | A descrição da informação de saúde. <br><br> Representa um texto gratuito utilizado para adicionar informações legíveis humanas sobre o relatório. O comprimento máximo da corda para a descrição é de 4096 caracteres. Se a corda fornecida for mais comprida, será automaticamente truncada. Quando truncados, os últimos caracteres da descrição contêm um marcador "[Truncated]", e o tamanho total da corda é de 4096 caracteres. A presença do marcador indica aos utilizadores que a truncação ocorreu. Note que, quando truncado, a descrição tem menos de 4096 caracteres da corda original. |
| --imediata | Uma bandeira que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de saúde é enviado para uma aplicação de gateway de tecido de serviço, que reencaminha para a loja de saúde. Se o Imediato for definido como verdadeiro, o relatório é enviado imediatamente de HTTP Gateway para a loja de saúde, independentemente das definições do cliente de tecido que a Aplicação HTTP Gateway está a usar. Isto é útil para relatórios críticos que devem ser enviados o mais rapidamente possível. Dependendo do tempo e de outras condições, o envio do relatório pode ainda falhar, por exemplo, se o GATEWAY HTTP estiver fechado ou a mensagem não chegar ao Gateway. Se o Immediate for definido como falso, o relatório é enviado com base nas definições do cliente de saúde a partir do GATEWAY HTTP. Portanto, será loteado de acordo com a configuração HealthReportSendInterval. Esta é a configuração recomendada porque permite ao cliente de saúde otimizar as mensagens de relato de saúde para a loja de saúde, bem como o processamento de relatórios de saúde. Por predefinição, os relatórios não são enviados imediatamente. |
| --remover-quando expirado | Valor que indica se o relatório é removido da loja de saúde quando expira. <br><br> Se for verdadeiro, o relatório é removido da loja de saúde depois de expirar. Se for definido como falso, o relatório é tratado como um erro quando expirado. O valor desta propriedade é falso por defeito. Quando os clientes reportam periodicamente, devem definir RemoveWhenExpired falso (padrão). Desta forma, o repórter tem problemas (por exemplo, impasse) e não pode reportar, a entidade é avaliada por erro quando o relatório de saúde expira. Isto sinaliza a entidade como estando no estado de saúde error. |
| --número de sequência | O número da sequência deste relatório de saúde como uma corda numérica. <br><br> O número da sequência do relatório é usado pela loja de saúde para detetar relatórios antigos. Se não for especificado, um número de sequência é gerado automaticamente pelo cliente de saúde quando um relatório é adicionado. |
| --tipo de serviço | O tipo de réplica de serviço (apátrida ou imponente) para a qual a saúde está a ser reportada. Seguem-se os possíveis valores \: 'apátridas', 'Stateful'.  Estado \: padrão. |
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

## <a name="sfctl-replica-restart"></a>recomeçar réplica sfctl
Reinicia uma réplica de serviço de um serviço persistido que funciona num nó.

Reinicia uma réplica de serviço de um serviço persistido que funciona num nó. Aviso - Não existem verificações de segurança quando esta API é utilizada. A utilização incorreta desta API pode levar à perda de disponibilidade para serviços estatais.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

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

---
title: Azure Service Fabric CLI-sfctl compor
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para aplicações Docker Compose.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: e84b35ff3dfa7be0c848a4d7b906c622d9b88914
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257344"
---
# <a name="sfctl-compose"></a>sfctl compose
Criar, excluir e gerir aplicações Docker Compose.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| criar | Cria uma implantação de composição de tecido de serviço. |
| lista | Obtém a lista de implementações de composição criadas no cluster de Tecido de Serviço. |
| remover | Elimina uma implantação de composição de tecido de serviço existente do cluster. |
| status | Obtém informações sobre uma implantação de composição de tecido de serviço. |
| atualizar | Inicia a atualização de uma implantação de composição no cluster de Tecido de Serviço. |
| upgrade-rollback | Começa a reverter uma atualização de implementação de composição no cluster de Tecido de Serviço. |
| estado de atualização | Obtém detalhes para a mais recente atualização realizada nesta implementação de composição de Tecido de Serviço. |

## <a name="sfctl-compose-create"></a>sfctl compor criar
Cria uma implantação de composição de tecido de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de implantação [Obrigatório] | O nome da implantação. |
| --caminho de arquivo [Obrigatório] | Caminho para o ficheiro target Docker Compose. |
| --passe encriptado | Em vez de pedir uma senha de registo de contentores, use uma frase-passe já encriptada. |
| --tem-passe | Pedirá uma senha para o registo do contentor. |
| --timeout -t | Padrão \: 60. |
| --user | Nome do utilizador para ligar ao registo do contentor. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-compose-list"></a>lista de composição sfctl
Obtém a lista de implementações de composição criadas no cluster de Tecido de Serviço.

Obtém o estado sobre as implementações de composição que foram criadas ou em processo de criação no cluster de Tecido de Serviço. A resposta inclui o nome, estado e outros detalhes sobre as implementações de composição. Se a lista de implementações não encaixar numa página, uma página de resultados é devolvida, bem como um token de continuação, que pode ser usado para obter a página seguinte.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificado por URL. |
| --resultados máximos | O número máximo de resultados a devolver como parte das consultas de página. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições máximas do tamanho da mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta paged inclui o maior número possível de resultados que se encaixam na mensagem de retorno. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-compose-remove"></a>sfctl compor remover
Elimina uma implantação de composição de tecido de serviço existente do cluster.

Elimina uma implantação de composição de tecido de serviço existente.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de implantação [Obrigatório] | A identidade do destacamento. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-compose-status"></a>sfctl composição status
Obtém informações sobre uma implantação de composição de tecido de serviço.

Devolve o estado da implantação de composição que foi criada ou em processo de criação no cluster de Tecido de Serviço e cujo nome corresponde ao especificado como parâmetro. A resposta inclui o nome, estado e outros detalhes sobre a implantação.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de implantação [Obrigatório] | A identidade do destacamento. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-compose-upgrade"></a>sfctl compor upgrade
Inicia a atualização de uma implantação de composição no cluster de Tecido de Serviço.

Valida os parâmetros de atualização fornecidos e começa a atualizar a implementação se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de implantação [Obrigatório] | O nome da implantação. |
| --caminho de arquivo [Obrigatório] | Caminho para o ficheiro de composição do Docker. |
| --Padrão-svc-tipo-mapa de saúde | O dicionário codificado json que descreve a política de saúde usada para avaliar a saúde dos serviços. |
| --passe encriptado | Em vez de pedir uma senha de registo de contentores, use uma frase-passe já encriptada. |
| ...-falha de ação | Os valores possíveis incluem \: 'Inválido', 'Rollback', 'Manual'. |
| ...-força-reiniciar | Os processos são reiniciados com força durante a atualização, mesmo quando a versão código não foi alterada. <br><br> A atualização apenas altera a configuração ou dados. |
| --tem-passe | Pedirá uma senha para o registo do contentor. |
| --verificação da saúde-redação | O período de tempo entre tentativas de efetuar controlos de saúde se a aplicação ou o agrupamento não forem saudáveis. |
| --saúde-check-estável | O tempo que a aplicação ou cluster deve permanecer saudável antes da atualização avançar para o próximo domínio de atualização. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --saúde-check-espera | O tempo de espera após completar um domínio de upgrade antes de iniciar o processo de verificação de saúde. |
| --réplica-verificação de conjuntos | O tempo máximo para bloquear o processamento de um domínio de upgrade e evitar a perda de disponibilidade quando há problemas inesperados. <br><br> Quando este tempo de tempo expirar, o processamento do domínio de atualização irá prosseguir independentemente dos problemas de perda de disponibilidade. O tempo limite é reiniciado no início de cada domínio de atualização. Os valores válidos são entre 0 e 42949672925 inclusive. |
| --svc-tipo-mapa de saúde | A JSON codificou a lista de objetos que descrevem as políticas de saúde usadas para avaliar a saúde de diferentes tipos de serviços. |
| --timeout -t | Padrão \: 60. |
| --aplicação pouco saudável | A percentagem máxima permitida de aplicações não saudáveis antes de reportar um erro. <br><br> Por exemplo, para permitir que 10% das aplicações não sejam saudáveis, este valor seria de 10. A percentagem representa a percentagem máxima tolerada de aplicações que podem não ser saudáveis antes de o cluster ser considerado errado. Se a percentagem for respeitada mas houver pelo menos uma aplicação pouco saudável, a saúde é avaliada como Advertência. Isto é calculado dividindo o número de aplicações não saudáveis sobre o número total de instâncias de aplicação no cluster. |
| --upgrade-tempo limite de domínio | O tempo que cada domínio de atualização tem de completar antes da execução do FailureAction. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --upgrade-tipo | \:Rolamento padrão. |
| --modo de atualização | Os valores possíveis incluem \: 'Inválido', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitorado'.  Padrão \: não monitorizado. |
| --upgrade-tempo limite | O tempo que a atualização global tem de ser concluída antes da execução do FailureAction. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --user | Nome do utilizador para ligar ao registo do contentor. |
| ...-aviso-como-erro | Indica se os avisos são tratados com a mesma gravidade que os erros. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl compor upgrade-rollback
Começa a reverter uma atualização de implementação de composição no cluster de Tecido de Serviço.

Revoque uma atualização de implementação de tecido de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de implantação [Obrigatório] | A identidade do destacamento. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl compor estado de upgrade
Obtém detalhes para a mais recente atualização realizada nesta implementação de composição de Tecido de Serviço.

Devolve as informações sobre o estado da atualização da implantação de composição, juntamente com detalhes para ajudar a depurar problemas de saúde de aplicações.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --nome de implantação [Obrigatório] | A identidade do destacamento. |
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
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .

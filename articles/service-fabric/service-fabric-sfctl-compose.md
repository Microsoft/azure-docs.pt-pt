---
title: Azure Service Fabric CLI-sfctl comp
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para aplicações de Docker Compose.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1e40ca4e3c5ec8b7566646aa7ef723bd4c9e45a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906127"
---
# <a name="sfctl-compose"></a>sfctl compose
Crie, elimine e gereas aplicações do Docker Compose.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| criar | Cria uma implantação de composição de tecido de serviço. |
| list | Obtém a lista de implementações de composição criadas no cluster Service Fabric. |
| remover | Elimina uma implantação existente do Tecido de Serviço do Cluster. |
| status | Obtém informações sobre uma implantação de composição de tecido de serviço. |
| atualizar | Começa a atualizar uma implantação de composição no cluster Service Fabric. |
| upgrade-rollback | Começa a reverter uma atualização de implantação de composição no cluster Service Fabric. |
| estado de atualização | Obtém detalhes para a mais recente atualização realizada nesta implementação de composição de Tecido de Serviço. |

## <a name="sfctl-compose-create"></a>sfctl compor criar
Cria uma implantação de composição de tecido de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [Obrigatório] | O nome do destacamento. |
| --caminho de arquivo [Obrigatório] | Caminho para o ficheiro alvo Docker Compose. |
| --passe encriptado | Em vez de pedir uma senha de registo de contentores, utilize uma frase de passe já encriptada. |
| --tem-passe | Pedirá uma senha para o registo do contentor. |
| --timeout -t | Padrão\: 60. |
| --user | Nome do utilizador para ligar ao registo de contentores. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-compose-list"></a>sfctl compor lista
Obtém a lista de implementações de composição criadas no cluster Service Fabric.

Obtém o estatuto sobre as implementações de composição que foram criadas ou em processo de criação no cluster Service Fabric. A resposta inclui o nome, estado e outros detalhes sobre as implementações de composição. Se a lista de implementações não encaixar numa página, uma página de resultados é devolvida, bem como um token de continuação, que pode ser usado para obter a página seguinte.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --continuação-token | O parâmetro simbólico de continuação é utilizado para obter o próximo conjunto de resultados. Um símbolo de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se encaixam numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o sinal de continuação não contém um valor. O valor deste parâmetro não deve ser codificado. |
| -resultados máximos | O número máximo de resultados a devolver como parte das consultas páginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta páginada inclui o maior número possível de resultados que se encaixem na mensagem de devolução. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-compose-remove"></a>sfctl compor remover
Elimina uma implantação existente do Tecido de Serviço do Cluster.

Elimina uma implantação de composição de tecido de serviço existente.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [Obrigatório] | A identidade do destacamento. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-compose-status"></a>estado de composição sfctl
Obtém informações sobre uma implantação de composição de tecido de serviço.

Devolve o estado da implantação de composição que foi criada ou em processo de criação no cluster Service Fabric e cujo nome corresponde ao especificado como parâmetro. A resposta inclui o nome, estado e outros detalhes sobre a implementação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [Obrigatório] | A identidade do destacamento. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-compose-upgrade"></a>sfctl compor upgrade
Começa a atualizar uma implantação de composição no cluster Service Fabric.

Valida os parâmetros de atualização fornecidos e começa a atualizar a implementação se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [Obrigatório] | O nome do destacamento. |
| --caminho de arquivo [Obrigatório] | Caminho para o ficheiro de composição do alvo Do docker. |
| -padrão-svc-tipo-mapa de saúde | JSON codificadicionário codificado que descreve a política de saúde usada para avaliar a saúde dos serviços. |
| --passe encriptado | Em vez de pedir uma senha de registo de contentores, utilize uma frase de passe já encriptada. |
| --falha-acção | Os valores possíveis incluem\: 'Inválido', 'Rollback', 'Manual'. |
| --reinício da força | Os processos são reiniciados com força durante a atualização, mesmo quando a versão do código não mudou. <br><br> A atualização apenas altera a configuração ou os dados. |
| --tem-passe | Pedirá uma senha para o registo do contentor. |
| --saúde-check-retry | O período de tempo entre as tentativas de realização de verificações de saúde se a aplicação ou o cluster não forem saudáveis. |
| --saúde-check-estável | O tempo que a aplicação ou o cluster devem manter-se saudáveis antes da atualização passar para o próximo domínio de atualização. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --saúde-check-wait | O tempo de espera após completar um domínio de atualização antes de iniciar o processo de verificação de saúde. |
| --replica-set-check | O máximo de tempo para bloquear o processamento de um domínio de upgrade e evitar a perda de disponibilidade quando há problemas inesperados. <br><br> Quando este prazo expirar, o processamento do domínio de atualização prosseguirá independentemente dos problemas de perda de disponibilidade. O tempo é reiniciado no início de cada domínio de atualização. Os valores válidos estão entre 0 e 42949672925 inclusive. |
| --svc-tipo-mapa de saúde | JSON codificada lista de objetos que descrevem as políticas de saúde usadas para avaliar a saúde de diferentes tipos de serviço. |
| --timeout -t | Padrão\: 60. |
| --app pouco saudável | A percentagem máxima permitida de aplicações não saudáveis antes de reportar um erro. <br><br> Por exemplo, para permitir que 10% das aplicações não sejam saudáveis, este valor seria de 10. A percentagem representa a percentagem máxima tolerada de aplicações que podem ser insalubres antes de o cluster ser considerado por engano. Se a percentagem for respeitada mas houver pelo menos uma aplicação pouco saudável, a saúde é avaliada como Aviso. Isto é calculado dividindo o número de aplicações não saudáveis sobre o número total de instâncias de aplicação no cluster. |
| --upgrade-tempo de domínio | O tempo que cada domínio de atualização tem de completar antes da execução da Ação de Falha. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --upgrade-tipo | Rolling\: padrão. |
| --upgrade-mode | Os valores possíveis incluem\: 'Inválido', 'UnmonitoredAuto', 'Unmonitormanual', 'Monitored'.  Padrão\: Não monitorizadoAuto. |
| --upgrade-timeout | O tempo que a atualização global tem de completar antes da execução da FailAction. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --user | Nome do utilizador para ligar ao registo de contentores. |
| --aviso-como-erro | Indica se os avisos são tratados com a mesma gravidade que os erros. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl compor upgrade-rollback
Começa a reverter uma atualização de implantação de composição no cluster Service Fabric.

Recue um tecido de serviço compõe a atualização de implantação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [Obrigatório] | A identidade do destacamento. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl compor estado de upgrade
Obtém detalhes para a mais recente atualização realizada nesta implementação de composição de Tecido de Serviço.

Devolve as informações sobre o estado da atualização de implantação de composição, juntamente com detalhes para ajudar a depurar problemas de saúde de aplicações.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [Obrigatório] | A identidade do destacamento. |
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
- [Instale](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).
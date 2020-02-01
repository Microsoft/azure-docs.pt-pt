---
title: CLI do Azure Service Fabric-sfctl Compose
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para Docker Compose aplicativos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1e40ca4e3c5ec8b7566646aa7ef723bd4c9e45a1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906127"
---
# <a name="sfctl-compose"></a>sfctl compose
Crie, exclua e gerencie aplicativos Docker Compose.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| criar | Cria uma implantação de Service Fabric compor. |
| list | Obtém a lista de implantações de composição criadas no Cluster Service Fabric. |
| remover | Exclui uma implantação existente de composição de Service Fabric do cluster. |
| status | Obtém informações sobre uma implantação de Service Fabric compor. |
| upgrade | Inicia a atualização de uma implantação do Compose no Cluster Service Fabric. |
| upgrade-rollback | Inicia a reversão de uma atualização de implantação do Compose no Cluster Service Fabric. |
| upgrade-status | Obtém detalhes para a atualização mais recente executada neste Service Fabric a implantação de composição. |

## <a name="sfctl-compose-create"></a>sfctl compor criação
Cria uma implantação de Service Fabric compor.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [Obrigatório] | O nome da implementação. |
| --caminho de arquivo [Obrigatório] | Caminho para o arquivo de Docker Compose de destino. |
| --criptografado-aprovado | Em vez de solicitar uma senha de registro de contêiner, use uma frase secreta já criptografada. |
| --aprovado | Solicitará uma senha para o registro de contêiner. |
| --Timeout-t | Padrão\: 60. |
| --user | Nome de usuário para se conectar ao registro de contêiner. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-compose-list"></a>lista de sfctl de composição
Obtém a lista de implantações de composição criadas no Cluster Service Fabric.

Obtém o status sobre as implantações de composição que foram criadas ou no processo de criação no cluster de Service Fabric. A resposta inclui o nome, o status e outros detalhes sobre as implantações do Compose. Se a lista de implantações não couber em uma página, uma página de resultados será retornada, bem como um token de continuação, que pode ser usado para obter a próxima página.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se ajustam em uma única resposta. Quando esse valor é passado para a próxima chamada à API, a API retorna o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser menores que os resultados máximos especificados se não couberem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero ou não for especificado, a consulta paginável incluirá o máximo possível de resultados que couberem na mensagem de retorno. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-compose-remove"></a>remover sfctl compor
Exclui uma implantação existente de composição de Service Fabric do cluster.

Exclui uma implantação existente de composição de Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [Obrigatório] | A identidade da implantação. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-compose-status"></a>sfctl o status de composição
Obtém informações sobre uma implantação de Service Fabric compor.

Retorna o status da implantação de composição que foi criada ou no processo de criação no cluster de Service Fabric e cujo nome corresponde ao especificado como o parâmetro. A resposta inclui o nome, o status e outros detalhes sobre a implantação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [Obrigatório] | A identidade da implantação. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-compose-upgrade"></a>sfctl a atualização de composição
Inicia a atualização de uma implantação do Compose no Cluster Service Fabric.

Valida os parâmetros de atualização fornecidos e inicia a atualização da implantação se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [Obrigatório] | O nome da implementação. |
| --caminho de arquivo [Obrigatório] | Caminho para o arquivo do Docker Compose de destino. |
| --default-svc-type-health-map | O dicionário codificado em JSON que descreve a política de integridade usada para avaliar a integridade dos serviços. |
| --criptografado-aprovado | Em vez de solicitar uma senha de registro de contêiner, use uma frase secreta já criptografada. |
| --ação de falha | Os valores possíveis incluem\: ' invalid ', ' Rollback ', ' manual '. |
| --Force-Restart | Os processos são reiniciados de modo forçado durante a atualização, mesmo quando a versão do código não foi alterada. <br><br> A atualização só altera a configuração ou os dados. |
| --aprovado | Solicitará uma senha para o registro de contêiner. |
| --health-check-retry | O período de tempo entre as tentativas de realizar verificações de integridade se o aplicativo ou o cluster não estiver íntegro. |
| --health-check-stable | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização prossiga para o próximo domínio de atualização. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --health-check-wait | O período de tempo de espera após a conclusão de um domínio de atualização antes de iniciar o processo de verificações de integridade. |
| --replica-set-check | A quantidade máxima de tempo para bloquear o processamento de um domínio de atualização e evitar a perda de disponibilidade quando houver problemas inesperados. <br><br> Quando esse tempo limite expirar, o processamento do domínio de atualização continuará independentemente dos problemas de perda de disponibilidade. O tempo limite é redefinido no início de cada domínio de atualização. Os valores válidos estão entre 0 e 42949672925, inclusive. |
| --svc-type-health-map | Lista de objetos codificados em JSON que descrevem as políticas de integridade usadas para avaliar a integridade de diferentes tipos de serviço. |
| --Timeout-t | Padrão\: 60. |
| --unhealthy-app | A porcentagem máxima permitida de aplicativos não íntegros antes de relatar um erro. <br><br> Por exemplo, para permitir que 10% das aplicações não sejam saudáveis, este valor seria de 10. A porcentagem representa a porcentagem máxima tolerada de aplicativos que podem não estar íntegros antes que o cluster seja considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um aplicativo não íntegro, a integridade será avaliada como aviso. Isso é calculado dividindo o número de aplicativos não íntegros no número total de instâncias do aplicativo no cluster. |
| --tempo limite do domínio de atualização | A quantidade de tempo que cada domínio de atualização precisa concluir antes que a falha seja executada. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --tipo de atualização | Padrão\: sem interrupção. |
| --modo de atualização | Os valores possíveis incluem\: ' invalid ', ' UnmonitoredAuto ', ' UnmonitoredManual ', ' Monitored '.  Padrão\: UnmonitoredAuto. |
| --upgrade-timeout | O tempo durante o qual a atualização geral deve ser concluída antes de FailureAction ser executada. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --user | Nome de usuário para se conectar ao registro de contêiner. |
| --warning-as-error | Indica se os avisos são tratados com a mesma severidade que os erros. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-compose-upgrade-rollback"></a>atualização de composição de sfctl-reversão
Inicia a reversão de uma atualização de implantação do Compose no Cluster Service Fabric.

Reverta uma atualização de implantação de composição do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [Obrigatório] | A identidade da implantação. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-compose-upgrade-status"></a>atualização do sfctl Compose-status
Obtém detalhes para a atualização mais recente executada neste Service Fabric a implantação de composição.

Retorna as informações sobre o estado da atualização da implantação do Compose junto com detalhes para ajudar a depurar problemas de integridade do aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [Obrigatório] | A identidade da implantação. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
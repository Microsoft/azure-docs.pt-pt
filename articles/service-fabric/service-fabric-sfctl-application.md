---
title: CLI do Azure Service Fabric-aplicativo sfctl | Microsoft Docs
description: Descreve os comandos do aplicativo Service Fabric CLI sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 5d9728db919f15eda49602f2619f1c27fbb42b57
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036536"
---
# <a name="sfctl-application"></a>sfctl application
Criar, excluir e gerenciar aplicativos e tipos de aplicativos.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| create | Cria um aplicativo Service Fabric usando a descrição especificada. |
| delete | Exclui um aplicativo Service Fabric existente. |
| deployed | Obtém as informações sobre um aplicativo implantado em um Service Fabric nó. |
| deployed-health | Obtém as informações sobre a integridade de um aplicativo implantado em um Service Fabric nó. |
| deployed-list | Obtém a lista de aplicativos implantados em um nó de Service Fabric. |
| health | Obtém a integridade do aplicativo do Service Fabric. |
| info | Obtém informações sobre um aplicativo Service Fabric. |
| list | Obtém a lista de aplicativos criados no Cluster Service Fabric que correspondem aos filtros especificados. |
| load | Obtém informações de carga sobre um aplicativo Service Fabric. |
| manifesto | Obtém o manifesto que descreve um tipo de aplicativo. |
| provision | Provisiona ou registra um tipo de aplicativo Service Fabric com o cluster usando o pacote. sfpkg no armazenamento externo ou usando o pacote de aplicativos no repositório de imagens. |
| report-health | Envia um relatório de integridade sobre o aplicativo Service Fabric. |
| type | Obtém a lista de tipos de aplicativos no Cluster Service Fabric que correspondem exatamente ao nome especificado. |
| type-list | Obtém a lista de tipos de aplicativos no Cluster Service Fabric. |
| unprovision | Remove ou cancela o registro de um tipo de aplicativo Service Fabric do cluster. |
| upgrade | Inicia a atualização de um aplicativo no Cluster Service Fabric. |
| upgrade-resume | Retoma a atualização de um aplicativo no Cluster Service Fabric. |
| upgrade-rollback | Inicia a reversão da atualização atual em andamento de um aplicativo no Cluster Service Fabric. |
| upgrade-status | Obtém detalhes para a atualização mais recente executada neste aplicativo. |
| upload | Copie um pacote de aplicativos Service Fabric para o repositório de imagens. |

## <a name="sfctl-application-create"></a>criação de aplicativo sfctl
Cria um aplicativo Service Fabric usando a descrição especificada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --App-Name [obrigatório] | O nome do aplicativo, incluindo o esquema de URI\:' Fabric '. |
| --App-Type [obrigatório] | O nome do tipo de aplicativo encontrado no manifesto do aplicativo. |
| --App-Version [obrigatório] | A versão do tipo de aplicativo, conforme definido no manifesto do aplicativo. |
| --max-node-count | O número máximo de nós em que Service Fabric reservará a capacidade para este aplicativo. Observe que isso não significa que os serviços desse aplicativo serão colocados em todos esses nós. |
| --métricas | Uma lista codificada em JSON de descrições de métrica de capacidade do aplicativo. Uma métrica é definida como um nome, associada a um conjunto de capacidades para cada nó no qual o aplicativo existe. |
| --min-contagem de nós | O número mínimo de nós em que Service Fabric reservará a capacidade para este aplicativo. Observe que isso não significa que os serviços desse aplicativo serão colocados em todos esses nós. |
| --parâmetros | Uma lista codificada em JSON de substituições de parâmetro de aplicativo a serem aplicadas ao criar o aplicativo. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-delete"></a>exclusão do aplicativo sfctl
Exclui um aplicativo Service Fabric existente.

Um aplicativo deve ser criado antes que possa ser excluído. A exclusão de um aplicativo excluirá todos os serviços que fazem parte desse aplicativo. Por padrão, Service Fabric tentará fechar as réplicas de serviço de maneira normal e, em seguida, excluirá o serviço. No entanto, se um serviço estiver tendo problemas para fechar a réplica normalmente, a operação de exclusão poderá levar muito tempo ou ficar presa. Use o sinalizador opcional ForceRemove para ignorar a sequência de fechamento normal e forçar a exclusão do aplicativo e de todos os seus serviços.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI\:' Fabric '. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "\:Fabric/MyApp/App1", a identidade do aplicativo será "\~MyApp App1" em 6.0 + e "MyApp/App1" nas versões anteriores. |
| --force-remove | Remova um aplicativo ou serviço Service Fabric de modo forçado sem passar pela sequência de desligamento normal. Esse parâmetro pode ser usado para forçar a exclusão de um aplicativo ou serviço para o qual a exclusão está atingindo o tempo limite devido a problemas no código de serviço que impedem o fechamento normal das réplicas. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-deployed"></a>aplicativo sfctl implantado
Obtém as informações sobre um aplicativo implantado em um Service Fabric nó.

Essa consulta retornará informações do aplicativo do sistema se a ID do aplicativo fornecida for para o aplicativo do sistema. Os resultados abrangem aplicativos implantados nos Estados ativo, ativando e baixando. Essa consulta requer que o nome do nó corresponda a um nó no cluster. A consulta falhará se o nome do nó fornecido não apontar para nenhum nó do Service Fabric ativo no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI\:' Fabric '. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "\:Fabric/MyApp/App1", a identidade do aplicativo será "\~MyApp App1" em 6.0 + e "MyApp/App1" nas versões anteriores. |
| --node-Name [obrigatório] | O nome do nó. |
| --include-estado de integridade | Inclua o estado de integridade de uma entidade. Se esse parâmetro for false ou não for especificado, o estado de integridade retornado será "Unknown". Quando definido como true, a consulta entra em paralelo ao nó e ao serviço do sistema de integridade antes que os resultados sejam mesclados. Como resultado, a consulta é mais cara e pode levar mais tempo. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-deployed-health"></a>aplicativo sfctl implantado – integridade
Obtém as informações sobre a integridade de um aplicativo implantado em um Service Fabric nó.

Obtém as informações sobre a integridade de um aplicativo implantado em um Service Fabric nó. Use EventsHealthStateFilter para filtrar opcionalmente a coleção de objetos HealthEvent relatados no aplicativo implantado com base no estado de integridade. Use Deployedservicepackageshealthstatefilter foi para filtrar opcionalmente para filhos do DeployedServicePackageHealth com base no estado de integridade.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI\:' Fabric '. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "\:Fabric/MyApp/App1", a identidade do aplicativo será "\~MyApp App1" em 6.0 + e "MyApp/App1" nas versões anteriores. |
| --node-Name [obrigatório] | O nome do nó. |
| --deployed-service-packages-health-state-filter | Permite a filtragem dos objetos de estado de integridade do pacote de serviço implantados retornados no resultado da consulta de integridade do aplicativo implantada com base em seu estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade. Somente pacotes de serviço implantados que correspondem ao filtro são retornados. Todos os pacotes de serviço implantados são usados para avaliar o estado de integridade agregado do aplicativo implantado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador e, portanto, o valor pode ser uma combinação desses valores, obtida usando o operador ' OR ' bit-up. Por exemplo, se o valor fornecido for 6, o estado de integridade dos pacotes de serviço com o valor OK (2) e Warning (4) será retornado.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador e, portanto, o valor pode ser uma combinação desses valores, obtida usando o operador ' OR ' bit-up. Por exemplo, se o valor fornecido for 6, todos os eventos com o valor OK (2) e Warning (4) serão retornados.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --Exclude-Statistics-Health | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. False por padrão. As estatísticas mostram o número de entidades filhas no estado de integridade Ok, aviso e erro. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-deployed-list"></a>aplicativo sfctl implantado-lista
Obtém a lista de aplicativos implantados em um nó de Service Fabric.

Obtém a lista de aplicativos implantados em um nó de Service Fabric. Os resultados não incluem informações sobre aplicativos de sistema implantados, a menos que sejam consultados explicitamente por ID. Os resultados abrangem aplicativos implantados nos Estados ativo, ativando e baixando. Essa consulta requer que o nome do nó corresponda a um nó no cluster. A consulta falhará se o nome do nó fornecido não apontar para nenhum nó do Service Fabric ativo no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --node-Name [obrigatório] | O nome do nó. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se ajustam em uma única resposta. Quando esse valor é passado para a próxima chamada à API, a API retorna o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --include-estado de integridade | Inclua o estado de integridade de uma entidade. Se esse parâmetro for false ou não for especificado, o estado de integridade retornado será "Unknown". Quando definido como true, a consulta entra em paralelo ao nó e ao serviço do sistema de integridade antes que os resultados sejam mesclados. Como resultado, a consulta é mais cara e pode levar mais tempo. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser menores que os resultados máximos especificados se não couberem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero ou não for especificado, a consulta paginável incluirá o máximo possível de resultados que couberem na mensagem de retorno. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-health"></a>integridade do aplicativo sfctl
Obtém a integridade do aplicativo do Service Fabric.

Retorna o estado integridade do aplicativo do Service Fabric. Os relatórios de resposta estão ok, erro ou estado de integridade de aviso. Se a entidade não for encontrada no repositório de integridade, ela retornará um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI\:' Fabric '. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "\:Fabric/MyApp/App1", a identidade do aplicativo será "\~MyApp App1" em 6.0 + e "MyApp/App1" nas versões anteriores. |
| --deployed-applications-health-state-filter | Permite a filtragem dos objetos de estado de integridade dos aplicativos implantados retornados no resultado da consulta de integridade do aplicativo com base em seu estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade. Somente os aplicativos implantados que correspondem ao filtro serão retornados. Todos os aplicativos implantados são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador e, portanto, o valor pode ser uma combinação desses valores, obtida usando o operador ' OR ' bit-up. Por exemplo, se o valor fornecido for 6, o estado de integridade dos aplicativos implantados com o valor OK (2) e Warning (4) será retornado.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador e, portanto, o valor pode ser uma combinação desses valores, obtida usando o operador ' OR ' bit-up. Por exemplo, se o valor fornecido for 6, todos os eventos com o valor OK (2) e Warning (4) serão retornados.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --Exclude-Statistics-Health | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. False por padrão. As estatísticas mostram o número de entidades filhas no estado de integridade Ok, aviso e erro. |
| --services-health-state-filter | Permite a filtragem dos objetos de estado de integridade dos serviços retornados no resultado da consulta de integridade de serviços com base em seu estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade. Somente os serviços que correspondem ao filtro são retornados. Todos os serviços são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador e, portanto, o valor pode ser uma combinação desses valores, obtida usando o operador ' OR ' bit-up. Por exemplo, se o valor fornecido for 6, o estado de integridade dos serviços com o valor OK (2) e Warning (4) será retornado.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-info"></a>informações do aplicativo sfctl
Obtém informações sobre um aplicativo Service Fabric.

Retorna as informações sobre o aplicativo que foi criado ou no processo de criação no cluster de Service Fabric e cujo nome corresponde ao especificado como o parâmetro. A resposta inclui o nome, o tipo, o status, os parâmetros e outros detalhes sobre o aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI\:' Fabric '. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "\:Fabric/MyApp/App1", a identidade do aplicativo será "\~MyApp App1" em 6.0 + e "MyApp/App1" nas versões anteriores. |
| --Exclude-parâmetros de aplicativo | O sinalizador que especifica se os parâmetros do aplicativo serão excluídos do resultado. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-list"></a>lista de aplicativos do sfctl
Obtém a lista de aplicativos criados no Cluster Service Fabric que correspondem aos filtros especificados.

Obtém as informações sobre os aplicativos que foram criados ou no processo de criação no Cluster Service Fabric e que correspondem aos filtros especificados. A resposta inclui o nome, o tipo, o status, os parâmetros e outros detalhes sobre o aplicativo. Se os aplicativos não couberem em uma página, uma página de resultados será retornada, bem como um token de continuação, que pode ser usado para obter a próxima página. Os filtros ApplicationTypeName e ApplicationDefinitionKindFilter não podem ser especificados ao mesmo tempo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-definition-kind-filter | Usado para filtrar em ApplicationDefinitionKind, que é o mecanismo usado para definir um aplicativo Service Fabric.  <br> -Default-valor padrão, que executa a mesma função que selecionar "All". O valor é 0.  <br> -All-filtro que corresponde à entrada com qualquer valor de ApplicationDefinitionKind. O valor é 65535.  <br> -ServiceFabricApplicationDescription-filtro que corresponde à entrada com o valor ApplicationDefinitionKind ServiceFabricApplicationDescription. O valor é 1.  <br> -Compose-filtro que corresponde à entrada com o valor de ApplicationDefinitionKind de composição. O valor é 2. |
| --tipo de aplicativo-nome | O nome do tipo de aplicativo usado para filtrar os aplicativos a serem consultados. Esse valor não deve conter a versão do tipo de aplicativo. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se ajustam em uma única resposta. Quando esse valor é passado para a próxima chamada à API, a API retorna o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --Exclude-parâmetros de aplicativo | O sinalizador que especifica se os parâmetros do aplicativo serão excluídos do resultado. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser menores que os resultados máximos especificados se não couberem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero ou não for especificado, a consulta paginável incluirá o máximo possível de resultados que couberem na mensagem de retorno. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-load"></a>carga do aplicativo sfctl
Obtém informações de carga sobre um aplicativo Service Fabric.

Retorna as informações de carga sobre o aplicativo que foi criado ou no processo de criação no cluster de Service Fabric e cujo nome corresponde ao especificado como o parâmetro. A resposta inclui o nome, os nós mínimos, os nós máximos, o número de nós que o aplicativo está ocupando no momento e as informações de métrica de carga do aplicativo sobre o aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI\:' Fabric '. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "\:Fabric/MyApp/App1", a identidade do aplicativo será "\~MyApp App1" em 6.0 + e "MyApp/App1" nas versões anteriores. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-manifest"></a>manifesto do aplicativo sfctl
Obtém o manifesto que descreve um tipo de aplicativo.

A resposta contém o XML do manifesto do aplicativo como uma cadeia de caracteres.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --tipo de aplicativo-nome [obrigatório] | O nome do tipo de aplicativo. |
| --tipo de aplicativo-versão [obrigatório] | A versão do tipo de aplicativo. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-provision"></a>provisionamento de aplicativo sfctl
Provisiona ou registra um tipo de aplicativo Service Fabric com o cluster usando o pacote. sfpkg no armazenamento externo ou usando o pacote de aplicativos no repositório de imagens.

Provisiona um tipo de aplicativo Service Fabric com o cluster. Isso é necessário antes que qualquer novo aplicativo possa ser instanciado. A operação de provisionamento pode ser executada no pacote de aplicativos especificado pelo relativePathInImageStore ou usando o URI de external. sfpkg. A menos que--external-Provision esteja definido, esse comando esperará o provisionamento do repositório de imagens.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-package-download-uri | O caminho para o pacote de aplicativos '. sfpkg ' de onde o pacote de aplicativos pode ser baixado usando protocolos HTTP ou HTTPS. <br><br> Somente para o tipo de provisionamento armazenamento externo. O pacote de aplicativos pode ser armazenado em um repositório externo que fornece a operação GET para baixar o arquivo. Os protocolos com suporte são HTTP e HTTPS, e o caminho deve permitir acesso de leitura. |
| --tipo de aplicativo-compilação-caminho | Somente para o tipo de provisionamento, armazenar imagem. O caminho relativo para o pacote de aplicativos no repositório de imagens especificado durante a operação de carregamento anterior. |
| --tipo de aplicativo-nome | Somente para o tipo de provisionamento armazenamento externo. O nome do tipo de aplicativo representa o nome do tipo de aplicativo encontrado no manifesto do aplicativo. |
| --tipo de aplicativo-versão | Somente para o tipo de provisionamento armazenamento externo. A versão do tipo de aplicativo representa a versão do tipo de aplicativo encontrada no manifesto do aplicativo. |
| --provisionamento externo | O local de onde o pacote de aplicativos pode ser registrado ou provisionado. Indica que o provisionamento é para um pacote de aplicativos que foi previamente carregado em um repositório externo. O pacote de aplicativos termina com a extensão *. sfpkg. |
| --sem espera | Indica se o provisionamento deve ocorrer de forma assíncrona ou não. <br><br> Quando definido como true, a operação de provisionamento retorna quando a solicitação é aceita pelo sistema e a operação de provisionamento continua sem nenhum limite de tempo limite. O valor predefinido é false. Para pacotes de aplicativos grandes, é recomendável definir o valor como true. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-report-health"></a>relatório do aplicativo sfctl – integridade
Envia um relatório de integridade sobre o aplicativo Service Fabric.

Relata o estado de integridade do aplicativo Service Fabric especificado. O relatório deve conter as informações sobre a origem do relatório de integridade e a propriedade na qual ele é relatado. O relatório é enviado para um aplicativo Service Fabric gateway, que encaminha para o repositório de integridade. O relatório pode ser aceito pelo gateway, mas rejeitado pelo repositório de integridade após a validação extra. Por exemplo, o repositório de integridade pode rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoleto. Para ver se o relatório foi aplicado no repositório de integridade, obtenha a integridade do aplicativo e verifique se o relatório é exibido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. <br><br> Normalmente, é o nome completo do aplicativo sem o esquema de URI\:' Fabric '. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere '\~'. Por exemplo, se o nome do aplicativo for '\:Fabric/MyApp/App1 ', a identidade do aplicativo será '\~MyApp App1 ' em 6.0 + e ' MyApp/App1 ' nas versões anteriores. |
| --Health-Property [obrigatório] | A propriedade das informações de integridade. <br><br> Uma entidade pode ter relatórios de integridade para propriedades diferentes. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir a flexibilidade do reporter para categorizar a condição de estado que dispara o relatório. Por exemplo, um reporter com SourceID "LocalWatchdog" pode monitorar o estado do disco disponível em um nó, para que ele possa relatar a propriedade "AvailableDisk" nesse nó. O mesmo Reporter pode monitorar a conectividade do nó, para que possa relatar uma propriedade "Connectivity" no mesmo nó. No repositório de integridade, esses relatórios são tratados como eventos de integridade separados para o nó especificado. Junto com SourceID, a propriedade identifica exclusivamente as informações de integridade. |
| --Estado de integridade [obrigatório] | Os valores possíveis\: incluem "Invalid", "OK", "Warning", "Error", "Unknown". |
| --Source-ID [obrigatório] | O nome de origem que identifica o componente de cliente/Watchdog/sistema que gerou as informações de integridade. |
| --Descrição | A descrição das informações de integridade. <br><br> Ele representa o texto livre usado para adicionar informações legíveis ao relatório. O comprimento máximo da cadeia de caracteres para a descrição é de 4096 caracteres. Se a cadeia de caracteres fornecida for maior, ela será truncada automaticamente. Quando truncado, os últimos caracteres da descrição contêm um marcador "[truncado]" e o tamanho total da cadeia de caracteres é de 4096 caracteres. A presença do marcador indica para os usuários que o truncamento ocorreu. Observe que, quando truncado, a descrição tem menos de 4096 caracteres a partir da cadeia de caracteres original. |
| --imediato | Um sinalizador que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de integridade é enviado para um aplicativo Service Fabric gateway, que encaminha para o repositório de integridade. Se Immediate for definido como true, o relatório será enviado imediatamente do gateway HTTP para o repositório de integridade, independentemente das configurações do cliente de malha que o aplicativo de gateway HTTP está usando. Isso é útil para relatórios críticos que devem ser enviados assim que possível. Dependendo do tempo e de outras condições, o envio do relatório ainda poderá falhar, por exemplo, se o gateway HTTP estiver fechado ou se a mensagem não alcançar o gateway. Se Immediate for definido como false, o relatório será enviado com base nas configurações do cliente de integridade do gateway HTTP. Portanto, ele será em lote de acordo com a configuração do HealthReportSendInterval. Essa é a configuração recomendada porque permite que o cliente de integridade Otimize as mensagens de relatório de integridade para o repositório de integridade, bem como o processamento do relatório de integridade. Por padrão, os relatórios não são enviados imediatamente. |
| --remove-when-expired | Valor que indica se o relatório será removido do repositório de integridade quando ele expirar. <br><br> Se definido como true, o relatório será removido do repositório de integridade depois de expirar. Se definido como false, o relatório será tratado como um erro quando expirado. O valor dessa propriedade é false por padrão. Quando os clientes relatam periodicamente, eles devem definir RemoveWhenExpired false (padrão). Dessa forma, o relator tem problemas (por exemplo, deadlock) e não pode relatar, a entidade é avaliada com erro quando o relatório de integridade expira. Isso sinaliza a entidade como estando em estado de integridade de erro. |
| --sequence-number | O número de sequência deste relatório de integridade como uma cadeia de caracteres numérica. <br><br> O número de sequência do relatório é usado pelo repositório de integridade para detectar relatórios obsoletos. Se não for especificado, um número de sequência será gerado automaticamente pelo cliente de integridade quando um relatório for adicionado. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --ttl | A duração para a qual este relatório de integridade é válido. Esse campo usa o formato ISO8601 para especificar a duração. <br><br> Quando os clientes relatam periodicamente, eles devem enviar relatórios com mais frequência do que a vida útil. Se os clientes reportarem a transição, eles poderão definir a vida útil como infinito. Quando a vida útil expira, o evento de integridade que contém as informações de integridade é removido do repositório de integridade, se RemoveWhenExpired for verdadeiro ou avaliado com erro, se RemoveWhenExpired false. Se não for especificado, o padrão de vida útil para valor infinito. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-type"></a>tipo de aplicativo sfctl
Obtém a lista de tipos de aplicativos no Cluster Service Fabric que correspondem exatamente ao nome especificado.

Retorna as informações sobre os tipos de aplicativo que são provisionados ou que estão sendo provisionados no Cluster Service Fabric. Esses resultados são de tipos de aplicativos cujo nome corresponde exatamente ao especificado como parâmetro e que estão em conformidade com os parâmetros de consulta fornecidos. Todas as versões do tipo de aplicativo correspondentes ao nome do tipo de aplicativo são retornadas, com cada versão retornada como um tipo de aplicativo. A resposta inclui o nome, a versão, o status e outros detalhes sobre o tipo de aplicativo. Essa é uma consulta paginada, o que significa que, se nem todos os tipos de aplicativos couberem em uma página, uma página de resultados será retornada, bem como um token de continuação, que pode ser usado para obter a próxima página. Por exemplo, se houver 10 tipos de aplicativos, mas uma página se ajustar apenas aos três primeiros tipos de aplicativo, ou se os resultados máximos forem definidos como 3, então três serão retornadas. Para acessar o restante dos resultados, recupere as páginas subsequentes usando o token de continuação retornado na próxima consulta. Um token de continuação vazio será retornado se não houver nenhuma página subsequente.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --tipo de aplicativo-nome [obrigatório] | O nome do tipo de aplicativo. |
| --tipo de aplicativo-versão | A versão do tipo de aplicativo. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se ajustam em uma única resposta. Quando esse valor é passado para a próxima chamada à API, a API retorna o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --Exclude-parâmetros de aplicativo | O sinalizador que especifica se os parâmetros do aplicativo serão excluídos do resultado. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser menores que os resultados máximos especificados se não couberem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero ou não for especificado, a consulta paginável incluirá o máximo possível de resultados que couberem na mensagem de retorno. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-type-list"></a>tipo de aplicativo sfctl-lista
Obtém a lista de tipos de aplicativos no Cluster Service Fabric.

Retorna as informações sobre os tipos de aplicativo que são provisionados ou que estão sendo provisionados no Cluster Service Fabric. Cada versão de um tipo de aplicativo é retornada como um tipo de aplicativo. A resposta inclui o nome, a versão, o status e outros detalhes sobre o tipo de aplicativo. Essa é uma consulta paginada, o que significa que, se nem todos os tipos de aplicativos couberem em uma página, uma página de resultados será retornada, bem como um token de continuação, que pode ser usado para obter a próxima página. Por exemplo, se houver 10 tipos de aplicativos, mas uma página se ajustar apenas aos três primeiros tipos de aplicativo, ou se os resultados máximos forem definidos como 3, então três serão retornadas. Para acessar o restante dos resultados, recupere as páginas subsequentes usando o token de continuação retornado na próxima consulta. Um token de continuação vazio será retornado se não houver nenhuma página subsequente.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --tipo de aplicativo-definição de tipo-filtro | Usado para filtrar em ApplicationTypeDefinitionKind, que é o mecanismo usado para definir um tipo de aplicativo Service Fabric.  <br> -Default-valor padrão, que executa a mesma função que selecionar "All". O valor é 0.  <br> -All-filtro que corresponde à entrada com qualquer valor de ApplicationTypeDefinitionKind. O valor é 65535.  <br> -ServiceFabricApplicationPackage-filtro que corresponde à entrada com o valor ApplicationTypeDefinitionKind ServiceFabricApplicationPackage. O valor é 1.  <br> -Compose-filtro que corresponde à entrada com o valor de ApplicationTypeDefinitionKind de composição. O valor é 2. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se ajustam em uma única resposta. Quando esse valor é passado para a próxima chamada à API, a API retorna o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --Exclude-parâmetros de aplicativo | O sinalizador que especifica se os parâmetros do aplicativo serão excluídos do resultado. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser menores que os resultados máximos especificados se não couberem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero ou não for especificado, a consulta paginável incluirá o máximo possível de resultados que couberem na mensagem de retorno. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-unprovision"></a>desprovisionamento de aplicativo sfctl
Remove ou cancela o registro de um tipo de aplicativo Service Fabric do cluster.

Esta operação só poderá ser executada se todas as instâncias do aplicativo do tipo de aplicativo tiverem sido excluídas. Após o cancelamento do registro do tipo de aplicativo, nenhuma nova instância do aplicativo poderá ser criada para esse tipo de aplicativo específico.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --tipo de aplicativo-nome [obrigatório] | O nome do tipo de aplicativo. |
| --tipo de aplicativo-versão [obrigatório] | A versão do tipo de aplicativo, conforme definido no manifesto do aplicativo. |
| --Async-Parameter | O sinalizador que indica se o desprovisionamento deve ocorrer de forma assíncrona. Quando definido como true, a operação de desprovisionamento retorna quando a solicitação é aceita pelo sistema e a operação de desprovisionamento continua sem nenhum limite de tempo limite. O valor predefinido é false. No entanto, é recomendável defini-lo como true para pacotes de aplicativos grandes que foram provisionados. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-upgrade"></a>atualização do aplicativo sfctl
Inicia a atualização de um aplicativo no Cluster Service Fabric.

Valida os parâmetros de atualização de aplicativo fornecidos e inicia a atualização do aplicativo se os parâmetros forem válidos. Observe que a descrição da atualização substitui a descrição do aplicativo existente. Isso significa que, se os parâmetros não forem especificados, os parâmetros existentes nos aplicativos serão substituídos pela lista de parâmetros vazia. Isso resultaria no aplicativo usando o valor padrão dos parâmetros do manifesto do aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. <br><br> Normalmente, é o nome completo do aplicativo sem o esquema de URI\:' Fabric '. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "\:Fabric/MyApp/App1", a identidade do aplicativo será "\~MyApp App1" em 6.0 + e "MyApp/App1" nas versões anteriores. |
| --versão do aplicativo [obrigatório] | A versão do tipo de aplicativo de destino (encontrada no manifesto do aplicativo) para a atualização do aplicativo. |
| --parâmetros [obrigatório] | Uma lista codificada em JSON de substituições de parâmetro de aplicativo a serem aplicadas ao atualizar o aplicativo. |
| --default-serviço-integridade-política | Especificação codificada em JSON da política de integridade usada por padrão para avaliar a integridade de um tipo de serviço. |
| --ação de falha | A ação a ser executada quando uma atualização monitorada encontra política de monitoramento ou violações de política de integridade. |
| --Force-Restart | Reinicie processos de modo forçado durante a atualização, mesmo quando a versão do código não tiver sido alterada. |
| --health-check-retry-timeout | O período de tempo entre as tentativas de realizar verificações de integridade se o aplicativo ou o cluster não estiver íntegro.  PT0H10M0S\: padrão. |
| --health-check-stable-duration | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização prossiga para o próximo domínio de atualização.  PT0H2M0S\: padrão. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --health-check-wait-duration | O período de tempo de espera após a conclusão de um domínio de atualização antes de iniciar o processo de verificações de integridade.  Padrão\: 0. |
| --max-unhealthy-apps | A porcentagem máxima permitida de aplicativos implantados não íntegros. Representado como um número entre 0 e 100. |
| --modo | O modo usado para monitorar a integridade durante uma atualização sem interrupção.  UnmonitoredAuto\: padrão. |
| --replica-set-check-timeout | A quantidade máxima de tempo para bloquear o processamento de um domínio de atualização e evitar a perda de disponibilidade quando houver problemas inesperados. Medido em segundos. |
| --service-health-policy | Mapa codificado em JSON com política de integridade do tipo de serviço por nome de tipo de serviço. O mapa está vazio como padrão. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --tempo limite do domínio de atualização | A quantidade de tempo que cada domínio de atualização precisa concluir antes que a falha seja executada.  Default\: P10675199DT02H48M05.4775807S. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --upgrade-timeout | O tempo durante o qual a atualização geral deve ser concluída antes de FailureAction ser executada.  Default\: P10675199DT02H48M05.4775807S. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --warning-as-error | Indica se os avisos são tratados com a mesma severidade que os erros. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-upgrade-resume"></a>atualização do aplicativo sfctl-retomar
Retoma a atualização de um aplicativo no Cluster Service Fabric.

Retoma uma atualização de aplicativo Service Fabric manual não monitorada. Service Fabric atualiza um domínio de atualização por vez. Para atualizações manuais não monitoradas, depois que Service Fabric concluir um domínio de atualização, ele aguardará que você chame essa API antes de prosseguir para o próximo domínio de atualização.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI\:' Fabric '. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "\:Fabric/MyApp/App1", a identidade do aplicativo será "\~MyApp App1" em 6.0 + e "MyApp/App1" nas versões anteriores. |
| --Upgrade-Domain-Name [obrigatório] | O nome do domínio de atualização no qual a atualização deve ser retomada. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-upgrade-rollback"></a>atualização do aplicativo sfctl-reversão
Inicia a reversão da atualização atual em andamento de um aplicativo no Cluster Service Fabric.

Inicia a reversão da atualização do aplicativo atual para a versão anterior. Essa API só pode ser usada para reverter a atualização em andamento atual que está sendo revertida para a nova versão. Se o aplicativo não estiver sendo atualizado no momento, use a API StartApplicationUpgrade para atualizá-lo para a versão desejada, incluindo reverter para uma versão anterior.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI\:' Fabric '. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "\:Fabric/MyApp/App1", a identidade do aplicativo será "\~MyApp App1" em 6.0 + e "MyApp/App1" nas versões anteriores. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-upgrade-status"></a>atualização do aplicativo sfctl-status
Obtém detalhes para a atualização mais recente executada neste aplicativo.

Retorna informações sobre o estado da atualização mais recente do aplicativo junto com detalhes para ajudar a depurar problemas de integridade do aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI\:' Fabric '. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "\:Fabric/MyApp/App1", a identidade do aplicativo será "\~MyApp App1" em 6.0 + e "MyApp/App1" nas versões anteriores. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-application-upload"></a>carregamento do aplicativo sfctl
Copie um pacote de aplicativos Service Fabric para o repositório de imagens.

Opcionalmente, exiba o progresso do carregamento de cada arquivo no pacote. O progresso do upload é `stderr`enviado para.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --caminho [obrigatório] | Caminho para o pacote de aplicativo local. |
| --imagestore-string | Armazenamento de imagens de destino para carregar o pacote de aplicativos.  ImageStore\: de\:malha padrão. |
| --mostrar-progresso | Mostrar o progresso do upload de arquivo para pacotes grandes. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos Seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).

---
title: CLI do Azure Service Fabric-serviço sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para gerenciar serviços, tipos de serviço e pacotes de serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 696de713129ca71dd7f2451501a7cc9eca0ee9b9
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906232"
---
# <a name="sfctl-service"></a>sfctl service
Criar, excluir e gerenciar serviço, tipos de serviço e pacotes de serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| nome do aplicativo | Obtém o nome do aplicativo Service Fabric para um serviço. |
| code-package-list | Obtém a lista de pacotes de código implantados em um nó de Service Fabric. |
| criar | Cria o serviço de Service Fabric especificado. |
| delete | Exclui um serviço de Service Fabric existente. |
| tipo implantado | Obtém as informações sobre um tipo de serviço especificado do aplicativo implantado em um nó em um Cluster Service Fabric. |
| lista de tipos implantados | Obtém a lista que contém as informações sobre os tipos de serviço dos aplicativos implantados em um nó em um Cluster Service Fabric. |
| descrição | Obtém a descrição de um serviço de Service Fabric existente. |
| get-container-logs | Obtém os logs de contêiner para o contêiner implantado em um nó de Service Fabric. |
| saúde | Obtém a integridade do serviço de Service Fabric especificado. |
| info | Obtém as informações sobre o serviço específico que pertence ao aplicativo Service Fabric. |
| list | Obtém as informações sobre todos os serviços pertencentes ao aplicativo especificado pela ID do aplicativo. |
| manifesto | Obtém o manifesto que descreve um tipo de serviço. |
| package-deploy | Baixa os pacotes associados ao manifesto do serviço especificado para o cache de imagem no nó especificado. |
| package-health | Obtém as informações sobre a integridade de um pacote de serviço para um aplicativo específico implantado para um nó Service Fabric e um aplicativo. |
| package-info | Obtém a lista de pacotes de serviço implantados em um nó Service Fabric correspondendo exatamente ao nome especificado. |
| package-list | Obtém a lista de pacotes de serviço implantados em um nó de Service Fabric. |
| recuperar | Indica ao Cluster Service Fabric que ele deve tentar recuperar o serviço especificado que está bloqueado no momento na perda de quorum. |
| report-health | Envia um relatório de integridade sobre o serviço de Service Fabric. |
| resolve | Resolver uma partição de Service Fabric. |
| type-list | Obtém a lista que contém as informações sobre os tipos de serviço que têm suporte de um tipo de aplicativo provisionado em um Cluster Service Fabric. |
| update | Atualiza o serviço especificado usando a descrição de atualização fornecida. |

## <a name="sfctl-service-app-name"></a>nome do aplicativo do serviço sfctl
Obtém o nome do aplicativo Service Fabric para um serviço.

Obtém o nome do aplicativo para o serviço especificado. Um erro de 404 FABRIC_E_SERVICE_DOES_NOT_EXIST é devolvido se não existir um serviço com o ID de serviço fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-code-package-list"></a>código do serviço sfctl – lista de pacotes
Obtém a lista de pacotes de código implantados em um nó de Service Fabric.

Obtém a lista de pacotes de código implantados em um Service Fabric nó para o determinado aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --código-pacote-nome | O nome do pacote de códigos especificado no manifesto do serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
| --service-manifest-name | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-create"></a>criação do serviço sfctl
Cria o serviço de Service Fabric especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-id [Obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere '\~'. Por exemplo, se o nome do aplicativo for ' Fabric\:/MyApp/App1 ', a identidade do aplicativo será ' MyApp\~App1 ' em 6.0 + e ' MyApp/App1 ' nas versões anteriores. |
| --nome [Obrigatório] | Nome do serviço. Deve ser um filho da ID do aplicativo. Este é o nome completo, incluindo o URI de `fabric\:`. Por exemplo `fabric\:/A/B` de serviço é um filho de `fabric\:/A`de aplicativos. |
| -tipo de serviço [Obrigatório] | Nome do tipo de serviço. |
| --modo de ativação | O modo de ativação para o pacote de serviço. |
| --restrições | As restrições de posicionamento como uma cadeia de caracteres. As restrições de posicionamento são expressões booleanas em Propriedades de nó e permitem restringir um serviço a nós específicos com base nos requisitos de serviço. Por exemplo, para posicionar um serviço em nós em que NodeType é azul, especifique o seguinte\:"NodeColor = = Blue". |
| --correlated-service | Nome do serviço de destino com o qual correlacionar. |
| --correlação | Correlacione o serviço a um serviço existente usando uma afinidade de alinhamento. |
| --dns-name | O nome DNS do serviço a ser criado. O serviço do sistema DNS Service Fabric deve ser habilitado para essa configuração. |
| --contagem de instâncias | A contagem de instâncias. Isso se aplica somente a serviços sem monitoração de estado. |
| --int-scheme | Indica que o serviço deve ser particionado uniformemente em um intervalo de inteiros não assinados. |
| --int-scheme-count | O número de partições dentro do intervalo de chaves de inteiro a ser criado, se usar um esquema de partição de inteiro uniforme. |
| --int-esquema-alto | O final do intervalo de inteiros de chave, se estiver usando um esquema de partição de inteiro uniforme. |
| --int-scheme-low | O início do intervalo inteiro de chave, se estiver usando um esquema de partição de inteiro uniforme. |
| --métricas de carga | Lista de métricas codificada em JSON usada quando os serviços de balanceamento de carga entre nós. |
| --min-replica-set-size | O tamanho mínimo do conjunto de réplicas como um número. Isso se aplica somente a serviços com estado. |
| --move-cost | Especifica o custo de movimentação para o serviço. Os valores possíveis são\: 'Zero', 'Low', 'Medium', 'High', 'VeryHigh'. |
| --named-scheme | Indica que o serviço deve ter várias partições nomeadas. |
| --named-scheme-list | Lista codificada em JSON de nomes para particionar o serviço, se estiver usando o esquema de partição nomeado. |
| --sem estado de persistência | Se for true, isso indicará que o serviço não tem um estado persistente armazenado no disco local ou que ele armazena apenas o estado na memória. |
| --posicionamento-lista de políticas | Lista codificada em JSON de políticas de posicionamento para o serviço e quaisquer nomes de domínio associados. As políticas podem ser um ou mais dos\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain``RequireDomainDistribution`. |
| --quorum-perda-espera | A duração máxima, em segundos, para a qual uma partição pode estar em um estado de perda de quorum. Isso se aplica somente a serviços com estado. |
| --Replica-Restart-Wait | A duração, em segundos, entre quando uma réplica fica inativa e quando uma nova réplica é criada. Isso se aplica somente a serviços com estado. |
| --políticas de dimensionamento | Lista codificada JSON de políticas de dimensionamento para este serviço. |
| --tempo de colocação de serviço | A duração pela qual as réplicas podem ficar inBuild antes de informar que a construção está presa. Isso se aplica somente a serviços com estado. |
| --singleton-scheme | Indica que o serviço deve ter uma única partição ou ser um serviço não particionado. |
| --Reserva-por-réplica-manter | A duração máxima, em segundos, para a qual as réplicas em espera serão mantidas antes de serem removidas. Isso se aplica somente a serviços com estado. |
| --com estado | Indica que o serviço é um serviço com estado. |
| --sem estado | Indica que o serviço é um serviço sem estado. |
| --target-replica-set-size | O tamanho do conjunto de réplicas de destino como um número. Isso se aplica somente a serviços com estado. |
| --Timeout-t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-delete"></a>exclusão do serviço sfctl
Exclui um serviço de Service Fabric existente.

Um serviço deve ser criado antes que possa ser excluído. Por padrão, Service Fabric tentará fechar as réplicas de serviço de maneira normal e, em seguida, excluirá o serviço. No entanto, se o serviço estiver tendo problemas ao fechar a réplica normalmente, a operação de exclusão poderá levar muito tempo ou ficar presa. Use o sinalizador opcional ForceRemove para ignorar a sequência de fechamento normal e forçar a exclusão do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --force-remove | Remova um aplicativo ou serviço Service Fabric de modo forçado sem passar pela sequência de desligamento normal. Esse parâmetro pode ser usado para forçar a exclusão de um aplicativo ou serviço para o qual a exclusão está atingindo o tempo limite devido a problemas no código de serviço que impedem o fechamento normal das réplicas. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-deployed-type"></a>serviço sfctl implantado-tipo
Obtém as informações sobre um tipo de serviço especificado do aplicativo implantado em um nó em um Cluster Service Fabric.

Obtém a lista que contém as informações sobre um tipo de serviço específico dos aplicativos implantados em um nó em um Cluster Service Fabric. A resposta inclui o nome do tipo de serviço, seu status de registro, o pacote de códigos que registrou a ti e a ID de ativação do pacote de serviço. Cada entrada representa uma ativação de um tipo de serviço, diferenciada pela ID de ativação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome do tipo de serviço [Obrigatório] | Especifica o nome de um tipo de serviço Service Fabric. |
| --service-manifest-name | O nome do manifesto do serviço para filtrar a lista de informações do tipo de serviço implantado. Se especificado, a resposta conterá apenas as informações sobre os tipos de serviço definidos neste manifesto do serviço. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-deployed-type-list"></a>serviço sfctl implantado-tipo-lista
Obtém a lista que contém as informações sobre os tipos de serviço dos aplicativos implantados em um nó em um Cluster Service Fabric.

Obtém a lista que contém as informações sobre os tipos de serviço dos aplicativos implantados em um nó em um Cluster Service Fabric. A resposta inclui o nome do tipo de serviço, seu status de registro, o pacote de códigos que registrou a ti e a ID de ativação do pacote de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --service-manifest-name | O nome do manifesto do serviço para filtrar a lista de informações do tipo de serviço implantado. Se especificado, a resposta conterá apenas as informações sobre os tipos de serviço definidos neste manifesto do serviço. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-description"></a>Descrição do serviço sfctl
Obtém a descrição de um serviço de Service Fabric existente.

Obtém a descrição de um serviço de Service Fabric existente. Um serviço deve ser criado antes que sua descrição possa ser obtida.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-get-container-logs"></a>sfctl do serviço Get-contêiner-logs
Obtém os logs de contêiner para o contêiner implantado em um nó de Service Fabric.

Obtém os logs de contêiner para o contêiner implantado em um Service Fabric nó para o pacote de códigos fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --código-pacote-nome [Obrigatório] | O nome do pacote de códigos especificado no manifesto do serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome manifesto de serviço [Obrigatório] | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
| --anterior | Especifica se é para obter logs de contêiner de contêineres encerrados/inativos da instância do pacote de códigos. |
| --cauda | Número de linhas a serem mostradas do final dos logs. O padrão é 100. ' todos ' para mostrar os logs completos. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-health"></a>integridade do serviço sfctl
Obtém a integridade do serviço de Service Fabric especificado.

Obtém as informações de integridade do serviço especificado. Use EventsHealthStateFilter para filtrar a coleção de eventos de integridade relatados no serviço com base no estado de integridade. Use PartitionsHealthStateFilter para filtrar a coleção de partições retornadas. Se você especificar um serviço que não existe no repositório de integridade, essa solicitação retornará um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador e, portanto, o valor pode ser uma combinação desses valores, obtida usando o operador ' OR ' bit-up. Por exemplo, se o valor fornecido for 6, todos os eventos com o valor OK (2) e Warning (4) serão retornados.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --Exclude-Statistics-Health | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. false por padrão. As estatísticas mostram o número de entidades filhas no estado de integridade Ok, aviso e erro. |
| --partitions-health-state-filter | Permite a filtragem de objetos de estado de integridade de partições retornados no resultado da consulta de integridade do serviço com base em seu estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade. Somente partições que correspondem ao filtro são retornadas. Todas as partições são usadas para avaliar o estado de integridade agregada. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador, portanto, o valor pode ser uma combinação desse valor obtido usando o operador ' OR ' bit-up. Por exemplo, se o valor fornecido for 6, o estado de integridade das partições com o valor OK (2) e Warning (4) será retornado.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-info"></a>informações do serviço sfctl
Obtém as informações sobre o serviço específico que pertence ao aplicativo Service Fabric.

Retorna as informações sobre o serviço especificado que pertence ao aplicativo de Service Fabric especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-list"></a>lista de serviços sfctls
Obtém as informações sobre todos os serviços pertencentes ao aplicativo especificado pela ID do aplicativo.

Retorna as informações sobre todos os serviços pertencentes ao aplicativo especificado pela ID do aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se ajustam em uma única resposta. Quando esse valor é passado para a próxima chamada à API, a API retorna o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --service-type-name | O nome do tipo de serviço usado para filtrar os serviços para consulta. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-manifest"></a>manifesto do serviço sfctl
Obtém o manifesto que descreve um tipo de serviço.

Obtém o manifesto que descreve um tipo de serviço. A resposta contém o XML do manifesto do serviço como uma cadeia de caracteres.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do tipo de aplicação [Obrigatório] | O nome do tipo de aplicativo. |
| --aplicação-tipo-versão [Obrigatório] | A versão do tipo de aplicativo. |
| --nome manifesto de serviço [Obrigatório] | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-package-deploy"></a>pacote de serviço do sfctl – implantar
Baixa os pacotes associados ao manifesto do serviço especificado para o cache de imagem no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-tipo-nome [Obrigatório] | O nome do manifesto do aplicativo para o manifesto do serviço solicitado correspondente. |
| --app-type-versão [Obrigatório] | A versão do manifesto do aplicativo para o manifesto do serviço solicitado correspondente. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome manifesto de serviço [Obrigatório] | O nome do manifesto do serviço associado aos pacotes que serão baixados. |
| --compartilhamento-política | Lista codificada em JSON de políticas de compartilhamento. Cada elemento de política de compartilhamento é composto por um ' name ' e ' Scope '. O nome corresponde ao nome do código, à configuração ou ao pacote de dados a ser compartilhado. O escopo pode ser ' none ', ' all', ' code ', ' config ' ou ' data '. |
| --Timeout-t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-package-health"></a>pacote de serviço sfctl-integridade
Obtém as informações sobre a integridade de um pacote de serviço para um aplicativo específico implantado para um nó Service Fabric e um aplicativo.

Obtém as informações sobre a integridade de um pacote de serviço para um aplicativo específico implantado em um nó de Service Fabric. Use EventsHealthStateFilter para filtrar opcionalmente a coleção de objetos HealthEvent relatados no pacote de serviço implantado com base no estado de integridade.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome do pacote de serviço [Obrigatório] | O nome do pacote de serviço. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador e, portanto, o valor pode ser uma combinação desses valores, obtida usando o operador ' OR ' bit-up. Por exemplo, se o valor fornecido for 6, todos os eventos com o valor OK (2) e Warning (4) serão retornados.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-package-info"></a>pacote de serviço do sfctl-informações
Obtém a lista de pacotes de serviço implantados em um nó Service Fabric correspondendo exatamente ao nome especificado.

Retorna as informações sobre os pacotes de serviço implantados em um Service Fabric nó para o determinado aplicativo. Esses resultados são de pacotes de serviço cujo nome corresponde exatamente ao nome do pacote de serviço especificado como o parâmetro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome do pacote de serviço [Obrigatório] | O nome do pacote de serviço. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-package-list"></a>pacote de serviço do sfctl-lista
Obtém a lista de pacotes de serviço implantados em um nó de Service Fabric.

Retorna as informações sobre os pacotes de serviço implantados em um Service Fabric nó para o determinado aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-recover"></a>recuperação do serviço sfctl
Indica ao Cluster Service Fabric que ele deve tentar recuperar o serviço especificado que está bloqueado no momento na perda de quorum.

Indica ao Cluster Service Fabric que ele deve tentar recuperar o serviço especificado que está bloqueado no momento na perda de quorum. Esta operação só deve ser executada se for conhecida que as réplicas que estão inativas não podem ser recuperadas. O uso incorreto dessa API pode causar potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-report-health"></a>relatório do serviço sfctl-integridade
Envia um relatório de integridade sobre o serviço de Service Fabric.

Relata o estado de integridade do serviço de Service Fabric especificado. O relatório deve conter as informações sobre a origem do relatório de integridade e a propriedade na qual ele é relatado. O relatório é enviado para um serviço de gateway Service Fabric, que encaminha para o repositório de integridade. O relatório pode ser aceito pelo gateway, mas rejeitado pelo repositório de integridade após a validação extra. Por exemplo, o repositório de integridade pode rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoleto. Para ver se o relatório foi aplicado no repositório de integridade, verifique se o relatório aparece nos eventos de integridade do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --propriedade de saúde [Necessária] | A propriedade das informações de integridade. <br><br> Uma entidade pode ter relatórios de integridade para propriedades diferentes. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir a flexibilidade do reporter para categorizar a condição de estado que dispara o relatório. Por exemplo, um reporter com SourceID "LocalWatchdog" pode monitorar o estado do disco disponível em um nó, para que ele possa relatar a propriedade "AvailableDisk" nesse nó. O mesmo Reporter pode monitorar a conectividade do nó, para que possa relatar uma propriedade "Connectivity" no mesmo nó. No repositório de integridade, esses relatórios são tratados como eventos de integridade separados para o nó especificado. Junto com SourceID, a propriedade identifica exclusivamente as informações de integridade. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem\: "Invalid", "OK", "Warning", "Error", "Unknown". |
| --service-id [Obrigatório] | A identidade do serviço. <br><br> Normalmente, é o nome completo do serviço sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere '\~'. Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --source-id [Obrigatório] | O nome de origem que identifica o componente de cliente/Watchdog/sistema que gerou as informações de integridade. |
| --Descrição | A descrição das informações de integridade. <br><br> Ele representa o texto livre usado para adicionar informações legíveis ao relatório. O comprimento máximo da cadeia de caracteres para a descrição é de 4096 caracteres. Se a cadeia de caracteres fornecida for maior, ela será truncada automaticamente. Quando truncados, os últimos caracteres da descrição contêm um marcador "[Truncado]", e o tamanho total da corda é de 4096 caracteres. A presença do marcador indica para os usuários que o truncamento ocorreu. Observe que, quando truncado, a descrição tem menos de 4096 caracteres a partir da cadeia de caracteres original. |
| --imediato | Um sinalizador que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de integridade é enviado para um aplicativo Service Fabric gateway, que encaminha para o repositório de integridade. Se Immediate for definido como true, o relatório será enviado imediatamente do gateway HTTP para o repositório de integridade, independentemente das configurações do cliente de malha que o aplicativo de gateway HTTP está usando. Isso é útil para relatórios críticos que devem ser enviados assim que possível. Dependendo do tempo e de outras condições, o envio do relatório ainda poderá falhar, por exemplo, se o gateway HTTP estiver fechado ou se a mensagem não alcançar o gateway. Se Immediate for definido como false, o relatório será enviado com base nas configurações do cliente de integridade do gateway HTTP. Portanto, ele será em lote de acordo com a configuração do HealthReportSendInterval. Essa é a configuração recomendada porque permite que o cliente de integridade Otimize as mensagens de relatório de integridade para o repositório de integridade, bem como o processamento do relatório de integridade. Por padrão, os relatórios não são enviados imediatamente. |
| --remove-when-expired | Valor que indica se o relatório será removido do repositório de integridade quando ele expirar. <br><br> Se definido como true, o relatório será removido do repositório de integridade depois de expirar. Se definido como false, o relatório será tratado como um erro quando expirado. O valor dessa propriedade é false por padrão. Quando os clientes relatam periodicamente, eles devem definir RemoveWhenExpired false (padrão). Dessa forma, o relator tem problemas (por exemplo, deadlock) e não pode relatar, a entidade é avaliada com erro quando o relatório de integridade expira. Isso sinaliza a entidade como estando em estado de integridade de erro. |
| --sequence-number | O número de sequência deste relatório de integridade como uma cadeia de caracteres numérica. <br><br> O número de sequência do relatório é usado pelo repositório de integridade para detectar relatórios obsoletos. Se não for especificado, um número de sequência será gerado automaticamente pelo cliente de integridade quando um relatório for adicionado. |
| --Timeout-t | Padrão\: 60. |
| --ttl | A duração para a qual este relatório de integridade é válido. Esse campo usa o formato ISO8601 para especificar a duração. <br><br> Quando os clientes relatam periodicamente, eles devem enviar relatórios com mais frequência do que a vida útil. Se os clientes reportarem a transição, eles poderão definir a vida útil como infinito. Quando a vida útil expira, o evento de integridade que contém as informações de integridade é removido do repositório de integridade, se RemoveWhenExpired for verdadeiro ou avaliado com erro, se RemoveWhenExpired false. Se não for especificado, o padrão de vida útil para valor infinito. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-resolve"></a>resolução do serviço sfctl
Resolver uma partição de Service Fabric.

Resolva uma partição de serviço Service Fabric para obter os pontos de extremidade das réplicas de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o esquema de URI "Fabric\:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --partition-key-type | Tipo de chave para a partição. Esse parâmetro será necessário se o esquema de partição para o serviço for Int64Range ou nomeado. Os valores possíveis são os seguintes. -None (1)-indica que o parâmetro PartitionKeyValue não foi especificado. Isso é válido para as partições com o esquema de particionamento como singleton. Este é o valor predefinido. O valor é 1. -Int64Range (2) – indica que o parâmetro PartitionKeyValue é uma chave de partição Int64. Isso é válido para as partições com esquema de particionamento como Int64Range. O valor é 2. -Named (3) – indica que o parâmetro PartitionKeyValue é um nome da partição. Isso é válido para as partições com o esquema de particionamento como nomeado. O valor é 3. |
| --partition-key-value | Chave de partição. Isso será necessário se o esquema de partição para o serviço for Int64Range ou nomeado. Essa não é a ID da partição, mas sim o valor da chave de número inteiro ou o nome da ID da partição. Por exemplo, se o serviço estiver usando partições de intervalo de 0 a 10, elas PartitionKeyValue seriam um número inteiro nesse intervalo. Descrição do serviço de consulta para ver o intervalo ou o nome. |
| --previous-rsp-version | O valor no campo versão da resposta que foi recebida anteriormente. Isso será necessário se o usuário souber que o resultado ficou obsoleto anteriormente. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-type-list"></a>tipo de serviço sfctl-lista
Obtém a lista que contém as informações sobre os tipos de serviço que têm suporte de um tipo de aplicativo provisionado em um Cluster Service Fabric.

Obtém a lista que contém as informações sobre os tipos de serviço que têm suporte de um tipo de aplicativo provisionado em um Cluster Service Fabric. O tipo de aplicativo fornecido deve existir. Caso contrário, um status de 404 será retornado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do tipo de aplicação [Obrigatório] | O nome do tipo de aplicativo. |
| --aplicação-tipo-versão [Obrigatório] | A versão do tipo de aplicativo. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-service-update"></a>atualização do serviço sfctl
Atualiza o serviço especificado usando a descrição de atualização fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Normalmente, é o nome completo do serviço sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "Fabric\:/MyApp/App1/svc1", a identidade do serviço será "MyApp\~App1\~svc1" na versão 6.0 e superiores, e "MyApp/App1/svc1" nas versões anteriores. |
| --restrições | As restrições de posicionamento como uma cadeia de caracteres. As restrições de posicionamento são expressões booleanas em Propriedades de nó e permitem restringir um serviço a nós específicos com base nos requisitos de serviço. Por exemplo, para posicionar um serviço em nós em que NodeType é azul, especifique o seguinte\: "NodeColor = = Blue". |
| --correlated-service | Nome do serviço de destino com o qual correlacionar. |
| --correlação | Correlacione o serviço a um serviço existente usando uma afinidade de alinhamento. |
| --contagem de instâncias | A contagem de instâncias. Isso se aplica somente a serviços sem monitoração de estado. |
| --métricas de carga | Lista de métricas codificada em JSON usada quando o balanceamento de carga entre nós. |
| --min-replica-set-size | O tamanho mínimo do conjunto de réplicas como um número. Isso se aplica somente a serviços com estado. |
| --move-cost | Especifica o custo de movimentação para o serviço. Os valores possíveis são\: 'Zero', 'Low', 'Medium', 'High', 'VeryHigh'. |
| --posicionamento-lista de políticas | Lista codificada em JSON de políticas de posicionamento para o serviço e quaisquer nomes de domínio associados. As políticas podem ser um ou mais dos\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain``RequireDomainDistribution`. |
| --quorum-perda-espera | A duração máxima, em segundos, para a qual uma partição pode estar em um estado de perda de quorum. Isso se aplica somente a serviços com estado. |
| --Replica-Restart-Wait | A duração, em segundos, entre quando uma réplica fica inativa e quando uma nova réplica é criada. Isso se aplica somente a serviços com estado. |
| --políticas de dimensionamento | Lista codificada JSON de políticas de dimensionamento para este serviço. |
| --tempo de colocação de serviço | A duração pela qual as réplicas podem ficar inBuild antes de informar que a construção está presa. Isso se aplica somente a serviços com estado. |
| --Reserva-por-réplica-manter | A duração máxima, em segundos, para a qual as réplicas em espera serão mantidas antes de serem removidas. Isso se aplica somente a serviços com estado. |
| --com estado | Indica que o serviço de destino é um serviço com estado. |
| --sem estado | Indica que o serviço de destino é um serviço sem estado. |
| --target-replica-set-size | O tamanho do conjunto de réplicas de destino como um número. Isso se aplica somente a serviços com estado. |
| --Timeout-t | Padrão\: 60. |

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

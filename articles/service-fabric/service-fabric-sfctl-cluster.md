---
title: CLI do Azure Service Fabric-cluster sfctl | Microsoft Docs
description: Descreve os comandos de cluster do Service Fabric CLI sfctl.
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
ms.openlocfilehash: 305b1e11841dd2da4aa6c0bdeb3df2c76addad87
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036501"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Selecione, gerencie e opere Service Fabric clusters.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| versões de código | Obtém uma lista de versões de código de malha que são provisionadas em um Cluster Service Fabric. |
| configurações-versões | Obtém uma lista de versões de configuração de malha que são provisionadas em um Cluster Service Fabric. |
| health | Obtém a integridade de um Cluster Service Fabric. |
| manifesto | Obtenha o manifesto do Cluster Service Fabric. |
| operação-cancelar | Cancela uma operação de falha induzida pelo usuário. |
| operação-lista | Obtém uma lista de operações de falha induzidas pelo usuário filtradas pela entrada fornecida. |
| provision | Provisionar o código ou os pacotes de configuração de um Cluster Service Fabric. |
| recover-system | Indica ao Cluster Service Fabric que ele deve tentar recuperar os serviços do sistema que estão presos no momento na perda de quorum. |
| report-health | Envia um relatório de integridade sobre o Cluster Service Fabric. |
| seleccionar | Conecta-se a um ponto de extremidade de Cluster Service Fabric. |
| Mostrar conexão | Mostre a qual Service Fabric cluster a instância do sfctl está conectada. |
| unprovision | Desprovisione o código ou os pacotes de configuração de um Cluster Service Fabric. |
| upgrade | Inicie a atualização do código ou da versão de configuração de um Cluster Service Fabric. |
| atualização-retomar | Faça a atualização do cluster passar para o próximo domínio de atualização. |
| upgrade-rollback | Reverter a atualização de um Cluster Service Fabric. |
| atualização-status | Obtém o progresso da atualização de cluster atual. |
| atualização-atualização | Atualize os parâmetros de atualização de uma atualização de Cluster Service Fabric. |

## <a name="sfctl-cluster-code-versions"></a>código de cluster sfctl-versões
Obtém uma lista de versões de código de malha que são provisionadas em um Cluster Service Fabric.

Obtém uma lista de informações sobre as versões de código de malha que são provisionadas no cluster. O parâmetro CodeVersion pode ser usado para filtrar opcionalmente a saída somente para essa versão específica.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --versão do código | A versão do produto do Service Fabric. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-config-versions"></a>configuração do cluster sfctl – versões
Obtém uma lista de versões de configuração de malha que são provisionadas em um Cluster Service Fabric.

Obtém uma lista de informações sobre as versões de configuração do Fabric que são provisionadas no cluster. O parâmetro ConfigVersion pode ser usado para filtrar opcionalmente a saída somente para essa versão específica.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --config-version | A versão de configuração do Service Fabric. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-health"></a>integridade do cluster sfctl
Obtém a integridade de um Cluster Service Fabric.

Use EventsHealthStateFilter para filtrar a coleção de eventos de integridade relatados no cluster com base no estado de integridade. Da mesma forma, use NodesHealthStateFilter e ApplicationsHealthStateFilter para filtrar a coleção de nós e aplicativos retornados com base em seu estado de integridade agregado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --aplicativos-estado de integridade do filtro | Permite a filtragem dos objetos de estado de integridade do aplicativo retornados no resultado da consulta de integridade do cluster com base em seu estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro obtido de membros ou operações bit a bit em membros da enumeração HealthStateFilter. Somente os aplicativos que correspondem ao filtro são retornados. Todos os aplicativos são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador, portanto, o valor pode ser uma combinação desses valores obtidos usando o operador ' OR Por exemplo, se o valor fornecido for 6, o estado de integridade dos aplicativos com o valor OK (2) e Warning (4) de HealthState será retornado.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador e, portanto, o valor pode ser uma combinação desses valores, obtida usando o operador ' OR ' bit-up. Por exemplo, se o valor fornecido for 6, todos os eventos com o valor OK (2) e Warning (4) serão retornados.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --Exclude-Statistics-Health | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. False por padrão. As estatísticas mostram o número de entidades filhas no estado de integridade Ok, aviso e erro. |
| --include-System-Application-Health-Statistics | Indica se as estatísticas de integridade devem incluir as\:estatísticas de integridade do aplicativo do Fabric/estado. False por padrão. Se IncludeSystemApplicationHealthStatistics for definido como true, as estatísticas de integridade incluirão as entidades que pertencem ao\:aplicativo Fabric/estado. Caso contrário, o resultado da consulta incluirá estatísticas de integridade somente para aplicativos de usuário. As estatísticas de integridade devem ser incluídas no resultado da consulta para que esse parâmetro seja aplicado. |
| --nodes-health-state-filter | Permite a filtragem dos objetos de estado de integridade do nó retornados no resultado da consulta de integridade do cluster com base em seu estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes Estados de integridade. Somente os nós que correspondem ao filtro são retornados. Todos os nós são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador, portanto, o valor pode ser uma combinação desses valores obtidos usando o operador ' OR Por exemplo, se o valor fornecido for 6, o estado de integridade dos nós com o valor OK (2) e Warning (4) de HealthState será retornado.  <br> -Default-valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None-filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em uma determinada coleção de Estados. O valor é 1.  <br> -Ok-filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning-filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4.  <br> -Error-filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.  <br> -All-filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-manifest"></a>manifesto do cluster sfctl
Obtenha o manifesto do Cluster Service Fabric.

Obtenha o manifesto do Cluster Service Fabric. O manifesto do cluster contém as propriedades do cluster que incluem diferentes tipos de nó no cluster, configurações de segurança, topologias de domínio de atualização e de falha, etc. Essas propriedades são especificadas como parte do arquivo ClusterConfig. JSON durante a implantação de um cluster autônomo. No entanto, a maioria das informações no manifesto do cluster é gerada internamente pelo Service Fabric durante a implantação do cluster em outros cenários de implantação (por exemplo, ao usar o portal do Azure). O conteúdo do manifesto do cluster é apenas para fins informativos e não se espera que os usuários tenham uma dependência do formato do conteúdo do arquivo ou de sua interpretação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-operation-cancel"></a>operação de cluster sfctl-cancelar
Cancela uma operação de falha induzida pelo usuário.

As APIs a seguir iniciam as operações de falha que podem ser\: canceladas usando CancelOperation StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Se Force for false, a operação induzida pelo usuário especificada será interrompida e limpada normalmente.  Se Force for true, o comando será anulado e algum estado interno poderá ser deixado para trás.  Especificar Force as true deve ser usado com cuidado. Não é permitido chamar essa API com Force Set como true até que essa API já tenha sido chamada no mesmo comando de teste com Force definido como false primeiro, ou a menos que o comando de teste já tenha um Operationstate de Operationstate. RollingBack. 

O\: esclarecimento operationstate. RollingBack significa que o sistema será/está limpando o estado interno do sistema causado pela execução do comando.  Ele não restaurará dados se o comando de teste tiver que causar perda de dados.  Por exemplo, se você chamar StartDataLoss e chamar essa API, o sistema só limpará o estado interno da execução do comando. Ele não restaurará os dados da partição de destino, se o comando tiver progredido o suficiente para causar perda de dados. 

> [!NOTE]
> Se essa API for chamada com Force = = true, o estado interno poderá ser deixado para trás.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Operation-ID [obrigatório] | Um GUID que identifica uma chamada dessa API.  Isso é passado para a API GetProgress correspondente. |
| --Force | Indica se é para reverter e limpar o estado interno do sistema modificado com a execução da operação induzida pelo usuário. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-operation-list"></a>operação de cluster sfctl-lista
Obtém uma lista de operações de falha induzidas pelo usuário filtradas pela entrada fornecida.

Obtém a lista de operações de falha induzidas pelo usuário filtradas pela entrada fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Estado-filtro | Usado para filtrar em Operationstate para operações induzidas pelo usuário. <br> 65535-selecionar tudo <br> 1-selecionar em execução <br> 2-selecionar RollingBack <br>8-selecionar concluído <br>16-seleção com falha <br>32-selecionar cancelado <br>64-selecione ForceCancelled.  <br>Padrão\: 65535. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --type-filter | Usado para filtrar em OperationType para operações induzidas pelo usuário. <br> 65535-selecionar tudo <br> 1-Selecione PartitionDataLoss. <br> 2-Selecione PartitionQuorumLoss. <br> 4-Selecione PartitionRestart. <br> 8-selecione NodeTransition.  <br> Padrão\: 65535. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-provision"></a>provisionamento de cluster sfctl
Provisionar o código ou os pacotes de configuração de um Cluster Service Fabric.

Validar e provisionar o código ou os pacotes de configuração de um Cluster Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --cluster-manifest-arquivo-caminho | O caminho do arquivo de manifesto do cluster. |
| --code-file-path | O caminho do arquivo do pacote de código do cluster. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-recover-system"></a>recuperação de cluster sfctl-System
Indica ao Cluster Service Fabric que ele deve tentar recuperar os serviços do sistema que estão presos no momento na perda de quorum.

Indica ao Cluster Service Fabric que ele deve tentar recuperar os serviços do sistema que estão presos no momento na perda de quorum. Esta operação só deve ser executada se for conhecida que as réplicas que estão inativas não podem ser recuperadas. O uso incorreto dessa API pode causar potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-report-health"></a>relatório de cluster sfctl-integridade
Envia um relatório de integridade sobre o Cluster Service Fabric.

O relatório deve conter as informações sobre a origem do relatório de integridade e a propriedade na qual ele é relatado. O relatório é enviado para um nó Service Fabric gateway, que encaminha para o repositório de integridade. O relatório pode ser aceito pelo gateway, mas rejeitado pelo repositório de integridade após a validação extra. Por exemplo, o repositório de integridade pode rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoleto. Para ver se o relatório foi aplicado no repositório de integridade, verifique se o relatório aparece no HealthEvents do cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
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

## <a name="sfctl-cluster-select"></a>seleção de cluster sfctl
Conecta-se a um ponto de extremidade de Cluster Service Fabric.

Se estiver se conectando ao cluster seguro, especifique um caminho absoluto para um certificado (. CRT) e um arquivo de chave (. Key) ou um único arquivo com ambos (. pem). Não especifique ambos. Opcionalmente, se estiver se conectando a um cluster seguro, especifique também um caminho absoluto para um arquivo de pacote de autoridade de certificação ou diretório de certificados de autoridade de certificação confiáveis. Se estiver usando um diretório de certificados de autoridade `c_rehash <directory>` de certificação, fornecidos pelo OpenSSL devem ser executados primeiro para computar os hashes de certificado e criar os links simbólicos apropriados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --ponto de extremidade [obrigatório] | URL do ponto de extremidade do cluster, incluindo a porta e o prefixo HTTP ou HTTPS. |
| --AAD | Use Azure Active Directory para autenticação. |
| --ca | Caminho absoluto para o diretório de certificados de CA para tratar como um arquivo de pacote válido ou de autoridade de certificação. |
| --cert | Caminho absoluto para um arquivo de certificado de cliente. |
| --key | Caminho absoluto para o arquivo de chave de certificado do cliente. |
| --sem verificação | Desabilitar a verificação de certificados ao usar HTTPS,\: Observe que essa é uma opção insegura e não deve ser usada para ambientes de produção. |
| --pem | Caminho absoluto para o certificado do cliente, como um arquivo. PEM. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-conexão
Mostre a qual Service Fabric cluster a instância do sfctl está conectada.

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-unprovision"></a>desprovisionamento do cluster sfctl
Desprovisione o código ou os pacotes de configuração de um Cluster Service Fabric.

Há suporte para desprovisionar o código e a configuração separadamente.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --versão do código | A versão do pacote de código do cluster. |
| --config-version | A versão do manifesto do cluster. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-upgrade"></a>atualização do cluster sfctl
Inicie a atualização do código ou da versão de configuração de um Cluster Service Fabric.

Valide os parâmetros de atualização fornecidos e comece a atualizar o código ou a versão de configuração de um Cluster Service Fabric se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-health-map | Dicionário codificado em JSON de pares de nome do aplicativo e percentual máximo não íntegro antes de gerar erro. |
| --app-type-health-map | Dicionário codificado em JSON de pares de nome do tipo de aplicativo e percentual máximo não íntegro antes de gerar erro. |
| --versão do código | A versão do código do cluster. |
| --config-version | A versão de configuração do cluster. |
| --delta-health-evaluation | Habilita a avaliação de integridade Delta em vez da avaliação de integridade absoluta após a conclusão de cada domínio de atualização. |
| --delta-unhealthy-nodes | A porcentagem máxima permitida de degradação de integridade de nós permitida durante atualizações de cluster.  Padrão\: 10. <br><br> O Delta é medido entre o estado dos nós no início da atualização e o estado dos nós no momento da avaliação de integridade. A verificação é executada após a conclusão de cada atualização de domínio de atualização para garantir que o estado global do cluster esteja dentro dos limites tolerados. |
| --ação de falha | Os valores possíveis\: incluem ' invalid ', ' Rollback ', ' manual '. |
| --Force-Restart | Os processos são reiniciados de modo forçado durante a atualização, mesmo quando a versão do código não foi alterada. <br><br> A atualização só altera a configuração ou os dados. |
| --health-check-retry | O período de tempo entre as tentativas de realizar verificações de integridade se o aplicativo ou o cluster não estiver íntegro. |
| --health-check-stable | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização prossiga para o próximo domínio de atualização. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --health-check-wait | O período de tempo de espera após a conclusão de um domínio de atualização antes de iniciar o processo de verificações de integridade. |
| --replica-set-check-timeout | A quantidade máxima de tempo para bloquear o processamento de um domínio de atualização e evitar a perda de disponibilidade quando houver problemas inesperados. <br><br> Quando esse tempo limite expirar, o processamento do domínio de atualização continuará independentemente dos problemas de perda de disponibilidade. O tempo limite é redefinido no início de cada domínio de atualização. Os valores válidos estão entre 0 e 42949672925, inclusive. |
| --modo de atualização sem interrupção | Os valores possíveis\: incluem ' invalid ', ' UnmonitoredAuto ', ' UnmonitoredManual ', ' Monitored '.  UnmonitoredAuto\: padrão. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --aplicativos não íntegros | A porcentagem máxima permitida de aplicativos não íntegros antes de relatar um erro. <br><br> Por exemplo, para permitir que 10% dos aplicativos sejam não íntegros, esse valor seria 10. A porcentagem representa a porcentagem máxima tolerada de aplicativos que podem não estar íntegros antes que o cluster seja considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um aplicativo não íntegro, a integridade será avaliada como aviso. Isso é calculado dividindo o número de aplicativos não íntegros no número total de instâncias do aplicativo no cluster, excluindo aplicativos de tipos de aplicativos que estão incluídos no ApplicationTypeHealthPolicyMap. A computação arredonda até tolerar uma falha em pequenos números de aplicativos. |
| --Nós não íntegros | A porcentagem máxima permitida de nós não íntegros antes de relatar um erro. <br><br> Por exemplo, para permitir que 10% dos nós sejam não íntegros, esse valor seria 10. A porcentagem representa a porcentagem máxima tolerada de nós que podem não estar íntegros antes que o cluster seja considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um nó não íntegro, a integridade será avaliada como aviso. A porcentagem é calculada pela divisão do número de nós não íntegros no número total de nós no cluster. A computação arredonda até tolerar uma falha em pequenos números de nós. Em clusters grandes, alguns nós sempre estarão inativos ou fora para reparos, portanto, esse percentual deve ser configurado para tolerar isso. |
| --upgrade-domain-delta-unhealthy-nodes | A porcentagem máxima permitida da degradação de integridade dos nós de domínio de atualização permitida durante as atualizações do cluster.  Padrão\: 15. <br><br> O Delta é medido entre o estado dos nós de domínio de atualização no início da atualização e o estado dos nós de domínio de atualização no momento da avaliação de integridade. A verificação é realizada após cada conclusão de atualização de domínio de atualização para todos os domínios de atualização concluídos para garantir que o estado dos domínios de atualização esteja dentro dos limites tolerados. |
| --tempo limite do domínio de atualização | A quantidade de tempo que cada domínio de atualização precisa concluir antes que a falha seja executada. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --upgrade-timeout | O tempo durante o qual a atualização geral deve ser concluída antes de FailureAction ser executada. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --warning-as-error | Indica se os avisos são tratados com a mesma severidade que os erros. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-upgrade-resume"></a>atualização do cluster sfctl-retomar
Faça a atualização do cluster passar para o próximo domínio de atualização.

Faça com que o código de cluster ou atualização de configuração passe para o próximo domínio de atualização, se apropriado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Upgrade-Domain [obrigatório] | O próximo domínio de atualização para esta atualização de cluster. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-upgrade-rollback"></a>atualização do cluster sfctl-reversão
Reverter a atualização de um Cluster Service Fabric.

Reverter o código ou a atualização de configuração de um Cluster Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-upgrade-status"></a>atualização do cluster sfctl-status
Obtém o progresso da atualização de cluster atual.

Obtém o progresso atual da atualização de cluster em andamento. Se nenhuma atualização estiver em andamento, obtenha o último estado da atualização de cluster anterior.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-cluster-upgrade-update"></a>atualização do cluster sfctl-atualização
Atualize os parâmetros de atualização de uma atualização de Cluster Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-health-map | Dicionário codificado em JSON de pares de nome do aplicativo e percentual máximo não íntegro antes de gerar erro. |
| --app-type-health-map | Dicionário codificado em JSON de pares de nome do tipo de aplicativo e percentual máximo não íntegro antes de gerar erro. |
| --delta-health-evaluation | Habilita a avaliação de integridade Delta em vez da avaliação de integridade absoluta após a conclusão de cada domínio de atualização. |
| --delta-unhealthy-nodes | A porcentagem máxima permitida de degradação de integridade de nós permitida durante atualizações de cluster.  Padrão\: 10. <br><br> O Delta é medido entre o estado dos nós no início da atualização e o estado dos nós no momento da avaliação de integridade. A verificação é executada após a conclusão de cada atualização de domínio de atualização para garantir que o estado global do cluster esteja dentro dos limites tolerados. |
| --ação de falha | Os valores possíveis\: incluem ' invalid ', ' Rollback ', ' manual '. |
| --Force-Restart | Os processos são reiniciados de modo forçado durante a atualização, mesmo quando a versão do código não foi alterada. <br><br> A atualização só altera a configuração ou os dados. |
| --health-check-retry | O período de tempo entre as tentativas de realizar verificações de integridade se o aplicativo ou o cluster não estiver íntegro. |
| --health-check-stable | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização prossiga para o próximo domínio de atualização. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --health-check-wait | O período de tempo de espera após a conclusão de um domínio de atualização antes de iniciar o processo de verificações de integridade. |
| --replica-set-check-timeout | A quantidade máxima de tempo para bloquear o processamento de um domínio de atualização e evitar a perda de disponibilidade quando houver problemas inesperados. <br><br> Quando esse tempo limite expirar, o processamento do domínio de atualização continuará independentemente dos problemas de perda de disponibilidade. O tempo limite é redefinido no início de cada domínio de atualização. Os valores válidos estão entre 0 e 42949672925, inclusive. |
| --modo de atualização sem interrupção | Os valores possíveis\: incluem ' invalid ', ' UnmonitoredAuto ', ' UnmonitoredManual ', ' Monitored '.  UnmonitoredAuto\: padrão. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --aplicativos não íntegros | A porcentagem máxima permitida de aplicativos não íntegros antes de relatar um erro. <br><br> Por exemplo, para permitir que 10% dos aplicativos sejam não íntegros, esse valor seria 10. A porcentagem representa a porcentagem máxima tolerada de aplicativos que podem não estar íntegros antes que o cluster seja considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um aplicativo não íntegro, a integridade será avaliada como aviso. Isso é calculado dividindo o número de aplicativos não íntegros no número total de instâncias do aplicativo no cluster, excluindo aplicativos de tipos de aplicativos que estão incluídos no ApplicationTypeHealthPolicyMap. A computação arredonda até tolerar uma falha em pequenos números de aplicativos. |
| --Nós não íntegros | A porcentagem máxima permitida de nós não íntegros antes de relatar um erro. <br><br> Por exemplo, para permitir que 10% dos nós sejam não íntegros, esse valor seria 10. A porcentagem representa a porcentagem máxima tolerada de nós que podem não estar íntegros antes que o cluster seja considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um nó não íntegro, a integridade será avaliada como aviso. A porcentagem é calculada pela divisão do número de nós não íntegros no número total de nós no cluster. A computação arredonda até tolerar uma falha em pequenos números de nós. Em clusters grandes, alguns nós sempre estarão inativos ou fora para reparos, portanto, esse percentual deve ser configurado para tolerar isso. |
| --upgrade-domain-delta-unhealthy-nodes | A porcentagem máxima permitida da degradação de integridade dos nós de domínio de atualização permitida durante as atualizações do cluster.  Padrão\: 15. <br><br> O Delta é medido entre o estado dos nós de domínio de atualização no início da atualização e o estado dos nós de domínio de atualização no momento da avaliação de integridade. A verificação é realizada após cada conclusão de atualização de domínio de atualização para todos os domínios de atualização concluídos para garantir que o estado dos domínios de atualização esteja dentro dos limites tolerados. |
| --tempo limite do domínio de atualização | A quantidade de tempo que cada domínio de atualização precisa concluir antes que a falha seja executada. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --tipo de atualização | Os valores possíveis\: incluem ' invalid ', ' revertida ', ' Rolling_ForceRestart '.  Sem\: interrupção padrão. |
| --upgrade-timeout | O tempo durante o qual a atualização geral deve ser concluída antes de FailureAction ser executada. <br><br> Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. |
| --warning-as-error | Indica se os avisos são tratados com a mesma severidade que os erros. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
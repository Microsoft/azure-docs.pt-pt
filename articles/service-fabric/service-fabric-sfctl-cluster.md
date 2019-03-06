---
title: Cluster do Azure Service Fabric CLI - sfctl | Documentos da Microsoft
description: Descreve os comandos de cluster do Service Fabric CLI sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: e3aae5f7936204a7fe4fbce4102fc2727088e025
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442036"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Selecione, gerir e operar a clusters do Service Fabric.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| versões de código | Obtém uma lista de recursos de infraestrutura versões de código aprovisionadas num cluster do Service Fabric. |
| config-versions | Obtém uma lista de recursos de infraestrutura versões de configuração que são aprovisionadas num cluster do Service Fabric. |
| saúde | Obtém o estado de funcionamento de um cluster do Service Fabric. |
| Manifesto | Obter o manifesto de cluster do Service Fabric. |
| operation-cancel | Cancela uma operação de índice de falhas induzidas pelo utilizador. |
| operation-list | Obtém uma lista de operações de índice de falhas induzidas pelo utilizador filtradas pela entrada fornecida. |
| Aprovisionar | Aprovisione os pacotes de código ou configuração de um cluster do Service Fabric. |
| recover-system | Indica ao cluster do Service Fabric que ele deve tentar recuperar os serviços do sistema que estão atualmente bloqueados em perda de quórum. |
| report-health | Envia um relatório de estado de funcionamento no cluster do Service Fabric. |
| seleccionar | Liga-se para um ponto de extremidade do cluster do Service Fabric. |
| show-connection | Mostre qual esta instância de sfctl está ligada ao cluster do Service Fabric. |
| não aprovisionamento | Anular o aprovisionamento os pacotes de código ou configuração de um cluster do Service Fabric. |
| Atualizar | Começar a atualizar a versão de código ou configuração do cluster do Service Fabric. |
| upgrade-resume | Fazer a atualização do cluster passar para o domínio de atualização seguinte. |
| reversão de atualização | Reverta a atualização de um cluster do Service Fabric. |
| upgrade-status | Obtém o progresso da atualização do cluster atual. |
| atualização-atualização | Atualize os parâmetros de atualização de uma atualização de cluster do Service Fabric. |

## <a name="sfctl-cluster-code-versions"></a>versões de código do cluster sfctl
Obtém uma lista de recursos de infraestrutura versões de código aprovisionadas num cluster do Service Fabric.

Obtém uma lista de informações sobre recursos de infraestrutura versões de código que são aprovisionadas no cluster. O parâmetro CodeVersion pode ser utilizado para filtrar, opcionalmente, a saída para apenas essa versão específica.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --code-version | A versão de produto do Service Fabric. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-config-versions"></a>versões de configuração do cluster sfctl
Obtém uma lista de recursos de infraestrutura versões de configuração que são aprovisionadas num cluster do Service Fabric.

Obtém uma lista de informações sobre recursos de infraestrutura versões de configuração que são aprovisionadas no cluster. O parâmetro ConfigVersion pode ser utilizado para filtrar, opcionalmente, a saída para apenas essa versão específica.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --config-version | A versão de configuração do Service Fabric. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-health"></a>Estado de funcionamento de cluster de sfctl
Obtém o estado de funcionamento de um cluster do Service Fabric.

Utilize EventsHealthStateFilter para filtrar a recolha de eventos de estado de funcionamento reportadas no cluster, com base no estado de funcionamento. Da mesma forma, use o NodesHealthStateFilter e ApplicationsHealthStateFilter para filtrar a coleção de nós e devolvidos com base no respetivo estado de funcionamento agregado de aplicativos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --applications-health-state-filter | Permite a filtragem dos objetos de estado do Estado de funcionamento da aplicação devolvidos no resultado da consulta de estado de funcionamento do cluster com base no respetivo estado de funcionamento. Os valores possíveis para este parâmetro incluem obtida a partir de membros ou operações bit a bit em membros da enumeração HealthStateFilter de valor de número inteiro. Apenas as aplicações que correspondem ao filtro são devolvidas. Todas as aplicações são utilizadas para avaliar o estado de funcionamento agregado. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, estado de funcionamento das aplicações com o valor HealthState OK (2) e (4) de aviso são devolvidos.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| --events-health-state-filter | Permite a filtragem da coleção de objetos de HealthEvent devolvidos com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregado. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores, obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, todos os eventos com o valor HealthState OK (2) e (4) de aviso são devolvidos.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| --exclude-health-statistics | Indica se as estatísticas de estado de funcionamento devem ser devolvidas como parte do resultado da consulta. FALSE por padrão. As estatísticas mostram o número de subordinados entidades no estado de funcionamento Ok, aviso e erro. |
| --include-system-application-health-statistics | Indica se as estatísticas de estado de funcionamento devem incluir os recursos de infraestrutura\:estatísticas de estado de funcionamento da aplicação. cmd /System. FALSE por padrão. Se IncludeSystemApplicationHealthStatistics estiver definido como true, o estado de funcionamento, as estatísticas incluem as entidades que pertencem aos recursos de infraestrutura\:application. cmd /System. Caso contrário, o resultado da consulta inclui as estatísticas de estado de funcionamento apenas para aplicações de utilizador. As estatísticas de estado de funcionamento tem de ser incluídas no resultado da consulta para este parâmetro a ser aplicado. |
| --nodes-health-state-filter | Permite a filtragem de objetos de estado de funcionamento nó devolvidos no resultado da consulta de estado de funcionamento do cluster com base no respetivo estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento. Apenas nós que correspondem ao filtro são devolvidos. Todos os nós são utilizados para avaliar o estado de funcionamento agregado. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, estado de funcionamento de nós com o valor HealthState OK (2) e (4) de aviso são devolvidos.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-manifest"></a>manifesto do cluster sfctl
Obter o manifesto de cluster do Service Fabric.

Obter o manifesto de cluster do Service Fabric. O manifesto do cluster contém as propriedades do cluster, que incluem os tipos de nó diferente no cluster, configurações de segurança, falhas e topologias de domínio de atualização, etc. Estas propriedades são especificadas como parte do arquivo ClusterConfig.JSON durante a implementação de um cluster autónomo. No entanto, a maioria das informações no manifesto do cluster é gerado internamente pelo service fabric durante a implementação de cluster nos outros cenários de implementação (por exemplo, quando utilizar o portal do Azure). O conteúdo do manifesto do cluster é apenas para fins informativos e os utilizadores não são esperados para efetuar uma dependência em formato de conteúdos do ficheiro ou sua interpretação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-operation-cancel"></a>operação de cluster de sfctl-Cancelar
Cancela uma operação de índice de falhas induzidas pelo utilizador.

As seguintes APIs iniciar as operações de índice de falhas que podem ser canceladas utilizando CancelOperation\: StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Se forçar for false, em seguida, a operação especificada induzidas pelo usuário será corretamente parada e limpos.  Se forçar for VERDADEIRO, o comando será anulado, e algum Estado interno pode ser deixado para trás.  Especificação força como true deve ser usado com cuidado. Não é permitida a chamar esta API com força definido como true até que esta API já foi chamada no mesmo comando de teste com conjunto de força primeiro false ou, a menos que o comando de teste já tem um OperationState OperationState.RollingBack. 

Esclarecimento\: OperationState.RollingBack significa que o sistema será/limpeza de sistema interno provocado por executar o comando de estado.  Não irá restaurar dados se o comando de teste foi causar perda de dados.  Por exemplo, se chama StartDataLoss, em seguida, chama esta API, o sistema será apenas Limpar estado interno de executar o comando. Não irá restaurar dados a partição de destino, se o comando progrediu suficiente para causar perda de dados. 

> [!NOTE]
> Se esta API é invocada com force = = true, interno de estado pode ser deixado para trás.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de operação [necessário] | Um GUID que identifica uma chamada desta API.  Isso é passado para a API de GetProgress correspondente. |
| --force | Indica se devem ser corretamente revertê-lo e limpar o estado do sistema interno modificado por executar a operação induzidas pelo utilizador. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-operation-list"></a>operação de cluster de sfctl-lista
Obtém uma lista de operações de índice de falhas induzidas pelo utilizador filtradas pela entrada fornecida.

Obtém a lista de operações de índice de falhas induzidas pelo utilizador filtradas pela entrada fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --state-filter | Utilizado para filtrar em do OperationState para operações de induzidas pelo utilizador. <br> 65535 - Selecionar tudo <br> 1 - selecionar em execução <br> 2 - selecionar RollingBack <br>8 - selecione concluído <br>16 - selecione Faulted <br>32 - selecione cancelada <br>64 - selecione ForceCancelled.  <br>Predefinido\: 65535. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |
| --type-filter | Utilizado para filtrar em OperationType para operações de induzidas pelo utilizador. <br> 65535 - Selecionar tudo <br> 1 - Selecione PartitionDataLoss. <br> 2 - selecione PartitionQuorumLoss. <br> 4 - selecione PartitionRestart. <br> 8 - selecione NodeTransition.  <br> Predefinido\: 65535. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-provision"></a>Aprovisionamento do cluster de sfctl
Aprovisione os pacotes de código ou configuração de um cluster do Service Fabric.

Validar e provisionar os pacotes de código ou configuração de um cluster do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --cluster-manifest-file-path | O caminho de ficheiro de manifesto do cluster. |
| --code-file-path | Caminho de ficheiro de pacote do código de cluster. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-recover-system"></a>sistema de recuperação do cluster sfctl
Indica ao cluster do Service Fabric que ele deve tentar recuperar os serviços do sistema que estão atualmente bloqueados em perda de quórum.

Indica ao cluster do Service Fabric que ele deve tentar recuperar os serviços do sistema que estão atualmente bloqueados em perda de quórum. Esta operação só deve ser efetuada se se sabe que não não possível recuperar as réplicas que estão indisponíveis. Utilização incorreta desta API pode causar a potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-report-health"></a>o sfctl cluster relatório de estado de funcionamento
Envia um relatório de estado de funcionamento no cluster do Service Fabric.

O relatório tem de conter as informações sobre a origem do relatório de estado de funcionamento e em que é comunicado de propriedade. O relatório é enviado para um nó de gateway do Service Fabric, que encaminha para o arquivo de estado de funcionamento. O relatório pode ser aceites pelo gateway, mas rejeitado pelo arquivo de estado de funcionamento após a validação extra. Por exemplo, o arquivo de estado de funcionamento poderá rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoletos. Para ver se o relatório foi aplicado no arquivo de estado de funcionamento, verifique que o relatório é apresentado no HealthEvents do cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – [necessária] de propriedade de estado de funcionamento | A propriedade das informações de estado de funcionamento. <br><br> Uma entidade pode ter relatórios de estado de funcionamento para diferentes propriedades. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir que a flexibilidade de gerador de relatórios categorizar a condição de estado que aciona o relatório. Por exemplo, um gerador de relatórios com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que ele pode informar a propriedade de "AvailableDisk" nesse nó. O mesmo gerador de relatórios pode monitorizar a conectividade de nó, para que ele pode reportar uma propriedade "Conectividade" no mesmo nó. No arquivo de estado de funcionamento, esses relatórios são tratados como eventos de estado de funcionamento separado para o nó especificado. Em conjunto com o SourceId, a propriedade identifica exclusivamente as informações de estado de funcionamento. |
| -Estado de funcionamento [necessário] | Os valores possíveis incluem\: "Inválido", "Ok", "Aviso", "Error", "Desconhecido". |
| – id de origem [necessário] | O nome de origem que identifica o componente de sistema/watchdog/cliente gerado as informações de estado de funcionamento. |
| --description | A descrição das informações de estado de funcionamento. <br><br> Ele representa o texto livre usado para adicionar informações legíveis humanas sobre o relatório. O comprimento máximo da cadeia para a descrição é 4096 carateres. Se a cadeia fornecida é mais longa, ele será automaticamente truncado. Quando truncados, os últimos carateres da descrição contêm um marcador "[truncado]" e o tamanho total da cadeia de caracteres é 4096 carateres. A presença do marcador indica aos utilizadores esse truncamento ocorreu. Observe que, quando truncados, a descrição tem menos de 4096 carateres a partir da cadeia original. |
| – imediata | Um sinalizador que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de estado de funcionamento é enviado para um aplicativo, que encaminha para o arquivo de estado de funcionamento de gateway do Service Fabric. Se Immediate estiver definido como true, o relatório será enviado imediatamente do Gateway de HTTP para o armazenamento de estado de funcionamento, independentemente das definições de cliente de recursos de infraestrutura que está a utilizar a aplicação de Gateway HTTP. Isto é útil para os relatórios críticos que devem ser enviados logo que possível. Dependendo do tempo e outras condições, enviar o relatório poderá ainda falhar, por exemplo, se o HTTP Gateway foi fechado ou a mensagem não aceder ao Gateway. Se Immediate estiver definido como false, o relatório é enviado com base nas definições de cliente do Estado de funcionamento do HTTP Gateway. Por conseguinte, irá ser loteado, de acordo com a configuração de HealthReportSendInterval. Esta é a definição recomendada porque permite que o cliente do Estado de funcionamento otimizar as mensagens para o arquivo de estado de funcionamento, bem como o processamento de relatórios de estado de funcionamento de relatórios de estado de funcionamento. Por predefinição, os relatórios não são enviados imediatamente. |
| --remove-when-expired | Valor que indica se o relatório é removido do arquivo de estado de funcionamento, quando este expirar. <br><br> Se definido como true, o relatório for removido do arquivo de estado de funcionamento, depois de expirar. Se definido como false, o relatório é tratado como um erro quando a expirou. O valor desta propriedade é false por padrão. Quando os clientes reportem periodicamente, eles devem definir RemoveWhenExpired false (predefinição). Dessa forma, é o gerador de relatórios tem problemas (por exemplo, o deadlock) e não é possível reportar a entidade é avaliada em erro quando expira o relatório de estado de funcionamento. Este processo sinaliza a entidade como sendo num Estado de funcionamento de erro. |
| --sequence-number | O número de sequência para este relatório de estado de funcionamento como uma cadeia numérica. <br><br> O número de sequência de relatório é utilizado pelo arquivo de estado de funcionamento para detetar relatórios obsoletos. Se não for especificado, um número de sequência é gerado automaticamente pelo cliente do Estado de funcionamento quando é adicionado um relatório. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |
| --ttl | A duração para o qual este relatório de estado de funcionamento é válido. Este campo utiliza o formato ISO8601 para especificar a duração. <br><br> Quando os clientes reportem periodicamente, eles devem enviar relatórios com freqüência mais alta do que o tempo de duração. Se os clientes comunicam na transição, eles podem definir o tempo de duração para infinito. Quando expira o TTL, o evento de estado de funcionamento que contém as informações de estado de funcionamento é seja removido do arquivo de estado de funcionamento, se RemoveWhenExpired for true, ou avaliadas no erro, se RemoveWhenExpired false. Se não for especificado, tempo de duração a predefinição é o valor de infinito. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-select"></a>o sfctl cluster select
Liga-se para um ponto de extremidade do cluster do Service Fabric.

Se ligar ao cluster seguro, especifique um caminho absoluto para um certificado (. crt) e o ficheiro de chave (.key) ou um único ficheiro com os dois (. pem). Não especifique ambos. Opcionalmente, se ligar a um cluster seguro, também de especificar um caminho absoluto para um ficheiro de agrupamento de AC ou o diretório de certificados de AC fidedignos. Se utilizar um diretório de certificados de AC, `c_rehash <directory>` fornecida pelo OpenSSL tem de ser executado pela primeira vez para computar os hashes de certificado e criar as ligações simbólicas apropriadas.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – endpoint [necessário] | URL de ponto de extremidade, incluindo a porta e o prefixo HTTP ou HTTPS do cluster. |
| --aad | Utilize o Azure Active Directory para autenticação. |
| --ca | Caminho absoluto para o diretório de certificados de AC para tratar como válido ou um ficheiro de pacote de AC. |
| --cert | Caminho absoluto para um ficheiro de certificado de cliente. |
| --key | Caminho absoluto do ficheiro de chave do certificado de cliente. |
| – Certifique-se de não | Desativar a verificação de certificados ao utilizar o HTTPS, tenha em atenção\: esta é uma opção insegura e não deve ser utilizada para ambientes de produção. |
| --pem | Caminho absoluto para o certificado de cliente, como um ficheiro. pem. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-show-connection"></a>cluster de sfctl show-connection
Mostre qual esta instância de sfctl está ligada ao cluster do Service Fabric.

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-unprovision"></a>não aprovisionamento do cluster de sfctl
Anular o aprovisionamento os pacotes de código ou configuração de um cluster do Service Fabric.

É suportada para anular o aprovisionamento de código e a configuração em separado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --code-version | A versão de pacote de código do cluster. |
| --config-version | A versão do manifesto do cluster. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-upgrade"></a>atualização do cluster sfctl
Começar a atualizar a versão de código ou configuração do cluster do Service Fabric.

Valide os parâmetros de atualização fornecidos e começar a atualizar a versão de código ou configuração do cluster do Service Fabric se os parâmetros são válidos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-health-map | JSON com a codificação de dicionário de pares de nome da aplicação e a percentagem máxima em mau estado de funcionamento antes de gerar o erro. |
| --app-type-health-map | JSON com a codificação de dicionário de pares de nome de tipo de aplicação e a percentagem máxima em mau estado de funcionamento antes de gerar o erro. |
| --code-version | A versão de código do cluster. |
| --config-version | A versão de configuração do cluster. |
| --delta-health-evaluation | Permite a avaliação de integridade de delta, em vez de avaliação de estado de funcionamento absoluto após a conclusão de cada domínio de atualização. |
| --delta-unhealthy-nodes | O máximo permitido a percentagem de nós degradação de estado de funcionamento permitida durante as atualizações de cluster.  Predefinido\: 10. <br><br> O delta é medido entre o estado de nós no início da atualização e o estado de nós no momento da avaliação do Estado de funcionamento. A verificação é executada após a conclusão de atualização cada domínio de atualização para se certificar de que o estado global do cluster está dentro dos limites tolerados. |
| --failure-action | Os valores possíveis incluem\: 'inválido,""Reversão","Manual". |
| --force-restart | Processos forçadamente são reiniciados durante a atualização, mesmo quando a versão de código não foi alterada. <br><br> A atualização só podem ser alterados ou dados de configuração. |
| --health-check-retry | O período de tempo entre tentativas para realizar verificações de estado de funcionamento se o aplicativo ou o cluster não está em bom estado. |
| --health-check-stable | A quantidade de tempo que a aplicação ou o cluster deve permanecer em bom estado antes que a atualização prossiga para o domínio de atualização seguinte. <br><br> Em primeiro lugar será interpretado como uma cadeia que representa uma duração ISO 8601. Se isso falhar, ela é interpretada como um número que representa o número total de milissegundos. |
| --health-check-wait | O período de tempo de espera após a conclusão de um domínio de atualização antes de iniciar o estado de funcionamento verifica o processo. |
| --replica-set-check-timeout | A quantidade máxima de tempo para bloquear o processamento de um domínio de atualização e evitar a perda de disponibilidade quando surgem problemas inesperados. <br><br> Quando este tempo limite expira, o processamento de domínio de atualização irá continuar, independentemente de problemas de perda de disponibilidade. O tempo limite é reiniciado no início de cada domínio de atualização. Valores válidos são entre 0 e 42949672925, inclusive. |
| --rolling-upgrade-mode | Os valores possíveis incluem\: 'Inválido', 'UnmonitoredAuto', 'UnmonitoredManual', "Monitorizado".  Predefinido\: UnmonitoredAuto. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |
| --unhealthy-applications | O máximo permitido porcentagem de aplicativos de mau estado de funcionamento antes de comunicar um erro. <br><br> Por exemplo, para permitir que 10% dos aplicativos problemático, este valor seria de 10. A percentagem representa a percentagem máxima de tolerado de aplicativos que podem ser mau estado de funcionamento antes do cluster será considerado como erro. Se a percentagem é respeitada, mas há pelo menos uma aplicação de mau estado de funcionamento, o estado de funcionamento é avaliado como aviso. Isso é calculado dividindo o número de aplicativos de mau estado de funcionamento e o número total de instâncias da aplicação no cluster, excluindo os aplicativos dos tipos de aplicativos que estão incluídos no ApplicationTypeHealthPolicyMap. A computação Arredonda por excesso para tolerar uma falha em pequenos números de aplicativos. |
| --unhealthy-nodes | O máximo permitido de percentagem de nós de mau estado de funcionamento antes de comunicar um erro. <br><br> Por exemplo, para permitir que 10% de nós para ser mau estado de funcionamento, este valor seria de 10. A percentagem representa a percentagem de tolerado máxima de nós que podem ser mau estado de funcionamento antes do cluster será considerado como erro. Se a percentagem é respeitada, mas não existe, pelo menos, um nó de mau estado de funcionamento, o estado de funcionamento é avaliado como aviso. A percentagem é calculada dividindo o número de nós de mau estado de funcionamento e o número total de nós no cluster. A computação Arredonda por excesso para tolerar uma falha num pequeno número de nós. Em grandes clusters, alguns nós será sempre verticalmente ou horizontalmente para reparos, para que esta percentagem deve ser configurada para tolerar que. |
| --upgrade-domain-delta-unhealthy-nodes | O máximo permitido percentagem de nós de domínio de atualização de degradação de estado de funcionamento permitida durante as atualizações de cluster.  Predefinido\: 15. <br><br> O delta é medido entre o estado de nós do domínio de atualização no início da atualização e o estado de nós do domínio de atualização no momento da avaliação do Estado de funcionamento. A verificação é executada após a conclusão de cada conclusão de atualização de domínio de atualização para todos os domínios de atualização para se certificar de que o estado de domínios de atualização é dentro dos limites tolerados. |
| --upgrade-domain-timeout | A quantidade de tempo de cada domínio de atualização tem de concluir antes de ser executada FailureAction. <br><br> Em primeiro lugar será interpretado como uma cadeia que representa uma duração ISO 8601. Se isso falhar, ela é interpretada como um número que representa o número total de milissegundos. |
| --upgrade-timeout | A quantidade de tempo a atualização global tem de concluir antes de ser executada FailureAction. <br><br> Em primeiro lugar será interpretado como uma cadeia que representa uma duração ISO 8601. Se isso falhar, ela é interpretada como um número que representa o número total de milissegundos. |
| --warning-as-error | Indica se os avisos são tratados com a mesma gravidade como erros. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-upgrade-resume"></a>atualização de cluster de sfctl-retomar
Fazer a atualização do cluster passar para o domínio de atualização seguinte.

Migrar cluster código ou configuração de atualização para o domínio de atualização seguinte se tal for apropriado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -domínios de atualização [necessário] | O domínio de atualização seguinte para esta atualização de cluster. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-upgrade-rollback"></a>reversão de atualização do cluster sfctl
Reverta a atualização de um cluster do Service Fabric.

Reverta a atualização de código ou configuração de um cluster do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-upgrade-status"></a>Estado de atualização do cluster sfctl
Obtém o progresso da atualização do cluster atual.

Obtém o progresso atual da atualização do cluster em curso. Se nenhuma atualização está atualmente em curso, obtenha o último Estado da atualização do cluster anterior.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-cluster-upgrade-update"></a>cluster de sfctl atualização-atualização
Atualize os parâmetros de atualização de uma atualização de cluster do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-health-map | JSON com a codificação de dicionário de pares de nome da aplicação e a percentagem máxima em mau estado de funcionamento antes de gerar o erro. |
| --app-type-health-map | JSON com a codificação de dicionário de pares de nome de tipo de aplicação e a percentagem máxima em mau estado de funcionamento antes de gerar o erro. |
| --delta-health-evaluation | Permite a avaliação de integridade de delta, em vez de avaliação de estado de funcionamento absoluto após a conclusão de cada domínio de atualização. |
| --delta-unhealthy-nodes | O máximo permitido a percentagem de nós degradação de estado de funcionamento permitida durante as atualizações de cluster.  Predefinido\: 10. <br><br> O delta é medido entre o estado de nós no início da atualização e o estado de nós no momento da avaliação do Estado de funcionamento. A verificação é executada após a conclusão de atualização cada domínio de atualização para se certificar de que o estado global do cluster está dentro dos limites tolerados. |
| --failure-action | Os valores possíveis incluem\: 'inválido,""Reversão","Manual". |
| --force-restart | Processos forçadamente são reiniciados durante a atualização, mesmo quando a versão de código não foi alterada. <br><br> A atualização só podem ser alterados ou dados de configuração. |
| --health-check-retry | O período de tempo entre tentativas para realizar verificações de estado de funcionamento se o aplicativo ou o cluster não está em bom estado. |
| --health-check-stable | A quantidade de tempo que a aplicação ou o cluster deve permanecer em bom estado antes que a atualização prossiga para o domínio de atualização seguinte. <br><br> Em primeiro lugar será interpretado como uma cadeia que representa uma duração ISO 8601. Se isso falhar, ela é interpretada como um número que representa o número total de milissegundos. |
| --health-check-wait | O período de tempo de espera após a conclusão de um domínio de atualização antes de iniciar o estado de funcionamento verifica o processo. |
| --replica-set-check-timeout | A quantidade máxima de tempo para bloquear o processamento de um domínio de atualização e evitar a perda de disponibilidade quando surgem problemas inesperados. <br><br> Quando este tempo limite expira, o processamento de domínio de atualização irá continuar, independentemente de problemas de perda de disponibilidade. O tempo limite é reiniciado no início de cada domínio de atualização. Valores válidos são entre 0 e 42949672925, inclusive. |
| --rolling-upgrade-mode | Os valores possíveis incluem\: 'Inválido', 'UnmonitoredAuto', 'UnmonitoredManual', "Monitorizado".  Predefinido\: UnmonitoredAuto. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |
| --unhealthy-applications | O máximo permitido porcentagem de aplicativos de mau estado de funcionamento antes de comunicar um erro. <br><br> Por exemplo, para permitir que 10% dos aplicativos problemático, este valor seria de 10. A percentagem representa a percentagem máxima de tolerado de aplicativos que podem ser mau estado de funcionamento antes do cluster será considerado como erro. Se a percentagem é respeitada, mas há pelo menos uma aplicação de mau estado de funcionamento, o estado de funcionamento é avaliado como aviso. Isso é calculado dividindo o número de aplicativos de mau estado de funcionamento e o número total de instâncias da aplicação no cluster, excluindo os aplicativos dos tipos de aplicativos que estão incluídos no ApplicationTypeHealthPolicyMap. A computação Arredonda por excesso para tolerar uma falha em pequenos números de aplicativos. |
| --unhealthy-nodes | O máximo permitido de percentagem de nós de mau estado de funcionamento antes de comunicar um erro. <br><br> Por exemplo, para permitir que 10% de nós para ser mau estado de funcionamento, este valor seria de 10. A percentagem representa a percentagem de tolerado máxima de nós que podem ser mau estado de funcionamento antes do cluster será considerado como erro. Se a percentagem é respeitada, mas não existe, pelo menos, um nó de mau estado de funcionamento, o estado de funcionamento é avaliado como aviso. A percentagem é calculada dividindo o número de nós de mau estado de funcionamento e o número total de nós no cluster. A computação Arredonda por excesso para tolerar uma falha num pequeno número de nós. Em grandes clusters, alguns nós será sempre verticalmente ou horizontalmente para reparos, para que esta percentagem deve ser configurada para tolerar que. |
| --upgrade-domain-delta-unhealthy-nodes | O máximo permitido percentagem de nós de domínio de atualização de degradação de estado de funcionamento permitida durante as atualizações de cluster.  Predefinido\: 15. <br><br> O delta é medido entre o estado de nós do domínio de atualização no início da atualização e o estado de nós do domínio de atualização no momento da avaliação do Estado de funcionamento. A verificação é executada após a conclusão de cada conclusão de atualização de domínio de atualização para todos os domínios de atualização para se certificar de que o estado de domínios de atualização é dentro dos limites tolerados. |
| --upgrade-domain-timeout | A quantidade de tempo de cada domínio de atualização tem de concluir antes de ser executada FailureAction. <br><br> Em primeiro lugar será interpretado como uma cadeia que representa uma duração ISO 8601. Se isso falhar, ela é interpretada como um número que representa o número total de milissegundos. |
| --upgrade-kind | Os valores possíveis incluem\: 'Inválido","Implementar", 'Rolling_ForceRestart'.  Predefinido\: sem interrupção. |
| --upgrade-timeout | A quantidade de tempo a atualização global tem de concluir antes de ser executada FailureAction. <br><br> Em primeiro lugar será interpretado como uma cadeia que representa uma duração ISO 8601. Se isso falhar, ela é interpretada como um número que representa o número total de milissegundos. |
| --warning-as-error | Indica se os avisos são tratados com a mesma gravidade como erros. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |


## <a name="next-steps"></a>Passos Seguintes
- [Configuração](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como utilizar a CLI do Service Fabric utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
---
title: Aplicação do Azure Service Fabric CLI - sfctl | Documentos da Microsoft
description: Descreve os comandos de aplicação do Service Fabric CLI sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: d4fec5d8131d269d3df229360066452c37a92430
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837492"
---
# <a name="sfctl-application"></a>sfctl application
Criar, eliminar e gerir aplicações e tipos de aplicativos.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| create | Cria uma aplicação de Service Fabric com a descrição especificada. |
| delete | Elimina uma aplicação de Service Fabric existente. |
| deployed | Obtém as informações sobre uma aplicação implementada num nó do Service Fabric. |
| deployed-health | Obtém as informações sobre o estado de funcionamento de um aplicativo implantado num nó do Service Fabric. |
| deployed-list | Obtém a lista de aplicativos implantados num nó do Service Fabric. |
| health | Obtém o estado de funcionamento da aplicação do service fabric. |
| info | Obtém informações sobre uma aplicação do Service Fabric. |
| list | Obtém a lista de aplicativos criados no cluster do Service Fabric que correspondem aos filtros especificados. |
| load | Obtém informações sobre uma aplicação do Service Fabric de carga. |
| manifesto | Obtém o manifesto que descreve um tipo de aplicação. |
| provision | Provisiona ou registros de tipo de aplicação do Service Fabric com o cluster utilizando o pacote. sfpkg no arquivo externo ou utilizando o pacote de aplicação no arquivo de imagem. |
| report-health | Envia um relatório de estado de funcionamento sobre a aplicação do Service Fabric. |
| type | Obtém a lista de tipos de aplicação no cluster do Service Fabric que corresponde exatamente ao nome especificado. |
| type-list | Obtém a lista de tipos de aplicação no cluster do Service Fabric. |
| unprovision | Remove ou anula o registo de um tipo de aplicação do Service Fabric do cluster. |
| upgrade | Começa a atualizar uma aplicação no cluster do Service Fabric. |
| upgrade-resume | Retoma a atualizar uma aplicação no cluster do Service Fabric. |
| upgrade-rollback | Inicia a reverter a atualização de atualmente em curso de uma aplicação no cluster do Service Fabric. |
| upgrade-status | Obtém os detalhes para a atualização mais recente efetuada nesta aplicação. |
| upload | Copie um pacote de aplicação do Service Fabric para o armazenamento de imagens. |

## <a name="sfctl-application-create"></a>Criar aplicação de sfctl
Cria uma aplicação de Service Fabric com a descrição especificada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| [necessário] - aplicação-nome | O nome do aplicativo, incluindo o "recursos de infraestrutura\:" esquema de URI. |
| [necessário] de tipo de aplicação – | O nome do tipo de aplicação encontrado no manifesto do aplicativo. |
| [necessário] versão da aplicação – | A versão do tipo de aplicação conforme definido no manifesto do aplicativo. |
| --max-node-count | O número máximo de nós em que o Service Fabric serão capacidade de reserva para esta aplicação. Tenha em atenção que isto não significa que os serviços desta aplicação serão colocados em todos esses nós. |
| -métricas | Um JSON com a codificação de lista de descrições de métricas de capacidade de aplicativos. Uma métrica é definida como um nome, associado a um conjunto de capacidades para cada nó, a aplicação existente no. |
| --min-node-count | O número mínimo de nós em que o Service Fabric serão capacidade de reserva para esta aplicação. Tenha em atenção que isto não significa que os serviços desta aplicação serão colocados em todos esses nós. |
| -parâmetros | Uma lista JSON codificado do parâmetro de aplicação substitui a ser aplicado ao criar a aplicação. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-delete"></a>o sfctl application delete
Elimina uma aplicação de Service Fabric existente.

Uma aplicação tem de ser criada antes de poder ser eliminado. A eliminar uma aplicação, irá eliminar todos os serviços que fazem parte do aplicativo. Por predefinição, o Service Fabric tentará fechar as réplicas do serviço de forma amigável e, em seguida, eliminar o serviço. No entanto, se um serviço está a ter problemas corretamente a fechar a réplica, a operação de eliminação pode demorar muito tempo ou ficar preso. Utilize o sinalizador de ForceRemove opcional para ignorar a sequência de fechar anulações normal e forçadamente eliminar a aplicação e todos os seus serviços.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| --force-remove | Remova uma aplicação do Service Fabric ou serviço forçadamente sem passar pela sequência de encerramento correto. Este parâmetro pode ser utilizado para forçadamente eliminar uma aplicação ou serviço para que delete é exceder o tempo limite devido a problemas com o código do serviço que impede que as anulações normal fechar das réplicas. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-deployed"></a>aplicação de sfctl implementada
Obtém as informações sobre uma aplicação implementada num nó do Service Fabric.

Esta consulta devolve as informações de aplicação do sistema se o ID de aplicação fornecido é para a aplicação de sistema. Resultados abrangem aplicações implementadas no Active Directory, ativação e a transferência de Estados. Esta consulta requer que o nome do nó corresponde a um nó no cluster. A consulta falha se o nome do nó fornecido não aponta para quaisquer nós de recursos de infraestrutura do serviço do Active Directory no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| --nome do nó [necessário] | O nome do nó. |
| --include-health-state | Inclua o estado de funcionamento de uma entidade. Se este parâmetro for false ou não foi especificado, o estado de funcionamento devolvido é "Desconhecido". Quando definido como true, a consulta vai em paralelo para o nó e o serviço de sistema de estado de funcionamento antes dos resultados são mesclados. Como resultado, a consulta é mais cara e pode demorar mais tempo. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-deployed-health"></a>o sfctl aplicação implementada-estado de funcionamento
Obtém as informações sobre o estado de funcionamento de um aplicativo implantado num nó do Service Fabric.

Obtém as informações sobre o estado de funcionamento de um aplicativo implantado num nó do Service Fabric. Utilize EventsHealthStateFilter opcionalmente filtrar por coleção de objetos de HealthEvent comunicado a aplicação implementada com base no estado de funcionamento. Utilize DeployedServicePackagesHealthStateFilter opcionalmente filtrar por crianças DeployedServicePackageHealth com base no estado de funcionamento.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| --nome do nó [necessário] | O nome do nó. |
| --deployed-service-packages-health-state-filter | Permite a filtragem dos objetos de estado do Estado de funcionamento do serviço implementado pacote devolvidos no resultado da consulta de estado de funcionamento da aplicação implementada com base no respetivo estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento. Apenas implementaram o serviço de pacotes que correspondem ao filtro são devolvidos. Todos os pacotes de serviços implementados são utilizados para avaliar o estado de funcionamento agregado da aplicação implementada. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores, obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, estado de funcionamento dos pacotes de serviço com o valor HealthState OK (2) e (4) de aviso são devolvidos.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| --events-health-state-filter | Permite a filtragem da coleção de objetos de HealthEvent devolvidos com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregado. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores, obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, todos os eventos com o valor HealthState OK (2) e (4) de aviso são devolvidos.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| --exclude-health-statistics | Indica se as estatísticas de estado de funcionamento devem ser devolvidas como parte do resultado da consulta. FALSE por padrão. As estatísticas mostram o número de subordinados entidades no estado de funcionamento Ok, aviso e erro. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-deployed-list"></a>o sfctl implementado-lista de aplicativos
Obtém a lista de aplicativos implantados num nó do Service Fabric.

Obtém a lista de aplicativos implantados num nó do Service Fabric. Os resultados não incluem informações sobre os aplicativos de sistema implementado, a menos que explicitamente consultado relativamente ao ID. Resultados abrangem aplicações implementadas no Active Directory, ativação e a transferência de Estados. Esta consulta requer que o nome do nó corresponde a um nó no cluster. A consulta falha se o nome do nó fornecido não aponta para quaisquer nós de recursos de infraestrutura do serviço do Active Directory no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário] | O nome do nó. |
| --continuation-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API, quando os resultados do sistema não se encaixam numa única resposta. Quando esse valor é passado para a próxima chamada de API, a API devolve o próximo conjunto de resultados. Se não existirem mais resultados, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificada com URL. |
| --include-health-state | Inclua o estado de funcionamento de uma entidade. Se este parâmetro for false ou não foi especificado, o estado de funcionamento devolvido é "Desconhecido". Quando definido como true, a consulta vai em paralelo para o nó e o serviço de sistema de estado de funcionamento antes dos resultados são mesclados. Como resultado, a consulta é mais cara e pode demorar mais tempo. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos pode ser menor do que os resultados máximos especificados se eles não se encaixam na mensagem de acordo com as restrições de tamanho de mensagem máximo definido na configuração. Se este parâmetro for igual a zero ou não especificado, a consulta paginada inclui resultados tantos possível que se enquadram na mensagem de retorna. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-health"></a>Estado de funcionamento de aplicação de sfctl
Obtém o estado de funcionamento da aplicação do service fabric.

Devolve o estado de integridade da aplicação do service fabric. A resposta relatórios de estado de funcionamento Ok, erro ou aviso. Se a entidade não foi encontrada no arquivo de estado de funcionamento, retornará o erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| --deployed-applications-health-state-filter | Permite a filtragem dos objetos de estado de funcionamento de aplicações implementadas devolvido no resultado da consulta de estado de funcionamento do aplicativo com base no respetivo estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento. Apenas as aplicações implementadas que correspondem ao filtro vão ser devolvidas. Todos os aplicativos implantados são utilizados para avaliar o estado de funcionamento agregado. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores, obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, estado de funcionamento de aplicações implementadas com o valor HealthState OK (2) e (4) de aviso são devolvidos.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| --events-health-state-filter | Permite a filtragem da coleção de objetos de HealthEvent devolvidos com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregado. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores, obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, todos os eventos com o valor HealthState OK (2) e (4) de aviso são devolvidos.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| --exclude-health-statistics | Indica se as estatísticas de estado de funcionamento devem ser devolvidas como parte do resultado da consulta. FALSE por padrão. As estatísticas mostram o número de subordinados entidades no estado de funcionamento Ok, aviso e erro. |
| --services-health-state-filter | Permite a filtragem de objetos de estado de funcionamento dos serviços de devolvido no resultado da consulta de estado de funcionamento dos serviços com base no respetivo estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento. Apenas os serviços que correspondem ao filtro são devolvidos. Todos os serviços são utilizados para avaliar o estado de funcionamento agregado. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores, obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, estado de funcionamento de serviços com o valor HealthState OK (2) e de aviso (4) vai ser devolvido.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-info"></a>informação sobre a aplicação sfctl
Obtém informações sobre uma aplicação do Service Fabric.

Devolve as informações sobre a aplicação que foi criada ou no processo a ser criada no cluster do Service Fabric e cujo nome corresponde ao foi especificado como parâmetro. A resposta inclui o nome, tipo, estado, parâmetros e outros detalhes sobre a aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| – parâmetros da aplicação de exclusão | O sinalizador que especifica se os parâmetros de aplicação serão excluídos do resultado. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-list"></a>lista de aplicativos de sfctl
Obtém a lista de aplicativos criados no cluster do Service Fabric que correspondem aos filtros especificados.

Obtém as informações sobre as aplicações que foram criadas ou no processo a ser criada em recursos de infraestrutura do serviço de cluster e corresponder os filtros especificados. A resposta inclui o nome, tipo, estado, parâmetros e outros detalhes sobre a aplicação. Se os aplicativos não se encaixam numa página, uma página de resultados é retornada, bem como um token de continuação, que pode ser utilizado para obter a página seguinte. Filtros ApplicationTypeName e ApplicationDefinitionKindFilter não podem ser especificados ao mesmo tempo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-definition-kind-filter | Utilizado para filtrar em ApplicationDefinitionKind, que é o mecanismo usado para definir uma aplicação do Service Fabric.  <br> -Predefinição - o valor predefinido, que executa a mesma função que selecionar "Todos". O valor for 0.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor ApplicationDefinitionKind. O valor é de 65535.  <br> -ServiceFabricApplicationDescription - filtro que corresponda a entrada com o valor de ApplicationDefinitionKind ServiceFabricApplicationDescription. O valor é 1.  <br> Filtro de - Compose - que corresponde à entrada com o valor de ApplicationDefinitionKind Compose. O valor é 2. |
| --application-type-name | O nome do tipo de aplicativo utilizado para filtrar os aplicativos a consultar. Este valor não deve conter a versão do tipo de aplicação. |
| --continuation-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API, quando os resultados do sistema não se encaixam numa única resposta. Quando esse valor é passado para a próxima chamada de API, a API devolve o próximo conjunto de resultados. Se não existirem mais resultados, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificada com URL. |
| – parâmetros da aplicação de exclusão | O sinalizador que especifica se os parâmetros de aplicação serão excluídos do resultado. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos pode ser menor do que os resultados máximos especificados se eles não se encaixam na mensagem de acordo com as restrições de tamanho de mensagem máximo definido na configuração. Se este parâmetro for igual a zero ou não especificado, a consulta paginada inclui resultados tantos possível que se enquadram na mensagem de retorna. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-load"></a>o sfctl carga da aplicação
Obtém informações sobre uma aplicação do Service Fabric de carga.

Devolve as carregar as informações sobre a aplicação que foi criada ou no processo a ser criada no cluster do Service Fabric e cujo nome corresponde ao foi especificado como parâmetro. A resposta inclui o nome, número mínimo de nós, o número máximo de nós, o número de nós que o aplicativo ocupa atualmente e informações de métrica de carga de aplicação sobre a aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-manifest"></a>manifesto do aplicativo sfctl
Obtém o manifesto que descreve um tipo de aplicação.

A resposta contém o XML do manifesto do aplicativo, como uma cadeia de caracteres.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome de tipo de aplicação [necessário] | O nome do tipo de aplicação. |
| – versão de tipo de aplicação [necessário] | A versão do tipo de aplicação. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-provision"></a>Aprovisionamento de aplicação de sfctl
Provisiona ou registros de tipo de aplicação do Service Fabric com o cluster utilizando o pacote. sfpkg no arquivo externo ou utilizando o pacote de aplicação no arquivo de imagem.

Aprovisiona um tipo de aplicação do Service Fabric com o cluster. Isto é necessário antes de quaisquer novas aplicações podem ser instanciadas. A operação de aprovisionamento pode ser executada no pacote de aplicação especificada pelo relativePathInImageStore ou com o URI da. sfpkg externo. A menos que – aprovisionar externo é definido, este comando irá esperar aprovisionar do arquivo de imagem.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-package-download-uri | O caminho para o pacote de aplicação ". sfpkg" de onde o pacote de aplicação pode ser transferido através de protocolos HTTP ou HTTPS. <br><br> Para o tipo de aprovisionamento externo armazene apenas. O pacote de aplicação pode ser armazenado num repositório externo que fornece a operação de obtenção de para transferir o ficheiro. Protocolos suportados são HTTP e HTTPS, e o caminho tem de permitir o acesso de leitura. |
| --application-type-build-path | Para aprovisionar tipo armazenamento de imagens apenas. O caminho relativo para o pacote de aplicação no arquivo de imagem especificado durante a operação de carregamento anteriores. |
| --application-type-name | Para o tipo de aprovisionamento externo armazene apenas. O nome do tipo de aplicativo representa o nome do tipo de aplicação encontrado no manifesto do aplicativo. |
| --application-type-version | Para o tipo de aprovisionamento externo armazene apenas. A versão do tipo de aplicativo representa a versão do tipo de aplicação encontrada no manifesto do aplicativo. |
| --external-provision | A localização de onde o pacote de aplicação pode ser registado ou aprovisionado. Indica que o aprovisionamento destina-se um pacote de aplicação que foi carregado anteriormente para um armazenamento externo. O pacote de aplicação termina com a extensão *.sfpkg. |
| – não-wait | Indica se é ou não aprovisionamento deve ocorrer de forma assíncrona. <br><br> Quando definida como true, a operação de aprovisionamento devolve quando o pedido foi aceite pelo sistema e a operação de aprovisionamento continua sem qualquer limite de tempo limite. O valor predefinido é false. Para pacotes de aplicações grande, recomendamos a definição do valor como true. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-report-health"></a>o sfctl aplicação relatório de estado de funcionamento
Envia um relatório de estado de funcionamento sobre a aplicação do Service Fabric.

Relatórios de estado de funcionamento da aplicação especificada do Service Fabric. O relatório tem de conter as informações sobre a origem do relatório de estado de funcionamento e em que é comunicado de propriedade. O relatório é enviado para um aplicativo, que encaminha para o arquivo de estado de funcionamento de gateway do Service Fabric. O relatório pode ser aceites pelo gateway, mas rejeitado pelo arquivo de estado de funcionamento após a validação extra. Por exemplo, o arquivo de estado de funcionamento poderá rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoletos. Para ver se o relatório foi aplicado no arquivo de estado de funcionamento, obter o estado de funcionamento do aplicativo e verificar que o relatório é apresentado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. <br><br> Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o '\~"caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:/myapp/app1 ', a identidade da aplicação seria" myapp\~app1' no 6.0 + e "myapp/app1" nas versões anteriores. |
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

## <a name="sfctl-application-type"></a>tipo de aplicação de sfctl
Obtém a lista de tipos de aplicação no cluster do Service Fabric que corresponde exatamente ao nome especificado.

Devolve as informações sobre os tipos de aplicações aprovisionadas ou no processo a ser aprovisionada no cluster do Service Fabric. Esses resultados são tipos de aplicativos cujo nome corresponde exatamente aquele especificado como o parâmetro e que estão em conformidade com os parâmetros de consulta especificada. Todas as versões do tipo de aplicação que corresponde ao nome do tipo de aplicação são devolvidas, com cada versão devolvida como um tipo de aplicação. A resposta inclui o nome, versão, estado e outros detalhes sobre o tipo de aplicação. Esta é uma consulta paginada, o que significa que, se não todos os tipos de aplicação se encaixam numa página, é devolvida uma página de resultados, bem como um token de continuação, que pode ser utilizado para obter a página seguinte. Por exemplo, se há 10 tipos de aplicativos, mas uma página só se encaixa os primeiros tipos de aplicação de três, ou se os resultados máximos for definido como 3, em seguida, três é devolvido. Para acessar o resto dos resultados, obter as páginas subsequentes, com o token de continuação devolvido na consulta seguinte. Um token de continuação vazio é devolvido se não houver nenhuma das páginas subsequente.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome de tipo de aplicação [necessário] | O nome do tipo de aplicação. |
| --application-type-version | A versão do tipo de aplicação. |
| --continuation-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API, quando os resultados do sistema não se encaixam numa única resposta. Quando esse valor é passado para a próxima chamada de API, a API devolve o próximo conjunto de resultados. Se não existirem mais resultados, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificada com URL. |
| – parâmetros da aplicação de exclusão | O sinalizador que especifica se os parâmetros de aplicação serão excluídos do resultado. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos pode ser menor do que os resultados máximos especificados se eles não se encaixam na mensagem de acordo com as restrições de tamanho de mensagem máximo definido na configuração. Se este parâmetro for igual a zero ou não especificado, a consulta paginada inclui resultados tantos possível que se enquadram na mensagem de retorna. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-type-list"></a>lista de tipo do aplicativo sfctl
Obtém a lista de tipos de aplicação no cluster do Service Fabric.

Devolve as informações sobre os tipos de aplicações aprovisionadas ou no processo a ser aprovisionada no cluster do Service Fabric. Cada versão de um tipo de aplicação é devolvido como tipo de um aplicativo. A resposta inclui o nome, versão, estado e outros detalhes sobre o tipo de aplicação. Esta é uma consulta paginada, o que significa que, se não todos os tipos de aplicação se encaixam numa página, é devolvida uma página de resultados, bem como um token de continuação, que pode ser utilizado para obter a página seguinte. Por exemplo, se há 10 tipos de aplicativos, mas uma página só se encaixa os primeiros tipos de aplicação de três, ou se os resultados máximos for definido como 3, em seguida, três é devolvido. Para acessar o resto dos resultados, obter as páginas subsequentes, com o token de continuação devolvido na consulta seguinte. Um token de continuação vazio é devolvido se não houver nenhuma das páginas subsequente.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-type-definition-kind-filter | Utilizado para filtrar em ApplicationTypeDefinitionKind que é o mecanismo usado para definir um tipo de aplicação do Service Fabric.  <br> -Predefinição - o valor predefinido, que executa a mesma função que selecionar "Todos". O valor for 0.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor ApplicationTypeDefinitionKind. O valor é de 65535.  <br> -ServiceFabricApplicationPackage - filtro que corresponda a entrada com o valor de ApplicationTypeDefinitionKind ServiceFabricApplicationPackage. O valor é 1.  <br> Filtro de - Compose - que corresponde à entrada com o valor de ApplicationTypeDefinitionKind Compose. O valor é 2. |
| --continuation-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API, quando os resultados do sistema não se encaixam numa única resposta. Quando esse valor é passado para a próxima chamada de API, a API devolve o próximo conjunto de resultados. Se não existirem mais resultados, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificada com URL. |
| – parâmetros da aplicação de exclusão | O sinalizador que especifica se os parâmetros de aplicação serão excluídos do resultado. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos pode ser menor do que os resultados máximos especificados se eles não se encaixam na mensagem de acordo com as restrições de tamanho de mensagem máximo definido na configuração. Se este parâmetro for igual a zero ou não especificado, a consulta paginada inclui resultados tantos possível que se enquadram na mensagem de retorna. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-unprovision"></a>não aprovisionamento de aplicação de sfctl
Remove ou anula o registo de um tipo de aplicação do Service Fabric do cluster.

Esta operação só pode ser efetuada se todas as instâncias da aplicação do tipo de aplicação tiverem sido eliminadas. Assim que o tipo de aplicação não está registado, não existem novas instâncias da aplicação podem ser criadas para este tipo de aplicativo em particular.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome de tipo de aplicação [necessário] | O nome do tipo de aplicação. |
| – versão de tipo de aplicação [necessário] | A versão do tipo de aplicação conforme definido no manifesto do aplicativo. |
| --async-parameter | O sinalizador que indica se é ou não não aprovisionamento deve ocorrer no modo assíncrono. Quando definida como true, a operação de não aprovisionamento devolve quando o pedido foi aceite pelo sistema e a operação de não aprovisionamento continua sem qualquer limite de tempo limite. O valor predefinido é false. No entanto, é recomendável defini-la como true para pacotes de aplicações grandes que tenham sido aprovisionados. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-upgrade"></a>atualização da aplicação sfctl
Começa a atualizar uma aplicação no cluster do Service Fabric.

Valida os parâmetros de atualização de aplicativos fornecidos e começa a atualizar a aplicação se os parâmetros são válidos. Tenha em atenção que a descrição de atualização substitui a descrição da aplicação existente. Isso significa que, se não forem especificados os parâmetros, os parâmetros existentes nas aplicações serão substituídos com a lista de parâmetros vazios. Isso poderia resultar na aplicação com o valor predefinido dos parâmetros do manifesto do aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. <br><br> Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| – a versão de aplicação [necessária] | O destino tipo versão da aplicação (encontrado no manifesto do aplicativo) para a atualização da aplicação. |
| – parâmetros [necessários] | Uma lista JSON codificado do parâmetro de aplicação substitui a ser aplicado ao atualizar a aplicação. |
| --default-service-health-policy | JSON codificado especificação da política de estado de funcionamento usada por padrão para avaliar o estado de funcionamento de um tipo de serviço. |
| --failure-action | A ação a executar quando uma atualização de monitorizada encontra monitorização violações de políticas de política ou o estado de funcionamento. |
| --force-restart | Reinicie forçadamente processos durante a atualização, mesmo quando a versão de código não foi alterada. |
| --health-check-retry-timeout | O período de tempo entre tentativas para realizar verificações de estado de funcionamento se o aplicativo ou o cluster não está em bom estado.  Default\: PT0H10M0S. |
| --health-check-stable-duration | A quantidade de tempo que a aplicação ou o cluster deve permanecer em bom estado antes que a atualização prossiga para o domínio de atualização seguinte.  Default\: PT0H2M0S. <br><br> Em primeiro lugar será interpretado como uma cadeia que representa uma duração ISO 8601. Se isso falhar, ela é interpretada como um número que representa o número total de milissegundos. |
| --health-check-wait-duration | O período de tempo de espera após a conclusão de um domínio de atualização antes de iniciar o estado de funcionamento verifica o processo.  Predefinido\: 0. |
| --max-unhealthy-apps | O máximo permitido de percentagem de mau estado de funcionamento aplicações implementadas. Representada como um número entre 0 e 100. |
| --mode | O modo utilizado para monitorizar o estado de funcionamento durante uma atualização sem interrupção.  Predefinido\: UnmonitoredAuto. |
| --replica-set-check-timeout | A quantidade máxima de tempo para bloquear o processamento de um domínio de atualização e evitar a perda de disponibilidade quando surgem problemas inesperados. Medido em segundos. |
| --service-health-policy | JSON codificado mapa com a política de estado de funcionamento do tipo de serviço por nome do tipo de serviço. O mapa está vazio ser predefinido. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |
| --upgrade-domain-timeout | A quantidade de tempo de cada domínio de atualização tem de concluir antes de ser executada FailureAction.  Default\: P10675199DT02H48M05.4775807S. <br><br> Em primeiro lugar será interpretado como uma cadeia que representa uma duração ISO 8601. Se isso falhar, ela é interpretada como um número que representa o número total de milissegundos. |
| --upgrade-timeout | A quantidade de tempo a atualização global tem de concluir antes de ser executada FailureAction.  Default\: P10675199DT02H48M05.4775807S. <br><br> Em primeiro lugar será interpretado como uma cadeia que representa uma duração ISO 8601. Se isso falhar, ela é interpretada como um número que representa o número total de milissegundos. |
| --warning-as-error | Indica se os avisos são tratados com a mesma gravidade como erros. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-upgrade-resume"></a>atualização de aplicativo de sfctl-retomar
Retoma a atualizar uma aplicação no cluster do Service Fabric.

Retoma atualizar de uma aplicação do Service Fabric manual não monitorizada. Service Fabric atualiza um domínio de atualização de cada vez. Para atualizações do manuais não monitorizadas, após a conclusão de um domínio de atualização do Service Fabric aguarda chamar esta API antes de prosseguir para o domínio de atualização seguinte.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| -nome de domínio de atualização [necessário] | O nome do domínio de atualização na qual é possível retomar a atualização. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-upgrade-rollback"></a>reversão de atualização do aplicativo sfctl
Inicia a reverter a atualização de atualmente em curso de uma aplicação no cluster do Service Fabric.

Começa a reverter a aplicação atual atualizar para a versão anterior. Esta API só pode ser usada para reverter a atualização em curso atual que está a ser reencaminhar para a nova versão. Se o aplicativo atualmente não está a ser atualizado StartApplicationUpgrade API utilizada para atualizá-lo para a versão pretendida, incluindo a reversão para uma versão anterior.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-upgrade-status"></a>o sfctl atualização-estado da aplicação
Obtém os detalhes para a atualização mais recente efetuada nesta aplicação.

Devolve informações sobre o estado da atualização mais recente da aplicação, juntamente com detalhes para ajudar a depuração problemas de estado de funcionamento da aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-application-upload"></a>carregamento da aplicação de sfctl
Copie um pacote de aplicação do Service Fabric para o armazenamento de imagens.

Opcionalmente, exiba o progresso do carregamento para cada ficheiro no pacote. Carregar progresso é enviado para `stderr`.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – o caminho [necessário] | Caminho para o pacote de aplicação de local. |
| --imagestore-string | Armazenar a imagem de destino para carregar o pacote de aplicação para.  Predefinido\: fabric\:ImageStore. |
| --show-progress | Mostre o progresso do carregamento de ficheiro para pacotes grandes. |

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

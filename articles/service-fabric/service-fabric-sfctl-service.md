---
title: Serviço do Azure Service Fabric CLI sfctl | Documentos da Microsoft
description: Descreve os comandos de serviço do Service Fabric CLI sfctl.
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
ms.openlocfilehash: e0454d0124efba04434884fbac9056c5e324710d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670885"
---
# <a name="sfctl-service"></a>sfctl service
Criar, eliminar e gerir o serviço, os tipos de serviço e pacotes de serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| app-name | Obtém o nome da aplicação do Service Fabric para um serviço. |
| code-package-list | Obtém a lista de pacotes de código implementado num nó do Service Fabric. |
| criar | Cria o serviço do Service Fabric especificado. |
| delete | Elimina um serviço do Service Fabric existente. |
| tipo de implementação | Obtém as informações sobre um tipo de serviço especificado da aplicação implementada num nó no cluster do Service Fabric. |
| deployed-type-list | Obtém a lista que contém as informações sobre os tipos de serviço a partir dos aplicativos implantados num nó no cluster do Service Fabric. |
| descrição | Obtém a descrição de um serviço do Service Fabric existente. |
| get-container-logs | Obtém os registos de contentor para contentor implementado num nó do Service Fabric. |
| saúde | Obtém o estado de funcionamento do serviço especificado do Service Fabric. |
| informações | Obtém as informações sobre o serviço específico a que pertencem à aplicação do Service Fabric. |
| list | Obtém as informações sobre todos os serviços que pertencem à aplicação especificada pelo ID de aplicação. |
| Manifesto | Obtém o manifesto que descreve um tipo de serviço. |
| package-deploy | Transfere os pacotes associados à manifesto de serviço especificado para a cache de imagem no nó especificado. |
| package-health | Obtém as informações sobre o estado de funcionamento de um pacote de serviço para uma aplicação específica implementada para um nó do Service Fabric e a aplicação. |
| package-info | Obtém a lista de pacotes de serviços implementados num nó do Service Fabric que corresponde exatamente ao nome especificado. |
| package-list | Obtém a lista de pacotes de serviços implementados num nó do Service Fabric. |
| recuperar | Indica ao cluster do Service Fabric que ele deve tentar recuperar o serviço especificado que está atualmente parado em perda de quórum. |
| report-health | Envia um relatório de estado de funcionamento do serviço do Service Fabric. |
| resolver | Resolva uma partição do Service Fabric. |
| type-list | Obtém a lista que contém as informações sobre os tipos de serviço que são suportados por um tipo de aplicação aprovisionada no cluster do Service Fabric. |
| update | Atualizações de serviço especificado usando a descrição de determinada atualização. |

## <a name="sfctl-service-app-name"></a>nome de aplicação do serviço sfctl
Obtém o nome da aplicação do Service Fabric para um serviço.

Obtém o nome da aplicação para o serviço especificado. Um erro FABRIC_E_SERVICE_DOES_NOT_EXIST 404 é devolvido se não existir um serviço com o ID de serviço fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-code-package-list"></a>o sfctl serviço código--lista de pacotes
Obtém a lista de pacotes de código implementado num nó do Service Fabric.

Obtém a lista de pacotes de código implementado num nó do Service Fabric para o determinado aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| --nome do nó [necessário] | O nome do nó. |
| --code-package-name | O nome do pacote de código especificado no manifesto de serviço registado como parte de um tipo de aplicação no cluster do Service Fabric. |
| --service-manifest-name | O nome de um manifesto de serviço registado como parte de um tipo de aplicação no cluster do Service Fabric. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-create"></a>criar serviço de sfctl
Cria o serviço do Service Fabric especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| [necessário] - aplicação-id | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o '\~"caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:/myapp/app1 ', a identidade da aplicação seria" myapp\~app1' no 6.0 + e "myapp/app1" nas versões anteriores. |
| – o nome [necessário] | Nome do serviço. Deve ser um filho do id da aplicação. Este é o nome incluindo o `fabric\:` URI. Por exemplo de serviço `fabric\:/A/B` é um filho do aplicativo `fabric\:/A`. |
| – [necessário] de tipo de serviço | Nome do tipo de serviço. |
| --activation-mode | O modo de ativação para o pacote de serviço. |
| – restrições | As restrições de posicionamento, como uma cadeia de caracteres. Restrições de posicionamento são expressões booleanas nas propriedades de nó e permitem restringir um serviço para nós determinado com base nos requisitos de serviço. Por exemplo, para colocar um serviço em nós em que é azul NodeType especifique o seguinte\:"NodeColor = = blue". |
| --correlated-service | Nome do serviço de destino para correlacionar com. |
| --correlation | Correlacione o serviço com um serviço existente utilizando uma afinidade de alinhamento. |
| --dns-name | O nome DNS do serviço a ser criada. O serviço de sistema do Service Fabric DNS tem de ser ativado para esta definição. |
| --instance-count | A contagem de instâncias. Isso se aplica apenas a serviços sem estado. |
| --int-scheme | Indica que o serviço deve ser particionado uniformemente numa variedade de números inteiros não assinados. |
| --int-scheme-count | O número de partições dentro do intervalo da chave de número inteiro para criar, se utilizar um esquema de partição de número inteiro uniforme. |
| --int-scheme-high | O fim do intervalo de número inteiro de chave, se utilizar um esquema de partição de número inteiro uniforme. |
| --int-scheme-low | O início do intervalo de número inteiro de chave, se utilizar um esquema de partição de número inteiro uniforme. |
| – as métricas de carregamento | JSON com a codificação de lista de métricas utilizadas quando o balanceamento de carga services em todos os nós. |
| --min-replica-set-size | A réplica mínima definir tamanho como um número. Isso se aplica apenas a serviços com estado. |
| --move-cost | Especifica o custo de mudança para o serviço. Os valores possíveis são\: 'Zero', "Baixo", "Médio", "Alto". |
| --named-scheme | Indica que o serviço deve ter várias partições com nome. |
| --named-scheme-list | JSON codificado lista de nomes para particionar o serviço, se utilizar o esquema de partição com nome. |
| --no-persisted-state | Se for true, isso indica que o serviço não tem nenhum estado persistente armazenado no disco local ou Estado que só armazena na memória. |
| --placement-policy-list | Lista de políticas de colocação para o serviço de codificado em JSON e quaisquer associados nomes de domínio. As políticas podem ser um ou mais dos\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | A duração máxima, em segundos, para que uma partição pode estar num Estado de perda de quórum. Isso se aplica apenas a serviços com estado. |
| --replica-restart-wait | A duração, em segundos, entre quando uma réplica se tornar inativo e quando é criada uma nova réplica. Isso se aplica apenas a serviços com estado. |
| – as políticas de dimensionamento | JSON com a codificação de lista de políticas para este serviço de dimensionamento. |
| --singleton-scheme | Indica o serviço deve ter uma única partição ou um serviço não particionada. |
| --stand-by-replica-keep | A duração máxima, em segundos, para o modo de espera réplicas serão mantidas antes de serem removidos. Isso se aplica apenas a serviços com estado. |
| --stateful | Indica que o serviço é um serviço com estado. |
| --stateless | Indica que o serviço é um serviço sem estado. |
| --target-replica-set-size | A réplica de destino definir tamanho como um número. Isso se aplica apenas a serviços com estado. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-delete"></a>eliminação do serviço de sfctl
Elimina um serviço do Service Fabric existente.

Um serviço tem de ser criado antes de poder ser eliminado. Por predefinição, o Service Fabric tentará fechar as réplicas do serviço de forma amigável e, em seguida, eliminar o serviço. No entanto, se o serviço está a ter problemas corretamente a fechar a réplica, a operação de eliminação pode demorar muito tempo ou ficar preso. Utilize o sinalizador de ForceRemove opcional para ignorar a sequência de fechar anulações normal e forçadamente eliminar o serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
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

## <a name="sfctl-service-deployed-type"></a>serviço de sfctl tipo implementado
Obtém as informações sobre um tipo de serviço especificado da aplicação implementada num nó no cluster do Service Fabric.

Obtém a lista que contém as informações sobre um tipo de serviço específico a partir dos aplicativos implantados num nó no cluster do Service Fabric. A resposta inclui o nome do tipo de serviço, o estado de registo, o pacote de código registado-lo e a ativação do ID do pacote de serviço. Cada entrada representa uma única ativação de um tipo de serviço, diferenciado pelo ID de ativação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| --nome do nó [necessário] | O nome do nó. |
| -nome de tipo de serviço [necessário] | Especifica o nome de um tipo de serviço do Service Fabric. |
| --service-manifest-name | O nome do manifesto do serviço para filtrar a lista de informações de tipo de serviço implementado. Se for especificado, a resposta irá conter apenas as informações sobre os tipos de serviço que estão definidos nesse manifesto de serviço. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-deployed-type-list"></a>o sfctl serviço implementado-tipo-lista
Obtém a lista que contém as informações sobre os tipos de serviço a partir dos aplicativos implantados num nó no cluster do Service Fabric.

Obtém a lista que contém as informações sobre os tipos de serviço a partir dos aplicativos implantados num nó no cluster do Service Fabric. A resposta inclui o nome do tipo de serviço, o estado de registo, o pacote de código registado-lo e a ativação do ID do pacote de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| --nome do nó [necessário] | O nome do nó. |
| --service-manifest-name | O nome do manifesto do serviço para filtrar a lista de informações de tipo de serviço implementado. Se for especificado, a resposta irá conter apenas as informações sobre os tipos de serviço que estão definidos nesse manifesto de serviço. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-description"></a>Descrição do serviço de sfctl
Obtém a descrição de um serviço do Service Fabric existente.

Obtém a descrição de um serviço do Service Fabric existente. Um serviço tem de ser criado antes de sua descrição pode ser obtida.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-get-container-logs"></a>o sfctl get-container-registos do serviço
Obtém os registos de contentor para contentor implementado num nó do Service Fabric.

Obtém os registos de contentor para contentor implementado num nó do Service Fabric para o pacote de código específico.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| -nome de pacote de código [necessário] | O nome do pacote de código especificado no manifesto de serviço registado como parte de um tipo de aplicação no cluster do Service Fabric. |
| --nome do nó [necessário] | O nome do nó. |
| -nome de manifesto de serviço [necessário] | O nome de um manifesto de serviço registado como parte de um tipo de aplicação no cluster do Service Fabric. |
| – anterior | Especifica se pretende obter registos de contentor do contentores/dead. exe saiu da instância de pacote do código. |
| – Cauda | Número de linhas para mostrar a partir do final dos registos. A predefinição é 100. 'tudo' para mostrar os registos completos. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-health"></a>Estado de funcionamento de serviço de sfctl
Obtém o estado de funcionamento do serviço especificado do Service Fabric.

Obtém as informações de estado de funcionamento do serviço especificado. Utilize EventsHealthStateFilter para filtrar a recolha de eventos de estado de funcionamento comunicadas falhas no serviço com base no estado de funcionamento. Utilize PartitionsHealthStateFilter para filtrar a coleção de partições devolvido. Se especificar um serviço que não existe no arquivo de estado de funcionamento, este pedido devolve um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| --events-health-state-filter | Permite a filtragem da coleção de objetos de HealthEvent devolvidos com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregado. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores, obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, todos os eventos com o valor HealthState OK (2) e (4) de aviso são devolvidos.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| --exclude-health-statistics | Indica se as estatísticas de estado de funcionamento devem ser devolvidas como parte do resultado da consulta. FALSE por padrão. As estatísticas mostram o número de subordinados entidades no estado de funcionamento Ok, aviso e erro. |
| --partitions-health-state-filter | Permite a filtragem dos objetos de estado de funcionamento de partições devolvido no resultado da consulta de estado de funcionamento de serviço com base no respetivo estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento. Apenas as partições que correspondem ao filtro são devolvidas. Todas as partições são utilizadas para avaliar o estado de funcionamento agregado. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valor obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, estado de funcionamento de partições com o valor HealthState OK (2) e de aviso (4) vai ser devolvido.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-info"></a>informações de serviço de sfctl
Obtém as informações sobre o serviço específico a que pertencem à aplicação do Service Fabric.

Devolve as informações sobre o serviço especificado que pertencem à aplicação especificada do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-list"></a>lista de serviços de sfctl
Obtém as informações sobre todos os serviços que pertencem à aplicação especificada pelo ID de aplicação.

Devolve as informações sobre todos os serviços que pertencem à aplicação especificada pelo ID de aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| --continuation-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API, quando os resultados do sistema não se encaixam numa única resposta. Quando esse valor é passado para a próxima chamada de API, a API devolve o próximo conjunto de resultados. Se não existirem mais resultados, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificada com URL. |
| --service-type-name | O nome do tipo de serviço utilizado para filtrar os serviços para consultar. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-manifest"></a>manifesto do serviço de sfctl
Obtém o manifesto que descreve um tipo de serviço.

Obtém o manifesto que descreve um tipo de serviço. A resposta contém XML do manifesto do serviço como uma cadeia de caracteres.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome de tipo de aplicação [necessário] | O nome do tipo de aplicação. |
| – versão de tipo de aplicação [necessário] | A versão do tipo de aplicação. |
| --service-manifest-name    [Required] | O nome de um manifesto de serviço registado como parte de um tipo de aplicação no cluster do Service Fabric. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-package-deploy"></a>implementar no serviço de sfctl pacote
Transfere os pacotes associados à manifesto de serviço especificado para a cache de imagem no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| nome de tipo de aplicação – [necessário] | O nome do manifesto do aplicativo para o manifesto do serviço pedido correspondente. |
| versão de tipo de aplicação – [necessário] | A versão do manifesto do aplicativo para o manifesto do serviço pedido correspondente. |
| --nome do nó [necessário] | O nome do nó. |
| -nome de manifesto de serviço [necessário] | O nome do manifesto de serviço associado com os pacotes que serão transferidos. |
| --share-policy | JSON com a codificação de lista de políticas de partilha. Cada elemento de política de partilha é composto por um 'name' e "scope". O nome corresponde ao nome do pacote do código, configuração ou dados que está a ser partilhado. O âmbito pode ser 'None', 'All', 'Code', 'Configuração' ou 'Dados de'. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-package-health"></a>o sfctl serviço pacote-estado de funcionamento
Obtém as informações sobre o estado de funcionamento de um pacote de serviço para uma aplicação específica implementada para um nó do Service Fabric e a aplicação.

Obtém as informações sobre o estado de funcionamento de um pacote de serviço para uma aplicação específica, implementada num nó do Service Fabric. Utilize EventsHealthStateFilter opcionalmente filtrar por coleção de objetos de HealthEvent comunicado no pacote de serviço implementado com base no estado de funcionamento.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| --nome do nó [necessário] | O nome do nó. |
| -nome de pacote de serviço [necessário] | O nome do pacote de serviço. |
| --events-health-state-filter | Permite a filtragem da coleção de objetos de HealthEvent devolvidos com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de estado de funcionamento. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregado. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são baseados no sinalizador de enumeração, para que o valor pode ser uma combinação destes valores, obtido usando o operador de "OR" bit a bit. Por exemplo, se o valor fornecido é de 6, em seguida, todos os eventos com o valor HealthState OK (2) e (4) de aviso são devolvidos.  <br> -Predefinição - o valor predefinido. Corresponde a qualquer HealthState. O valor for igual a zero.  <br> -None - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa dada coleção de Estados. O valor é 1.  <br> -Ok - filtre que correspondências de entrada com o valor de HealthState Ok. O valor é 2.  <br> -Aviso - filtro que a entrada de correspondências com HealthState valor aviso. O valor é 4.  <br> -Erro - filtro que corresponda a entrada com o valor de HealthState erro. O valor é 8.  <br> -Tudo - filtro que corresponda a entrada com qualquer valor HealthState. O valor é de 65535. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-package-info"></a>informações de pacote do serviço sfctl
Obtém a lista de pacotes de serviços implementados num nó do Service Fabric que corresponde exatamente ao nome especificado.

Devolve as informações sobre os pacotes de serviço implementado num nó do Service Fabric para o determinado aplicativo. Esses resultados estão de pacotes de serviço cujo nome corresponde exatamente o nome do pacote de serviço especificados como parâmetro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| --nome do nó [necessário] | O nome do nó. |
| -nome de pacote de serviço [necessário] | O nome do pacote de serviço. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-package-list"></a>lista de pacotes do serviço sfctl
Obtém a lista de pacotes de serviços implementados num nó do Service Fabric.

Devolve as informações sobre os pacotes de serviço implementado num nó do Service Fabric para o determinado aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de aplicação [necessário] | A identidade da aplicação. Isso normalmente é o nome completo do aplicativo sem que o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome da aplicação é "recursos de infraestrutura\:app1/myapp /", a identidade da aplicação seria "myapp\~app1" em 6.0 + e "myapp/app1" nas versões anteriores. |
| --nome do nó [necessário] | O nome do nó. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-recover"></a>recuperação do serviço de sfctl
Indica ao cluster do Service Fabric que ele deve tentar recuperar o serviço especificado que está atualmente parado em perda de quórum.

Indica ao cluster do Service Fabric que ele deve tentar recuperar o serviço especificado que está atualmente parado em perda de quórum. Esta operação só deve ser efetuada se se sabe que não não possível recuperar as réplicas que estão indisponíveis. Utilização incorreta desta API pode causar a potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-report-health"></a>o sfctl serviço relatório de estado de funcionamento
Envia um relatório de estado de funcionamento do serviço do Service Fabric.

Relatórios de estado de funcionamento do serviço especificado do Service Fabric. O relatório tem de conter as informações sobre a origem do relatório de estado de funcionamento e em que é comunicado de propriedade. O relatório é enviado para um serviço, que encaminha para o arquivo de estado de funcionamento do gateway do Service Fabric. O relatório pode ser aceites pelo gateway, mas rejeitado pelo arquivo de estado de funcionamento após a validação extra. Por exemplo, o arquivo de estado de funcionamento poderá rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoletos. Para ver se o relatório foi aplicado no arquivo de estado de funcionamento, verifique que o relatório é apresentado nos eventos de estado de funcionamento do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – [necessária] de propriedade de estado de funcionamento | A propriedade das informações de estado de funcionamento. <br><br> Uma entidade pode ter relatórios de estado de funcionamento para diferentes propriedades. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir que a flexibilidade de gerador de relatórios categorizar a condição de estado que aciona o relatório. Por exemplo, um gerador de relatórios com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que ele pode informar a propriedade de "AvailableDisk" nesse nó. O mesmo gerador de relatórios pode monitorizar a conectividade de nó, para que ele pode reportar uma propriedade "Conectividade" no mesmo nó. No arquivo de estado de funcionamento, esses relatórios são tratados como eventos de estado de funcionamento separado para o nó especificado. Em conjunto com o SourceId, a propriedade identifica exclusivamente as informações de estado de funcionamento. |
| -Estado de funcionamento [necessário] | Os valores possíveis incluem\: "Inválido", "Ok", "Aviso", "Error", "Desconhecido". |
| – id de serviço [necessário] | A identidade do serviço. <br><br> Isso normalmente é o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o '\~"caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1', a identidade de serviço seria" myapp\~app1\~svc1' no 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
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

## <a name="sfctl-service-resolve"></a>o sfctl service resolve
Resolva uma partição do Service Fabric.

Resolva uma partição de serviço do Service Fabric para obter os pontos de extremidade as réplicas do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário] | A identidade do serviço. Este ID é normalmente o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| --partition-key-type | Tipo de chave para a partição. Este parâmetro é necessário se o esquema de partição para o serviço é Int64Range ou nomeado. Seguem-se os valores possíveis. -None (1) - indica que o parâmetro de PartitionKeyValue não for especificado. Isso é válido para as partições com a criação de partições de esquema como Singleton. Este é o valor predefinido. O valor é 1. -Int64Range (2) - indica que o parâmetro de PartitionKeyValue é uma chave de partição de int64. Isso é válido para as partições com esquema como Int64Range de particionamento. O valor é 2. -Com o nome (3) - indica que o parâmetro de PartitionKeyValue é um nome da partição. Isso é válido para as partições com a criação de partições de esquema como nomeado. O valor é 3. |
| --partition-key-value | Chave de partição. Isto é necessário se o esquema de partição para o serviço é Int64Range ou nomeado. Não é o ID de partição, mas em vez disso, o número inteiro chave valor ou o nome do ID de partição. Por exemplo, se o seu serviço está a utilizar partições ranged de 0 a 10, em seguida, eles PartitionKeyValue seria um número inteiro nesse intervalo. Descrição do serviço para ver o nome ou um intervalo de consulta. |
| --previous-rsp-version | O valor no campo da versão de resposta que recebeu anteriormente. Isto é necessário se o usuário souber que o resultado que foi obtido anteriormente está obsoleto. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-service-type-list"></a>lista de tipo do serviço sfctl
Obtém a lista que contém as informações sobre os tipos de serviço que são suportados por um tipo de aplicação aprovisionada no cluster do Service Fabric.

Obtém a lista que contém as informações sobre os tipos de serviço que são suportados por um tipo de aplicação aprovisionada no cluster do Service Fabric. O tipo de aplicação fornecido tem de existir. Caso contrário, é devolvido um estado 404.

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

## <a name="sfctl-service-update"></a>atualização do serviço sfctl
Atualizações de serviço especificado usando a descrição de determinada atualização.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário] | A identidade do serviço. Isso normalmente é o nome completo do serviço sem o "recursos de infraestrutura\:" esquema de URI. A partir da versão 6.0, são delimitados nomes hierárquicos com o "\~" caráter. Por exemplo, se o nome do serviço é "recursos de infraestrutura\:/myapp/app1/svc1', a identidade de serviço seria" myapp\~app1\~svc1' no 6.0 + e "myapp/app1/svc1" nas versões anteriores. |
| – restrições | As restrições de posicionamento, como uma cadeia de caracteres. Restrições de posicionamento são expressões booleanas nas propriedades de nó e permitem restringir um serviço para nós determinado com base nos requisitos de serviço. Por exemplo, para colocar um serviço em nós em que é azul NodeType especifique o seguinte\: "NodeColor = = blue". |
| --correlated-service | Nome do serviço de destino para correlacionar com. |
| --correlation | Correlacione o serviço com um serviço existente utilizando uma afinidade de alinhamento. |
| --instance-count | A contagem de instâncias. Isso se aplica apenas a serviços sem estado. |
| – as métricas de carregamento | Lista JSON com codificação de métricas utilizado quando balanceamento de carga em nós. |
| --min-replica-set-size | A réplica mínima definir tamanho como um número. Isso se aplica apenas a serviços com estado. |
| --move-cost | Especifica o custo de mudança para o serviço. Os valores possíveis são\: 'Zero', "Baixo", "Médio", "Alto". |
| --placement-policy-list | Lista de políticas de colocação para o serviço de codificado em JSON e quaisquer associados nomes de domínio. As políticas podem ser um ou mais dos\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | A duração máxima, em segundos, para que uma partição pode estar num Estado de perda de quórum. Isso se aplica apenas a serviços com estado. |
| --replica-restart-wait | A duração, em segundos, entre quando uma réplica se tornar inativo e quando é criada uma nova réplica. Isso se aplica apenas a serviços com estado. |
| – as políticas de dimensionamento | JSON com a codificação de lista de políticas para este serviço de dimensionamento. |
| --stand-by-replica-keep | A duração máxima, em segundos, para o modo de espera réplicas serão mantidas antes de serem removidos. Isso se aplica apenas a serviços com estado. |
| --stateful | Indica que o serviço de destino é um serviço com estado. |
| --stateless | Indica que o serviço de destino é um serviço sem estado. |
| --target-replica-set-size | A réplica de destino definir tamanho como um número. Isso se aplica apenas a serviços com estado. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |


## <a name="next-steps"></a>Passos Seguintes
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como utilizar a CLI do Service Fabric utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).

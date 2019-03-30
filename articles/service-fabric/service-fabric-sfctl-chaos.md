---
title: O Azure Service Fabric CLI - sfctl chaos | Documentos da Microsoft
description: Descreve os comandos do CLI do Service Fabric sfctl caos.
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
ms.openlocfilehash: b584ec301f0f4841c8df8fbbafb410abf645c373
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666754"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Iniciar, parar e gerar relatórios sobre o serviço de teste caos.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Descrição|
| --- | --- |
| [schedule](service-fabric-sfctl-chaos-schedule.md) | Obter e definir a agenda de caos. |
## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| eventos | Obtém o segmento seguinte dos eventos de Chaos com base no token de continuação ou o intervalo de tempo. |
| Get | Obter o estado de confusão. |
| start | Inicia o caos no cluster. |
| Parar | Para o Chaos se ele está em execução no cluster e coloca a agenda de Chaos no estado parado. |

## <a name="sfctl-chaos-events"></a>eventos de chaos sfctl
Obtém o segmento seguinte dos eventos de Chaos com base no token de continuação ou o intervalo de tempo.

Para obter o segmento seguinte dos eventos caos, pode especificar o ContinuationToken. Para obter o início de um novo segmento de eventos de caos, pode especificar o intervalo de tempo por meio de StartTimeUtc e EndTimeUtc. Não é possível especificar o ContinuationToken e o intervalo de tempo na mesma chamada. Quando há mais de 100 eventos de caos, o Chaos eventos são devolvidos em vários segmentos em que um segmento contém mais do que 100 eventos de Chaos e para obter o segmento seguinte fizer uma chamada para esta API com o token de continuação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --continuation-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API, quando os resultados do sistema não se encaixam numa única resposta. Quando esse valor é passado para a próxima chamada de API, a API devolve o próximo conjunto de resultados. Se não existirem mais resultados, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificada com URL. |
| -hora de fim-utc | Hora que representa a hora de fim do intervalo de tempo para o qual vai ser gerado um relatório de Chaos do ficheiro dos Windows. Consultar [DateTime.ToFileTimeUtc método](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) para obter detalhes. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos pode ser menor do que os resultados máximos especificados se eles não se encaixam na mensagem de acordo com as restrições de tamanho de mensagem máximo definido na configuração. Se este parâmetro for igual a zero ou não especificado, a consulta paginada inclui resultados tantos possível que se enquadram na mensagem de retorna. |
| --start-time-utc | Hora que representa a hora de início do intervalo de tempo para o qual vai ser gerado um relatório de Chaos do ficheiro dos Windows. Consultar [DateTime.ToFileTimeUtc método](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) para obter detalhes. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-chaos-get"></a>o sfctl chaos get
Obter o estado de confusão.

Obtenha o estado caótico que indica se é ou não Chaos está em execução, os parâmetros de Chaos utilizados para executar o Chaos e o estado da agenda caos.

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

## <a name="sfctl-chaos-start"></a>início de chaos sfctl
Inicia o caos no cluster.

Se Chaos não está já em execução no cluster, o Chaos ele começa com o com êxito nos parâmetros de caos. Se o Chaos já está em execução quando esta chamada é feita, a chamada falha com o código de erro FABRIC_E_CHAOS_ALREADY_RUNNING.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-type-health-policy-map | JSON codificado lista com aplicativos de mau estado de funcionamento de percentagem máxima para tipos específicos de aplicativos. Cada entrada especifica como uma chave, o nome do tipo de aplicação e como um valor de um número inteiro que representa a percentagem de MaxPercentUnhealthyApplications utilizada para avaliar os aplicativos do tipo de aplicação especificado. <br><br> Define um mapa com aplicativos de mau estado de funcionamento de percentagem máxima para tipos específicos de aplicativos. Cada entrada especifica como chave, o nome do tipo de aplicação e como valor de um número inteiro que representa a percentagem de MaxPercentUnhealthyApplications utilizada para avaliar os aplicativos do tipo de aplicação especificado. O mapa de política de estado de funcionamento de tipo de aplicativo pode ser utilizado durante a avaliação de estado de funcionamento do cluster para descrever os tipos de aplicativos especiais. Os tipos de aplicativos incluídos no mapa são avaliados em relação a percentagem especificada no mapa e não com o MaxPercentUnhealthyApplications global definidos na política de estado de funcionamento do cluster. Os aplicativos dos tipos de aplicativos especificados no mapa não são contados em relação ao conjunto global de aplicativos. Por exemplo, se alguns aplicativos de um tipo são essenciais, o administrador de cluster pode adicionar uma entrada para o mapa para esse tipo de aplicação e atribua-lhe um valor de 0% (ou seja, não tolerar falhas). Todas as outras aplicações podem ser avaliadas com MaxPercentUnhealthyApplications definido como 20% para tolerar algumas falhas fora de milhares de instâncias da aplicação. O mapa de política de estado de funcionamento de tipo de aplicação é utilizado apenas se o manifesto do cluster permite a avaliação de integridade de tipo de aplicação com a entrada de configuração para HealthManager/EnableApplicationTypeHealthEvaluation. |
| --chaos-target-filter | JSON codificado dicionário com duas chaves de tipo de cadeia de caracteres. As duas chaves são NodeTypeInclusionList e ApplicationInclusionList. Os valores para ambas estas chaves são lista de cadeia de caracteres. chaos_target_filter define todos os filtros para destinados Chaos falhas, por exemplo, a falha de apenas determinados tipos de nó ou a falha de apenas determinados aplicativos. <br><br> Se não for utilizado chaos_target_filter, Chaos falhas de todas as entidades de cluster. Se for utilizado chaos_target_filter, o Chaos falhas apenas as entidades que satisfazem a especificação de chaos_target_filter. NodeTypeInclusionList e ApplicationInclusionList permitir que apenas uma semântica de União. Não é possível especificar uma interseção dos NodeTypeInclusionList e ApplicationInclusionList. Por exemplo, não é possível especificar "falhas desta aplicação apenas quando se encontra com esse tipo de nó." Depois de uma entidade está incluída no NodeTypeInclusionList ou ApplicationInclusionList, essa entidade não é possível excluir ChaosTargetFilter. Mesmo se applicationX não aparece na ApplicationInclusionList, em alguns iteração Chaos applicationX pode ser com falhas porque acaba sendo num nó de nodeTypeY que está incluído no NodeTypeInclusionList. Se NodeTypeInclusionList e ApplicationInclusionList estiverem vazios, é emitida uma ArgumentException. Todos os tipos de falhas (reiniciar nó, reinicie o pacote do código, remover a réplica, reiniciar a réplica, mover primário e mover secundário) estão ativadas para os nós de um desses tipos de nó. Se um tipo de nó (Digamos NodeTypeX) não for apresentada a NodeTypeInclusionList, em seguida, falhas de nível de nó (como NodeRestart) nunca serão ativadas para os nós de NodeTypeX, mas as falhas de pacote e de réplica de código ainda podem ser ativadas para NodeTypeX se uma aplicação no ApplicationInclusionList acontece residam num nó de NodeTypeX. No máximo nomes de tipo de nó 100 podem ser incluídos nesta lista, para aumentar este número, uma atualização de configuração é necessária para a configuração de MaxNumberOfNodeTypesInChaosEntityFilter. Todas as réplicas que pertencem aos serviços desses aplicativos são propensos a falhas de réplica (réplica de reinício, remover réplica, movimentação primário e secundário de movimentação) ao caos. Chaos pode reiniciar um pacote de código apenas se o pacote do código aloja réplicas desses aplicativos apenas. Se um aplicativo não aparecer nesta lista, ele pode ainda ser com falhas em alguns iteração Chaos se o aplicativo acaba num nó de um tipo de nó que está incluído no NodeTypeInclusionList. No entanto se applicationX está associada ao nodeTypeY por meio de restrições de posicionamento e applicationX está ausente da ApplicationInclusionList e nodeTypeY está ausente da NodeTypeInclusionList, em seguida, applicationX será nunca possível com falhas. No máximo 1000 nomes de aplicativo podem ser incluídos nesta lista, para aumentar este número, uma atualização de configuração é necessária para a configuração de MaxNumberOfApplicationsInChaosEntityFilter. |
| --context | Pares chave-valor de tipo de mapa JSON com codificação de (cadeia, cadeia de caracteres). O mapa pode ser utilizado para registar informações sobre a execução de caos. Não pode haver mais de 100 esses pares e cada cadeia de caracteres (chave ou valor) pode ter um máximo de 4095 carateres de comprimento. Este mapa é definido pelo starter caótico executar armazenar opcionalmente o contexto sobre a execução específica. |
| --disable-move-replica-faults | Desativa o principal de movimentação e mover falhas secundárias. |
| --max-cluster-stabilization | A quantidade máxima de tempo de espera para todas as entidades para se tornar estável e bom estado de funcionamento do cluster.  Predefinido\: 60. <br><br> Chaos executa em iterações e no início de cada iteração valida o estado de funcionamento de entidades de cluster. Durante a validação de uma entidade de cluster não está estável e bom estado de funcionamento no MaxClusterStabilizationTimeoutInSeconds, Chaos gera um evento de falha de validação. |
| --max-concurrent-faults | O número máximo de falhas em simultâneo induzidas por iteração. Chaos executa em iterações e duas iterações consecutivas são separadas por uma fase de validação. Quanto maior for a simultaneidade, quanto mais agressivo a injeção de falhas – induzindo série mais complexa de Estados para descobrir bugs. A recomendação é começar com um valor de 2 ou 3 e cuidado ao mover a cópia de segurança.  Predefinido\: 1. |
| --max-percent-unhealthy-apps | Quando avaliar o estado de funcionamento do cluster durante caos, o máximo permitido porcentagem de aplicativos de mau estado de funcionamento antes de comunicar um erro. <br><br> O máximo permitido porcentagem de aplicativos de mau estado de funcionamento antes de comunicar um erro. Por exemplo, para permitir que 10% dos aplicativos problemático, este valor seria de 10. A percentagem representa a percentagem máxima de tolerado de aplicativos que podem ser mau estado de funcionamento antes do cluster será considerado como erro. Se a percentagem é respeitada, mas há pelo menos uma aplicação de mau estado de funcionamento, o estado de funcionamento é avaliado como aviso. Isso é calculado dividindo o número de aplicativos de mau estado de funcionamento e o número total de instâncias da aplicação no cluster, excluindo os aplicativos dos tipos de aplicativos que estão incluídos no ApplicationTypeHealthPolicyMap. A computação Arredonda por excesso para tolerar uma falha em pequenos números de aplicativos. Percentagem de padrão é zero. |
| --max-percent-unhealthy-nodes | Quando avaliar o estado de funcionamento do cluster durante caos, o máximo permitido percentagem de nós de mau estado de funcionamento antes de comunicar um erro. <br><br> O máximo permitido de percentagem de nós de mau estado de funcionamento antes de comunicar um erro. Por exemplo, para permitir que 10% de nós para ser mau estado de funcionamento, este valor seria de 10. A percentagem representa a percentagem de tolerado máxima de nós que podem ser mau estado de funcionamento antes do cluster será considerado como erro. Se a percentagem é respeitada, mas não existe, pelo menos, um nó de mau estado de funcionamento, o estado de funcionamento é avaliado como aviso. A percentagem é calculada dividindo o número de nós de mau estado de funcionamento e o número total de nós no cluster. A computação Arredonda por excesso para tolerar uma falha num pequeno número de nós. Percentagem de padrão é zero. Em grandes clusters, alguns nós será sempre verticalmente ou horizontalmente para reparos, para que esta percentagem deve ser configurada para tolerar que. |
| – tempo para executar | Tempo total (em segundos) para o qual o Chaos será executado antes de parar automaticamente. O valor máximo permitido é 4.294.967.295 (System.UInt32.MaxValue).  Predefinido\: 4294967295. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |
| – espera tempo entre falhas | De tempo de espera (em segundos) entre as falhas consecutivas dentro de uma única iteração.  Predefinido\: 20. <br><br> Quanto maior for o valor, menor a sobreposição entre as falhas e mais simples a seqüência de estado faz a transição que atravessa a cluster. A recomendação é começar com um valor entre 1 e 5 e exercício cuidado ao mover a cópia de segurança. |
| --wait-time-between-iterations | Tempo-separação (em segundos) entre as duas iterações consecutivas de caos. Quanto maior for o valor, menor taxa de injeção de falhas.  Predefinido\: 30. |
| --warning-as-error | Indica se os avisos são tratados com a mesma gravidade como erros. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-chaos-stop"></a>parar de chaos sfctl
Para o Chaos se ele está em execução no cluster e coloca a agenda de Chaos no estado parado.

Interrompe o Chaos a execução novas falhas. As falhas em trânsito vão continuar a executar até ficarem concluídos. A agenda de Chaos atual será colocada no estado parado. Assim que uma agenda está parada, irá permanecer no estado parado e não ser utilizada para execuções de nova agenda de Chaos caótico. Tem de definir uma nova agenda de Chaos para retomar o agendamento.

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


## <a name="next-steps"></a>Passos Seguintes
- [Configuração](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como utilizar a CLI do Service Fabric utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
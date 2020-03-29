---
title: Tecido de serviço Azure CLI- sfctl caos
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para gerir o caos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6668446363361fbc6d24afc3d11a36a0b786667d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906174"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Comece, pare e informe sobre o serviço de teste do caos.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Descrição|
| --- | --- |
| [agenda](service-fabric-sfctl-chaos-schedule.md) | Pegue e estabeleça o calendário do caos. |
## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| eventos | Obtém o próximo segmento dos eventos caos com base no token de continuação ou no intervalo de tempo. |
| get | Obter o estado do Caos. |
| start | Começa o caos no aglomerado. |
| parar | Para o Caos se estiver a funcionar no aglomerado e colocar a Agenda do Caos num estado de paragem. |

## <a name="sfctl-chaos-events"></a>eventos de caos sfctl
Obtém o próximo segmento dos eventos caos com base no token de continuação ou no intervalo de tempo.

Para obter o próximo segmento dos eventos caos, pode especificar o ContinuationToken. Para iniciar um novo segmento de eventos caos, pode especificar o intervalo de tempo através do StartTimeUtc e endTimeUtc. Não é possível especificar tanto o Continuatoken como o intervalo de tempo na mesma chamada. Quando há mais de 100 eventos caos, os eventos caos são devolvidos em vários segmentos onde um segmento não contém mais de 100 eventos caos e para obter o próximo segmento você faz uma chamada para esta API com o token de continuação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --continuação-token | O parâmetro simbólico de continuação é utilizado para obter o próximo conjunto de resultados. Um símbolo de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se encaixam numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o sinal de continuação não contém um valor. O valor deste parâmetro não deve ser codificado. |
| --fim-tempo-utc | O tempo de ficheiro do Windows que representa o tempo final do intervalo de tempo para o qual deve ser gerado um relatório Caos. Consulte o [método DateTime.ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) para obter mais detalhes. |
| -resultados máximos | O número máximo de resultados a devolver como parte das consultas páginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta páginada inclui o maior número possível de resultados que se encaixem na mensagem de devolução. |
| --início-tempo-utc | O tempo de ficheiro do Windows que representa o tempo de início do intervalo de tempo para o qual deve ser gerado um relatório Caos. Consulte o [método DateTime.ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) para obter mais detalhes. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-chaos-get"></a>sfctl caos obter
Obter o estado do Caos.

Obtenha o estado do Caos indicando se o Caos está ou não a decorrer, os parâmetros do Caos usados para executar o Caos e o estado da Agenda do Caos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-chaos-start"></a>início do caos sfctl
Começa o caos no aglomerado.

Se o Caos ainda não está a correr no aglomerado, começa o Caos com os parâmetros do Caos. Se o Caos já estiver em execução quando esta chamada for feita, a chamada falha com o código de erro FABRIC_E_CHAOS_ALREADY_RUNNING. Consulte o artigo Induzir o [caos controlado nos clusters de tecidode serviço](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos) para mais detalhes.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -app-tipo-mapa de política de saúde | Lista codificada pela JSON com aplicações não saudáveis em percentagem máxima para tipos específicos de aplicação. Cada entrada especifica como chave o nome do tipo de aplicação e como um valor um inteiro que representa a percentagem MaxPercentUnhealthyApplications utilizada para avaliar as aplicações do tipo de aplicação especificado. <br><br> Define um mapa com aplicações não saudáveis em percentagem máxima para tipos específicos de aplicação. Cada entrada especifica como chave o nome do tipo de aplicação e como valor um inteiro que representa a percentagem MaxPercentUnhealthyApplications utilizada para avaliar as aplicações do tipo de aplicação especificado. O mapa de política de saúde do tipo de aplicação pode ser usado durante a avaliação de saúde do cluster para descrever tipos de aplicação especiais. Os tipos de aplicação incluídos no mapa são avaliados em comparação com a percentagem especificada no mapa, e não com as Aplicações MaxPercentUnhealthy global definidas na política de saúde do cluster. As aplicações dos tipos de aplicações especificadas no mapa não são contadas contra o conjunto global de aplicações. Por exemplo, se algumas aplicações de um tipo forem críticas, o administrador do cluster pode adicionar uma entrada no mapa para esse tipo de aplicação e atribuir-lhe um valor de 0% (isto é, não tolerar quaisquer falhas). Todas as outras aplicações podem ser avaliadas com Aplicações MaxPercentUnhealthy definidas em 20% para tolerar algumas falhas dos milhares de casos de aplicações. O mapa de política de saúde do tipo de aplicação só é utilizado se o manifesto do cluster permitir a avaliação de saúde do tipo de aplicação utilizando a entrada de configuração para HealthManager/EnableApplicationTypeHealthEvaluation. |
| --chaos-target-filter | JSON codificado dicionário com duas teclas do tipo de corda. As duas teclas são NodeTypeInclusionList e ApplicationInclusionList. Os valores para ambas as chaves são a lista de cordas. chaos_target_filter define todos os filtros para falhas de caos direcionadas, por exemplo, falhando apenas certos tipos de nó ou falhando apenas certas aplicações. <br><br> Se chaos_target_filter não for usado, o Caos falha todas as entidades de cluster. Se chaos_target_filter for usado, o Caos falha apenas as entidades que cumprem a especificação chaos_target_filter. NodeTypeInclusionList e ApplicationInclusionList permitem apenas uma semântica sindical. Não é possível especificar uma intersecção entre o NodeTypeInclusionList e applicationInclusionList. Por exemplo, não é possível especificar "falha nesta aplicação apenas quando está nesse tipo de nó". Uma vez que uma entidade é incluída no NodeTypeInclusionList ou na ApplicationInclusionList, essa entidade não pode ser excluída usando o ChaosTargetFilter. Mesmo que o applicationX não apareça na ApplicationInclusionList, em algumas aplicações de iteração caosX pode ser defeituoso porque acontece que está num nó de nóTypeY que está incluído no NodeTypeInclusionList. Se tanto o NodeTypeInclusionList como o ApplicationInclusionList estiverem vazios, é lançada uma Exceção de Argumentos. Todos os tipos de avarias (reiniciam o nó, reiniciar o pacote de códigos, remover réplicas, reiniciar a réplica, mover-se primário e mover-se secundário) estão ativados para os nós destes tipos de nós. Se um tipo de nó (digamos NodeTypeX) não aparecer na Lista de Inclusão no Nó, então as falhas de nível do nó (como nodeRestart) nunca serão ativadas para os nós do NodeTypeX, mas as falhas de código e réplica ainda podem ser ativadas para o NodeTypeX se uma aplicação no AplicaçõesInclusionList por acaso residem num nó de NodeTypeX. No máximo, 100 nomes de tipo de nó podem ser incluídos nesta lista, para aumentar este número, é necessária uma atualização de config para a configuração MaxNumberOfNodeTypesInChaosEntityFilter. Todas as réplicas pertencentes aos serviços destas aplicações são passíveis de réplica de falhas (réplica de reinício, remoção de réplicas, movimento primário e movimento secundário) pelo Caos. O caos só pode reiniciar um pacote de código se o pacote de código supor apenas réplicas destas aplicações. Se uma aplicação não aparecer nesta lista, ainda pode ser defeituosa em alguma iteração caos se a aplicação acabar num nó de um nó que está incluído no NodeTypeInclusionList. No entanto, se o applicationX estiver ligado ao nodeTypeY através de restrições de colocação e aplicaçõesX está ausente da ApplicationInclusionList e nodeTypeY está ausente do NodeTypeInclusionList, então a aplicaçãoX nunca será defeituosa. No máximo, 1000 nomes de aplicações podem ser incluídos nesta lista, para aumentar este número, é necessária uma atualização de config para a configuração MaxNumberOfApplicationsInChaosEntityFilter. |
| --contexto | JSON mapa codificado de pares de valor de chave (string, string) tipo.value. O mapa pode ser usado para gravar informações sobre a corrida do Caos. Não pode haver mais de 100 pares deste tipo e cada corda (chave ou valor) pode ter no máximo 4095 caracteres de comprimento. Este mapa é definido pelo início da corrida do Caos para armazenar opcionalmente o contexto sobre a execução específica. |
| --desactivar-mover-réplica-falhas | Desativa o movimento primário e move falhas secundárias. |
| ---estabilização de cluster-máximo | O máximo de tempo para esperar que todas as entidades de cluster se tornem estáveis e saudáveis.  Padrão\: 60. <br><br> O caos executa em iterações e no início de cada iteração valida a saúde das entidades de cluster. Durante a validação se uma entidade de cluster não for estável e saudável dentro do MaxClusterStabilizationTimeoutInSeconds, o Caos gera um evento falhado de validação. |
| -- falhas simultâneas | O número máximo de falhas simultâneas induzidas por iteração. O caos executa em iterações e duas iterações consecutivas são separadas por uma fase de validação. Quanto maior for a conmoeda, mais agressiva é a injeção de falhas, induzindo séries mais complexas de estados para descobrir insetos. A recomendação é começar com um valor de 2 ou 3 e ter cuidado ao subir.  Padrão\: 1. |
| -- max-percent-unhealthy-apps | Ao avaliar a saúde do cluster durante o Caos, a percentagem máxima permitida de aplicações não saudáveis antes de reportar um erro. <br><br> A percentagem máxima permitida de aplicações não saudáveis antes de reportar um erro. Por exemplo, para permitir que 10% das aplicações não sejam saudáveis, este valor seria de 10. A percentagem representa a percentagem máxima tolerada de aplicações que podem ser insalubres antes de o cluster ser considerado por engano. Se a percentagem for respeitada mas houver pelo menos uma aplicação pouco saudável, a saúde é avaliada como Aviso. Isto é calculado dividindo o número de aplicações não saudáveis sobre o número total de instâncias de aplicação no cluster, excluindo aplicações de tipos de aplicações que estão incluídas no ApplicationTypeHealthPolicyMap. O cálculo reúne-se para tolerar uma falha em um pequeno número de aplicações. A percentagem de incumprimento é zero. |
| -- max-percent-unhealthy-nódes | Ao avaliar a saúde do cluster durante o Caos, a percentagem máxima permitida de nós não saudáveis antes de reportar um erro. <br><br> A percentagem máxima permitida de nódosos não saudáveis antes de reportar um erro. Por exemplo, para permitir que 10% dos nós não sejam saudáveis, este valor seria de 10. A percentagem representa a percentagem máxima tolerada de nós que pode ser insalubre antes de o cluster ser considerado por engano. Se a percentagem for respeitada mas houver pelo menos um nó insalubre, a saúde é avaliada como Aviso. A percentagem é calculada dividindo o número de nós não saudáveis sobre o número total de nós no cluster. A computação reúne-se para tolerar uma falha em pequenos números de nódosos. A percentagem de incumprimento é zero. Em grandes aglomerados, alguns nós estarão sempre para baixo ou para reparações, por isso esta percentagem deve ser configurada para tolerar isso. |
| -tempo para correr | O tempo total (em segundos) para o qual o Caos correrá antes de parar automaticamente. O valor máximo permitido é de 4.294.967.295 (System.UInt32.MaxValue).  Padrão\: 4294967295. |
| --timeout -t | Padrão\: 60. |
| --esperar-tempo-entre-falhas | Tempo de espera (em segundos) entre falhas consecutivas numa única iteração.  Padrão\: 20. <br><br> Quanto maior for o valor, menor é a sobreposição entre falhas e mais simples a sequência de transições estatais por que o cluster passa. A recomendação é começar com um valor entre 1 e 5 e ter cuidado ao subir. |
| --esperar-tempo-entre-iterações | Separação do tempo (em segundos) entre duas iterações consecutivas do Caos. Quanto maior for o valor, menor a taxa de injeção de falhas.  Padrão\: 30. |
| --aviso-como-erro | Indica se os avisos são tratados com a mesma gravidade que os erros. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-chaos-stop"></a>sfctl caos parar
Para o Caos se estiver a funcionar no aglomerado e colocar a Agenda do Caos num estado de paragem.

Impede o Caos de executar novas falhas. As falhas no voo continuarão a ser executadas até estarem completas. A atual Agenda do Caos está num estado de paragem. Uma vez que um horário seja interrompido, permanecerá no estado de paragem e não será usado para o Caos Agendar novas corridas do Caos. Deve ser definido um novo Calendário do Caos para retomar o agendamento.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
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
- [Configurar](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).
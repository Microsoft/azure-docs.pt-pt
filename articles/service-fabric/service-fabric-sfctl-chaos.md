---
title: Azure Service Fabric CLI-sfctl caos
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para gerir o caos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f59eb3296c27e64eb6a4644b2f455e3704381f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260830"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Começa, para e reporta o serviço de testes do caos.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Description|
| --- | --- |
| [agendar](service-fabric-sfctl-chaos-schedule.md) | Arranja e define o calendário do caos. |
## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| eventos | Obtém o próximo segmento dos eventos Caos com base no token de continuação ou no intervalo de tempo. |
| get | Obtenha o estado de Caos. |
| iniciar | Começa o caos no aglomerado. |
| parar | Para o Caos se estiver a funcionar no aglomerado e colocar a Agenda do Caos num estado parado. |

## <a name="sfctl-chaos-events"></a>sfctl eventos caos
Obtém o próximo segmento dos eventos Caos com base no token de continuação ou no intervalo de tempo.

Para obter o próximo segmento dos eventos Caos, você pode especificar o ContinuationToken. Para iniciar um novo segmento de eventos Chaos, pode especificar o intervalo de tempo através do StartTimeUtc e endTimeUtc. Não é possível especificar tanto o ContinuationToken como o intervalo de tempo na mesma chamada. Quando há mais de 100 eventos caos, os eventos caos são devolvidos em vários segmentos onde um segmento não contém mais de 100 eventos caos e para obter o próximo segmento você faz uma chamada para esta API com o token de continuação.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificado por URL. |
| --fim-tempo-utc | O tempo de ficheiro do Windows que representa o tempo final do intervalo de tempo para o qual deve ser gerado um relatório Caos. Consulte [o método DateTime.ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) para obter mais detalhes. |
| --resultados máximos | O número máximo de resultados a devolver como parte das consultas de página. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições máximas do tamanho da mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta paged inclui o maior número possível de resultados que se encaixam na mensagem de retorno. |
| --tempo de início-utc | O tempo de ficheiro do Windows que representa a hora de início do intervalo de tempo para o qual deve ser gerado um relatório Caos. Consulte [o método DateTime.ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) para obter mais detalhes. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-chaos-get"></a>caos sfctl obter
Obtenha o estado de Caos.

Obtenha o estado do Caos indicando se o Caos está ou não em funcionamento, os parâmetros do Caos usados para executar o Caos e o estado da Agenda do Caos.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-chaos-start"></a>sfctl início caos
Começa o caos no aglomerado.

Se o Caos ainda não está a funcionar no aglomerado, começa o Caos com os parâmetros passados no Caos. Se o Caos já estiver a ser em execução quando esta chamada é feita, a chamada falha com o código de erro FABRIC_E_CHAOS_ALREADY_RUNNING. Consulte o artigo [Induzir caos controlado em clusters de tecido de serviço](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos) para obter mais detalhes.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --app-tipo-plano de política de saúde | JSON codificava matriz de entradas de dicionário (chave/valor) com aplicações não saudáveis de percentagem máxima para tipos de aplicações específicos. Cada entrada no dicionário especifica como chave o nome do tipo de aplicação e um número inteiro de valor que representa a percentagem de MaxPercentUnhealthyApplications usada para avaliar as aplicações do tipo de aplicação especificado. <br><br> Define um mapa com aplicações não saudáveis de percentagem máxima para tipos específicos de aplicações. O mapa da política de saúde do tipo de aplicação pode ser usado durante a avaliação da saúde do cluster para descrever os tipos de aplicação individuais. Os tipos de aplicações incluídos no mapa são avaliados em comparação com a percentagem especificada no mapa, e não com as maxpercentunhealthyApplicações globais definidas na política de saúde do cluster. As aplicações de tipos de aplicação especificados no mapa não são contabilizadas contra o conjunto global de aplicações. Por exemplo, se algumas aplicações de um tipo forem críticas, o administrador de cluster pode adicionar uma entrada no mapa para esse tipo de aplicação e atribuir-lhe um valor de 0% (não tolerar quaisquer falhas). Todas as outras aplicações podem ser avaliadas com maxPercentUnhealthyApplicações definidas para 20% para tolerar algumas falhas dos milhares de casos de aplicações. O mapa da política de saúde do tipo de aplicação só é utilizado se o manifesto do cluster permitir uma avaliação de saúde tipo de aplicação utilizando a entrada de configuração para HealthManager/EnableApplicationTypeHealthEvaluation. <br><br> Exemplo JSON codificado cadeia: \" [{tecla \" : \" tecido:/Votação, \" valor : \" \" \" 0 \" }] |
| --caos-filtro-alvo | Dicionário codificado JSON com duas teclas tipo de corda. As duas teclas são NodeTypeInclusionList e ApplicationInclusionList. Os valores para ambas as teclas são uma lista de cordas. chaos_target_filter define todos os filtros para falhas de caos direcionadas, por exemplo, falhando apenas certos tipos de nós ou falhando apenas certas aplicações. <br><br> Se chaos_target_filter não for utilizado, o Caos falha todas as entidades do cluster. Se chaos_target_filter for utilizada, o Caos falha apenas as entidades que cumprem a especificação chaos_target_filter. NodeTypeInclusionList e ApplicationInclusionList permitem apenas uma semântica sindical. Não é possível especificar uma intersecção entre NodeTypeInclusionList e ApplicationInclusionList. Por exemplo, não é possível especificar "avaria esta aplicação apenas quando está nesse tipo de nó". Uma vez que uma entidade é incluída no NodeTypeInclusionList ou no ApplicationInclusionList, essa entidade não pode ser excluída usando o ChaosTargetFilter. Mesmo que a aplicaçãoX não apareça no ApplicationInclusionList, em algumas aplicações de iteração caosX pode ser falha porque acontece que está num nó de nodeTypeY que está incluído no NodeTypeInclusionList. Se tanto o NodeTypeInclusionList como o ApplicationInclusionList estiverem vazios, é lançado um ArgumentException. Todos os tipos de falhas (reinicie o nó, reinicie o pacote de código, remova a réplica, reinicie a réplica, mova-se primáriamente e mova-se secundário) para os nós destes tipos de nós. Se um tipo de nó (digamos NodeTypeX) não aparecer no NodeTypeInclusionList, então as falhas do nível do nó (como nodeRestart) nunca serão ativadas para os nós de NodeTypeX, mas o pacote de código e as falhas de réplica ainda podem ser ativadas para NodeTypeX se uma aplicação na Lista de Exclusões de Aplicação acontecer reside num nó de NodeTypeX. No máximo 100 nomes do tipo nó podem ser incluídos nesta lista, para aumentar este número, é necessária uma atualização config para a configuração MaxNumberOfNodeTypesInChaosEntityFilter. Todas as réplicas pertencentes a serviços destas aplicações são passíveis de réplicas (reiniciar réplicas, remover réplicas, mover-se primária e mover-se secundária) pelo Caos. O caos só pode reiniciar um pacote de código se o pacote de código hospedar réplicas destas aplicações apenas. Se uma aplicação não aparecer nesta lista, pode ainda ser defeituosa em alguma iteração do Caos se a aplicação acabar num nó de um nó que está incluído no NodeTypeInclusionList. No entanto, se a aplicaçãoX estiver ligada ao nodeTypeY através de restrições de colocação e a aplicaçãoX estiver ausente do ApplicationInclusionList e o nodeTypeY estiver ausente do NodeTypeInclusionList, então a aplicaçãoX nunca será defeituosa. No máximo 1000 nomes de aplicações podem ser incluídos nesta lista, para aumentar este número, é necessária uma atualização config para a configuração do MaxNumberOfApplicationsInChaosEntityFilter. |
| -contexto | JSON codificado mapa de pares de valor-chave do tipo (string, string). O mapa pode ser usado para gravar informações sobre a corrida do Caos. Não pode haver mais de 100 pares deste tipo e cada corda (chave ou valor) pode ter no máximo 4095 caracteres. Este mapa é definido pelo arranque do Caos correr para armazenar opcionalmente o contexto sobre a execução específica. |
| --desativação de falhas de réplicas de movimento | Desativa o movimento primário e move falhas secundárias. |
| --estabilização do aglomerado máximo | O máximo de tempo para esperar que todas as entidades do cluster se tornem estáveis e saudáveis.  Padrão \: 60. <br><br> O caos executa-se em iterações e no início de cada iteração valida a saúde das entidades do cluster. Durante a validação se uma entidade de cluster não for estável e saudável dentro do MaxClusterStabilizationTimeoutInSeconds, o Caos gera um evento falhado de validação. |
| --max-simultâneo-falhas | O número máximo de falhas simultâneas induzidas por iteração. O caos executa-se em iterações e duas iterações consecutivas são separadas por uma fase de validação. Quanto maior a concunciência, mais agressiva a injeção de falhas, induzindo uma série mais complexa de estados a descobrir insetos. A recomendação é começar com um valor de 2 ou 3 e ter cuidado ao subir.  Predefinição \: 1. |
| --max-percent-insalubre-apps | Ao avaliar a saúde do cluster durante o Caos, a percentagem máxima permitida de aplicações não saudáveis antes de reportar um erro. <br><br> A percentagem máxima permitida de aplicações não saudáveis antes de reportar um erro. Por exemplo, para permitir que 10% das aplicações não sejam saudáveis, este valor seria de 10. A percentagem representa a percentagem máxima tolerada de aplicações que podem não ser saudáveis antes de o cluster ser considerado errado. Se a percentagem for respeitada mas houver pelo menos uma aplicação pouco saudável, a saúde é avaliada como Advertência. Isto é calculado dividindo o número de aplicações não saudáveis sobre o número total de instâncias de aplicação no cluster, excluindo aplicações de tipos de aplicação que estão incluídos no ApplicationTypeHealthPolicyMap. O cálculo reúne-se para tolerar uma falha em um pequeno número de aplicações. A percentagem de incumprimento é zero. |
| --max-percent-unhealthy-nodes | Ao avaliar a saúde do cluster durante o Caos, a percentagem máxima permitida de nós não saudáveis antes de reportar um erro. <br><br> A percentagem máxima permitida de nós não saudáveis antes de reportar um erro. Por exemplo, para permitir que 10% dos nós não sejam saudáveis, este valor seria 10. A percentagem representa a percentagem máxima tolerada de nós que podem não ser saudáveis antes de o cluster ser considerado errado. Se a percentagem for respeitada mas houver pelo menos um nó pouco saudável, a saúde é avaliada como Advertência. A percentagem é calculada dividindo o número de nós não saudáveis sobre o número total de nós no cluster. A computação arredonda para tolerar uma falha em um pequeno número de nós. A percentagem de incumprimento é zero. Em grandes aglomerados, alguns nós estarão sempre em baixo ou fora para reparações, pelo que esta percentagem deve ser configurada para tolerar isso. |
| --tempo a correr | Tempo total (em segundos) para o qual o Caos irá funcionar antes de parar automaticamente. O valor máximo permitido é de 4.294.967.295 (System.UInt32.MaxValue).  Padrão \: 4294967295. |
| --timeout -t | Padrão \: 60. |
| --tempo de espera entre falhas | Tempo de espera (em segundos) entre falhas consecutivas dentro de uma única iteração.  Padrão \: 20. <br><br> Quanto maior o valor, menor a sobreposição entre falhas e mais simples a sequência de transições de estado que o cluster passa. A recomendação é começar com um valor entre 1 e 5 e ter cuidado ao subir. |
| --tempo de espera entre iterações | Separação do tempo (em segundos) entre duas iterações consecutivas do Caos. Quanto maior o valor, menor a taxa de injeção de avaria.  Padrão \: 30. |
| ...-aviso-como-erro | Indica se os avisos são tratados com a mesma gravidade que os erros. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-chaos-stop"></a>sfctl caos parar
Para o Caos se estiver a funcionar no aglomerado e colocar a Agenda do Caos num estado parado.

Impede o Caos de executar novas falhas. As falhas a bordo continuarão a ser executadas até estarem completas. A atual Agenda do Caos é posta num estado parado. Uma vez que um horário é interrompido, ele permanecerá no estado parado e não será usado para caos Agendar novas séries de caos. Deve ser definida uma nova Agenda caos para retomar o agendamento.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI do Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .

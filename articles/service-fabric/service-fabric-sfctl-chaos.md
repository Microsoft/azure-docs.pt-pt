---
title: CLI do Azure Service Fabric-sfctl caos
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para gerenciar o caos.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: b91c11dfb9b1249420429bee20f12439fffa4853
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646182"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Inicie, pare e relate o serviço de teste de caos.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Descrição|
| --- | --- |
| [schedule](service-fabric-sfctl-chaos-schedule.md) | Obter e definir o cronograma de caos. |
## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| eventos | Obtém o próximo segmento dos eventos de caos com base no token de continuação ou no intervalo de tempo. |
| Get | Obtenha o status do caos. |
| start | Inicia o caos no cluster. |
| parar | Interrompe o caos se estiver em execução no cluster e colocar a agenda de caos em um estado parado. |

## <a name="sfctl-chaos-events"></a>eventos de caos sfctl
Obtém o próximo segmento dos eventos de caos com base no token de continuação ou no intervalo de tempo.

Para obter o próximo segmento dos eventos de caos, você pode especificar o ContinuationToken. Para obter o início de um novo segmento de eventos de caos, você pode especificar o intervalo de tempo por meio de StartTimeUtc e EndTimeUtc. Você não pode especificar o ContinuationToken e o intervalo de tempo na mesma chamada. Quando há mais de 100 eventos de caos, os eventos de caos são retornados em vários segmentos em que um segmento contém no máximo 100 eventos de caos e para obter o próximo segmento, você faz uma chamada para essa API com o token de continuação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se ajustam em uma única resposta. Quando esse valor é passado para a próxima chamada à API, a API retorna o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --hora de término – UTC | O horário do arquivo do Windows que representa a hora de término do intervalo de tempo para o qual um relatório de caos deve ser gerado. Consulte o [método DateTime. ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) para obter detalhes. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser menores que os resultados máximos especificados se não couberem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero ou não for especificado, a consulta paginável incluirá o máximo possível de resultados que couberem na mensagem de retorno. |
| --hora de início UTC | O horário do arquivo do Windows que representa a hora de início do intervalo de tempo para o qual um relatório de caos deve ser gerado. Consulte o [método DateTime. ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) para obter detalhes. |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-chaos-get"></a>sfctl caos obter
Obtenha o status do caos.

Obtenha o status de caos que indica se o caos está em execução ou não, os parâmetros de caos usados para executar o caos e o status do cronograma de caos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-chaos-start"></a>início do caos sfctl
Inicia o caos no cluster.

Se o caos ainda não estiver em execução no cluster, ele iniciará o caos com os parâmetros de caos passados. Se o caos já estiver em execução quando essa chamada for feita, a chamada falhará com o código de erro FABRIC_E_CHAOS_ALREADY_RUNNING. Consulte o artigo [induzir o caos controlado em clusters Service Fabric](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos) para obter mais detalhes.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --App-Type-integridade-de-mapa de política | Lista codificada JSON com percentual máximo de aplicativos não íntegros para tipos de aplicativos específicos. Cada entrada especifica como uma chave o nome do tipo de aplicativo e como um valor inteiro que representa o percentual de MaxPercentUnhealthyApplications usado para avaliar os aplicativos do tipo de aplicativo especificado. <br><br> Define um mapa com percentual máximo de aplicativos não íntegros para tipos de aplicativos específicos. Cada entrada especifica como chave o nome do tipo de aplicativo e como valor um inteiro que representa o percentual de MaxPercentUnhealthyApplications usado para avaliar os aplicativos do tipo de aplicativo especificado. O mapa da política de integridade do tipo de aplicativo pode ser usado durante a avaliação de integridade do cluster para descrever tipos de aplicativos especiais. Os tipos de aplicativo incluídos no mapa são avaliados em relação à porcentagem especificada no mapa e não com o MaxPercentUnhealthyApplications global definido na política de integridade do cluster. Os aplicativos dos tipos de aplicativos especificados no mapa não são contados em relação ao pool global de aplicativos. Por exemplo, se alguns aplicativos de um tipo forem críticos, o administrador de cluster poderá adicionar uma entrada ao mapa para esse tipo de aplicativo e atribuir a ele um valor de 0% (ou seja, não tolerar nenhuma falha). Todos os outros aplicativos podem ser avaliados com MaxPercentUnhealthyApplications definido como 20% para tolerar algumas falhas das milhares de instâncias do aplicativo. O mapa da política de integridade do tipo de aplicativo será usado somente se o manifesto do cluster habilitar a avaliação de integridade do tipo de aplicativo usando a entrada de configuração para Healthmanager/EnableApplicationTypeHealthEvaluation. |
| --chaos-target-filter | Dicionário codificado em JSON com duas chaves de tipo de cadeia de caracteres. As duas chaves são NodeTypeInclusionList e ApplicationInclusionList. Os valores para ambas as chaves são lista de cadeia de caracteres. chaos_target_filter define todos os filtros para falhas de caos direcionadas, por exemplo, a falha somente de determinados tipos de nó ou a falha de determinados aplicativos. <br><br> Se chaos_target_filter não for usado, o caos falhará em todas as entidades de cluster. Se chaos_target_filter for usado, o caos falhará apenas nas entidades que atendem à especificação de chaos_target_filter. NodeTypeInclusionList e ApplicationInclusionList permitem apenas uma semântica de União. Não é possível especificar uma interseção de NodeTypeInclusionList e ApplicationInclusionList. Por exemplo, não é possível especificar "falha neste aplicativo somente quando ele está nesse tipo de nó". Depois que uma entidade é incluída em NodeTypeInclusionList ou ApplicationInclusionList, essa entidade não pode ser excluída usando ChaosTargetFilter. Mesmo que aplicativoX não apareça em ApplicationInclusionList, em alguma iteração de caos, aplicativoX pode ter falhado porque ele está em um nó de nodeType que está incluído no NodeTypeInclusionList. Se NodeTypeInclusionList e ApplicationInclusionList estiverem vazios, um ArgumentException será gerado. Todos os tipos de falhas (reinicie o nó, reinicie o pacote de códigos, remova a réplica, reinicie a réplica, mova primário e mova secundário) estão habilitados para os nós desses tipos de nó. Se um tipo de nó (digamos NodeTypeX) não aparecer no NodeTypeInclusionList, as falhas de nível de nó (como NodeRestart) nunca serão habilitadas para os nós de NodeTypeX, mas as falhas de pacote e de réplica ainda poderão ser habilitadas para NodeTypeX se um aplicativo no ApplicationInclusionList acontece em um nó de NodeTypeX. No máximo 100 nomes de tipo de nó podem ser incluídos nessa lista, para aumentar esse número, uma atualização de configuração é necessária para a configuração do MaxNumberOfNodeTypesInChaosEntityFilter. Todas as réplicas que pertencem aos serviços desses aplicativos são receptivos a falhas de réplica (reinicie a réplica, remova a réplica, mova o primário e mova o secundário) por caos. O caos poderá reiniciar um pacote de códigos somente se o pacote de códigos hospedar réplicas somente desses aplicativos. Se um aplicativo não aparecer nessa lista, ainda poderá ter falhado em alguma iteração de caos se o aplicativo terminar em um nó de um tipo de nó incluído no NodeTypeInclusionList. No entanto, se aplicativoX estiver vinculado ao nodeType por meio de restrições de posicionamento e aplicativoX estiver ausente de ApplicationInclusionList e nodeType estiver ausente de NodeTypeInclusionList, aplicativoX nunca terá falhado. No máximo 1000 nomes de aplicativos podem ser incluídos nessa lista, para aumentar esse número, uma atualização de configuração é necessária para a configuração do MaxNumberOfApplicationsInChaosEntityFilter. |
| --context | Mapa codificado em JSON de (cadeia de caracteres, Cadeia de caracteres) tipo pares chave-valor. O mapa pode ser usado para registrar informações sobre a execução do caos. Não pode haver mais de 100 pares de bytes e cada cadeia de caracteres (chave ou valor) pode ter no máximo 4095 caracteres. Esse mapa é definido pelo iniciador da execução de caos para armazenar opcionalmente o contexto sobre a execução específica. |
| --disable-move-replica-faults | Desabilita a movimentação primária e move as falhas secundárias. |
| --max-cluster-stabilization | A quantidade máxima de tempo de espera para que todas as entidades de cluster se tornem estáveis e íntegras.  Padrão\: 60. <br><br> O caos é executado em iterações e no início de cada iteração valida a integridade das entidades de cluster. Durante a validação, se uma entidade de cluster não for estável e estiver íntegra no MaxClusterStabilizationTimeoutInSeconds, o caos gerará um evento de falha na validação. |
| --max-concurrent-faults | O número máximo de falhas simultâneas induzidas por iteração. O caos é executado em iterações e duas iterações consecutivas são separadas por uma fase de validação. Quanto maior a simultaneidade, mais agressiva a injeção de falhas – induzir uma série mais complexa de Estados para descobrir bugs. A recomendação é começar com um valor de 2 ou 3 e ter cuidado ao mover para cima.  Padrão\: 1. |
| --max-percent-unhealthy-apps | Ao avaliar a integridade do cluster durante o caos, o percentual máximo permitido de aplicativos não íntegros antes de relatar um erro. <br><br> A porcentagem máxima permitida de aplicativos não íntegros antes de relatar um erro. Por exemplo, para permitir que 10% dos aplicativos sejam não íntegros, esse valor seria 10. A porcentagem representa a porcentagem máxima tolerada de aplicativos que podem não estar íntegros antes que o cluster seja considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um aplicativo não íntegro, a integridade será avaliada como aviso. Isso é calculado dividindo o número de aplicativos não íntegros no número total de instâncias do aplicativo no cluster, excluindo aplicativos de tipos de aplicativos que estão incluídos no ApplicationTypeHealthPolicyMap. A computação arredonda até tolerar uma falha em pequenos números de aplicativos. O percentual padrão é zero. |
| --max-percent-unhealthy-nodes | Ao avaliar a integridade do cluster durante o caos, o percentual máximo permitido de nós não íntegros antes de relatar um erro. <br><br> A porcentagem máxima permitida de nós não íntegros antes de relatar um erro. Por exemplo, para permitir que 10% dos nós sejam não íntegros, esse valor seria 10. A porcentagem representa a porcentagem máxima tolerada de nós que podem não estar íntegros antes que o cluster seja considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um nó não íntegro, a integridade será avaliada como aviso. A porcentagem é calculada pela divisão do número de nós não íntegros no número total de nós no cluster. A computação arredonda até tolerar uma falha em pequenos números de nós. O percentual padrão é zero. Em clusters grandes, alguns nós sempre estarão inativos ou fora para reparos, portanto, esse percentual deve ser configurado para tolerar isso. |
| --tempo de execução | Tempo total (em segundos) para o qual o caos será executado antes de parar automaticamente. O valor máximo permitido é 4.294.967.295 (System. UInt32. MaxValue).  Padrão\: 4294967295. |
| --Timeout-t | Padrão\: 60. |
| --tempo de espera entre falhas | Tempo de espera (em segundos) entre falhas consecutivas em uma única iteração.  Padrão\: 20. <br><br> Quanto maior o valor, menor a sobreposição entre falhas e a sequência de transições de estado que o cluster passa. A recomendação é começar com um valor entre 1 e 5 e ter cuidado ao mover para cima. |
| --tempo de espera entre as iterações | Separação de tempo (em segundos) entre duas iterações consecutivas de caos. Quanto maior o valor, menor será a taxa de injeção de falha.  Padrão\: 30. |
| --warning-as-error | Indica se os avisos são tratados com a mesma severidade que os erros. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-chaos-stop"></a>parada de caos sfctl
Interrompe o caos se estiver em execução no cluster e colocar a agenda de caos em um estado parado.

Impede o caos de executar novas falhas. As falhas em andamento continuarão a ser executadas até que sejam concluídas. O agendamento de caos atual é colocado em um estado parado. Quando uma agenda for interrompida, ela permanecerá no estado parado e não será usada para a programação de novas execuções de caos. Uma nova agenda de caos deve ser definida para retomar o agendamento.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
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
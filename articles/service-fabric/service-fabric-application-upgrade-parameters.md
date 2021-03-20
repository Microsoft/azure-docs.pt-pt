---
title: 'Atualização da aplicação: parâmetros de upgrade'
description: Descreve parâmetros relacionados com a atualização de uma aplicação de Tecido de Serviço, incluindo verificações de saúde para executar e políticas para desfazer automaticamente a atualização.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 6b6116bf1188fcf191b2d672e6c698bb3c050e6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018482"
---
# <a name="application-upgrade-parameters"></a>Parâmetros da atualização da aplicação
Este artigo descreve os vários parâmetros que se aplicam durante a atualização de uma aplicação de Tecido de Serviço Azure. Os parâmetros de atualização da aplicação controlam os intervalos e os controlos de saúde que são aplicados durante a atualização, e especificam as políticas que devem ser aplicadas quando uma atualização falha. Os parâmetros de aplicação aplicam-se às atualizações utilizando:
- PowerShell
- Visual Studio
- SFCTL
- [REST](/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

As atualizações de aplicações são iniciadas através de um dos três modos de atualização selecionáveis pelo utilizador. Cada modo tem o seu próprio conjunto de parâmetros de aplicação:
- Monitorizado
- Auto não monitorizado
- Manual não monitorizado

Os parâmetros aplicáveis e opcionais são descritos em cada secção da seguinte forma.

## <a name="visual-studio-and-powershell-parameters"></a>Estúdio Visual e Parâmetros PowerShell

As atualizações da aplicação de tecido de serviço utilizando o PowerShell utilizam o comando [Start-ServiceFabricApplicationUpgrade.](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) O modo de atualização é selecionado passando o parâmetro **Monitor,** **UnmonitoredAuto** ou **UnmonitoredManual** para [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Os parâmetros de atualização da aplicação visual Studio Service Fabric são definidos através do diálogo visual de atualização do estúdio. O modo de atualização do Estúdio Visual é selecionado utilizando a caixa de dropdown **do modo de atualização** para **monitorado**, **UnmonitoredAuto** ou **UnmonitoredManual**. Para mais informações, consulte [configurar a atualização de uma aplicação de Tecido de Serviço no Estúdio Visual.](service-fabric-visualstudio-configure-upgrade.md)

### <a name="required-parameters"></a>Parâmetros necessários
(PS=PowerShell, VS=Visual Studio)

| Parâmetro | Aplica-se A | Description |
| --- | --- | --- |
ApplicationName |PS| Nome da aplicação que está a ser atualizada. Exemplos: tecido:/VisualObjects, tecido:/ClusterMonitor. |
AplicaçãoTypeVersion|PS|A versão do tipo de aplicação que o upgrade visa. |
Falha deacção |PS, VS|Os valores permitidos são **Reversão,** **Manual** e **Inválido**. A ação compensatória a realizar quando uma atualização *monitorizada* encontra políticas de monitorização ou violações da política de saúde. <br>**A reversão** especifica que a atualização irá automaticamente reverter para a versão pré-actualização. <br>**O manual** indica que a atualização irá mudar para o modo de atualização *Não monitorizadoManual.* <br>**Inválido** indica que a ação de avaria é inválida.|
Monitorizado |PS|Indica que o modo de atualização é monitorizado. Depois de o cmdlet terminar uma atualização para um domínio de upgrade, se a saúde do domínio de upgrade e o cluster cumprirem as políticas de saúde que define, o Service Fabric atualiza o próximo domínio de atualização. Se o domínio ou cluster de atualização não cumprir as políticas de saúde, a atualização falha e o Service Fabric reverte a atualização para o domínio de atualização ou reverte para o modo manual de acordo com a política especificada. Este é o modo recomendado para atualizações de aplicações em ambiente de produção. |
UpgradeMode | VS | Os valores permitidos são **monitorizados** (padrão), **UnmonitoredAuto,** ou **UnmonitoredManual**. Consulte os parâmetros PowerShell para cada modo neste artigo para obter mais detalhes. |
Não monitorizadoauto | PS | Indica que o modo de atualização não é monitorizado automaticamente. Depois de o Service Fabric atualizar um domínio de upgrade, o Service Fabric atualiza o próximo domínio de atualização independentemente do estado de saúde da aplicação. Este modo não é recomendado para produção, e só é útil durante o desenvolvimento de uma aplicação. |
Não monitorizadoManual | PS | Indica que o modo de atualização não é monitorizado manualmente. Depois de o Service Fabric atualizar um domínio de atualização, espera que atualize o próximo domínio de atualização utilizando o *cmdlet Resume-ServiceFabricApplicationUpgrade.* |

### <a name="optional-parameters"></a>Parâmetros opcionais

Os parâmetros de avaliação da saúde são opcionais. Se os critérios de avaliação da saúde não forem especificados quando uma atualização começar, o Service Fabric utiliza as políticas de saúde da aplicação especificadas no ApplicationManifest.xml da instância de aplicação.

> [!div class="mx-tdBreakAll"]
> | Parâmetro | Aplica-se A | Description |
> | --- | --- | --- |
> | AplicativoParameter |PS, VS| Especifica as sobreposições para os parâmetros de aplicação.<br>Os parâmetros de aplicação PowerShell são especificados como pares de nome/valor de hashtable. Por exemplo, "VotingData_MinReplicaSetSize" @ = "3"; "VotingData_PartitionCount" = "1" }.<br>Os parâmetros de aplicação do Estúdio Visual podem ser especificados no diálogo de aplicação de tecido de serviço de publicação no campo **de ficheiros de parâmetros de aplicação.**
> | Confirmar |PS| Os valores permitidos são **verdadeiros** e **falsos.** Solicita a confirmação antes de executar o cmdlet. |
> | ConsidereWarningAsError |PS, VS |Os valores permitidos são **verdadeiros** e **falsos.** O valor predefinido é **falso.** Trate os eventos de saúde de advertência para a aplicação como erros ao avaliar a saúde da aplicação durante a atualização. Por padrão, o Service Fabric não avalia os eventos de saúde de aviso como falhas (erros), pelo que a atualização pode prosseguir mesmo que existam eventos de alerta. |
> | DefaultServiceTypeHealthPolicy | PS, VS |Especifica a política de saúde para o tipo de serviço predefinido a utilizar para a atualização monitorizada no formato MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Por exemplo, 5,10,15 indica os seguintes valores: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Os valores permitidos são **verdadeiros** e **falsos.** Indica que o processo de atualização ignora a mensagem de aviso e força a atualização mesmo quando o número da versão não foi alterado. Isto é útil para testes locais, mas não é recomendado para ser utilizado num ambiente de produção, uma vez que requer a remoção da implantação existente que causa tempo de inacomprêmo e potencial perda de dados. |
> | ForceRestart |PS, VS |Se atualizar uma configuração ou pacote de dados sem atualizar o código de serviço, o serviço só será reiniciado se a propriedade ForceRestart estiver definida como **True**. Quando a atualização estiver concluída, o Service Fabric notifica o serviço de que está disponível um novo pacote de configuração ou pacote de dados. O serviço é responsável pela aplicação das alterações. Se necessário, o serviço pode reiniciar-se sozinho. |
> | HealthCheckRetryTimeoutSec |PS, VS |A duração (em segundos) que o Service Fabric continua a efetuar a avaliação de saúde antes de declarar a atualização como falhada. O padrão é de 600 segundos. Esta duração começa após *o healthCheckWaitDurationSec* ser alcançado. Dentro deste *HealthCheckRetryTimeout,* o Service Fabric poderá realizar múltiplas verificações de saúde da aplicação. O valor predefinido é de 10 minutos e deve ser personalizado adequadamente para a sua aplicação. |
> | HealthCheckStableDurationSec |PS, VS |A duração (em segundos) para verificar se a aplicação é estável antes de passar para o domínio de atualização seguinte ou completar a atualização. Esta duração de espera é utilizada para evitar alterações não detetadas de saúde logo após a verificação de saúde. O valor predefinido é de 120 segundos e deve ser personalizado adequadamente para a sua aplicação. |
> | HealthCheckWaitDurationSec |PS, VS | O tempo para esperar (em segundos) depois da atualização ter terminado no domínio de atualização antes que o Service Fabric avalie a saúde da aplicação. Esta duração também pode ser considerada como o tempo que um pedido deve ser executando antes de poder ser considerado saudável. Se o exame de saúde passar, o processo de atualização segue para o domínio de upgrade seguinte.  Se o exame de saúde falhar, o Service Fabric aguarda o [UpgradeHealthCheckInterval](./service-fabric-cluster-fabric-settings.md#clustermanager) antes de voltar a tentar o exame de saúde até que o *HealthCheckRetryTimeoutSec* seja atingido. O valor predefinido e recomendado é de 0 segundos. |
> | MaxPercentUnhealthyDeployedApplicações|PS, VS |O valor predefinido e recomendado é 0. Especifique o número máximo de aplicações implementadas (ver [secção Saúde)](service-fabric-health-introduction.md)que podem não ser saudáveis antes de a aplicação ser considerada insalubre e falhar a atualização. Este parâmetro define a saúde da aplicação no nó e ajuda a detetar problemas durante a atualização. Tipicamente, as réplicas da aplicação ficam equilibradas em carga para o outro nó, o que permite que a aplicação pareça saudável, permitindo assim que a atualização prossiga. Ao especificar uma saúde rígida *de MaxPercentUnhealthyDeployedApplications,* o Service Fabric consegue detetar rapidamente um problema com o pacote de aplicações e ajudar a produzir uma atualização rápida de falhas. |
> | MaxPercentUnhealthyServices |PS, VS |Um parâmetro para *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. O valor predefinido e recomendado é 0. Especifique o número máximo de serviços no caso da aplicação que pode não ser saudável antes de o pedido ser considerado insalubre e falhar a atualização. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |Um parâmetro para *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. O valor predefinido e recomendado é 0. Especifique o número máximo de divisórias num serviço que pode não ser saudável antes de o serviço ser considerado insalubre. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |Um parâmetro para *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. O valor predefinido e recomendado é 0. Especifique o número máximo de réplicas em partição que podem não ser saudáveis antes da partição ser considerada insalubre. |
> | ServiceTypeHealthPolicyMap | PS, VS | Representa a política de saúde utilizada para avaliar a saúde dos serviços pertencentes a um tipo de serviço. Toma uma entrada de hash table no seguinte formato: @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} Por exemplo: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
> | TimeoutSec | PS, VS | Especifica o período de tempo de ício em segundos para a operação. |
> | UpgradeDomainTimeoutSec |PS, VS |Tempo máximo (em segundos) para a atualização de um único domínio de atualização. Se este intervalo for alcançado, a atualização para e procede com base na definição de *FalhaAction*. O valor predefinido nunca é (Infinito) e deve ser personalizado adequadamente para a sua aplicação. |
> | UpgradeReplicaSetCheckTimeoutSec |PS, VS |Medido em segundos.<br>**Serviço apátrida**--Dentro de um único domínio de upgrade, a Service Fabric tenta garantir que estão disponíveis instâncias adicionais do serviço. Se a contagem de casos-alvo for superior a uma, o Service Fabric aguarda a disponibilização de mais de um caso, até um valor máximo de tempo de desaporo. Este tempo de paragem é especificado utilizando a propriedade *UpgradeReplicaSetCheckTimeoutSec.* Se o tempo de 20 horas expirar, a Service Fabric procede à atualização, independentemente do número de casos de serviço. Se a contagem de casos-alvo for uma, o Service Fabric não espera e procede imediatamente com a atualização.<br><br>**Serviço imponente**-- Dentro de um único domínio de upgrade, o Service Fabric tenta garantir que o conjunto de réplicas tem um quórum. O Service Fabric aguarda a disponibilização de um quórum, até um valor máximo de tempo de descontê-lo (especificado pela propriedade *UpgradeReplicaSetCheckTimeoutSec).* Se o tempo de 20 horas expirar, o Service Fabric procede com a atualização, independentemente do quórum. Esta definição é definida como nunca (infinita) quando avança e 1200 segundos ao rodar para trás. |
> | UpgradeTimeoutSec |PS, VS |Um intervalo (em segundos) que se aplica a toda a atualização. Se este intervalo for atingido, a atualização para e *o FailureAction* é acionado. O valor predefinido nunca é (Infinito) e deve ser personalizado adequadamente para a sua aplicação. |
> | WhatIf | PS | Os valores permitidos são **verdadeiros** e **falsos.** Apresenta o que aconteceria mediante a execução do cmdlet. O cmdlet não é executado. |

Os *critérios MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService* e *MaxPercentUnhealthyReplicasPerPartition* podem ser especificados por tipo de serviço para uma instância de aplicação. A definição destes parâmetros por serviço permite que uma aplicação contenha diferentes tipos de serviços com diferentes políticas de avaliação. Por exemplo, um tipo de serviço de gateway apátrida pode ter um *MaxPercentUnhealthyPartitionsPerService* que é diferente de um tipo de serviço de motor imponente para uma determinada instância de aplicação.

## <a name="sfctl-parameters"></a>Parâmetros SFCTL

As atualizações da aplicação de tecido de serviço utilizando o CLI do tecido de serviço utilizam o comando [de atualização de aplicações sfctl](./service-fabric-sfctl-application.md#sfctl-application-upgrade) juntamente com os seguintes parâmetros necessários e opcionais.

### <a name="required-parameters"></a>Parâmetros necessários

| Parâmetro | Descrição |
| --- | --- |
| id aplicação  |Identificação da aplicação que está a ser atualizada. <br> Este é tipicamente o nome completo da aplicação sem o esquema URI de tecido.. A partir da versão 6.0, os nomes hierárquicos são delimitados com o \~ ' ' caráter. Por exemplo, se o nome da aplicação for 'tecido:/myapp/app1', a identidade da aplicação seria 'myapp \~ app1' em 6.0+ e 'myapp/app1' em versões anteriores.|
versão de aplicação |A versão do tipo de aplicação que o upgrade visa.|
parâmetros  |Uma lista codificada por JSON de sobreposições de parâmetros de aplicação a aplicar ao atualizar a aplicação.|

### <a name="optional-parameters"></a>Parâmetros opcionais

| Parâmetro | Descrição |
| --- | --- |
incumprimento serviço-saúde-política | [A JSON](/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) codificou a especificação da política de saúde utilizada por defeito para avaliar a saúde de um tipo de serviço. O mapa está vazio por defeito. |
falha de ação | Os valores permitidos são **Reversão,** **Manual** e **Inválido**. A ação compensatória a realizar quando uma atualização *monitorizada* encontra políticas de monitorização ou violações da política de saúde. <br>**A reversão** especifica que a atualização irá automaticamente reverter para a versão pré-actualização. <br>**O manual** indica que a atualização irá mudar para o modo de atualização *Não monitorizadoManual.* <br>**Inválido** indica que a ação de avaria é inválida.|
forçar a reiniciar | Se atualizar uma configuração ou pacote de dados sem atualizar o código de serviço, o serviço só será reiniciado se a propriedade ForceRestart estiver definida como **True**. Quando a atualização estiver concluída, o Service Fabric notifica o serviço de que está disponível um novo pacote de configuração ou pacote de dados. O serviço é responsável pela aplicação das alterações. Se necessário, o serviço pode reiniciar-se sozinho. |
verificação de saúde-retry-timeout | O tempo para repetir a avaliação de saúde quando a aplicação ou o cluster não é saudável antes *da execução do FailureAction.* É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. Predefinição: PT0H10M0s. |
duração do controlo da saúde estável | O tempo que a aplicação ou cluster deve permanecer saudável antes da atualização avançar para o próximo domínio de atualização. É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. Padrão: PT0H2M0s. |
saúde-check-wait-duração | A quantidade de tempo para esperar depois de completar um domínio de upgrade antes de aplicar políticas de saúde. É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. Predefinição: 0.|
max-unhealthy-apps | O valor predefinido e recomendado é 0. Especifique o número máximo de aplicações implementadas (ver [secção Saúde)](service-fabric-health-introduction.md)que podem não ser saudáveis antes de a aplicação ser considerada insalubre e falhar a atualização. Este parâmetro define a saúde da aplicação no nó e ajuda a detetar problemas durante a atualização. Tipicamente, as réplicas da aplicação ficam equilibradas em carga para o outro nó, o que permite que a aplicação pareça saudável, permitindo assim que a atualização prossiga. Ao especificar uma saúde rigorosa *de aplicações max-unhealthy,* o Service Fabric pode detetar rapidamente um problema com o pacote de aplicações e ajudar a produzir uma atualização rápida de falhas. Representado como um número entre 0 e 100. |
mode | Os valores permitidos são **monitorizados,** **UpgradeMode**, **UnmonitoredAuto,** **UnmonitoredManual**. O padrão é **UnmonitoredAuto**. Consulte a secção de *Parâmetros Necessários* para o Estúdio Visual e PowerShell para obter descrições destes valores.|
replica-set-check-timeout |Medido em segundos. <br>**Serviço apátrida**--Dentro de um único domínio de upgrade, a Service Fabric tenta garantir que estão disponíveis instâncias adicionais do serviço. Se a contagem de casos-alvo for superior a uma, o Service Fabric aguarda a disponibilização de mais de um caso, até um valor máximo de tempo de desaporo. Este intervalo de tempo é especificado utilizando a propriedade *de verificação de réplica-set-time-out.* Se o tempo de 20 horas expirar, a Service Fabric procede à atualização, independentemente do número de casos de serviço. Se a contagem de casos-alvo for uma, o Service Fabric não espera e procede imediatamente com a atualização.<br><br>**Serviço imponente**-- Dentro de um único domínio de upgrade, o Service Fabric tenta garantir que o conjunto de réplicas tem um quórum. O Tecido de Serviço aguarda a disponibilização de um quórum, até um valor máximo de tempo de desaporência (especificado pela propriedade *de verificação de tempo de verificação de réplica).* Se o tempo de 20 horas expirar, o Service Fabric procede com a atualização, independentemente do quórum. Esta definição é definida como nunca (infinita) quando avança e 1200 segundos ao rodar para trás. |
política de saúde de serviço | Mapa codificado JSON com política de saúde tipo de serviço por nome do tipo de serviço. O mapa está vazio. [Formato JSON do parâmetro.](/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). . O JSON para a parte "Value" contém **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService**, e **MaxPercentUnhealthyReplicasPerPartition**. Consulte a secção de Parâmetros Opcionais Visual Studio e PowerShell para obter descrições destes parâmetros.
tempo limite | Especifica o período de tempo de ício em segundos para a operação. Padrão: 60. |
upgrade-domain-timeout | O tempo que cada domínio de atualização tem de completar antes *da execução do FailureAction.* É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. O valor predefinido nunca é (Infinito) e deve ser personalizado adequadamente para a sua aplicação. Predefinição: P10675199DT02H48M05.4775807S. |
upgrade-timeout | O tempo que cada domínio de atualização tem de completar antes *da execução do FailureAction.* É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. O valor predefinido nunca é (Infinito) e deve ser personalizado adequadamente para a sua aplicação. Predefinição: P10675199DT02H48M05.4775807S.|
advertência como erro | Os valores permitidos são **verdadeiros** e **falsos.** O valor predefinido é **falso.** Pode ser passado como uma bandeira. Trate os eventos de saúde de advertência para a aplicação como erros ao avaliar a saúde da aplicação durante a atualização. Por padrão, o Service Fabric não avalia os eventos de saúde de aviso como falhas (erros), pelo que a atualização pode prosseguir mesmo que existam eventos de alerta. |

## <a name="next-steps"></a>Passos seguintes
[A atualização da sua aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) acompanha-o através de uma atualização de aplicações utilizando o Visual Studio.

[Atualizar a sua aplicação Utilizando o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) acompanha-o através de uma atualização de aplicações utilizando o PowerShell.

[Atualizar a sua Aplicação utilizando o Service Fabric CLI no Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) acompanha-o através de uma atualização de aplicações utilizando o Service Fabric CLI.

[Atualizar a sua aplicação utilizando o Service Fabric Eclipse Plugin](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Torne as atualizações da sua aplicação compatíveis aprendendo a utilizar a [Serialização de Dados.](service-fabric-application-upgrade-data-serialization.md)

Aprenda a utilizar funcionalidades avançadas ao atualizar a sua aplicação referindo-se a [Tópicos Avançados.](service-fabric-application-upgrade-advanced.md)

Corrija problemas comuns nas atualizações de aplicações referindo-se aos passos em [Atualizações de Aplicações de Resolução de Problemas](service-fabric-application-upgrade-troubleshooting.md).

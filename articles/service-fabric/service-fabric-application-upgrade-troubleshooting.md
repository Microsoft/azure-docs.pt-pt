---
title: Atualizações de aplicações de resolução de problemas
description: Este artigo aborda algumas questões comuns em torno da atualização de uma aplicação de Tecido de Serviço e como resolvê-las.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75377927"
---
# <a name="troubleshoot-application-upgrades"></a>Resolver problemas da atualização da aplicação

Este artigo aborda algumas das questões comuns em torno da atualização de uma aplicação Azure Service Fabric e como resolvê-las.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Resolução de problemas de uma atualização de aplicação falhada

Quando uma atualização falha, a saída do comando **Get-ServiceFabricApplicationUpgrade** contém informações adicionais para depurar a falha.  A lista a seguir especifica como as informações adicionais podem ser utilizadas:

1. Identifique o tipo de falha.
2. Identifique a razão da falha.
3. Isole um ou mais componentes falhados para uma investigação mais aprofundada.

Esta informação está disponível quando o Service Fabric deteta a falha, independentemente de a **Falha de reação** ser para retroceda ou suspender a atualização.

### <a name="identify-the-failure-type"></a>Identificar o tipo de falha

Na saída do **Get-ServiceFabricApplicationUpgrade,** **o FailureTimestampUtc** identifica o tempotalho (em UTC) no qual foi detetada uma falha de atualização pelo Service Fabric e **falha.** **FailureReason** identifica uma das três potenciais causas de alto nível da falha:

1. UpgradeDomainTimeout - Indica que um determinado domínio de atualização demorou demasiado tempo a ser concluído e **o UpgradeDomainTimeout** expirou.
2. OverallUpgradeTimeout - Indica que a atualização global demorou demasiado tempo a ser concluída e **o UpgradeTimeout** expirou.
3. HealthCheck - Indica que após a atualização de um domínio de atualização, a aplicação permaneceu insalubre de acordo com as políticas de saúde especificadas e **o HealthCheckRetryTimeout** expirou.

Estas entradas só aparecem na saída quando a atualização falha e começa a retrovirar. São apresentadas mais informações dependendo do tipo de avaria.

### <a name="investigate-upgrade-timeouts"></a>Investigue os intervalos de upgrade

As falhas de tempo de atualização são mais frequentemente causadas por problemas de disponibilidade de serviço. A saída que se segue a este parágrafo é típica de atualizações onde réplicas ou instâncias de serviço não começam na nova versão de código. O **campo UpgradeDomainProgressAtFailure** captura uma imagem de qualquer trabalho de upgrade pendente no momento da falha.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

Neste exemplo, a atualização falhou no domínio de upgrade *MYUD1* e duas divisórias (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* e *4b43f4d8-b26b-424e-9307-7a7a62e79750*) ficaram presas. As divisórias ficaram presas porque o tempo de execução não foi capaz de colocar réplicas primárias *(WaitForPrimaryPlacement*) nos nós-alvo *Node1* e *Node4*.

O comando **Get-ServiceFabricNode** pode ser utilizado para verificar se estes dois nós estão no domínio de upgrade *MYUD1*. O *UpgradePhase* diz *que o PostUpgradeSafetyCheck,* o que significa que estas verificações de segurança estão a ocorrer depois de todos os nós no domínio de atualização terem terminado a atualização. Toda esta informação aponta para um problema potencial com a nova versão do código de aplicação. As questões mais comuns são erros de serviço abertos ou de promoção para caminhos de código primário.

Uma *Fase de Upgrade* do *Pré-UpgradeSafetyCheck* significa que houve problemas a preparar o domínio de atualização antes de ser realizado. As questões mais comuns neste caso são erros de serviço na proximidade ou despromoção das vias de código primário.

O **atual UpgradeState** é *RollingBackCompleted*, pelo que a atualização original deve ter sido realizada com uma **Falha de Reversão**, que revirou automaticamente a atualização após falha. Se a atualização original foi realizada com uma **Falha**manual, então a atualização estaria em estado suspenso para permitir a depuração ao vivo da aplicação.

Em casos raros, o campo **UpgradeDomainProgressAtFailure** pode estar vazio se o tempo de atualização geral for necessário, assim como o sistema completa todo o trabalho para o domínio de upgrade atual. Se isso acontecer, tente aumentar os valores dos parâmetros de upgrade **do UpgradeTimeout** e **do UpgradeDomainTimeout** e re-tente a atualização.

### <a name="investigate-health-check-failures"></a>Investigar falhas na verificação de saúde

Falhas na verificação de saúde podem ser desencadeadas por vários problemas que podem acontecer depois de todos os nós num domínio de upgrade terminar a atualização e passar todas as verificações de segurança. A saída que se segue a este parágrafo é típica de uma falha de atualização devido a verificações de saúde falhadas. O campo **Desafetada** de Reavaliações capta uma imagem instantânea dos controlos de saúde que falharam no momento da atualização de acordo com a política de [saúde](service-fabric-health-introduction.md)especificada.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

A investigação de falhas na verificação de saúde requer primeiro uma compreensão do modelo de saúde do Service Fabric. Mas mesmo sem um entendimento tão aprofundado, podemos ver que dois serviços não são saudáveis: *tecido:/DemoApp/Svc3* e *tecido:/DemoApp/Svc2,* juntamente com os relatórios de saúde de erro ("InjectedFault" neste caso). Neste exemplo, dois em cada quatro serviços não são saudáveis, o que é inferior ao objetivo padrão de 0% de insalubres *(MaxPercentUnhealthyServices).*

A atualização foi suspensa após a falha, especificando uma Falha de **Funcionamento** manual ao iniciar a atualização. Este modo permite-nos investigar o sistema ao vivo no estado falhado antes de tomar qualquer outra ação.

### <a name="recover-from-a-suspended-upgrade"></a>Recuperar de uma atualização suspensa

Com uma **Falha de Reversão,** não é necessária recuperação, uma vez que a atualização volta automaticamente a falhar. Com uma **Falha manual, existem**várias opções de recuperação:

1.  desencadear um rollback
2. Proceda através do restante da atualização manualmente
3. Retomar a atualização monitorizada

O comando **Start-ServiceFabricApplicationRollback** pode ser utilizado a qualquer momento para começar a revirar a aplicação. Uma vez que o comando retorna com sucesso, o pedido de reversão foi registado no sistema e começa pouco tempo depois.

O comando **Resume-ServiceFabricApplicationUpgrade** pode ser utilizado para proceder através do restante do upgrade manualmente, um domínio de upgrade de cada vez. Neste modo, apenas as verificações de segurança são efetuadas pelo sistema. Não são realizados mais exames de saúde. Este comando só pode ser utilizado quando o *Estado de Atualização* mostrar *RollingForwardPending*, o que significa que o domínio atual de atualização terminou a atualização, mas o seguinte ainda não começou (pendente).

O comando **Update-ServiceFabricApplicationUpgrade** pode ser utilizado para retomar a atualização monitorizada, com verificações de segurança e saúde a serem realizadas.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

A atualização continua a partir do domínio de upgrade onde foi suspensa pela última vez e usa os mesmos parâmetros de upgrade e políticas de saúde como antes. Se necessário, qualquer um dos parâmetros de atualização e políticas de saúde mostrados na saída anterior pode ser alterado no mesmo comando quando a atualização recomeçar. Neste exemplo, a atualização foi retomada em modo Monitor, com os parâmetros e as políticas de saúde inalteradas.

## <a name="further-troubleshooting"></a>Resolução de problemas adicionais

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>O Service Fabric não está a seguir as políticas de saúde especificadas

Causa possível 1:

O Service Fabric traduz todas as percentagens em número real de entidades (por exemplo, réplicas, divisórias e serviços) para avaliação de saúde e reúne-se sempre para entidades inteiras. Por exemplo, se o *máximo MaxPercentUnhealthyReplicasPerPartition* for de 21% e existirem cinco réplicas, então o Service Fabric permite até duas réplicas pouco saudáveis (isto é, `Math.Ceiling (5*0.21)` ). Assim, as políticas de saúde devem ser definidas em conformidade.

Causa Possível 2:

As políticas de saúde são especificadas em termos de percentagens de serviços totais e não de instâncias de serviço específicas. Por exemplo, antes de uma atualização, se uma aplicação tiver quatro instâncias de serviço A, B, C e D, onde o serviço D não é saudável mas com pouco impacto na aplicação. Queremos ignorar o conhecido serviço insalubre D durante a atualização e definir o parâmetro *MaxPercentUnhealthyServices* para ser de 25%, assumindo apenas A, B e C precisam de ser saudáveis.

No entanto, durante a atualização, D pode tornar-se saudável enquanto C torna-se insalubre. A atualização continuaria a ter sucesso porque apenas 25% dos serviços não são saudáveis. No entanto, pode resultar em erros inesperados devido a C ser inesperadamente insalubre em vez de D. Nesta situação, D deve ser modelado como um tipo de serviço diferente de A, B e C. Uma vez que as políticas de saúde são especificadas por tipo de serviço, diferentes limiares percentuais não saudáveis podem ser aplicados a diferentes serviços. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Não especifiquei uma política de saúde para a atualização de aplicações, mas a atualização ainda falha em alguns intervalos que nunca especifiquei.

Quando as políticas de saúde não são fornecidas ao pedido de upgrade, são retiradas do *ApplicationManifest.xml* da versão atual da aplicação. Por exemplo, se estiver a atualizar a Aplicação X da versão 1.0 para a versão 2.0, são utilizadas as políticas de saúde da aplicação especificadas na versão 1.0. Se uma política de saúde diferente deve ser utilizada para a atualização, então a política deve ser especificada como parte da chamada de API de atualização de aplicações. As políticas especificadas como parte da chamada da API só se aplicam durante a atualização. Uma vez concluída a atualização, as políticas especificadas no *ApplicationManifest.xml* são utilizadas.

### <a name="incorrect-time-outs-are-specified"></a>São especificados intervalos incorretos

Deves ter-te perguntado o que acontece quando os intervalos são definidos de forma inconsistente. Por exemplo, pode ter um *UpgradeTimeout* que é inferior ao *UpgradeDomainTimeout*. A resposta é que um erro é devolvido. Os erros são devolvidos se o *UpgradeDomainTimeout* for inferior à soma de *HealthCheckWaitDuration* e *HealthCheckRetryTimeout*, ou se o *UpgradeDomainTimeout* for inferior à soma de *HealthCheckWaitDuration* e *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>As minhas atualizações estão a demorar muito tempo.

O tempo para uma atualização ser concluída depende dos controlos de saúde e dos intervalos especificados. Os controlos de saúde e os intervalos de tempo dependem do tempo que demora a copiar, implantar e estabilizar a aplicação. Ser demasiado agressivo com os intervalos pode significar mais melhorias falhadas, por isso recomendamos começar de forma conservadora com intervalos mais longos.

Aqui está uma atualização rápida sobre como os intervalos interagem com os tempos de upgrade:

As atualizações para um domínio de upgrade não podem ser concluídas mais rapidamente do que a HealthCheckStableDuration *HealthCheckWaitDuration*  +  *HealthCheckStableDuration*.

A falha de atualização não pode ocorrer mais rapidamente do que *o HealthCheckWaitDuration*  +  *HealthCheckRetryTimeout*.

O tempo de atualização para um domínio de upgrade é limitado por *UpgradeDomainTimeout*.  Se *o HealthCheckRetryTimeout* e *o HealthCheckStableDuration* não forem zero e a saúde da aplicação continuar a mudar para trás e para a frente, então a atualização acaba por ser esgotada no *UpgradeDomainTimeout*. *UpgradeDomainTimeout* começa a contagem rebatida assim que a atualização para o domínio de atualização atual começa.

## <a name="next-steps"></a>Passos seguintes

[A atualização da sua aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) acompanha-o através de uma atualização de aplicações utilizando o Visual Studio.

[Atualizar a sua aplicação Utilizando o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) acompanha-o através de uma atualização de aplicações utilizando o PowerShell.

Controle como a sua aplicação atualiza utilizando [parâmetros de atualização.](service-fabric-application-upgrade-parameters.md)

Torne as atualizações da sua aplicação compatíveis aprendendo a utilizar a [Serialização de Dados.](service-fabric-application-upgrade-data-serialization.md)

Aprenda a utilizar funcionalidades avançadas ao atualizar a sua aplicação referindo-se a [Tópicos Avançados.](service-fabric-application-upgrade-advanced.md)
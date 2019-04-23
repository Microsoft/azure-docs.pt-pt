---
title: Resolução de problemas de atualizações de aplicações | Documentos da Microsoft
description: Este artigo aborda alguns problemas comuns em torno de atualização de uma aplicação do Service Fabric e como resolvê-los.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: e393eb92e11dc8dc296f1dc5f1c0036566c285c5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797864"
---
# <a name="troubleshoot-application-upgrades"></a>Resolver problemas da atualização da aplicação

Este artigo aborda alguns dos problemas comuns relacionados com a atualizar uma aplicação do Azure Service Fabric e como resolvê-los.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Resolver problemas de uma atualização de aplicação que falhou

Quando uma atualização ocorre uma falha, o resultado do **Get-ServiceFabricApplicationUpgrade** comando contém informações adicionais para a falha de depuração.  A lista seguinte especifica como as informações adicionais podem ser usadas:

1. Identifica o tipo de falha.
2. Identifica o motivo da falha.
3. Isole um ou mais componentes de falha para uma investigação mais aprofundada.

Essas informações estão disponíveis quando o Service Fabric Deteta a falha independentemente do **FailureAction** é reverter ou suspender a atualização.

### <a name="identify-the-failure-type"></a>Identificar o tipo de falha

Na saída da **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifica o período de tempo (em UTC) em que foi detetada uma falha de atualização ao Service Fabric e  **FailureAction** foi acionada. **FailureReason** identifica uma das três causas potenciais de alto nível da falha:

1. UpgradeDomainTimeout - indica que um determinado domínio de atualização demorou demasiado tempo a concluir e **UpgradeDomainTimeout** expirou.
2. OverallUpgradeTimeout - indica que a atualização geral demorou demasiado tempo a concluir e **UpgradeTimeout** expirou.
3. HealthCheck - indica que depois de atualizar um domínio de atualização, o aplicativo permaneceu mau estado de funcionamento, de acordo com as políticas de estado de funcionamento especificado e **HealthCheckRetryTimeout** expirou.

Estas entradas só aparecem na saída quando a atualização falha e começa a reversão. São apresentadas informações adicionais consoante o tipo de falha.

### <a name="investigate-upgrade-timeouts"></a>Investigar tempos limite de atualização

Atualize o tempo limite de falhas são normalmente causadas por problemas de disponibilidade do serviço. A saída a seguir a este parágrafo é típica de atualizações em que as réplicas do serviço ou instâncias falharem ao iniciar na nova versão do código. O **UpgradeDomainProgressAtFailure** campo captura um instantâneo de qualquer trabalho de atualização pendente no momento da falha.

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

Neste exemplo, a atualização falhou no domínio de atualização *MYUD1* e duas partições (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* e *4b43f4d8-b26b-424e-9307-7a7a62e79750*) foram bloqueadas. As partições estavam bloqueadas porque o tempo de execução não foi possível colocar as réplicas primárias (*WaitForPrimaryPlacement*) em nós de destino *Node1* e *Nó4*.

O **Get-ServiceFabricNode** comando pode ser utilizado para verificar se estes dois nós estão no domínio de atualização *MYUD1*. O *UpgradePhase* diz *PostUpgradeSafetyCheck*, que significa que essas verificações de segurança estão a ocorrer depois de todos os nós no domínio de atualização tem concluído a atualização. Todas essas informações aponta para um problema em potencial com a nova versão do código da aplicação. Os problemas mais comuns são erros de serviço no aberto ou promoção para caminhos de código principal.

Uma *UpgradePhase* dos *PreUpgradeSafetyCheck* significa que ocorreram problemas a preparar o domínio de atualização para que ele foi efetuado. Neste caso, os problemas mais comuns são erros de serviço no fechamento ou despromoção de caminhos de código principal.

O atual **UpgradeState** é *RollingBackCompleted*, para que a atualização original tem de ter sido feita com uma reversão **FailureAction**, que foi revertida automaticamente Faça uma cópia da atualização após uma falha na. Se a atualização original foi executada com um manual **FailureAction**, em seguida, a atualização seria em vez disso, estar num estado suspenso para permitir ao vivo depuração do aplicativo.

Em casos raros, os **UpgradeDomainProgressAtFailure** campo pode estar vazio se a atualização Geral exceder o tempo limite tal como o sistema conclui todo o trabalho para o domínio de atualização atual. Se isto acontecer, experimente aumentar o **UpgradeTimeout** e **UpgradeDomainTimeout** atualizar valores de parâmetros e repita a atualização.

### <a name="investigate-health-check-failures"></a>Investigar falhas de verificação de estado de funcionamento

Falhas de verificação de estado de funcionamento podem ser acionadas por vários problemas que podem ocorrer todos os nós num domínio de atualização concluída a atualização e transmissão de todas as verificações de segurança. A saída a seguir a este parágrafo é típica de uma falha de atualização devido a verificações de estado de funcionamento com falha. O **UnhealthyEvaluations** campo captura um instantâneo de verificações de estado de funcionamento que falharam no momento da atualização, de acordo com o especificado [política de estado de funcionamento](service-fabric-health-introduction.md).

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

Primeiro investigar falhas de verificação de estado de funcionamento requer uma compreensão do modelo de estado de funcionamento do Service Fabric. Mas, mesmo sem essa uma compreensão aprofundada, podemos ver que os dois serviços estão mau estado de funcionamento: *fabric: / DemoApp/Svc3* e *fabric: / DemoApp/Svc2*, juntamente com os relatórios de estado de funcionamento de erro ("InjectedFault" Neste caso). Neste exemplo, duas de quatro serviços estão danificados, que é inferior ao objetivo do padrão de 0% mau estado de funcionamento (*MaxPercentUnhealthyServices*).

A atualização foi suspensa após falha, especificando uma **FailureAction** manual quando iniciar a atualização. Este modo permite-nos investigar o sistema ao vivo no Estado com falhas antes de fazer qualquer outra ação.

### <a name="recover-from-a-suspended-upgrade"></a>Recuperar a partir de uma atualização suspensa

Com uma reversão **FailureAction**, não há nenhuma necessária, já que a atualização automaticamente reverterá após a falha de recuperação. Com um manual **FailureAction**, há várias opções de recuperação:

1.  acionar uma reversão
2. Percorra o resto da atualização manualmente
3. Retomar a atualização monitorizada

O **Start-ServiceFabricApplicationRollback** comando pode ser utilizado em qualquer altura para iniciar a reverter a aplicação. Quando o comando retorna com êxito, o pedido de reversão foi registado no sistema e é iniciado pouco tempo depois.

O **retomar ServiceFabricApplicationUpgrade** comando pode ser utilizado para percorra o resto da atualização manualmente, um domínio de atualização de cada vez. Neste modo, são executadas verificações de segurança apenas pelo sistema. São executadas verificações do Estado de funcionamento não mais. Este comando só pode ser utilizado quando o *UpgradeState* mostra *RollingForwardPending*, que significa que o domínio de atualização atual concluiu a atualização, mas a próxima Sílaba não iniciado (pendente).

O **ServiceFabricApplicationUpgrade atualização** comando pode ser utilizado para retomar a atualização monitorizada com ambos os segurança e as verificações de estado de funcionamento que está sendo realizada.

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

A atualização continua a partir do domínio de atualização em que foi suspenso pela última vez e utilize o mesmo atualizar parâmetros e as políticas de estado de funcionamento, como antes. Se for necessário, qualquer um dos parâmetros de atualização e políticas de estado de funcionamento apresentadas no resultado anterior pode ser alterado no mesmo comando quando a atualização é retomada. Neste exemplo, a atualização foi retomada no modo de monitorizados, com os parâmetros e as políticas de estado de funcionamento inalteradas.

## <a name="further-troubleshooting"></a>Ainda mais a resolução de problemas

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric não está a seguir as políticas de estado de funcionamento especificado

Causa possível 1:

Traduz-se todas as percentagens em números reais de entidades (por exemplo, as réplicas, partições e serviços) para avaliação de estado de funcionamento e sempre Arredonda por excesso para entidades de todos o Service Fabric. Por exemplo, se o número máximo *MaxPercentUnhealthyReplicasPerPartition* é de 21% e há cinco réplicas, em seguida, o Service Fabric permite até duas réplicas mau estado de funcionamento (ou seja,`Math.Ceiling (5*0.21)`). Portanto, as diretivas de integridade devem ser definidas em conformidade.

Causa possível 2:

Diretivas de integridade são especificadas em termos de porcentagens de serviços total e instâncias de serviço não específico. Por exemplo, antes de uma atualização, se um aplicativo tem quatro instâncias A, B, C e D, do serviço em que o serviço D é mau estado de funcionamento, mas com pouco impacto para a aplicação. Queremos ignorar o serviço de mau estado de funcionamento conhecido D durante a atualização e defina o parâmetro *MaxPercentUnhealthyServices* para ser 25%, supondo que apenas A, B e C tem de ser bom estado de funcionamento.

No entanto, durante a atualização, 1!d pode se tornar íntegro, enquanto o C torna-se com problemas. A atualização seria ainda assim ter sucesso porque apenas 25% dos serviços estão em mau estado de funcionamento. No entanto, poderá resultar em erros inesperados devido a C que está a ser inesperadamente mau estado de funcionamento, em vez de D. Nesta situação, 1!d deve ser modelado como um tipo de serviço diferentes de A, B e C. Uma vez que as diretivas de integridade são especificadas por tipo de serviço, os limiares de percentagem de mau estado de funcionamento de diferentes podem ser aplicadas aos diferentes serviços. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Eu não especificou uma política de estado de funcionamento para a atualização da aplicação, mas a atualização ainda falha por algum tempos limite que eu nunca especificado

Quando as políticas de estado de funcionamento não são fornecidos para a solicitação de atualização, eles são obtidos a partir da *Applicationmanifest* da versão atual do aplicativo. Por exemplo, se estiver a atualizar a aplicação X da versão 1.0 para a versão 2.0, as políticas de estado de funcionamento de aplicações especificado para a versão 1.0 são utilizados. Se uma política de estado de funcionamento de diferentes deve ser usada para a atualização, a política tem de ser especificado como parte da chamada de API de atualização de aplicação. As políticas especificadas como parte da chamada à API só se aplicam durante a atualização. Quando a atualização estiver concluída, as políticas especificadas na *Applicationmanifest* são utilizados.

### <a name="incorrect-time-outs-are-specified"></a>Tempos limite incorreto é especificados

Talvez tenha se perguntado sobre o que acontece quando tempos limite está definidos de forma inconsistente. Por exemplo, pode ter uma *UpgradeTimeout* que menos do que o *UpgradeDomainTimeout*. A resposta é que é devolvido um erro. São devolvidos erros se o *UpgradeDomainTimeout* é inferior a soma dos *HealthCheckWaitDuration* e *HealthCheckRetryTimeout*, ou se  *UpgradeDomainTimeout* é menor do que a soma das *HealthCheckWaitDuration* e *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Meu atualizações demoram demasiado tempo a

O tempo para uma atualização para concluir depende as verificações de estado de funcionamento e tempos limite especificado. Verificações de estado de funcionamento e tempos limite depende do tempo que demora para copiar, implementar e estabilizar o aplicativo. A ser agressivo demais com tempos limite, pode significar mais atualizações com falha, pelo que recomendamos que comece moderadamente com tempos limite mais tempo.

Eis uma recapitulação rápida sobre como o tempo limite de interage com os tempos de atualização:

É atualizado para um domínio de atualização não é possível concluir mais rápido do que *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Não pode ocorrer falha da atualização mais rápido do que *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

O tempo de atualização para um domínio de atualização é limitado pela *UpgradeDomainTimeout*.  Se *HealthCheckRetryTimeout* e *HealthCheckStableDuration* são ambos diferente de zero e o estado de funcionamento da aplicação mantém alternância, em seguida, a atualização, eventualmente, tempo limite no *UpgradeDomainTimeout*. *UpgradeDomainTimeout* começa a contagem verticalmente uma vez, a atualização para o domínio de atualização atual começa.

## <a name="next-steps"></a>Passos Seguintes

[Atualizar a sua aplicação com o Visual Studio](service-fabric-application-upgrade-tutorial.md) explica-lhe uma atualização da aplicação com o Visual Studio.

[Atualizar a sua aplicação utilizar o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) explica-lhe uma atualização da aplicação com o PowerShell.

Controlar a forma como a aplicação seja atualizada com o [atualizar parâmetros](service-fabric-application-upgrade-parameters.md).

Tornar as atualizações de aplicações compatíveis, aprendendo a usar [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como utilizar funcionalidades avançadas ao atualizar a sua aplicação por consultar [tópicos avançados](service-fabric-application-upgrade-advanced.md).
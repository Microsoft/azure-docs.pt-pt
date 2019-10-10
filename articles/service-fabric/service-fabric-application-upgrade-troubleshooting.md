---
title: Solucionando problemas de atualizações de aplicativos | Microsoft Docs
description: Este artigo aborda alguns problemas comuns relacionados à atualização de um aplicativo Service Fabric e como resolvê-los.
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
ms.author: atsenthi
ms.openlocfilehash: f5df528c7e46a5cb2a5df98f0088a451eb08cd6a
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167526"
---
# <a name="troubleshoot-application-upgrades"></a>Resolução de problemas de atualização da aplicação

Este artigo aborda alguns dos problemas comuns relacionados à atualização de um aplicativo Service Fabric do Azure e como resolvê-los.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Solucionar problemas de uma atualização de aplicativo com falha

Quando uma atualização falha, a saída do comando **Get-ServiceFabricApplicationUpgrade** contém informações adicionais para depurar a falha.  A lista a seguir especifica como as informações adicionais podem ser usadas:

1. Identifique o tipo de falha.
2. Identifique o motivo da falha.
3. Isole um ou mais componentes com falha para investigar ainda mais.

Essas informações estão disponíveis quando Service Fabric detecta a falha, independentemente de a **falha** ser revertida ou suspensão da atualização.

### <a name="identify-the-failure-type"></a>Identificar o tipo de falha

Na saída de **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifica o carimbo de data/hora (em UTC) no qual uma falha de atualização foi detectada por Service Fabric e **FailureAction** foi disparado. **FailureReason** identifica uma das três possíveis causas de alto nível da falha:

1. UpgradeDomainTimeout-indica que um determinado domínio de atualização demorou muito para ser concluído e **UpgradeDomainTimeout** expirou.
2. OverallUpgradeTimeout-indica que a atualização geral demorou muito tempo para ser concluída e **UpgradeTimeout** expirou.
3. HealthCheck-indica que depois de atualizar um domínio de atualização, o aplicativo permaneceu não íntegro de acordo com as políticas de integridade especificadas e **HealthCheckRetryTimeout** expirou.

Essas entradas aparecem apenas na saída quando a atualização falha e começa a reverter. Mais informações são exibidas dependendo do tipo de falha.

### <a name="investigate-upgrade-timeouts"></a>Investigar tempos limite de atualização

As falhas de tempo limite de atualização são geralmente causadas por problemas de disponibilidade do serviço. A saída após este parágrafo é típica de atualizações em que as réplicas ou instâncias de serviço falham ao iniciar na nova versão de código. O campo **UpgradeDomainProgressAtFailure** captura um instantâneo de qualquer trabalho de atualização pendente no momento da falha.

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

Neste exemplo, a atualização falhou no domínio de atualização *MYUD1* e duas partições (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* e *4b43f4d8-b26b-424e-9307-7a7a62e79750*) estavam presas. As partições estavam presas porque o tempo de execução não pôde posicionar réplicas primárias (*WaitForPrimaryPlacement*) nos nós de destino *Node1* e *Nó4*.

O comando **Get-ServiceFabricNode** pode ser usado para verificar se esses dois nós estão no domínio de atualização *MYUD1*. O *UpgradePhase* diz *PostUpgradeSafetyCheck*, o que significa que essas verificações de segurança estão ocorrendo depois que todos os nós no domínio de atualização concluíram a atualização. Todas essas informações apontam para um problema potencial com a nova versão do código do aplicativo. Os problemas mais comuns são erros de serviço na abertura ou promoção para caminhos de código primários.

Um *UpgradePhase* de *PreUpgradeSafetyCheck* significa que houve problemas ao preparar o domínio de atualização antes que ele fosse executado. Os problemas mais comuns nesse caso são erros de serviço no fechamento ou rebaixamento dos caminhos de código primários.

O **upgradestate** atual é *RollingBackCompleted*, portanto, a atualização original deve ter sido executada com uma reversão **FailureAction**, que reverteva automaticamente a atualização em caso de falha. Se a atualização original foi executada com uma **falhaaction**manual, então a atualização estaria em um estado suspenso para permitir a depuração dinâmica do aplicativo.

Em casos raros, o campo **UpgradeDomainProgressAtFailure** pode estar vazio se a atualização geral atingir o tempo limite, assim como o sistema concluir todo o trabalho para o domínio de atualização atual. Se isso acontecer, tente aumentar os valores do parâmetro de atualização **UpgradeTimeout** e **UpgradeDomainTimeout** e repita a atualização.

### <a name="investigate-health-check-failures"></a>Investigar falhas de verificação de integridade

Falhas de verificação de integridade podem ser disparadas por vários problemas que podem ocorrer depois que todos os nós em um domínio de atualização concluírem a atualização e passarem todas as verificações de segurança. A saída após este parágrafo é típica de uma falha de atualização devido a falhas nas verificações de integridade. O campo **UnhealthyEvaluations** captura um instantâneo das verificações de integridade que falharam no momento da atualização de acordo com a [política de integridade](service-fabric-health-introduction.md)especificada.

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

A investigação de falhas de verificação de integridade requer primeiro uma compreensão do modelo de integridade Service Fabric. Mas mesmo sem uma compreensão aprofundada, podemos ver que dois serviços não estão íntegros: *Fabric:/demoApp/Svc3* e *Fabric:/demoApp/Svc2*, juntamente com os relatórios de integridade de erro ("InjectedFault" nesse caso). Neste exemplo, dois dos quatro serviços não estão íntegros, que está abaixo do destino padrão de 0% não íntegro (*MaxPercentUnhealthyServices*).

A atualização foi suspensa após falha, especificando uma **falha** de um manual ao iniciar a atualização. Esse modo nos permite investigar o sistema ao vivo no estado de falha antes de executar qualquer ação adicional.

### <a name="recover-from-a-suspended-upgrade"></a>Recuperar de uma atualização suspensa

Com uma reversão **FailureAction**, não há nenhuma recuperação necessária, pois a atualização é automaticamente revertida após a falha. Com uma **falha**manual, há várias opções de recuperação:

1.  disparar uma reversão
2. Prosseguir com o restante da atualização manualmente
3. Retomar a atualização monitorada

O comando **Start-ServiceFabricApplicationRollback** pode ser usado a qualquer momento para iniciar a reversão do aplicativo. Depois que o comando retornar com êxito, a solicitação de reversão foi registrada no sistema e iniciada logo depois.

O comando **resume-ServiceFabricApplicationUpgrade** pode ser usado para prosseguir no restante da atualização manualmente, um domínio de atualização por vez. Nesse modo, somente as verificações de segurança são executadas pelo sistema. Não são executadas mais verificações de integridade. Esse comando só pode ser usado quando o *upgradestate* mostra *RollingForwardPending*, o que significa que o domínio de atualização atual concluiu a atualização, mas o próximo não foi iniciado (pendente).

O comando **Update-ServiceFabricApplicationUpgrade** pode ser usado para retomar a atualização monitorada com verificações de segurança e de integridade que estão sendo executadas.

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

A atualização continua do domínio de atualização em que foi suspensa pela última vez e usa os mesmos parâmetros de atualização e políticas de integridade que antes. Se necessário, qualquer um dos parâmetros de atualização e as políticas de integridade mostrados na saída anterior podem ser alterados no mesmo comando quando a atualização é retomada. Neste exemplo, a atualização foi retomada no modo monitorado, com os parâmetros e as políticas de integridade inalteradas.

## <a name="further-troubleshooting"></a>Mais soluções de problemas

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric não está seguindo as políticas de integridade especificadas

Causa possível 1:

Service Fabric converte todas as porcentagens em números reais de entidades (por exemplo, réplicas, partições e serviços) para avaliação de integridade e sempre arredonda para as entidades inteiras. Por exemplo, se o máximo de *MaxPercentUnhealthyReplicasPerPartition* for 21% e houver cinco réplicas, Service Fabric permitirá até duas réplicas não íntegras (ou seja, `Math.Ceiling (5*0.21)`). Portanto, as políticas de integridade devem ser definidas adequadamente.

Causa possível 2:

As políticas de integridade são especificadas em termos de percentuais do total de serviços e não de instâncias de serviço específicas. Por exemplo, antes de uma atualização, se um aplicativo tiver quatro instâncias de serviço A, B, C e D, em que o serviço D não está íntegro, mas com pouco impacto no aplicativo. Queremos ignorar o serviço conhecido não íntegro D durante a atualização e definir o parâmetro *MaxPercentUnhealthyServices* como 25%, pressupondo que apenas um, B e C precisem ser íntegros.

No entanto, durante a atualização, a D pode se tornar íntegra enquanto C se tornar não íntegro. A atualização ainda terá sucesso porque apenas 25% dos serviços não estão íntegros. No entanto, isso pode resultar em erros não previstos devido à falta de integridade inesperada do C em vez de D. Nessa situação, D deve ser modelado como um tipo de serviço diferente de A, B e C. Como as políticas de integridade são especificadas por tipo de serviço, diferentes limites de porcentagem não íntegros podem ser aplicados a diferentes serviços. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Eu não especifiquei uma política de integridade para a atualização do aplicativo, mas a atualização ainda falha por alguns tempos limite que nunca especifiquei

Quando as políticas de integridade não são fornecidas à solicitação de atualização, elas são obtidas do *ApplicationManifest. xml* da versão atual do aplicativo. Por exemplo, se você estiver atualizando o aplicativo X da versão 1,0 para a versão 2,0, as políticas de integridade do aplicativo especificadas para na versão 1,0 serão usadas. Se uma política de integridade diferente deve ser usada para a atualização, a política precisa ser especificada como parte da chamada à API de atualização de aplicativo. As políticas especificadas como parte da chamada à API só se aplicam durante a atualização. Quando a atualização for concluída, as políticas especificadas no *ApplicationManifest. xml* serão usadas.

### <a name="incorrect-time-outs-are-specified"></a>Foram especificados tempos limite incorretos

Talvez você tenha se perguntou sobre o que acontece quando os tempos limite são definidos de forma inconsistente. Por exemplo, você pode ter um *UpgradeTimeout* que seja menor do que o *UpgradeDomainTimeout*. A resposta é que um erro é retornado. Os erros serão retornados se *UpgradeDomainTimeout* for menor que a soma de *HealthCheckWaitDuration* e *HealthCheckRetryTimeout*, ou se *UpgradeDomainTimeout* for menor que a soma de *HealthCheckWaitDuration* e *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Minhas atualizações estão demorando muito

O tempo de conclusão de uma atualização depende das verificações de integridade e dos tempos limite especificados. Verificações de integridade e tempos limite dependem de quanto tempo leva para copiar, implantar e estabilizar o aplicativo. Ser muito agressivo com tempos limite pode significar mais atualizações com falha, portanto, recomendamos a inicialização conservadora com tempos limite mais longos.

Aqui está um reartigo rápido sobre como os tempos limite interagem com os tempos de atualização:

As atualizações para um domínio de atualização não podem ser concluídas mais rápido do que *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

A falha de atualização não pode ocorrer mais rápido do que *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

O tempo de atualização para um domínio de atualização é limitado por *UpgradeDomainTimeout*.  Se *HealthCheckRetryTimeout* e *HealthCheckStableDuration* forem diferentes de zero e a integridade do aplicativo continuar alternando e em frente, a atualização eventualmente atingirá o tempo limite em *UpgradeDomainTimeout*. *UpgradeDomainTimeout* inicia a contagem de baixo quando a atualização para o domínio de atualização atual começa.

## <a name="next-steps"></a>Passos seguintes

[Atualizar seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante uma atualização de aplicativo usando o Visual Studio.

[Atualizar seu aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você durante uma atualização de aplicativo usando o PowerShell.

Controle como o aplicativo é atualizado usando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Faça com que o aplicativo seja atualizado de forma compatível aprendendo a usar a [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando [Tópicos avançados](service-fabric-application-upgrade-advanced.md).
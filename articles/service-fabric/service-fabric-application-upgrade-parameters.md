---
title: 'Atualização de aplicativo: parâmetros de atualização | Microsoft Docs'
description: Descreve os parâmetros relacionados à atualização de um aplicativo Service Fabric, incluindo verificações de integridade a serem executadas e políticas para desfazer a atualização automaticamente.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: atsenthi
ms.openlocfilehash: 6276f347f0cc0ecc03acc95282e83c243679957a
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802426"
---
# <a name="application-upgrade-parameters"></a>Parâmetros da atualização da aplicação
Este artigo descreve os vários parâmetros que se aplicam durante a atualização de um aplicativo de Service Fabric do Azure. Os parâmetros de atualização de aplicativo controlam os tempos limite e as verificações de integridade que são aplicadas durante a atualização e especificam as políticas que devem ser aplicadas quando uma atualização falha. Os parâmetros do aplicativo se aplicam a atualizações usando:
- PowerShell
- Visual Studio
- SFCTL
- [REST](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

As atualizações de aplicativo são iniciadas por meio de um dos três modos de atualização selecionáveis pelo usuário. Cada modo tem seu próprio conjunto de parâmetros de aplicativo:
- Monitora
- Automático não monitorado
- Manual não monitorado

Os parâmetros obrigatórios e opcionais aplicáveis são descritos em cada seção da seguinte maneira.

## <a name="visual-studio-and-powershell-parameters"></a>Parâmetros do Visual Studio e do PowerShell

Service Fabric atualizações de aplicativo usando o PowerShell, use o comando [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) . O modo de atualização é selecionado passando o parâmetro **Monitored**, **UnmonitoredAuto**ou **UnmonitoredManual** para [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

O Visual Studio Service Fabric parâmetros de atualização de aplicativos são definidos por meio da caixa de diálogo Configurações de atualização do Visual Studio. O modo de atualização do Visual Studio é selecionado usando a caixa suspensa **modo de atualização** para **monitorado**, **UnmonitoredAuto**ou **UnmonitoredManual**. Para obter mais informações, consulte [Configurar a atualização de um aplicativo Service Fabric no Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Parâmetros necessários
(PS = PowerShell, VS = Visual Studio)

| Parâmetro | Aplica-se a | Descrição |
| --- | --- | --- |
applicationName |PS| Nome do aplicativo que está sendo atualizado. Exemplos: Fabric:/VisualObjects, Fabric:/ClusterMonitor. |
ApplicationTypeVersion|PS|A versão do tipo de aplicativo para a qual a atualização se destina. |
FailureAction |PS, VS|Os valores permitidos são **Rollback**, **manual**e **Invalid**. A ação de compensação a ser executada quando uma atualização *monitorada* encontrar uma política de monitoramento ou violações de política de integridade. <br>A **reversão** especifica que a atualização será revertida automaticamente para a versão anterior à atualização. <br>**Manual** indica que a atualização mudará para o modo de atualização *UnmonitoredManual* . <br>**Inválido** indica que a ação de falha é inválida.|
Monitora |PS|Indica que o modo de atualização é monitorado. Depois que o cmdlet concluir uma atualização para um domínio de atualização, se a integridade do domínio de atualização e o cluster atenderem às políticas de integridade que você definir, Service Fabric atualizará o próximo domínio de atualização. Se o domínio ou o cluster de atualização falhar ao atender às políticas de integridade, a atualização falhará e Service Fabric reverterá a atualização para o domínio de atualização ou será revertida para o modo manual de acordo com a política especificada. Este é o modo recomendado para atualizações de aplicativo em um ambiente de produção. |
UpgradeMode | VS | Os valores permitidos são **monitorado** (padrão), **UnmonitoredAuto**ou **UnmonitoredManual**. Consulte parâmetros do PowerShell para cada modo neste artigo para obter detalhes. |
UnmonitoredAuto | PS | Indica que o modo de atualização não é monitorado automaticamente. Depois que Service Fabric atualiza um domínio de atualização, Service Fabric atualiza o próximo domínio de atualização, independentemente do estado de integridade do aplicativo. Esse modo não é recomendado para produção e só é útil durante o desenvolvimento de um aplicativo. |
UnmonitoredManual | PS | Indica que o modo de atualização é manual não monitorado. Depois que Service Fabric atualiza um domínio de atualização, ele aguarda que você atualize o próximo domínio de atualização usando o cmdlet *resume-ServiceFabricApplicationUpgrade* . |

### <a name="optional-parameters"></a>Parâmetros opcionais

Os parâmetros de avaliação de integridade são opcionais. Se os critérios de avaliação de integridade não forem especificados quando uma atualização for iniciada, Service Fabric usará as políticas de integridade do aplicativo especificadas no ApplicationManifest. XML da instância do aplicativo.

Use a barra de rolagem horizontal na parte inferior da tabela para exibir o campo Descrição completa.

(PS = PowerShell, VS = Visual Studio)

> [!div class="mx-tdBreakAll"]
> | Parâmetro | Aplica-se a | Descrição |
> | --- | --- | --- |
> | ApplicationParameter |PS, VS| Especifica as substituições para os parâmetros do aplicativo.<br>Os parâmetros de aplicativo do PowerShell são especificados como pares de nome/valor de Hashtable. Por exemplo, @ {"VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1"}.<br>Os parâmetros do aplicativo do Visual Studio podem ser especificados na caixa de diálogo Publicar Service Fabric aplicativo no campo **arquivo de parâmetros do aplicativo** .
> | Confirmar |PS| Os valores permitidos são **true** e **false**. Solicita confirmação antes de executar o cmdlet. |
> | ConsiderWarningAsError |PS, VS |Os valores permitidos são **true** e **false**. O valor predefinido é **Falso**. Trate os eventos de integridade de aviso para o aplicativo como erros ao avaliar a integridade do aplicativo durante a atualização. Por padrão, Service Fabric não avalia eventos de integridade de aviso como falhas (erros), portanto, a atualização pode continuar mesmo que haja eventos de aviso. |
> | DefaultServiceTypeHealthPolicy | PS, VS |Especifica a política de integridade para o tipo de serviço padrão a ser usado para a atualização monitorada no formato MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Por exemplo, 5, 10, 15 indica os seguintes valores: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Aplicação | PS, VS | Os valores permitidos são **true** e **false**. Indica que o processo de atualização ignora a mensagem de aviso e força a atualização mesmo quando o número de versão não foi alterado. Isso é útil para testes locais, mas não é recomendado para uso em um ambiente de produção, pois requer a remoção da implantação existente, o que causa perda de dados em tempo hábil e em potencial. |
> | ForceRestart |PS, VS |Se você atualizar uma configuração ou um pacote de dados sem Atualizar o código do serviço, o serviço será reiniciado somente se a propriedade ForceRestart estiver definida como **true**. Quando a atualização for concluída, o Service Fabric notificará o serviço de que um novo pacote de configuração ou pacote de dados está disponível. O serviço é responsável por aplicar as alterações. Se necessário, o serviço pode ser reiniciado. |
> | HealthCheckRetryTimeoutSec |PS, VS |A duração (em segundos) que Service Fabric continua a executar a avaliação de integridade antes de declarar a atualização como falha. O padrão é 600 segundos. Essa duração começa depois que *HealthCheckWaitDurationSec* é atingido. Nesse *HealthCheckRetryTimeout*, Service Fabric pode executar várias verificações de integridade da integridade do aplicativo. O valor padrão é 10 minutos e deve ser personalizado adequadamente para seu aplicativo. |
> | HealthCheckStableDurationSec |PS, VS |A duração (em segundos) para verificar se o aplicativo está estável antes de passar para o próximo domínio de atualização ou concluir a atualização. Essa duração de espera é usada para impedir alterações não detectadas de integridade logo depois que a verificação de integridade é executada. O valor padrão é 120 segundos e deve ser personalizado adequadamente para seu aplicativo. |
> | HealthCheckWaitDurationSec |PS, VS | O tempo de espera (em segundos) após a conclusão da atualização no domínio de atualização antes que Service Fabric avalie a integridade do aplicativo. Essa duração também pode ser considerada como o tempo que um aplicativo deve ser executado antes que possa ser considerado íntegro. Se a verificação de integridade for aprovada, o processo de atualização prosseguirá para o próximo domínio de atualização.  Se a verificação de integridade falhar, Service Fabric aguardará [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) antes de tentar novamente a verificação de integridade até que o *HealthCheckRetryTimeoutSec* seja atingido. O valor padrão e recomendado é 0 segundos. |
> | MaxPercentUnhealthyDeployedApplications|PS, VS |O valor padrão e recomendado é 0. Especifique o número máximo de aplicativos implantados (consulte a [seção integridade](service-fabric-health-introduction.md)) que podem não estar íntegros antes que o aplicativo seja considerado não íntegro e falhe na atualização. Esse parâmetro define a integridade do aplicativo no nó e ajuda a detectar problemas durante a atualização. Normalmente, as réplicas do aplicativo obtêm balanceamento de carga para o outro nó, o que permite que o aplicativo pareça íntegro, permitindo que a atualização continue. Ao especificar uma integridade *MaxPercentUnhealthyDeployedApplications* estrita, Service Fabric pode detectar um problema com o pacote de aplicativos rapidamente e ajudar a produzir uma atualização Fail Fast. |
> | MaxPercentUnhealthyServices |PS, VS |Um parâmetro para *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. O valor padrão e recomendado é 0. Especifique o número máximo de serviços na instância do aplicativo que pode não estar íntegro antes que o aplicativo seja considerado não íntegro e falhe na atualização. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |Um parâmetro para *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. O valor padrão e recomendado é 0. Especifique o número máximo de partições em um serviço que podem não estar íntegras antes que o serviço seja considerado não íntegro. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |Um parâmetro para *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. O valor padrão e recomendado é 0. Especifique o número máximo de réplicas na partição que podem não estar íntegras antes que a partição seja considerada não íntegra. |
> | ServiceTypeHealthPolicyMap | PS, VS | Representa a política de integridade usada para avaliar a integridade dos serviços pertencentes a um tipo de serviço. Usa uma entrada de tabela de hash no seguinte formato: @ {"createtypename": "MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices"} por exemplo: @ {"ServiceTypeName01" = "5, 10, 5"; "ServiceTypeName02" = "5, 5, 5"} |
> | TimeoutSec | PS, VS | Especifica o período de tempo limite em segundos para a operação. |
> | UpgradeDomainTimeoutSec |PS, VS |Tempo máximo (em segundos) para atualizar um único domínio de atualização. Se esse tempo limite for atingido, a atualização será interrompida e continuará com base na configuração de *falhaaction*. O valor padrão nunca é (infinito) e deve ser personalizado adequadamente para seu aplicativo. |
> | UpgradeReplicaSetCheckTimeoutSec |PS, VS |Medido em segundos.<br>**Serviço sem estado**– em um único domínio de atualização, Service Fabric tenta garantir que instâncias adicionais do serviço estejam disponíveis. Se a contagem de instâncias de destino for mais de uma, Service Fabric aguardará que mais de uma instância esteja disponível, até um valor de tempo limite máximo. Esse tempo limite é especificado usando a propriedade *UpgradeReplicaSetCheckTimeoutSec* . Se o tempo limite expirar, Service Fabric continuará com a atualização, independentemente do número de instâncias de serviço. Se a contagem de instâncias de destino for uma, Service Fabric não aguardará e continuará imediatamente com a atualização.<br><br>**Serviço com estado**– em um único domínio de atualização, Service Fabric tenta garantir que o conjunto de réplicas tenha um quorum. Service Fabric aguarda a disponibilidade de um quorum, até um valor de tempo limite máximo (especificado pela propriedade *UpgradeReplicaSetCheckTimeoutSec* ). Se o tempo limite expirar, Service Fabric continuará com a atualização, independentemente do quorum. Essa configuração é definida como nunca (infinito) ao rolar para frente e 1200 segundos durante a reversão. |
> | UpgradeTimeoutSec |PS, VS |Um tempo limite (em segundos) que se aplica a toda a atualização. Se esse tempo limite for atingido, a atualização será interrompida e a *falhaaction* será disparada. O valor padrão nunca é (infinito) e deve ser personalizado adequadamente para seu aplicativo. |
> | WhatIf | PS | Os valores permitidos são **true** e **false**. Mostra o que aconteceria se o cmdlet fosse executado. O cmdlet não é executado. |

Os critérios *MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService*e *MaxPercentUnhealthyReplicasPerPartition* podem ser especificados por tipo de serviço para uma instância do aplicativo. Definir esses parâmetros por serviço permite que um aplicativo contenha tipos de serviços diferentes com políticas de avaliação diferentes. Por exemplo, um tipo de serviço de gateway sem estado pode ter um *MaxPercentUnhealthyPartitionsPerService* que seja diferente de um tipo de serviço de mecanismo com estado para uma instância de aplicativo específica.

## <a name="sfctl-parameters"></a>Parâmetros de SFCTL

Service Fabric atualizações de aplicativo usando a CLI Service Fabric use o comando [sfctl Application upgrade](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) junto com os seguintes parâmetros obrigatórios e opcionais.

### <a name="required-parameters"></a>Parâmetros necessários

| Parâmetro | Descrição |
| --- | --- |
| ID do aplicativo  |ID do aplicativo que está sendo atualizado. <br> Normalmente, é o nome completo do aplicativo sem o esquema de URI "Fabric:". A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere ' \~ '. Por exemplo, se o nome do aplicativo for ' Fabric:/MyApp/App1 ', a identidade do aplicativo será ' MyApp\~App1 ' em 6.0 + e ' MyApp/App1 ' nas versões anteriores.|
versão do aplicativo |A versão do tipo de aplicativo para a qual a atualização se destina.|
parâmetros  |Uma lista codificada em JSON de substituições de parâmetro de aplicativo a serem aplicadas ao atualizar o aplicativo.|

### <a name="optional-parameters"></a>Parâmetros opcionais

| Parâmetro | Descrição |
| --- | --- |
política de integridade de serviço padrão | Especificação codificada em [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) da política de integridade usada por padrão para avaliar a integridade de um tipo de serviço. O mapa está vazio por padrão. |
ação de falha | Os valores permitidos são **Rollback**, **manual**e **Invalid**. A ação de compensação a ser executada quando uma atualização *monitorada* encontrar uma política de monitoramento ou violações de política de integridade. <br>A **reversão** especifica que a atualização será revertida automaticamente para a versão anterior à atualização. <br>**Manual** indica que a atualização mudará para o modo de atualização *UnmonitoredManual* . <br>**Inválido** indica que a ação de falha é inválida.|
forçar reinicialização | Se você atualizar uma configuração ou um pacote de dados sem Atualizar o código do serviço, o serviço será reiniciado somente se a propriedade ForceRestart estiver definida como **true**. Quando a atualização for concluída, o Service Fabric notificará o serviço de que um novo pacote de configuração ou pacote de dados está disponível. O serviço é responsável por aplicar as alterações. Se necessário, o serviço pode ser reiniciado. |
integridade – verificação de repetição-tempo limite | A quantidade de tempo para tentar a avaliação de integridade novamente quando o aplicativo ou o cluster não está íntegro antes de a *falha* ser executada. Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. Padrão: PT0H10M0S. |
integridade-verificação-duração estável | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização prossiga para o próximo domínio de atualização. Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. Padrão: PT0H2M0S. |
integridade-verificação-duração de espera | A quantidade de tempo de espera após a conclusão de um domínio de atualização antes de aplicar as políticas de integridade. Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. Padrão: 0.|
Max – não íntegro – aplicativos | O valor padrão e recomendado é 0. Especifique o número máximo de aplicativos implantados (consulte a [seção integridade](service-fabric-health-introduction.md)) que podem não estar íntegros antes que o aplicativo seja considerado não íntegro e falhe na atualização. Esse parâmetro define a integridade do aplicativo no nó e ajuda a detectar problemas durante a atualização. Normalmente, as réplicas do aplicativo obtêm balanceamento de carga para o outro nó, o que permite que o aplicativo pareça íntegro, permitindo que a atualização continue. Ao especificar uma integridade *máxima estrita – aplicativos não íntegros* , Service Fabric pode detectar um problema com o pacote de aplicativos rapidamente e ajudar a produzir uma atualização Fail Fast. Representado como um número entre 0 e 100. |
Moda | Os valores permitidos são **Monitored**, **upgrademode**, **UnmonitoredAuto**, **UnmonitoredManual**. O padrão é **UnmonitoredAuto**. Consulte a seção *parâmetros necessários* do Visual Studio e do PowerShell para obter descrições desses valores.|
réplica-Set-check-Timeout |Medido em segundos. <br>**Serviço sem estado**– em um único domínio de atualização, Service Fabric tenta garantir que instâncias adicionais do serviço estejam disponíveis. Se a contagem de instâncias de destino for mais de uma, Service Fabric aguardará que mais de uma instância esteja disponível, até um valor de tempo limite máximo. Esse tempo limite é especificado usando a propriedade *réplica-Set-check-Timeout* . Se o tempo limite expirar, Service Fabric continuará com a atualização, independentemente do número de instâncias de serviço. Se a contagem de instâncias de destino for uma, Service Fabric não aguardará e continuará imediatamente com a atualização.<br><br>**Serviço com estado**– em um único domínio de atualização, Service Fabric tenta garantir que o conjunto de réplicas tenha um quorum. Service Fabric aguarda a disponibilidade de um quorum, até um valor de tempo limite máximo (especificado pela propriedade *réplica-Set-check-Timeout* ). Se o tempo limite expirar, Service Fabric continuará com a atualização, independentemente do quorum. Essa configuração é definida como nunca (infinito) ao rolar para frente e 1200 segundos durante a reversão. |
serviço-integridade-política | Mapa codificado em JSON com política de integridade do tipo de serviço por nome de tipo de serviço. O mapa está vazio como padrão. [Formato JSON de parâmetro.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). O JSON para a parte "value" contém **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService**e **MaxPercentUnhealthyReplicasPerPartition**. Consulte a seção de parâmetros opcionais do Visual Studio e do PowerShell para obter descrições desses parâmetros.
tempo limite | Especifica o período de tempo limite em segundos para a operação. Padrão: 60. |
atualização-tempo limite do domínio | A quantidade de tempo que cada domínio de atualização precisa concluir antes que a *falha* seja executada. Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. O valor padrão nunca é (infinito) e deve ser personalizado adequadamente para seu aplicativo. Padrão: P10675199DT02H48M 05.4775807 S. |
atualização-tempo limite | A quantidade de tempo que cada domínio de atualização precisa concluir antes que a *falha* seja executada. Ele é primeiro interpretado como uma cadeia de caracteres que representa uma duração de ISO 8601. Se isso falhar, ele será interpretado como um número que representa o número total de milissegundos. O valor padrão nunca é (infinito) e deve ser personalizado adequadamente para seu aplicativo. Padrão: P10675199DT02H48M 05.4775807 S.|
aviso como erro | Os valores permitidos são **true** e **false**. O valor predefinido é **Falso**. Pode ser passado como um sinalizador. Trate os eventos de integridade de aviso para o aplicativo como erros ao avaliar a integridade do aplicativo durante a atualização. Por padrão, Service Fabric não avalia eventos de integridade de aviso como falhas (erros), portanto, a atualização pode continuar mesmo que haja eventos de aviso. |

## <a name="next-steps"></a>Passos seguintes
[Atualizar seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante uma atualização de aplicativo usando o Visual Studio.

[Atualizar seu aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você durante uma atualização de aplicativo usando o PowerShell.

[Atualizar seu aplicativo usando Service Fabric CLI no Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) orienta você pela atualização do aplicativo usando Service Fabric CLI.

[Atualizando seu aplicativo usando Service Fabric plug-in Eclipse](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Faça com que o aplicativo seja atualizado de forma compatível aprendendo a usar a [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativos consultando as etapas em [solução de problemas de atualizações de aplicativos](service-fabric-application-upgrade-troubleshooting.md).

---
title: Tópicos avançados de atualização de aplicativos
description: Este artigo aborda alguns tópicos avançados referentes à atualização de um aplicativo Service Fabric.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: bd95d651e02cb61bcbe7a108db92afce8b5484bd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457523"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Atualização do aplicativo Service Fabric: Tópicos avançados
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Adicionando ou removendo tipos de serviço durante uma atualização de aplicativo
Se um novo tipo de serviço for adicionado a um aplicativo publicado como parte de uma atualização, o novo tipo de serviço será adicionado ao aplicativo implantado. Essa atualização não afeta nenhuma das instâncias de serviço que já faziam parte do aplicativo, mas uma instância do tipo de serviço que foi adicionada deve ser criada para que o novo tipo de serviço esteja ativo (consulte [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Da mesma forma, os tipos de serviço podem ser removidos de um aplicativo como parte de uma atualização. No entanto, todas as instâncias de serviço do tipo de serviço a ser removido devem ser removidas antes de prosseguir com a atualização (consulte [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Modo de atualização manual
> [!NOTE]
> O modo de atualização *monitorado* é recomendado para todas as atualizações de Service Fabric.
> O modo de atualização *UnmonitoredManual* deve ser considerado somente para atualizações com falha ou suspensas. 
>
>

No modo *monitorado* , Service Fabric aplica as políticas de integridade para garantir que o aplicativo esteja íntegro à medida que a atualização progride. Se as políticas de integridade forem violadas, a atualização será suspensa ou automaticamente revertida, dependendo da *falhaaction*especificada.

No modo *UnmonitoredManual* , o administrador do aplicativo tem total controle sobre a progressão da atualização. Esse modo é útil ao aplicar políticas de avaliação de integridade personalizadas ou executar atualizações não convencionais para ignorar completamente o monitoramento de integridade (por exemplo, o aplicativo já está em perda de dados). Uma atualização em execução neste modo será suspensa após a conclusão de cada UD e deverá ser retomada explicitamente usando [resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Quando uma atualização é suspensa e está pronta para ser retomada pelo usuário, seu estado de atualização mostrará *RollforwardPending* (consulte [upgradestate](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Por fim, o modo *UnmonitoredAuto* é útil para executar iterações de atualização rápidas durante o desenvolvimento ou teste de serviço, já que nenhuma entrada do usuário é necessária e nenhuma política de integridade do aplicativo é avaliada.

## <a name="upgrade-with-a-diff-package"></a>Atualizar com um pacote diff
Em vez de provisionar um pacote de aplicativos completo, as atualizações também podem ser realizadas pelo provisionamento de pacotes diff que contêm apenas os pacotes atualizados de código/configuração/dados, juntamente com o manifesto completo do aplicativo e os manifestos do serviço completo. Os pacotes de aplicativos completos só são necessários para a instalação inicial de um aplicativo no cluster. As atualizações subsequentes podem ser de pacotes de aplicativos completos ou de pacotes diff.  

Qualquer referência no manifesto do aplicativo ou nos manifestos do serviço de um pacote diff que não pode ser encontrado no pacote de aplicativos é substituída automaticamente pela versão provisionada no momento.

Os cenários para usar um pacote diff são:

* Quando você tem um pacote de aplicativo grande que faz referência a vários arquivos de manifesto do serviço e/ou vários pacotes de código, pacotes de configuração ou pacotes de dados.
* Quando você tem um sistema de implantação que gera o layout de compilação diretamente do processo de compilação do aplicativo. Nesse caso, embora o código não tenha sido alterado, os assemblies recém-criados obtêm uma soma de verificação diferente. O uso de um pacote de aplicativos completo exigirá que você atualize a versão em todos os pacotes de código. Usando um pacote diff, você só fornece os arquivos que foram alterados e os arquivos de manifesto em que a versão foi alterada.

Quando um aplicativo é atualizado usando o Visual Studio, um pacote diff é publicado automaticamente. Para criar um pacote de comparação manualmente, o manifesto do aplicativo e os manifestos do serviço devem ser atualizados, mas somente os pacotes alterados devem ser incluídos no pacote de aplicativo final.

Por exemplo, vamos começar com o seguinte aplicativo (números de versão fornecidos para facilitar a compreensão):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Vamos supor que você quisesse atualizar apenas o pacote de códigos de Service1 usando um pacote diff. Seu aplicativo atualizado tem as seguintes alterações de versão:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Nesse caso, você atualiza o manifesto do aplicativo para 2.0.0 e o manifesto do serviço para Service1 para refletir a atualização do pacote de códigos. A pasta do seu pacote de aplicativos teria a seguinte estrutura:

```text
app1/
  service1/
    code/
```

Em outras palavras, crie um pacote de aplicativo completo normalmente e, em seguida, remova todas as pastas de pacote de código/config/de dados para as quais a versão não foi alterada.

## <a name="upgrade-application-parameters-independently-of-version"></a>Atualizar parâmetros do aplicativo independentemente da versão

Às vezes, é desejável alterar os parâmetros de um aplicativo Service Fabric sem alterar a versão do manifesto. Isso pode ser feito convenientemente usando o sinalizador **-ApplicationParameter** com o cmdlet **Start-ServiceFabricApplicationUpgrade** do PowerShell do Azure Service Fabric. Suponha um aplicativo Service Fabric com as seguintes propriedades:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Agora, atualize o aplicativo usando o cmdlet **Start-ServiceFabricApplicationUpgrade** . Este exemplo mostra uma atualização monitorada, mas uma atualização não monitorada também pode ser usada. Para ver uma descrição completa dos sinalizadores aceitos por esse cmdlet, consulte a [referência de módulo do PowerShell Service Fabric do Azure](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Após a atualização, confirme se o aplicativo tem os parâmetros atualizados e a mesma versão:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="rolling-back-application-upgrades"></a>Revertendo atualizações de aplicativos

Embora as atualizações possam ser roladas para frente em um dos três modos (*monitorado*, *UnmonitoredAuto*ou *UnmonitoredManual*), elas só podem ser revertidas no modo *UnmonitoredAuto* ou *UnmonitoredManual* . A reversão no modo *UnmonitoredAuto* funciona da mesma maneira que o progressivo com a exceção de que o valor padrão de *UpgradeReplicaSetCheckTimeout* é diferente-consulte [parâmetros de atualização de aplicativos](service-fabric-application-upgrade-parameters.md). A reversão no modo *UnmonitoredManual* funciona da mesma maneira que o roll forward – a reversão será suspensa após a conclusão de cada UD e deverá ser retomada explicitamente usando [resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) para continuar com a reversão.

As reversões podem ser disparadas automaticamente quando as políticas de integridade de uma atualização no modo *monitorado* com uma *falhaaction* de *reversão* são violadas (consulte [parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md)) ou explicitamente usando [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Durante a reversão, o valor de *UpgradeReplicaSetCheckTimeout* e o modo ainda podem ser alterados a qualquer momento usando [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Passos seguintes
[Atualizar seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante uma atualização de aplicativo usando o Visual Studio.

[Atualizar seu aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você durante uma atualização de aplicativo usando o PowerShell.

Controle como o aplicativo é atualizado usando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Faça com que o aplicativo seja atualizado de forma compatível aprendendo a usar a [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Corrija problemas comuns em atualizações de aplicativos consultando as etapas em [solução de problemas de atualizações de aplicativos](service-fabric-application-upgrade-troubleshooting.md).

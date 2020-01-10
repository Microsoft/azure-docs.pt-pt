---
title: Ciclo de vida do aplicativo em Service Fabric
description: Descreve o desenvolvimento, a implantação, o teste, a atualização, a manutenção e a remoção de aplicativos Service Fabric.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: beeb1f1512cf94582dd561fa768f2e8e6649d686
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75378009"
---
# <a name="service-fabric-application-lifecycle"></a>Ciclo de vida do aplicativo Service Fabric
Assim como em outras plataformas, um aplicativo no Azure Service Fabric geralmente passa pelas seguintes fases: design, desenvolvimento, teste, implantação, atualização, manutenção e remoção. O Service Fabric fornece suporte de primeira classe para o ciclo de vida completo do aplicativo de aplicativos em nuvem, desde o desenvolvimento até a implantação, gerenciamento diário e manutenção até o eventual encerramento. O modelo de serviço permite que várias funções diferentes participem de forma independente no ciclo de vida do aplicativo. Este artigo fornece uma visão geral das APIs e como elas são usadas pelas diferentes funções em todas as fases do ciclo de vida de Service Fabric aplicativo.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Funções de modelo de serviço
As funções de modelo de serviço são:

* **Desenvolvedor de serviço**: desenvolve serviços modulares e genéricos que podem ser adaptados e usados em vários aplicativos do mesmo tipo ou tipos diferentes. Por exemplo, um serviço fila pode ser usado para criar um aplicativo de emissão de tíquetes (helpdesk) ou um aplicativo de comércio eletrônico (carrinho de compras).
* **Desenvolvedor de aplicativos**: cria aplicativos integrando uma coleção de serviços para atender a certos requisitos ou cenários específicos. Por exemplo, um site de comércio eletrônico pode integrar "serviço de front-end sem estado de JSON", "serviço com estado de leilão" e "serviço com estado de fila" para criar uma solução de leilão.
* **Administrador de aplicativos**: toma decisões sobre a configuração do aplicativo (preenchendo os parâmetros do modelo de configuração), a implantação (mapeamento para recursos disponíveis) e a qualidade do serviço. Por exemplo, um administrador de aplicativos decide a localidade da linguagem (Inglês para o Estados Unidos ou japonês para o Japão, por exemplo) do aplicativo. Um aplicativo implantado diferente pode ter configurações diferentes.
* **Operador**: implanta aplicativos com base na configuração do aplicativo e nos requisitos especificados pelo administrador do aplicativo. Por exemplo, um operador provisiona e implanta o aplicativo e garante que ele esteja em execução no Azure. Os operadores monitoram as informações de integridade e desempenho do aplicativo e mantêm a infraestrutura física conforme necessário.

## <a name="develop"></a>Desenvolva
1. Um *desenvolvedor de serviço* desenvolve diferentes tipos de serviços usando o modelo de programação [Reliable Actors](service-fabric-reliable-actors-introduction.md) ou [Reliable Services](service-fabric-reliable-services-introduction.md) .
2. Um *desenvolvedor de serviço* descreve declarativamente os tipos de serviço desenvolvidos em um arquivo de manifesto do serviço que consiste em um ou mais códigos, configurações e pacotes de dados.
3. Um *desenvolvedor de aplicativos* cria um aplicativo usando diferentes tipos de serviço.
4. Um *desenvolvedor de aplicativos* descreve declarativamente o tipo de aplicativo em um manifesto de aplicativo referenciando os manifestos de serviço dos serviços constituintes e substituindo e parametrizando adequadamente as diferentes configurações e definições de implantação dos serviços constituintes.

Consulte Introdução [ao Reliable Actors](service-fabric-reliable-actors-get-started.md) e [introdução ao Reliable Services](service-fabric-reliable-services-quick-start.md) para obter exemplos.

## <a name="deploy"></a>Implementar
1. Um *administrador de aplicativos* cauda o tipo de aplicativo para um aplicativo específico a ser implantado em um Cluster Service Fabric especificando os parâmetros apropriados do elemento **ApplicationType** no manifesto do aplicativo.
2. Um *operador* carrega o pacote de aplicativos no repositório de imagens do cluster usando o método [ **CopyApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) ou o cmdlet [ **Copy-ServiceFabricApplicationPackage** ](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). O pacote de aplicativos contém o manifesto do aplicativo e a coleção de pacotes de serviço. Service Fabric implanta aplicativos do pacote de aplicativos armazenados no repositório de imagens, que pode ser um repositório de blob do Azure ou o serviço do sistema Service Fabric.
3. Em seguida, o *operador* provisiona o tipo de aplicativo no cluster de destino do pacote de aplicativos carregado usando o [método **ProvisionApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [cmdlet **Register-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)ou a [operação REST **provisionar um aplicativo** ](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Depois de provisionar o aplicativo, um *operador* inicia o aplicativo com os parâmetros fornecidos pelo *administrador do aplicativo* usando o [método **CreateApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [cmdlet **New-ServiceFabricApplication** ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication)ou a [operação REST do **aplicativo Create** ](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Depois que o aplicativo tiver sido implantado, um *operador* usará o [método **CreateServiceAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), o [cmdlet **New-ServiceFabricService** ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice)ou a [operação REST do **serviço Create** ](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) para criar novas instâncias de serviço para o aplicativo com base nos tipos de serviço disponíveis.
6. O aplicativo agora está em execução no Cluster Service Fabric.

Consulte [implantar um aplicativo](service-fabric-deploy-remove-applications.md) para obter exemplos.

## <a name="test"></a>Testar
1. Após a implantação no cluster de desenvolvimento local ou em um cluster de teste, um *desenvolvedor de serviço* executa o cenário de teste de failover interno usando as classes [**FailoverTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) e [**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) , ou o [cmdlet **Invoke-ServiceFabricFailoverTestScenario** ](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). O cenário de teste de failover executa um serviço especificado por meio de transições e failovers importantes para garantir que ele ainda esteja disponível e funcionando.
2. Em seguida, o *desenvolvedor de serviço* executa o cenário de teste de caos interno usando as classes [**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) e [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) , ou o [cmdlet **Invoke-ServiceFabricChaosTestScenario** ](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). O cenário de teste de caos induzi aleatoriamente a vários nós, pacotes de código e falhas de réplica no cluster.
3. O *desenvolvedor do serviço* [testa a comunicação entre](service-fabric-testability-scenarios-service-communication.md) serviços criando cenários de teste que movem réplicas primárias em todo o cluster.

Consulte [introdução ao serviço de análise de falha](service-fabric-testability-overview.md) para obter mais informações.

## <a name="upgrade"></a>Atualização
1. Um *desenvolvedor de serviço* atualiza os serviços constituintes do aplicativo instanciado e/ou corrige bugs e fornece uma nova versão do manifesto do serviço.
2. Um *desenvolvedor de aplicativos* substitui e parametriza as configurações de implantação e configuração dos serviços consistentes e fornece uma nova versão do manifesto do aplicativo. O desenvolvedor do aplicativo incorpora as novas versões dos manifestos do serviço no aplicativo e fornece uma nova versão do tipo de aplicativo em um pacote de aplicativos atualizado.
3. Um *administrador de aplicativos* incorpora a nova versão do tipo de aplicativo ao aplicativo de destino atualizando os parâmetros apropriados.
4. Um *operador* carrega o pacote de aplicativos atualizado no repositório de imagens de cluster usando o [método **CopyApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) ou o [cmdlet **Copy-ServiceFabricApplicationPackage** ](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). O pacote de aplicativos contém o manifesto do aplicativo e a coleção de pacotes de serviço.
5. Um *operador* provisiona a nova versão do aplicativo no cluster de destino usando o [método **ProvisionApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [cmdlet **Register-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)ou a [operação REST **provisionar um aplicativo** ](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. Um *operador* atualiza o aplicativo de destino para a nova versão usando o [método **UpgradeApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **cmdlet Start-ServiceFabricApplicationUpgrade** ](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)ou a [operação REST **atualizar um aplicativo** ](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. Um *operador* verifica o progresso da atualização usando o [método **GetApplicationUpgradeProgressAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [cmdlet **Get-ServiceFabricApplicationUpgrade** ](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade)ou a [operação REST **obter progresso de atualização do aplicativo** ](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Se necessário, o *operador* modifica e reaplica os parâmetros da atualização do aplicativo atual usando o método [ **UpdateApplicationUpgradeAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o cmdlet [ **Update-ServiceFabricApplicationUpgrade** ](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade)ou a [operação REST atualizar **atualização do aplicativo** ](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Se necessário, o *operador* reverterá a atualização do aplicativo atual usando o [método **RollbackApplicationUpgradeAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [cmdlet **Start-ServiceFabricApplicationRollback** ](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback)ou a [operação REST de atualização do aplicativo de **reversão** ](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric atualiza o aplicativo de destino em execução no cluster sem perder a disponibilidade de nenhum de seus serviços constituintes.

Consulte o [tutorial de atualização de aplicativos](service-fabric-application-upgrade-tutorial.md) para obter exemplos.

## <a name="maintain"></a>Manter
1. Para atualizações e patches do sistema operacional, Service Fabric interfaces com a infraestrutura do Azure para garantir a disponibilidade de todos os aplicativos em execução no cluster.
2. Para atualizações e patches para a plataforma Service Fabric, Service Fabric atualiza a si mesmo sem perder a disponibilidade de nenhum dos aplicativos em execução no cluster.
3. Um *administrador de aplicativos* aprova a adição ou remoção de nós de um cluster depois de analisar dados históricos de utilização da capacidade e demanda futura projetada.
4. Um *operador* adiciona e remove nós especificados pelo *administrador do aplicativo*.
5. Quando novos nós são adicionados ao ou nós existentes são removidos do cluster, Service Fabric automaticamente equilibra os aplicativos em execução em todos os nós no cluster para obter um desempenho ideal.

## <a name="remove"></a>Remover
1. Um *operador* pode excluir uma instância específica de um serviço em execução no cluster sem remover todo o aplicativo usando o [método **DeleteServiceAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), o [cmdlet **Remove-ServiceFabricService** ](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice)ou a [operação REST do serviço de **exclusão** ](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. Um *operador* também pode excluir uma instância do aplicativo e todos os seus serviços usando o [método **DeleteApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **cmdlet Remove-ServiceFabricApplication** ](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication)ou a [operação REST **excluir aplicativo** ](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Depois que o aplicativo e os serviços forem interrompidos, o *operador* poderá desprovisionar o tipo de aplicativo usando o [método **UnprovisionApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [cmdlet **Unregister-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype)ou o [ **desprovisionamento de uma** operação REST do aplicativo](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). O cancelamento do provisionamento do tipo de aplicativo não remove o pacote de aplicativos do ImageStore. Você deve remover o pacote de aplicativos manualmente.
4. Um *operador* remove o pacote de aplicativos do ImageStore usando o [método **RemoveApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) ou o [cmdlet **Remove-ServiceFabricApplicationPackage** ](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Consulte [implantar um aplicativo](service-fabric-deploy-remove-applications.md) para obter exemplos.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como desenvolver, testar e gerenciar Service Fabric aplicativos e serviços, consulte:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)
* [Atualização da aplicação](service-fabric-application-upgrade.md)
* [Visão geral da capacidade de teste](service-fabric-testability-overview.md)

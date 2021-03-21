---
title: Ciclo de vida da aplicação em Tecido de Serviço
description: Descreve o desenvolvimento, implementação, teste, upgrade, manutenção e remoção de aplicações de Tecido de Serviço.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: ae0c79cdaafc8fc016d463a01046f0a02121330a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785740"
---
# <a name="service-fabric-application-lifecycle"></a>Ciclo de vida da aplicação do Service Fabric
Tal como acontece com outras plataformas, uma aplicação no Azure Service Fabric passa normalmente pelas seguintes fases: design, desenvolvimento, teste, implementação, upgrade, manutenção e remoção. A Service Fabric fornece suporte de primeira classe para o ciclo de vida completo da aplicação de aplicações em nuvem, desde o desenvolvimento até à implantação, gestão diária e manutenção até ao eventual desmantelamento. O modelo de serviço permite que várias funções diferentes participem de forma independente no ciclo de vida da aplicação. Este artigo fornece uma visão geral das APIs e como são usadas pelas diferentes funções ao longo das fases do ciclo de vida da aplicação Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Funções de modelo de serviço
As funções do modelo de serviço são:

* **Desenvolvedor de** serviços : Desenvolve serviços modulares e genéricos que podem ser reutilizados e utilizados em múltiplas aplicações do mesmo tipo ou tipos diferentes. Por exemplo, um serviço de fila pode ser usado para criar uma aplicação de bilhética (helpdesk) ou uma aplicação de e-commerce (carrinho de compras).
* **Desenvolvedor de aplicações**: Cria aplicações integrando uma coleção de serviços para satisfazer determinados requisitos ou cenários específicos. Por exemplo, um site de e-commerce pode integrar "JSON Stateless Front-End Service", "Auction Stateful Service" e "Queue Stateful Service" para construir uma solução de leilão.
* **Administrador de aplicação**: Toma decisões sobre a configuração da aplicação (preenchendo os parâmetros do modelo de configuração), implementação (mapeamento aos recursos disponíveis) e qualidade de serviço. Por exemplo, um administrador de aplicação decide a língua local (inglês para os Estados Unidos ou japonês para o Japão, por exemplo) da aplicação. Uma aplicação diferente pode ter configurações diferentes.
* **Operador**: Implementa aplicações com base na configuração e requisitos de aplicação especificados pelo administrador de aplicação. Por exemplo, um operador fornece e implementa a aplicação e assegura que está a funcionar em Azure. Os operadores monitorizam a saúde da aplicação e a informação de desempenho e mantêm a infraestrutura física conforme necessário.

## <a name="develop"></a>Desenvolver
1. Um *desenvolvedor de serviços* desenvolve diferentes tipos de serviços usando o modelo de programação [Reliable Actors](service-fabric-reliable-actors-introduction.md) ou [Reliable Services.](service-fabric-reliable-services-introduction.md)
2. Um *desenvolvedor de serviços* descreve declarativamente os tipos de serviço desenvolvidos num ficheiro manifesto de serviço constituído por um ou mais pacotes de código, configuração e dados.
3. Um *desenvolvedor de aplicações* constrói então uma aplicação utilizando diferentes tipos de serviço.
4. Um *desenvolvedor de aplicações* descreve declarativamente o tipo de aplicação num manifesto de aplicação, referindo os manifestos de serviço dos serviços constitutivos e primordialmente e parametrizando diferentes configurações e configurações de implementação dos serviços constituintes.

Veja [começar com atores fiáveis](service-fabric-reliable-actors-get-started.md) e [começar com serviços fiáveis,](service-fabric-reliable-services-quick-start.md) por exemplo.

## <a name="deploy"></a>Implementar
1. Um *administrador de aplicação* adapta o tipo de aplicação a uma aplicação específica a ser implantada num cluster de Tecido de Serviço, especificando os parâmetros adequados do elemento **ApplicationType** no manifesto de aplicação.
2. Um *operador* envia o pacote de aplicações para a loja de imagens de cluster utilizando o método [ **CopyApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) ou o [ **cmdlet Copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage). O pacote de aplicações contém o manifesto de aplicação e a recolha de pacotes de serviço. O Service Fabric implementa aplicações a partir do pacote de aplicações armazenado na loja de imagens, que pode ser uma loja de blob Azure ou o serviço de sistema Service Fabric.
3. Em seguida, o *operador* prevê o tipo de aplicação no cluster alvo do pacote de aplicações carregados utilizando o método [ **ProvisionApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **Register-ServiceFabricApplicationType** cmdlet](/powershell/module/servicefabric/register-servicefabricapplicationtype), ou a [ **Provision a Application** REST operation](/rest/api/servicefabric/provision-an-application).
4. Após a disponibilização da aplicação, um *operador* inicia a aplicação com os parâmetros fornecidos pelo administrador de *aplicação* utilizando o método [ **CreateApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **cmdlet New-ServiceFabricApplication**](/powershell/module/servicefabric/new-servicefabricapplication), ou a [operação **Create Application** REST](/rest/api/servicefabric/create-an-application).
5. Depois de a aplicação ter sido implementada, um *operador* utiliza o método [ **CreateServiceAsync**](/dotnet/api/system.fabric.fabricclient.servicemanagementclient), o [ **cmdlet New-ServiceFabricService**](/powershell/module/servicefabric/new-servicefabricservice), ou a [operação **Create Service** REST](/rest/api/servicefabric/create-a-service) para criar novas instâncias de serviço para a aplicação com base nos tipos de serviço disponíveis.
6. A aplicação está agora em execução no cluster de Tecido de Serviço.

Consulte [implementar uma aplicação](service-fabric-deploy-remove-applications.md) para exemplos.

## <a name="test"></a>Teste
1. Depois de se implantar no cluster de desenvolvimento local ou num cluster de teste, um desenvolvedor de *serviços* executa o cenário de teste de failover incorporado utilizando as classes [**FailoverTestScenarioParameters**](/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) e [**FailoverTestScenario,**](/dotnet/api/system.fabric.testability.scenario.failovertestscenario) ou o [ **Invoke-ServiceFabricFailoverTestScenario** cmdlet](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario). O cenário de teste de failover executa um serviço especificado através de transições e falhas importantes para garantir que ele ainda está disponível e funcionando.
2. O desenvolvedor de *serviços* executa então o cenário de teste do caos incorporado utilizando as classes [**ChaosTestScenarioParameters**](/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) e [**ChaosTestScenario,**](/dotnet/api/system.fabric.testability.scenario.chaostestscenario) ou o [ **cmdlet Invoke-ServiceFabricChaosTestScenario**](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario). O cenário de teste do caos induz aleatoriamente vários nós, pacote de código e falhas de réplica no cluster.
3. O *desenvolvedor de serviço* testa a [comunicação de serviço-a-serviço,](service-fabric-testability-scenarios-service-communication.md) autorizando cenários de teste que movem réplicas primárias em torno do cluster.

Consulte [introdução ao Serviço de Análise de Falhas](service-fabric-testability-overview.md) para obter mais informações.

## <a name="upgrade"></a>Atualizar
1. Um *desenvolvedor de serviços* atualiza os serviços constituímos da aplicação instantânea e/ou corrige bugs e fornece uma nova versão do manifesto de serviço.
2. Um *desenvolvedor de aplicações* sobrepõe-se e parametriza as definições de configuração e implementação dos serviços consistentes e fornece uma nova versão do manifesto de aplicação. O desenvolvedor de aplicações incorpora então as novas versões do serviço manifesta-se na aplicação e fornece uma nova versão do tipo de aplicação num pacote de aplicações atualizado.
3. Um *administrador de aplicação* incorpora a nova versão do tipo de aplicação na aplicação-alvo, atualizando os parâmetros adequados.
4. Um *operador* envia o pacote de aplicações atualizado para a loja de imagens do cluster utilizando o método [ **CopyApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) ou o [ **cmdlet Copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage). O pacote de aplicações contém o manifesto de aplicação e a recolha de pacotes de serviço.
5. Um *operador* fornece a nova versão da aplicação no cluster-alvo utilizando o método [ **ProvisionApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **Register-ServiceFabricApplicationType** cmdlet,](/powershell/module/servicefabric/register-servicefabricapplicationtype)ou a [ **Provision a Application** REST operation](/rest/api/servicefabric/provision-an-application).
6. Um *operador* atualiza a aplicação-alvo para a nova versão utilizando o método [ **UpgradeApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **Start-ServiceFabricApplicationUpgrade** cmdlet](/powershell/module/servicefabric/start-servicefabricapplicationupgrade), ou a [ **Atualização de uma** operação Rest de aplicação](/rest/api/servicefabric/upgrade-an-application).
7. Um *operador* verifica o progresso da atualização utilizando o método [ **GetApplicationUpgradeProgressAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **Get-ServiceFabricApplicationUpgrade** cmdlet](/powershell/module/servicefabric/get-servicefabricapplicationupgrade), ou a [operação Get Application Upgrade **Progress** REST](/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Se necessário, o *operador* modifica e reaplica os parâmetros da atualização da aplicação atual utilizando o método [ **UpdateApplicationUpgradeAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **cmdlet Update-ServiceFabricApplicationUpgrade**](/powershell/module/servicefabric/update-servicefabricapplicationupgrade), ou a [operação **Dereserva de Atualização de Aplicações**](/rest/api/servicefabric/update-an-application-upgrade)de Atualização .
9. Se necessário, o *operador* revoca a atualização da aplicação atual utilizando o método [ **RollbackApplicationUpgradeAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **cmdlet Start-ServiceFabricApplicationRollback**](/powershell/module/servicefabric/start-servicefabricapplicationrollback), ou a [operação **REST de Upgrade de Aplicação de Reversão**](/rest/api/servicefabric/rollback-an-application-upgrade).
10. O Service Fabric atualiza a aplicação-alvo que funciona no cluster sem perder a disponibilidade de qualquer um dos seus serviços constitutórios.

Consulte o tutorial de upgrade da [Aplicação,](service-fabric-application-upgrade-tutorial.md) por exemplo.

## <a name="maintain"></a>Manter
1. Para atualizações e patches do sistema operativo, o Service Fabric interage com a infraestrutura Azure para garantir a disponibilidade de todas as aplicações em execução no cluster.
2. Para atualizações e patches para a plataforma Service Fabric, o Service Fabric atualiza-se sem perder a disponibilidade de nenhuma das aplicações em execução no cluster.
3. Um *administrador de aplicação* aprova a adição ou remoção de nós de um cluster após analisar dados históricos de utilização da capacidade e procura futura projetada.
4. Um *operador* adiciona e remove nós especificados pelo administrador de *aplicação*.
5. Quando novos nós são adicionados ou os nós existentes são removidos do cluster, o Tecido de Serviço equilibra automaticamente as aplicações de execução em todos os nós do cluster para obter o melhor desempenho.

## <a name="remove"></a>Remover
1. Um *operador* pode eliminar uma instância específica de um serviço de funcionamento no cluster sem remover toda a aplicação utilizando o método [ **DeleteServiceAsync**](/dotnet/api/system.fabric.fabricclient.servicemanagementclient), o [ **cmdlet Remove-ServiceFabricService,**](/powershell/module/servicefabric/remove-servicefabricservice)ou a [operação Delete **Service** REST](/rest/api/servicefabric/delete-a-service).  
2. Um *operador* também pode eliminar uma instância de aplicação e todos os seus serviços utilizando o método [ **DeleteApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **cmdlet Remove-ServiceFabricApplication**](/powershell/module/servicefabric/remove-servicefabricapplication), ou a [operação Delete **Application** REST](/rest/api/servicefabric/delete-an-application).
3. Uma vez interrompida a aplicação e os serviços, o *operador* pode desacrutar o tipo de aplicação utilizando o método [ **UnprovisionApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [Cmdlet **Unregister-ServiceFabricApplicationType**](/powershell/module/servicefabric/unregister-servicefabricapplicationtype), ou a [ **operação Unprovision a Application** REST](/rest/api/servicefabric/unprovision-an-application). Desprovisionar o tipo de aplicação não remove o pacote de aplicações da ImageStore. Deve remover manualmente a embalagem de aplicação.
4. Um *operador* remove o pacote de aplicações da ImageStore utilizando o método [ **RemoveApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) ou o [ **cmdlet Remove-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/remove-servicefabricapplicationpackage).

Consulte [implementar uma aplicação](service-fabric-deploy-remove-applications.md) para exemplos.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o desenvolvimento, teste e gestão de aplicações e serviços do Service Fabric, consulte:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)
* [Atualização da aplicação](service-fabric-application-upgrade.md)
* [Visão geral da testability](service-fabric-testability-overview.md)

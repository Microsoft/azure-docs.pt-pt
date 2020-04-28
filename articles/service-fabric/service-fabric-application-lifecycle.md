---
title: Ciclo de vida de aplicação em Tecido de Serviço
description: Descreve o desenvolvimento, implantação, teste, modernização, manutenção e remoção de aplicações de Tecido de Serviço.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: beeb1f1512cf94582dd561fa768f2e8e6649d686
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75378009"
---
# <a name="service-fabric-application-lifecycle"></a>Ciclo de vida da aplicação do Service Fabric
Tal como acontece com outras plataformas, uma aplicação no Tecido de Serviço Azure passa geralmente pelas seguintes fases: conceção, desenvolvimento, teste, implementação, modernização, manutenção e remoção. O Service Fabric fornece suporte de primeira classe para o ciclo de vida completo das aplicações em nuvem, desde o desenvolvimento até à implantação, gestão diária e manutenção até eventual desmantelamento. O modelo de serviço permite que várias funções diferentes participem de forma independente no ciclo de vida da aplicação. Este artigo fornece uma visão geral das APIs e como são utilizadas pelas diferentes funções ao longo das fases do ciclo de vida da aplicação do Tecido de Serviço.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Funções de modelo de serviço
As funções do modelo de serviço são:

* **Programador de serviços**: Desenvolve serviços modulares e genéricos que podem ser reutilizados e utilizados em múltiplas aplicações do mesmo tipo ou tipos diferentes. Por exemplo, um serviço de fila pode ser usado para criar uma aplicação de bilhética (helpdesk) ou uma aplicação de e-commerce (carrinho de compras).
* **Programador de aplicações**: Cria aplicações integrando uma coleção de serviços para satisfazer determinados requisitos ou cenários específicos. Por exemplo, um site de e-commerce pode integrar "JSON Stateless Front-End Service", "Leiloamento Stateful Service" e "Serviço Estatal de Fila" para construir uma solução de leilão.
* **Administrador de aplicação**: Toma decisões sobre a configuração da aplicação (preenchendo os parâmetros do modelo de configuração), implementação (mapeamento para recursos disponíveis) e qualidade de serviço. Por exemplo, um administrador de aplicação decide a localização linguística (inglês para os Estados Unidos ou japonês para o Japão, por exemplo) da aplicação. Uma aplicação implementada diferente pode ter configurações diferentes.
* **Operador**: Implementa aplicações com base na configuração da aplicação e nos requisitos especificados pelo administrador de aplicação. Por exemplo, um operador provisões e implementa a aplicação e assegura que está a funcionar em Azure. Os operadores monitorizam a informação sobre a saúde das aplicações e o desempenho e mantêm a infraestrutura física conforme necessário.

## <a name="develop"></a>Programar
1. Um desenvolvedor de *serviços* desenvolve diferentes tipos de serviços usando o modelo de programação [De Atores Fiáveis](service-fabric-reliable-actors-introduction.md) ou [Serviços Fiáveis.](service-fabric-reliable-services-introduction.md)
2. Um desenvolvedor de *serviçodescreve* declarativamente os tipos de serviço desenvolvidos num ficheiro manifesto de serviço composto por um ou mais pacotes de código, configuração e dados.
3. Um desenvolvedor de *aplicações* constrói então uma aplicação utilizando diferentes tipos de serviço.
4. Um desenvolvedor de *aplicações* descreve declarativamente o tipo de aplicação num manifesto de aplicação, referindo os manifestos de serviço dos serviços constituintes e sobrepondo e parametrindo adequadamente diferentes configurações e configurações de implementação dos serviços constituintes.

Ver [Começar com Atores Fiáveis](service-fabric-reliable-actors-get-started.md) e Começar com [Serviços Fiáveis,](service-fabric-reliable-services-quick-start.md) por exemplo.

## <a name="deploy"></a>Implementação
1. Um administrador de *aplicação* adapta o tipo de aplicação a uma aplicação específica a ser implantada num cluster de Tecido de Serviço, especificando os parâmetros adequados do elemento **ApplicationType** no manifesto de aplicação.
2. Um *operador* envia o pacote de aplicação para o armazém de imagem do cluster utilizando o método [ **CopyApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) ou o [ **Copy-ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). O pacote de candidatura contém o manifesto de candidatura e a recolha de pacotes de serviços. Service Fabric implementa aplicações a partir do pacote de aplicações armazenado na loja de imagens, que pode ser uma loja de blob Azure ou o serviço de sistema Service Fabric.
3. O *operador* prevê então o tipo de aplicação no cluster-alvo do pacote de aplicações carregado utilizando o método [ **ProvisionApplicationAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)o [ **Register-ServiceFabricApplicationType** cmdlet,](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)ou a provisão de uma operação DE REPOUSO de [ **aplicação.** ](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)
4. Após o fornecimento da aplicação, um *operador* inicia a aplicação com os parâmetros fornecidos pelo administrador de *aplicação* utilizando o método [ **CreateApplicationAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)o [cmdlet **New-ServiceFabricApplication** ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication)ou a operação [ **Create Application** REST](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Depois de a aplicação ter sido implementada, um *operador* utiliza o método [ **CreateServiceAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), o [ **cmdlet New-ServiceFabricService** ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice)ou a operação [ **Create Service** REST](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) para criar novas instâncias de serviço para a aplicação com base nos tipos de serviço disponíveis.
6. A aplicação está agora a funcionar no cluster Service Fabric.

Consulte [a implementação de uma aplicação,](service-fabric-deploy-remove-applications.md) por exemplo.

## <a name="test"></a>Teste
1. Depois de ser implantado para o cluster de desenvolvimento local ou para um cluster de teste, um desenvolvedor de *serviços* executa o cenário de teste de failover incorporado utilizando as classes [**FailoverTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) e [**FailoverTestScenario,**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) ou o [cmdlet **Invoke-ServiceFabricFailoverTestScenario** ](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). O cenário de teste failover executa um serviço especificado através de transições importantes e falhas para garantir que ainda está disponível e funcionando.
2. Em seguida, o desenvolvedor de *serviçoexecuta* o cenário de teste de caos incorporado utilizando as classes [**ChaosTestScenarioParâmetros**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) e [**ChaosTestScenario,**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) ou o [ **cmdlet Invoke-ServiceFabricChaosTestScenario** ](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). O cenário de teste do caos induz aleatoriamente várias falhas no nó, no pacote de código e nas réplicas no cluster.
3. O *desenvolvedor* de [serviçotesta a comunicação serviço-a-serviço](service-fabric-testability-scenarios-service-communication.md) através da autoria de cenários de teste que movem réplicas primárias em torno do cluster.

Consulte [a Introdução ao Serviço](service-fabric-testability-overview.md) de Análise de Falhas para obter mais informações.

## <a name="upgrade"></a>Atualizar
1. Um desenvolvedor de *serviços* atualiza os serviços constituintes da aplicação instantânea e/ou corrige bugs e fornece uma nova versão do manifesto de serviço.
2. Um desenvolvedor de *aplicações* substitui e parametriza as configurações de configuração e implementação dos serviços consistentes e fornece uma nova versão do manifesto de aplicação. O desenvolvedor de aplicações incorpora então as novas versões do serviço manifesta-se na aplicação e fornece uma nova versão do tipo de aplicação num pacote de aplicações atualizado.
3. Um administrador de *aplicação* incorpora a nova versão do tipo de aplicação na aplicação-alvo através da atualização dos parâmetros adequados.
4. Um *operador* envia o pacote de aplicação atualizado para o cluster image store utilizando o método [ **CopyApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) ou o [ **Copy-ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). O pacote de candidatura contém o manifesto de candidatura e a recolha de pacotes de serviços.
5. Um *operador* prevê a nova versão da aplicação no cluster-alvo utilizando o método [ **ProvisionApplicationAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)o [ **Register-ServiceFabricApplicationType** cmdlet,](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)ou a [ **provisão de uma operação** DE REPOUSO](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)de aplicação .
6. Um *operador* atualiza a aplicação-alvo para a nova versão utilizando o método [ **UpgradeApplicationAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)o [ **Cmdlet start-ServiceFabricApplicationUpgrade** ](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)ou a [ **atualização de uma** operação DETRABALHO](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application)de Aplicação .
7. Um *operador* verifica o progresso da atualização utilizando o método [ **GetApplicationUpgradeProgressAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)o [ **Get-ServiceFabricApplicationUpgrade** cmdlet,](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade)ou a operação Get Application Upgrade [ **Progress** REST](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Se necessário, o *operador* modifica e reaplica os parâmetros da atualização da aplicação atual utilizando o método [ **UpdateApplicationUpgradeAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)o [ **cmdlet update-serviceFabricApplicationUpgrade** ](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade)ou a operação DE Atualização de Atualização de [ **Atualização** DE REposição .](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade)
9. Se necessário, o *operador* reverte a atualização atual da aplicação utilizando o método [ **RollbackApplicationUpgradeAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)o [ **cmdlet Start-ServiceFabricApplicationRollback** ](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback)ou a operação REST de atualização da [ **aplicação de reversão** ](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. O Service Fabric atualiza a aplicação-alvo em execução no cluster sem perder a disponibilidade de qualquer um dos seus serviços constituintes.

Consulte o tutorial de upgrade da [Aplicação,](service-fabric-application-upgrade-tutorial.md) por exemplo.

## <a name="maintain"></a>Manter
1. Para atualizações e patches do sistema operativo, interfaces de Tecido de Serviço com a infraestrutura Azure para garantir a disponibilidade de todas as aplicações em execução no cluster.
2. Para upgrades e patches para a plataforma Service Fabric, o Service Fabric atualiza-se sem perder a disponibilidade de qualquer uma das aplicações em execução no cluster.
3. Um administrador de *aplicação* aprova a adição ou remoção de nós de um cluster após analisar dados históricos de utilização da capacidade e procura futura projetada.
4. Um *operador* adiciona e remove os nódosos especificados pelo administrador de *aplicação*.
5. Quando novos nós são adicionados ou nódosos existentes são removidos do cluster, o Tecido de Serviço equilibra automaticamente as aplicações de funcionamento em todos os nós do cluster para obter um desempenho ótimo.

## <a name="remove"></a>Remover
1. Um *operador* pode eliminar uma instância específica de um serviço de funcionamento no cluster sem remover toda a aplicação utilizando o método [ **DeleteServiceAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient)o [ **cmdlet remove-ServiceFabricService** ](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice)ou a operação eliminar o [ **serviço** REST](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. Um *operador* também pode eliminar uma instância de aplicação e todos os seus serviços utilizando o método [ **DeleteApplicationAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)o [ **cmdlet remove-ServiceFabricApplication** ](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication)ou a operação eliminar [ **aplicação** REST](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Uma vez interrompida a aplicação e os serviços, o *operador* pode desfornecer o tipo de aplicação utilizando o método [ **UnprovisionApplicationAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)o [ **Unregister-ServiceFabricApplicationType** cmdlet,](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype)ou a [ **Unprovision a Application** REST operation](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). O desprovisionamento do tipo de aplicação não remove o pacote de aplicações da ImageStore. Tem de remover manualmente o pacote de aplicações.
4. Um *operador* remove o pacote de aplicações da ImageStore utilizando o método [ **RemoveApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) ou o [ **remove-ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Consulte [a implementação de uma aplicação,](service-fabric-deploy-remove-applications.md) por exemplo.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o desenvolvimento, teste e gestão de aplicações e serviços de Tecido de Serviço, consulte:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)
* [Atualização da aplicação](service-fabric-application-upgrade.md)
* [Visão geral da testabilidade](service-fabric-testability-overview.md)

---
title: Ciclo de vida do aplicativo no Service Fabric | Documentos da Microsoft
description: Descreve o desenvolvimento, implementação, teste, atualizar, manter e remover aplicações do Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/19/2018
ms.author: ryanwi
ms.openlocfilehash: 65104503af2e177f1898d8509c2d82bd9b58c266
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446968"
---
# <a name="service-fabric-application-lifecycle"></a>Ciclo de vida de aplicação de Service Fabric
Como com outras plataformas, uma aplicação no Azure Service Fabric normalmente executa as seguintes fases: design, desenvolvimento, teste, implantação, atualizar, manutenção e remoção. O Service Fabric fornece suporte de primeira classe para o ciclo de vida do aplicativo completo das aplicações na cloud, desde o desenvolvimento através de implementação, gestão diária e manutenção até à desativação final. O modelo de serviço permite que várias funções diferentes para participar de forma independente do ciclo de vida do aplicativo. Este artigo fornece uma descrição geral das APIs e como elas são usadas pelas funções de diferentes em todas as fases do ciclo de vida de aplicação do Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Funções do modelo de serviço
As funções de modelo de serviço são:

* **Desenvolvedor de serviço**: Desenvolve serviços modulares e genéricos que podem ser redirecionado para outros objetivos e utilizados em várias aplicações do mesmo tipo ou tipos diferentes. Por exemplo, um serviço de fila pode ser utilizado para a criação de um aplicativo de emissão de permissões (suporte técnico) ou uma aplicação de comércio eletrônico (carrinho de compras).
* **Desenvolvedor de aplicativos**: Cria aplicações através da integração de uma coleção de serviços para satisfazer determinados requisitos específicos ou cenários. Por exemplo, um Web site de comércio eletrônico pode integrar-se "JSON serviço sem estado do front-end," "Serviço com estado de leilões" e "Serviço com estado de fila" para criar uma solução auctioning.
* **Administrador da aplicação**: Toma decisões relativamente à configuração da aplicação (preencher os parâmetros de modelo de configuração), a implementação (mapeamento para recursos disponíveis) e a qualidade de serviço. Por exemplo, o administrador de uma aplicação decide a localidade de idioma (em inglês para os Estados Unidos) ou o japonês para o Japão, por exemplo, do aplicativo. Um aplicativo implantado diferente pode ter definições diferentes.
* **Operador**: Implementa aplicações, com base na configuração da aplicação e os requisitos especificados pelo administrador do aplicativo. Por exemplo, um operador Aprovisiona e implementa a aplicação e garante que está em execução no Azure. Operadores de monitorizar informações de estado de funcionamento e desempenho de aplicações e manter a infraestrutura física, conforme necessário.

## <a name="develop"></a>Programar
1. R *desenvolvedor de serviço* desenvolve diferentes tipos de serviços com o [Reliable Actors](service-fabric-reliable-actors-introduction.md) ou [Reliable Services](service-fabric-reliable-services-introduction.md) modelo de programação.
2. R *desenvolvedor de serviço* declarativamente descreve os tipos de serviços desenvolvidos num arquivo de manifesto do serviço consiste num ou mais pacotes de código, configuração e dados.
3. Uma *programador da aplicação* , em seguida, cria uma aplicação com diferentes tipos de serviços.
4. Uma *programador da aplicação* declarativamente descreve o tipo de aplicativo num manifesto de aplicativo que referencia os manifestos de serviço dos serviços de constituintes e substituindo adequadamente e parametrização diferentes definições de configuração e implementação dos serviços de constituintes.

Ver [introdução ao Reliable Actors](service-fabric-reliable-actors-get-started.md) e [começar com o Reliable Services](service-fabric-reliable-services-quick-start.md) para obter exemplos.

## <a name="deploy"></a>Implementação
1. Uma *administrador da aplicação* tailors o tipo de aplicação para uma aplicação específica para ser implementada num cluster do Service Fabric, especificando os parâmetros adequados da **ApplicationType** elemento no manifesto do aplicativo.
2. Uma *operador* carrega o pacote de aplicação para o armazenamento de imagens do cluster utilizando o [ **CopyApplicationPackage** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) ou o [  **Cópia ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). O pacote de aplicação contém o manifesto do aplicativo e a coleção de pacotes de serviço. Service Fabric implementa aplicações a partir do pacote de aplicações armazenados no arquivo de imagem, que pode ser um armazenamento de Blobs do Azure ou o serviço de sistema do Service Fabric.
3. O *operador* , em seguida, Aprovisiona o tipo de aplicação do cluster de destino do pacote de aplicação carregada com o [ **ProvisionApplicationAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o  [ **Register-ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype), ou o [ **aprovisionar uma aplicação** operação REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Após o aprovisionamento da aplicação, um *operador* inicia a aplicação com os parâmetros fornecidos pela *administrador da aplicação* utilizando o [  **CreateApplicationAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **New-ServiceFabricApplication** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication), ou o [ **Criar aplicação**  Operação REST](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Depois da aplicação foi implementada, uma *operador* utiliza o [ **CreateServiceAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), o [ **New-ServiceFabricService**  cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice), ou o [ **criar serviço** operação REST](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) para criar novas instâncias de serviço para a aplicação com base em tipos de serviço disponíveis.
6. A aplicação está agora em execução no cluster do Service Fabric.

Ver [implementar uma aplicação](service-fabric-deploy-remove-applications.md) para obter exemplos.

## <a name="test"></a>Teste
1. Depois de implementar para o cluster de desenvolvimento local ou um cluster de teste, uma *desenvolvedor de serviço* executa o cenário de teste de ativação pós-falha incorporada utilizando o [ **FailoverTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) e [ **FailoverTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) classes, ou o [ **Invoke-ServiceFabricFailoverTestScenario** cmdlet](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). O cenário de teste de ativação pós-falha executa um serviço especificado por meio de transições de importantes e as ativações pós-falha para verificar se está ainda disponível e em funcionamento.
2. O *desenvolvedor de serviço* , em seguida, executa o teste de chaos incorporada cenário com o [ **ChaosTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) e [  **ChaosTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) classes, ou o [ **Invoke-ServiceFabricChaosTestScenario** cmdlet](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). O cenário de teste de chaos induces aleatoriamente várias nó, o pacote de código e falhas de réplica para o cluster.
3. O *desenvolvedor de serviço* [testa a comunicação de serviço para serviço](service-fabric-testability-scenarios-service-communication.md) ao criar cenários de teste que mover réplicas primárias em todo o cluster.

Ver [introdução ao serviço de análise de falhas](service-fabric-testability-overview.md) para obter mais informações.

## <a name="upgrade"></a>Atualizar
1. R *desenvolvedor de serviço* atualiza os serviços de constituintes do aplicativo instanciado e/ou correções de bugs e fornece uma nova versão do manifesto do serviço.
2. Uma *programador da aplicação* substitui e parametriza as definições de configuração e implementação dos serviços consistentes e fornece uma nova versão do manifesto do aplicativo. O desenvolvedor do aplicativo, em seguida, incorpora as novas versões de manifestos de serviço no aplicativo e fornece uma nova versão do tipo de aplicação num pacote de aplicação atualizada.
3. Uma *administrador da aplicação* incorpora a nova versão do tipo de aplicação para o aplicativo de destino ao atualizar os parâmetros adequados.
4. Uma *operador* carrega o pacote de aplicação atualizada para a imagem de cluster store com o [ **CopyApplicationPackage** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) ou o [ **ServiceFabricApplicationPackage cópia** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). O pacote de aplicação contém o manifesto do aplicativo e a coleção de pacotes de serviço.
5. Uma *operador* Aprovisiona a nova versão da aplicação do cluster de destino utilizando o [ **ProvisionApplicationAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [  **Registre-se-ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype), ou o [ **aprovisionar uma aplicação** operação REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. Uma *operador* atualiza a aplicação de destino para a nova versão com o [ **UpgradeApplicationAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [  **Start-ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade), ou o [ **atualizar uma aplicação** operação REST](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. Uma *operador* verifica o progresso da atualização utilizando o [ **GetApplicationUpgradeProgressAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [  **Get-ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade), ou o [ **obter progresso de atualização do aplicativo** operação REST](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Se necessário, o *operador* modifica e volta a aplicar os parâmetros da aplicação atual atualizar com o [ **UpdateApplicationUpgradeAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **Atualização ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade), ou a [ **atualização da aplicação de atualização** operação REST](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Se necessário, o *operador* reverte o aplicativo atual atualizar com o [ **RollbackApplicationUpgradeAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **Start-ServiceFabricApplicationRollback** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback), ou o [ **atualização de aplicativo de reversão** operação REST](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric atualiza o aplicativo de destino em execução no cluster, sem perder a disponibilidade de qualquer um dos seus serviços constituintes.

Consulte a [tutorial da atualização de aplicativo](service-fabric-application-upgrade-tutorial.md) para obter exemplos.

## <a name="maintain"></a>Manter
1. Para atualizações do sistema operativo e patches, Service Fabric faz interface com a infraestrutura do Azure para garantir a disponibilidade de todos os aplicativos em execução no cluster.
2. Para as atualizações e patches para a plataforma do Service Fabric, o Service Fabric atualiza-se sem perder a disponibilidade de qualquer um dos aplicativos em execução no cluster.
3. Uma *administrador da aplicação* aprova a adição ou remoção de nós de um cluster depois de analisar os dados de utilização de capacidade histórica e a procura futura prevista.
4. Uma *operador* adiciona e remove nós especificado pela *administrador da aplicação*.
5. Quando são adicionados novos nós ou nós existentes são removidas do cluster, o Service Fabric automaticamente carga faz o balanceamento de aplicações em execução em todos os nós do cluster para obter um desempenho ideal.

## <a name="remove"></a>Remover
1. Uma *operador* pode eliminar uma instância específica de um serviço em execução no cluster, sem remover o aplicativo inteiro, utilizando o [ **DeleteServiceAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), o [ **Remove-ServiceFabricService** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice), ou o [ **eliminação de serviços** operação REST](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. Uma *operador* também pode eliminar uma instância de aplicação e todos os seus serviços usando o [ **DeleteApplicationAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [  **Remove-ServiceFabricApplication** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication), ou o [ **eliminar aplicação** operação REST](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Assim que tiveram parado a aplicações e serviços, o *operador* pode anular o aprovisionamento do tipo de aplicativo usando o [ **UnprovisionApplicationAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), o [ **Unregister-ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype), ou a [ **anular o aprovisionamento de um aplicativo** operação REST](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Anulação do aprovisionamento do tipo de aplicação não remove o pacote de aplicação da ImageStore. Tem de remover manualmente o pacote de aplicação.
4. Uma *operador* remove o pacote de aplicação do ImageStore com o [ **RemoveApplicationPackage** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) ou o [  **Remove-ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Ver [implementar uma aplicação](service-fabric-deploy-remove-applications.md) para obter exemplos.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como desenvolver, testar e gerir aplicações do Service Fabric e serviços, consulte:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)
* [Atualização da aplicação](service-fabric-application-upgrade.md)
* [Descrição geral do Testability](service-fabric-testability-overview.md)

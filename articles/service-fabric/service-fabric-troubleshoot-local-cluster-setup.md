---
title: Problemas de resolução do seu cluster de tecido de serviço Azure local
description: Este artigo cobre um conjunto de sugestões para resolver problemas no seu cluster de desenvolvimento local
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: ea313adb43f8d91ec9e57dd1d0b8d3447a8075f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75465498"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Resolver problemas na configuração do cluster do desenvolvimento local
Se tiver um problema enquanto interage com o seu cluster de desenvolvimento de Tecido de Serviço Azure local, reveja as seguintes sugestões para potenciais soluções.

## <a name="cluster-setup-failures"></a>Falhas na configuração do cluster
### <a name="cannot-clean-up-service-fabric-logs"></a>Não é possível limpar os registos de tecido de serviço
#### <a name="problem"></a>Problema
Durante a execução do script DevClusterSetup, vê o seguinte erro:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solução
Feche a janela atual da PowerShell e abra uma nova janela PowerShell como administrador. Agora pode executar com sucesso o guião.

## <a name="cluster-connection-failures"></a>Falhas de ligação ao cluster

### <a name="type-initialization-exception"></a>Exceção da inicialização do tipo
#### <a name="problem"></a>Problema
Quando estiver a ligar-se ao cluster no PowerShell, vê o erro TypeInitializationException for System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Solução
A variável do seu percurso não foi corretamente definida durante a instalação. Assine o Windows e volte a entrar. Isto refresca o seu caminho.

### <a name="cluster-connection-fails-with-object-is-closed"></a>A ligação do cluster falha com "O objeto está fechado"
#### <a name="problem"></a>Problema
Uma chamada para Connect-ServiceFabricCluster falha com um erro como este:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Solução
Feche a janela atual da PowerShell e abra uma nova janela PowerShell como administrador.

### <a name="fabric-connection-denied-exception"></a>Conexão de tecidoS Exceção negada
#### <a name="problem"></a>Problema
Ao depurar-se do Visual Studio, obtém-se um erro de exceção de FabricConnectionDenied.

#### <a name="solution"></a>Solução
Este erro ocorre geralmente quando se tenta iniciar manualmente um processo de hospedagem de serviço.

Certifique-se de que não tem quaisquer projetos de serviço definidos como projetos de arranque na sua solução. Apenas os projetos de aplicação Service Fabric devem ser definidos como projetos de arranque.

> [!TIP]
> Se, após a configuração, o seu cluster local começar a comportar-se de forma anormal, pode resetá-lo utilizando a aplicação de bandeja do gestor de cluster local. Isto remove o cluster existente e cria um novo. Note que todas as aplicações implementadas e dados associados são removidos.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Compreenda e atire problemas ao seu cluster com relatórios de saúde do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualizar o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)


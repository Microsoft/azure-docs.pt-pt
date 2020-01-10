---
title: Solucionar problemas de configuração do Cluster Service Fabric local do Azure
description: Este artigo aborda um conjunto de sugestões para solucionar problemas de seu cluster de desenvolvimento local
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: ea313adb43f8d91ec9e57dd1d0b8d3447a8075f2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465498"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Solucionar problemas de configuração do cluster de desenvolvimento local
Se você tiver um problema ao interagir com seu cluster de desenvolvimento Service Fabric do Azure local, examine as sugestões a seguir para obter as possíveis soluções.

## <a name="cluster-setup-failures"></a>Falhas na instalação do cluster
### <a name="cannot-clean-up-service-fabric-logs"></a>Não é possível limpar os logs de Service Fabric
#### <a name="problem"></a>Problema
Ao executar o script DevClusterSetup, você verá o seguinte erro:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solução
Feche a janela atual do PowerShell e abra uma nova janela do PowerShell como administrador. Agora você pode executar o script com êxito.

## <a name="cluster-connection-failures"></a>Falhas de conexão do cluster

### <a name="type-initialization-exception"></a>Exceção de inicialização de tipo
#### <a name="problem"></a>Problema
Quando estiver se conectando ao cluster no PowerShell, você verá o erro TypeInitializationException para System. Fabric. Common. AppTrace.

#### <a name="solution"></a>Solução
A variável de caminho não foi definida corretamente durante a instalação. Saia do Windows e entre novamente. Isso atualiza seu caminho.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Falha na conexão do cluster com "o objeto está fechado"
#### <a name="problem"></a>Problema
Uma chamada para Connect-ServiceFabricCluster falha com um erro como este:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Solução
Feche a janela atual do PowerShell e abra uma nova janela do PowerShell como administrador.

### <a name="fabric-connection-denied-exception"></a>Exceção de conexão de malha negada
#### <a name="problem"></a>Problema
Ao depurar do Visual Studio, você obtém um erro FabricConnectionDeniedException.

#### <a name="solution"></a>Solução
Esse erro geralmente ocorre quando você tenta iniciar um processo de host de serviço manualmente.

Verifique se você não tem projetos de serviço definidos como projetos de inicialização em sua solução. Somente os projetos de aplicativo Service Fabric devem ser definidos como projetos de inicialização.

> [!TIP]
> Se, após a instalação, o cluster local começar a se comportar de forma anormal, você poderá redefini-lo usando o aplicativo de bandeja do sistema gerenciador de cluster local. Isso remove o cluster existente e configura um novo. Observe que todos os aplicativos implantados e os dados associados são removidos.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Entender e solucionar problemas do cluster com relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualizar o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)


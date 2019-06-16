---
title: Resolver problemas de sua configuração de cluster do Azure Service Fabric local | Documentos da Microsoft
description: Este artigo aborda um conjunto de sugestões de resolução de problemas de cluster de desenvolvimento local
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 8bb32b2bded061bd19bcd7cfda4ef259a75b0626
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60864444"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Resolver problemas de sua configuração de cluster de desenvolvimento local
Caso se depare com um problema ao interagir com o seu cluster de desenvolvimento do Azure Service Fabric local, reveja as seguintes sugestões para possíveis soluções.

## <a name="cluster-setup-failures"></a>Falhas de configuração do cluster
### <a name="cannot-clean-up-service-fabric-logs"></a>Não é possível limpar registos do Service Fabric
#### <a name="problem"></a>Problema
Ao executar o script de DevClusterSetup, verá o seguinte erro:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solução
Feche a janela atual do PowerShell e abra uma nova janela do PowerShell como administrador. Agora pode executar o script com êxito.

## <a name="cluster-connection-failures"></a>Falhas de ligação do cluster

### <a name="type-initialization-exception"></a>Exceção de inicialização de tipo
#### <a name="problem"></a>Problema
Quando estiver a ligar ao cluster no PowerShell, verá o erro TypeInitializationException para System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Solução
A variável de caminho não foi corretamente definida durante a instalação. Terminar sessão do Windows e inicie sessão novamente. Isto atualiza o seu caminho.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Falha de ligação de cluster com "O objeto está fechado"
#### <a name="problem"></a>Problema
Uma chamada para o Connect-ServiceFabricCluster falha com um erro como este:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Solução
Feche a janela atual do PowerShell e abra uma nova janela do PowerShell como administrador.

### <a name="fabric-connection-denied-exception"></a>Exceção de negado de ligação de recursos de infraestrutura
#### <a name="problem"></a>Problema
Quando a depuração no Visual Studio, obterá um erro de FabricConnectionDeniedException.

#### <a name="solution"></a>Solução
Este erro ocorre normalmente quando tentar iniciar um processo de host de serviço manualmente.

Certifique-se de que não tem quaisquer projetos de serviço definido como projetos de arranque na sua solução. Apenas os projetos de aplicativos do Service Fabric devem ser definidos como projetos de arranque.

> [!TIP]
> Se, a seguir o programa de configuração, o seu cluster local começa a se comportar anormalmente, pode redefini-lo com a aplicação de tabuleiro de sistema de Gestor de local cluster. Esta ação remove o cluster existente e configurar um novo. Tenha em atenção que todos os aplicativos implantados e dados associados são removidos.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
* [Compreender e resolver problemas do seu cluster com relatórios de estado de funcionamento do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualizar o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)


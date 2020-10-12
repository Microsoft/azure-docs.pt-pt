---
title: Resolução de problemas na configuração do cluster de tecido de serviço Azure local
description: Este artigo cobre um conjunto de sugestões para resolver problemas no seu cluster de desenvolvimento local
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 76ca0bb7b81b3896538f08ff2ef52ed1ac6b363f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87091611"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Resolver problemas na configuração do cluster do desenvolvimento local
Se tiver um problema enquanto interage com o cluster local de desenvolvimento do Azure Service Fabric, reveja as seguintes sugestões para soluções potenciais.

## <a name="cluster-setup-failures"></a>Falhas de configuração do cluster
### <a name="cannot-clean-up-service-fabric-logs"></a>Não é possível limpar registos de tecido de serviço
#### <a name="problem"></a>Problema
Enquanto executa o script DevClusterSetup, vê o seguinte erro:

```output
Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
At line:1 char:1 + .\DevClusterSetup.ps1
+ ~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
+ FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1
```

#### <a name="solution"></a>Solução
Feche a janela Atual PowerShell e abra uma nova janela PowerShell como administrador. Agora pode executar o guião com sucesso.

## <a name="cluster-connection-failures"></a>Falhas de ligação do cluster

### <a name="type-initialization-exception"></a>Exceção da inicialização do tipo
#### <a name="problem"></a>Problema
Quando estiver a ligar-se ao cluster em PowerShell, vê o erro TypeInitializationException for System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Solução
A variável do seu percurso não foi corretamente definida durante a instalação. Assine fora do Windows e volte a entrar. Isto refresca o seu caminho.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Ligação do cluster falha com "Objeto está fechado"
#### <a name="problem"></a>Problema
Uma chamada para Connect-ServiceFabricCluster falha com um erro como este:

```output
Connect-ServiceFabricCluster : The object is closed.
At line:1 char:1
+ Connect-ServiceFabricCluster
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
+ FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster
```

#### <a name="solution"></a>Solução
Feche a janela Atual PowerShell e abra uma nova janela PowerShell como administrador.

### <a name="fabric-connection-denied-exception"></a>Ligação de tecido Negada exceção
#### <a name="problem"></a>Problema
Ao depurar do Visual Studio, obtém-se um erro de FabricConnectionDeniedException.

#### <a name="solution"></a>Solução
Este erro ocorre geralmente quando se tenta iniciar manualmente um processo de anfitrião de serviço.

Certifique-se de que não tem quaisquer projetos de serviço definidos como projetos de arranque na sua solução. Apenas os projetos de aplicação do Service Fabric devem ser definidos como projetos de arranque.

> [!TIP]
> Se, após a configuração, o seu cluster local começar a comportar-se de forma anormal, pode reiniciá-lo utilizando a aplicação de bandeja de gestão de cluster local. Isto remove o aglomerado existente e cria um novo. Note que todas as aplicações implementadas e dados associados são removidos.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Compreenda e resolva o seu cluster com relatórios de saúde do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualizar o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)


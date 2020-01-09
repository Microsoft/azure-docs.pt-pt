---
title: Implantar aplicativo em um cluster no PowerShell
description: Exemplo de script Azure PowerShell – implantar um aplicativo em um Cluster Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 207f2a4e8173aa1e5009435665532973045d9198
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610306"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Implementar uma aplicação num cluster do Service Fabric

Este script de exemplo copia um pacote de aplicativos para um repositório de imagens de cluster, registra o tipo de aplicativo no cluster, remove o pacote de aplicativos desnecessários e cria uma instância do aplicativo a partir do tipo de aplicativo.  Se algum serviço padrão tiver sido definido no manifesto do aplicativo do tipo de aplicativo de destino, esses serviços serão criados neste momento. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo PowerShell do Service Fabric com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Após a execução do exemplo de script, o script em [remover um aplicativo](service-fabric-powershell-remove-application.md) pode ser usado para remover a instância do aplicativo, cancelar o registro do tipo de aplicativo e excluir o pacote de aplicativos do repositório de imagens.

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Cria uma conexão com um Cluster Service Fabric. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copia um pacote de aplicativos para o repositório de imagens do cluster.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Registra um tipo de aplicativo e uma versão no cluster. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Cria um aplicativo a partir de um tipo de aplicativo registrado. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Remove um pacote de aplicativo Service Fabric do repositório de imagens.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Service Fabric PowerShell, consulte [Azure PowerShell documentação](/powershell/azure/service-fabric/?view=azureservicefabricps).

Pode ver exemplos adicionais do Powershell para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

---
title: Implementar aplicação para um cluster em Powershell
description: Amostra de script Azure PowerShell - Implemente uma aplicação para um cluster de tecido de serviço.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75610306"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Implementar uma aplicação num cluster do Service Fabric

Esta amostra copia um pacote de aplicação para uma loja de imagens de cluster, regista o tipo de aplicação no cluster, remove o pacote de aplicação desnecessário e cria uma instância de aplicação do tipo de aplicação.  Se quaisquer serviços predefinidos foram definidos no manifesto de aplicação do tipo de aplicação-alvo, então esses serviços são criados neste momento. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo PowerShell do Service Fabric com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de a amostra de script ter sido executada, o script em [Remover uma aplicação](service-fabric-powershell-remove-application.md) pode ser usado para remover a instância de aplicação, desregistar o tipo de aplicação e eliminar o pacote de aplicação da loja de imagens.

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Cria uma ligação a um cluster de Tecido de Serviço. |
|[Pacote de aplicações de copy-servicefabricapplication](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copia um pacote de aplicação para a loja de imagens do cluster.  |
|[Tipo de aplicação de serviço de registo](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Regista um tipo de aplicação e versão no cluster. |
|[Nova Aplicação de Fabricação de Serviços](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Cria uma aplicação a partir de um tipo de candidatura registada. |
| [Pacote de aplicações de eliminação de serviços](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Remove um pacote de aplicação Service Fabric da loja de imagens.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo PowerShell de tecido de serviço, consulte [a documentação Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Pode ver exemplos adicionais do Powershell para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

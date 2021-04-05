---
title: Implementar aplicação para um cluster em Powershell
description: Azure PowerShell Script Sample - Implemente uma aplicação num cluster de tecido de serviço.
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
ms.openlocfilehash: 1d25ede5ae871eddd965594224b518ec42525451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791294"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Implementar uma aplicação num cluster do Service Fabric

Este script de amostra copia um pacote de aplicação para uma loja de imagem cluster, regista o tipo de aplicação no cluster, remove o pacote de aplicação desnecessário, e cria uma instância de aplicação a partir do tipo de aplicação.  Se quaisquer serviços predefinidos foram definidos no manifesto de aplicação do tipo de aplicação alvo, então esses serviços são criados neste momento. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo PowerShell do Service Fabric com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Após a execução da amostra de script, o script em [Remover uma aplicação](service-fabric-powershell-remove-application.md) pode ser usada para remover a instância da aplicação, não registar o tipo de aplicação e eliminar o pacote de aplicações da loja de imagens.

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster)| Cria uma ligação a um cluster de Tecido de Serviço. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) | Copia um pacote de aplicações para a loja de imagens de cluster.  |
|[Registo-ServiçoFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype)| Regista um tipo de aplicação e versão no cluster. |
|[New-ServiceFabricApplicação](/powershell/module/servicefabric/new-servicefabricapplication)| Cria uma aplicação a partir de um tipo de aplicação registado. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage) | Remove um pacote de aplicações de Tecido de Serviço da loja de imagens.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo PowerShell do tecido de serviço, consulte [a documentação Azure PowerShell](/powershell/azure/service-fabric/overview).

Pode ver exemplos adicionais do Powershell para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

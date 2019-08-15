---
title: Exemplo de script Azure PowerShell-remover aplicativo de um cluster | Microsoft Docs
description: Azure PowerShell exemplo de script – remover um aplicativo de um Cluster Service Fabric.
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
ms.openlocfilehash: f572996b4e460480335dbe32c4599bde206251d4
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035502"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Remover uma aplicação de um cluster do Service Fabric Explorer

Este script de exemplo exclui uma instância de aplicativo Service Fabric em execução e cancela o registro de um tipo de aplicativo e versão do cluster.  A eliminação da instância da aplicação também elimina todas as instâncias do serviço em execução associadas a essa aplicação. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo PowerShell do Service Fabric com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Remove uma instância do aplicativo Service Fabric em execução do cluster.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Cancela o registro de um tipo de aplicativo Service Fabric e a versão do cluster. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Service Fabric PowerShell, consulte [Azure PowerShell documentação](/powershell/azure/service-fabric/?view=azureservicefabricps).

Pode ver exemplos adicionais do Powershell para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

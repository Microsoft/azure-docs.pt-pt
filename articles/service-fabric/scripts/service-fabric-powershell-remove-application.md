---
title: Remover a aplicação de um cluster em Powershell
description: Azure PowerShell Script Sample - Remova uma aplicação de um cluster de tecido de serviço.
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
ms.openlocfilehash: 8b4b07288ce2c3570da5482a446b9418c7319011
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87086205"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>Remova uma aplicação de um cluster de tecido de serviço usando Powershell

Este script de amostra elimina uma aplicação de serviço em execução e não registra um tipo de aplicação e versão do cluster.  A eliminação da instância da aplicação também elimina todas as instâncias do serviço em execução associadas a essa aplicação. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo PowerShell do Service Fabric com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Remover-ServiçoFabricApplicação](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Remove uma aplicação de tecido de serviço em execução do cluster.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Desregralizado um tipo de aplicação de Tecido de Serviço e versão do cluster. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo PowerShell do tecido de serviço, consulte [a documentação Azure PowerShell](/powershell/azure/service-fabric/overview?view=azureservicefabricps).

Pode ver exemplos adicionais do Powershell para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

---
title: Remover a aplicação de um cluster em Powershell
description: Amostra de script Azure PowerShell - Remova uma aplicação de um cluster de tecido de serviço.
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
ms.openlocfilehash: 9ac4c23468c74b5a2c6874de2cb6b8d0e6b9e7dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610289"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>Remova uma aplicação de um cluster de tecido de serviço usando powershell

Este script de amostra elimina uma instância de aplicação de serviço de funcionamento e desregista um tipo de aplicação e versão do cluster.  A eliminação da instância da aplicação também elimina todas as instâncias do serviço em execução associadas a essa aplicação. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo PowerShell do Service Fabric com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Remover-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Remove uma instância de aplicação de tecido de serviço em execução do cluster.  |
| [Não registro-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Desregistra um tipo de aplicação de Tecido de Serviço e versão do cluster. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo PowerShell de tecido de serviço, consulte [a documentação Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Pode ver exemplos adicionais do Powershell para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

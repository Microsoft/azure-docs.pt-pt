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
ms.openlocfilehash: 686afa791df88382e3e5e1b2d233317c36bf1dd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791310"
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
| [Remover-ServiçoFabricApplicação](/powershell/module/servicefabric/remove-servicefabricapplication) | Remove uma aplicação de tecido de serviço em execução do cluster.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | Desregralizado um tipo de aplicação de Tecido de Serviço e versão do cluster. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo PowerShell do tecido de serviço, consulte [a documentação Azure PowerShell](/powershell/azure/service-fabric/overview).

Pode ver exemplos adicionais do Powershell para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

---
title: Exemplo de Script do PowerShell do Azure - remover aplicações de um cluster | Documentos da Microsoft
description: Exemplo do Azure de Script do PowerShell - remover uma aplicação a partir de um cluster do Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 05edc6bce6744acd14dea2358663c4097922f2ce
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667468"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Remover uma aplicação de um cluster do Service Fabric Explorer

Este script de exemplo elimina uma instância de aplicação do Service Fabric em execução e anula o registo de um tipo de aplicação e a versão do cluster.  A eliminação da instância da aplicação também elimina todas as instâncias do serviço em execução associadas a essa aplicação. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo PowerShell do Service Fabric com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Remove uma instância de aplicação do Service Fabric em execução do cluster.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Anula o registo de um tipo de aplicação do Service Fabric e a versão do cluster. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do PowerShell do Service Fabric, veja [documentação do Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Pode ver exemplos adicionais do Powershell para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

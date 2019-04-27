---
title: Exemplo de Script do PowerShell do Azure - atualização de uma aplicação do Service Fabric | Documentos da Microsoft
description: Exemplo do Azure de Script do PowerShell - atualizar uma aplicação do Service Fabric.
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
ms.openlocfilehash: 2f896e0ff8d05d9c77a4a11c97afd9c0ffc17b7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621762"
---
# <a name="upgrade-a-service-fabric-application"></a>Atualizar uma aplicação do Service Fabric

Este script de exemplo atualiza uma instância de aplicação do Service Fabric em execução para a versão 1.3.0. O script copia o novo pacote de aplicação para o arquivo de imagem do cluster, registra o tipo de aplicação e remove o pacote de aplicações desnecessárias.  O script é iniciado uma atualização monitorizada e continuamente verifica o estado de atualização até que a atualização é concluída ou revertida. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo PowerShell do Service Fabric com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Obtém todas as aplicações no cluster do Service Fabric ou uma aplicação específica.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Obtém o estado de uma atualização de aplicação do Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Obtém os tipos de aplicação do Service Fabric registados no cluster do Service Fabric. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Anula o registo de um tipo de aplicação do Service Fabric.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copia um pacote de aplicação do Service Fabric para o arquivo de imagem.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Registra um tipo de aplicação do Service Fabric. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Atualiza uma aplicação do Service Fabric para a versão do tipo de aplicação especificada. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Remove um pacote de aplicação do Service Fabric a partir do arquivo de imagem.|


## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do PowerShell do Service Fabric, veja [documentação do Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Pode ver exemplos adicionais do Powershell para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

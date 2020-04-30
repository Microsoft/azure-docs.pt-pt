---
title: Atualizar uma aplicação de Tecido de Serviço em Powershell
description: Amostra de script Azure PowerShell - Atualize e monitorize uma aplicação De Tecido de Serviço Azure utilizando powershell.
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
ms.openlocfilehash: 3a4ef9fad8567eb145d51c6fef61773cc3a00b11
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75614745"
---
# <a name="upgrade-a-service-fabric-application"></a>Atualizar uma aplicação de Tecido de Serviço

Este script de amostra atualiza uma instância de aplicação de Serviço Fabric em execução para a versão 1.3.0. O script copia o novo pacote de aplicações para a loja de imagens cluster, regista o tipo de aplicação e remove o pacote de aplicação desnecessário.  O script inicia uma atualização monitorizada e verifica continuamente o estado de atualização até que a atualização complete ou recue. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo PowerShell do Service Fabric com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Aplicação get-servicefabricapplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Obtém todas as aplicações no cluster Service Fabric ou uma aplicação específica.  |
| [Upgrade get-serviceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Obtém o estatuto de uma atualização da aplicação Service Fabric. |
| [Tipo de aplicação get-serviceFabric](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Obtém os tipos de aplicação service Fabric registados no cluster Service Fabric. |
| [Não registro-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Desregistra um tipo de aplicação de Tecido de Serviço.  |
| [Pacote de aplicações de copy-servicefabricapplication](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copia um pacote de aplicação Service Fabric para a loja de imagens.  |
| [Tipo de aplicação de serviço de registo](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Regista um tipo de aplicação de Tecido de Serviço. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Atualiza uma aplicação de Tecido de Serviço para a versão especificada do tipo de aplicação. |
| [Pacote de aplicações de eliminação de serviços](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Remove um pacote de aplicação Service Fabric da loja de imagens.|


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo PowerShell de tecido de serviço, consulte [a documentação Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Pode ver exemplos adicionais do Powershell para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

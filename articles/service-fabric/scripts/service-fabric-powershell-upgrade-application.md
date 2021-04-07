---
title: Atualizar uma aplicação de tecido de serviço em Powershell
description: Azure PowerShell Script Sample - Atualize e monitorize uma aplicação de tecido de serviço Azure usando Powershell.
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
ms.openlocfilehash: 7b55dc6a400f936ac23b233e4c84a6b1aebf45cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784701"
---
# <a name="upgrade-a-service-fabric-application"></a>Atualizar uma aplicação de Tecido de Serviço

Este script de amostra atualiza uma aplicação de tecido de serviço em execução para a versão 1.3.0. O script copia o novo pacote de aplicações para a loja de imagem cluster, regista o tipo de aplicação e remove o pacote de aplicações desnecessário.  O script inicia uma atualização monitorizada e verifica continuamente o estado da atualização até que a atualização complete ou volte. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo PowerShell do Service Fabric com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-ServiceFabricApplicação](/powershell/module/servicefabric/get-servicefabricapplication) | Obtém todas as aplicações no cluster de Tecido de Serviço ou uma aplicação específica.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) | Obtém o estado de uma atualização de aplicação de Tecido de Serviço. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) | Obtém os tipos de aplicação de Tecido de Serviço registados no cluster de Tecido de Serviço. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | Desregista um tipo de aplicação de Tecido de Serviço.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) | Copia um pacote de aplicações de Tecido de Serviço na loja de imagens.  |
| [Registo-ServiçoFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) | Regista um tipo de aplicação de Tecido de Serviço. |
| [Serviço de InícioFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) | Atualiza uma aplicação de Tecido de Serviço para a versão especificada do tipo de aplicação. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage) | Remove um pacote de aplicações de Tecido de Serviço da loja de imagens.|


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo PowerShell do tecido de serviço, consulte [a documentação Azure PowerShell](/powershell/azure/service-fabric/overview).

Pode ver exemplos adicionais do Powershell para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

---
title: 'PowerShell: dimensionar com o Gerenciador de tráfego'
description: Saiba como usar Azure PowerShell para automatizar a implantação e o gerenciamento do serviço de aplicativo. Este exemplo mostra como dimensionar um aplicativo em todo o mundo com o Gerenciador de tráfego.
tags: azure-service-management
ms.assetid: 470f0129-1efe-462c-a029-5c66e04158a8
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 6ffcf22b640d6393ed48d803f6f52bac5334d4b7
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684331"
---
# <a name="scale-a-web-app-worldwide-with-a-high-availability-architecture"></a>Dimensionar uma aplicação Web para todo o mundo com uma arquitetura de elevada disponibilidade

Neste cenário criará um grupo de recursos, dois planos do serviço de aplicações, duas aplicações Web, um perfil do gestor de tráfego e dois pontos finais do gestor de tráfego. Assim que o exercício estiver concluído, terá uma arquitetura de elevada disponibilidade, que permite fornecer a disponibilidade global da sua aplicação Web com base na latência de rede mais baixa.

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure.

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/scale-geographic/scale-geographic.ps1 "Scale a web app worldwide with a high-availability architecture")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o seguinte comando pode ser utilizado para remover o grupo de recursos, a aplicação Web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Cria um perfil do Gestor de Tráfego. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um plano do Serviço de Aplicações. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria uma aplicação Web. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Cria um ponto final num Perfil do Gestor de Tráfego. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver os exemplos do Azure Powershell adicionais para as Aplicações Web do Serviço de Aplicações do Azure em [Exemplos do Azure PowerShell](../samples-powershell.md).

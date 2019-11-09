---
title: Azure PowerShell amostra de script – rotear o tráfego para alta disponibilidade de aplicativos | Microsoft Docs
description: Azure PowerShell amostra de script – rotear o tráfego para alta disponibilidade de aplicativos
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 05/16/2017
ms.author: allensu
ms.openlocfilehash: 2f3b4ef9abdfae2c0400f54a90694db468470c94
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889501"
---
# <a name="route-traffic-for-high-availability-of-applications"></a>Rotear o tráfego para alta disponibilidade de aplicativos

Esse script cria um grupo de recursos, dois planos do serviço de aplicativo, dois aplicativos Web, um perfil do Gerenciador de tráfego e dois pontos de extremidade do Gerenciador de tráfego. O Gerenciador de tráfego direciona o tráfego para o aplicativo em uma região como a região primária e para a região secundária quando o aplicativo na região primária está indisponível. Antes de executar o script, você deve alterar os valores de myWebApp, MyWebAppL1 e MyWebAppL2 para valores exclusivos no Azure. Depois de executar o script, você pode acessar o aplicativo na região primária com a URL mywebapp.trafficmanager.net.

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma aplicação Web, um perfil do gestor de tráfego e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um plano do Serviço de Aplicações. Isso é como um farm de servidores para seu aplicativo Web do Azure. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria um aplicativo Web do Azure dentro do plano do serviço de aplicativo. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Cria um aplicativo Web do Azure dentro do plano do serviço de aplicativo. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Cria um perfil do Gestor de Tráfego do Azure. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Adiciona um ponto final a um Perfil do Gestor de Tráfego do Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Encontrará exemplos adicionais de scripts do PowerShell de redes na [Documentação de Descrição Geral de Redes do Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).

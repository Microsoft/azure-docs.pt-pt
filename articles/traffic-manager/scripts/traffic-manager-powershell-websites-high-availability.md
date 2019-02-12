---
title: Exemplo do script do Azure PowerShell - encaminhar o tráfego de elevada disponibilidade de aplicativos | Documentos da Microsoft
description: Exemplo do script do Azure PowerShell - encaminhar o tráfego de elevada disponibilidade de aplicações
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: kumud
ms.openlocfilehash: 4991be7ecb252001245a0bcde8551d8432b0794b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998506"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-powershell"></a>Encaminhar o tráfego de elevada disponibilidade de aplicações com o Azure PowerShell

Este script cria um grupo de recursos, dois planos de serviço de aplicações, duas aplicações web, um perfil do Gestor de tráfego e dois pontos de extremidade de Gestor de tráfego. O Gestor de tráfego direciona o tráfego para a aplicação numa única região, como a região primária e para a região secundária quando a aplicação na região primária estiver indisponível. Antes de executar o script, tem de alterar os valores de MyWebApp, MyWebAppL1 e MyWebAppL2 para valores exclusivos em todo o Azure. Depois de executar o script, pode acessar a aplicação na região primária com o mywebapp.trafficmanager.net de URL.

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure.

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
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um plano do Serviço de Aplicações. É como um farm de servidores para a sua aplicação web do Azure. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria uma aplicação web do Azure no plano do serviço de aplicações. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Cria uma aplicação web do Azure no plano do serviço de aplicações. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Cria um perfil do Gestor de Tráfego do Azure. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Adiciona um ponto final a um perfil de Gestor de tráfego do Azure. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Encontrará exemplos adicionais de scripts do PowerShell de redes na [Documentação de Descrição Geral de Redes do Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).

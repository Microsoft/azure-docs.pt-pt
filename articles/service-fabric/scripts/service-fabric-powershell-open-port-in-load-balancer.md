---
title: Porta de aplicação aberta em equilibrador de carga em Powershell
description: Exemplo de Script do Azure PowerShell - Abrir uma porta no balanceador de carga do Azure para uma aplicação do Service Fabric.
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
ms.date: 05/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 3e5e1df77b8bc701bf330d98f264db26a01ea748
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614762"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Abrir uma porta de aplicação no balanceador de carga do Azure

Uma aplicação do Service Fabric em execução no Azure encontra-se por trás do balanceador de carga do Azure. Este script de exemplo abre uma porta num balanceador de carga do Azure para que uma aplicação do Service Fabric possa comunicar com clientes externos. Personalize os parâmetros conforme necessário. Se o cluster estiver num grupo de segurança de rede, também terá de [adicionar uma regra de grupo de segurança de rede de entrada](service-fabric-powershell-add-nsg-rule.md) para permitir tráfego de entrada.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessário, instale o módulo PowerShell do Service Fabric com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtém um recurso do Azure.  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | Obtém o balanceador de carga do Azure. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | Adiciona uma configuração de pesquisa a um balanceador de carga.|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | Obtém uma configuração de pesquisa para um balanceador de carga. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | Adiciona uma configuração de regra a um balanceador de carga. |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | Define o estado de objetivos para um balanceador de carga. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais do Powershell para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

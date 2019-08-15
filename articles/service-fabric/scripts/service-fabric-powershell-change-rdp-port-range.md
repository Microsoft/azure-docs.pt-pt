---
title: Exemplo de Script do Azure PowerShell - Alterar o intervalo de portas RDP | Microsoft Docs
description: Exemplo de Script do Azure PowerShell - Altera o intervalo de portas RDP de um cluster implementado.
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
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 4ca7b4085a3978dc3218480c5c8a42679d80de23
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035655"
---
# <a name="update-the-rdp-port-range-values"></a>Atualizar os valores de intervalos de portas RDP

Este script de exemplo altera os valores de intervalos de portas RDP nas VMs do nó de cluster após o cluster ser implementado.  O Azure PowerShell é utilizado para que não haja ciclos das VMs correspondentes.  O script obtém o recurso `Microsoft.Network/loadBalancers` no grupo de recursos de cluster e atualiza os valores `inboundNatPools.frontendPortRangeStart` e `inboundNatPools.frontendPortRangeEnd`. Personalize os parâmetros conforme necessário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessário, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtenha o recurso `Microsoft.Network/loadBalancers`. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Atualiza o recurso `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver os exemplos do Azure Powershell adicionais para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

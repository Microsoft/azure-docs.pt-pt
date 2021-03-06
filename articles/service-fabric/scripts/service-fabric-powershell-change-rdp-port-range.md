---
title: Exemplo de Script do Azure PowerShell - Alterar o intervalo de portas RDP | Microsoft Docs
description: Exemplo de Script do Azure PowerShell - Altera o intervalo de portas RDP de um cluster implementado.
services: service-fabric
tags: azure-service-management
author: athinanthny
ms.author: atsenthi
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.openlocfilehash: 750a2100d34e02cac7c613cc6b95160fc348b535
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576371"
---
# <a name="update-the-rdp-port-range-values"></a>Atualizar os valores de intervalos de portas RDP

Este script de exemplo altera os valores de intervalos de portas RDP nas VMs do nó de cluster após o cluster ser implementado.  O Azure PowerShell é utilizado para que não haja ciclos das VMs correspondentes.  O script obtém o `Microsoft.Network/loadBalancers` recurso no grupo de recursos do cluster e atualiza os `inboundNatPools.frontendPortRangeStart` valores e `inboundNatPools.frontendPortRangeEnd` valores. Personalize os parâmetros conforme necessário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessário, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/).

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtenha o recurso `Microsoft.Network/loadBalancers`. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Atualiza o recurso `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver os exemplos do Azure Powershell adicionais para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

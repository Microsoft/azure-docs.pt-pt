---
title: Exemplo de script Azure PowerShell-adicionar uma regra de grupo de segurança de rede | Microsoft Docs
description: Azure PowerShell exemplo de script – adiciona um grupo de segurança de rede para permitir o tráfego de entrada em uma porta específica.
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
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 1418b85bfa2579ed2dee5211a79a75adf14f1547
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592288"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Adicionar uma regra de grupo de segurança de rede de entrada

Este script de exemplo cria uma regra de grupo de segurança de rede para permitir o tráfego de entrada na porta 8081.  O script Obtém o `Microsoft.Network/networkSecurityGroups` recurso no qual o cluster está localizado, cria uma nova regra de configuração de segurança de rede e atualiza o grupo de segurança de rede. Personalize os parâmetros conforme necessário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessário, instale o Azure PowerShell usando as instruções encontradas no [Guia de Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtenha o recurso `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Obtém o grupo de segurança de rede pelo nome.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Define o estado da meta para um grupo de segurança de rede.|

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

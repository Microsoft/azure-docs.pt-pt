---
title: Script do Azure PowerShell de exemplo - adicionar uma regra de grupo de segurança de rede | Documentos da Microsoft
description: Exemplo de Script do PowerShell do Azure - adiciona um grupo de segurança de rede para permitir tráfego de entrada numa porta específica.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: fead6aa19775eab6cc3a1014e9f52b30cfa2cefa
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498320"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Adicionar uma regra de grupo de segurança de rede de entrada

Este script de exemplo cria uma regra de grupo de segurança de rede para permitir tráfego de entrada na porta 8081.  O script obtém o `Microsoft.Network/networkSecurityGroups` cria uma nova regra de configuração de segurança de rede de recursos que o cluster é localizado e atualiza o grupo de segurança de rede. Personalize os parâmetros conforme necessário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessário, instale o Azure PowerShell com as instruções no [Guia do Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtenha o recurso `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Obtém o grupo de segurança de rede com o nome.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Define o estado de objetivos para um grupo de segurança de rede.|

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

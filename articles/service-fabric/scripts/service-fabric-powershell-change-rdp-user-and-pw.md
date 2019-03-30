---
title: Exemplo de Script do Azure PowerShell - Atualizar o nome de utilizador e palavra-passe do RDP | Microsoft Docs
description: Exemplo de Script do Azure PowerShell - Atualizar o nome de utilizador e palavra-passe do RDP para todos os nós de cluster do Service Fabric de um tipo de nó específico.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 1528ce03f548c70d20d8f7af0238019305f01f2e
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660821"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Atualizar o nome de utilizador de administrador e a palavra-passe das VMs num cluster

Cada [tipo de nó](../service-fabric-cluster-nodetypes.md) num cluster do Service Fabric é um conjunto de dimensionamento de máquinas virtuais. Este script de exemplo atualiza o nome de utilizador de administrador e a palavra-passe para as máquinas virtuais do cluster de um tipo de nó específico.  Adicione a extensão VMAccessAgent ao conjunto de dimensionamento, porque a palavra-passe de administrador não é uma propriedade modificável do conjunto de dimensionamento.  As alterações de nome de utilizador e palavra-passe aplicam-se a todos os nós no conjunto de dimensionamento. Personalize os parâmetros conforme necessário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessário, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Obtém as propriedades de um tipo de nó de cluster (um conjunto de dimensionamento de máquinas virtuais).   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Adiciona uma extensão ao conjunto de dimensionamento de máquinas virtuais.|
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss)|Atualiza o estado de um conjunto de dimensionamento de máquinas virtuais definido como o estado de um objeto VMSS local.|

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver os exemplos do Azure Powershell adicionais para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

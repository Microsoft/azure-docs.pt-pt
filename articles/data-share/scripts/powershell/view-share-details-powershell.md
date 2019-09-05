---
title: 'Script do PowerShell: Listar compartilhamentos existentes no compartilhamento de dados do Azure | Microsoft Docs'
description: Este script do PowerShell lista e exibe detalhes de compartilhamentos.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 6314bd348c22c901001b88eda6875181a2f69df4
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307129"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Usar o PowerShell para exibir os detalhes de um compartilhamento de dados enviados

Esse script do PowerShell lista os compartilhamentos de dados de uma conta existente e obtém os detalhes de um compartilhamento específico.


## <a name="sample-script"></a>Script de exemplo

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare?view=azps-2.6.0) | Obtém e lista de compartilhamentos em uma conta. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script do PowerShell do compartilhamento de dados do Azure podem ser encontrados nos [exemplos do PowerShell do compartilhamento de dados do Azure](../../samples-powershell.md).

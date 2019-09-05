---
title: 'Script do PowerShell: Criar nova conta de compartilhamento de dados do Azure | Microsoft Docs'
description: Esse script do PowerShell cria uma nova conta de compartilhamento de dados.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c3852dd5f1d3d3df8a982716ce5dab9426782869
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307278"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Usar o PowerShell para criar uma conta de compartilhamento de dados no Azure

Esse script do PowerShell cria uma nova conta de compartilhamento de dados. 

## <a name="sample-script"></a>Script de exemplo

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [New-AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount?view=azps-2.6.0) | Cria uma conta de compartilhamento de dados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script do PowerShell do compartilhamento de dados do Azure podem ser encontrados nos [exemplos do PowerShell do compartilhamento de dados do Azure](../../samples-powershell.md).

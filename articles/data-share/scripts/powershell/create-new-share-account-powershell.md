---
title: 'Script PowerShell: Criar nova conta Azure Data Share [ Microsoft Docs'
description: Este script PowerShell cria uma nova conta Data Share.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c3852dd5f1d3d3df8a982716ce5dab9426782869
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70307278"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Use o PowerShell para criar uma conta de partilha de dados no Azure

Este script PowerShell cria uma nova conta Data Share. 

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
| [New-AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount?view=azps-2.6.0) | Cria uma conta de partilha de dados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script de partilha de dados azure PowerShell podem ser encontradas nas [amostras de PowerShell de partilha](../../samples-powershell.md)de dados do Azure .

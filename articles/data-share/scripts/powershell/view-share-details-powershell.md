---
title: 'Script PowerShell: Listar as ações existentes na Azure Data Share'
description: Este script PowerShell lista e apresenta detalhes de partilhas.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 54b4b6cedc2dc09e4a9d5c4b7b937e80750e17c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220827"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Use o PowerShell para ver os detalhes de uma partilha de dados enviada

Este script PowerShell lista as partilhas de dados de uma conta existente e obtém os detalhes de uma ação específica.


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
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare) | Recebe e listas de ações numa conta. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).

Amostras adicionais de scripts Azure Data Share PowerShell podem ser encontradas nas [amostras powerShell de partilha de dados Azure](../../samples-powershell.md).

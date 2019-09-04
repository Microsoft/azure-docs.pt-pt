---
title: 'Script do PowerShell: criar um novo compartilhamento de dados do Azure | Microsoft Docs'
description: Esse script do PowerShell cria um novo compartilhamento de dados em uma conta de compartilhamento de dados existente.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c76dfea83d0f736dbd48239cc3be3496a88ec8a2
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242999"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Usar o PowerShell para criar um compartilhamento de dados no Azure

Esse script do PowerShell cria um novo compartilhamento de dados em uma conta de compartilhamento de dados existente.

## <a name="sample-script"></a>Script de exemplo

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [New-AzDataShare](/powershell/module/az.resources/new-azdatashare) | Cria um compartilhamento de dados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script do PowerShell do compartilhamento de dados do Azure podem ser encontrados nos [exemplos do PowerShell do compartilhamento de dados do Azure](../../samples-powershell.md).

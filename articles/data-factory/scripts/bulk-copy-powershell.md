---
title: Copiar dados a granel usando powerShell
description: Este script PowerShell mostra como usar a Azure Data Factory para copiar dados de uma loja de dados de origem para uma loja de dados de destino a granel.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: efc79f82a2181099f832da0d4a17fc370bf4f7f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74929873"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell script - copie várias tabelas a granel usando a Azure Data Factory

Esta amostra de script PowerShell copia dados de várias tabelas numa base de dados Azure SQL para um armazém de dados Azure SQL.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Consulte [tutorial: cópia em massa](../tutorial-bulk-copy.md#prerequisites) para os pré-requisitos para executar esta amostra.

## <a name="sample-script"></a>Script de exemplo

> [!IMPORTANT]
> Este script cria ficheiros JSON que definem entidades da Data Factory (serviço ligado, conjunto de dados e pipeline) no seu disco rígido no c:\ pasta.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script da amostra, pode utilizar o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Para remover a fábrica de dados do grupo de recursos, executar o seguinte comando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Criar uma fábrica de dados. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Cria um serviço ligado na fábrica de dados. Um serviço ligado liga uma loja de dados ou uma computação a uma fábrica de dados. |
| [Conjunto-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Cria um conjunto de dados na fábrica de dados. Um conjunto de dados representa a entrada/saída para uma atividade num gasoduto. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Cria um oleoduto na fábrica de dados. Um oleoduto contém uma ou mais atividades que realizam uma determinada operação. Neste pipeline, uma atividade de cópia copia dados de um local para outro local num Armazém Azure Blob. |
| [Invocar-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Cria uma corrida para o oleoduto. Por outras palavras, corre o oleoduto. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | Obtém detalhes sobre o execução da atividade (execução de atividade) no oleoduto. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script da Azure Data Factory PowerShell podem ser encontradas nos [scripts PowerShell da Fábrica](../samples-powershell.md)de Dados Azure .

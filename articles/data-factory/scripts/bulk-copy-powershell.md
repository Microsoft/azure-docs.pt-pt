---
title: 'Script do PowerShell: copiar dados em massa usando Azure Data Factory '
description: Este script do PowerShell mostra como usar Azure Data Factory para copiar dados de um armazenamento de dados de origem para um armazenamento de dados de destino em massa.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 8ab373d049e34a6a5528a4e0a3418a3304835d4b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684440"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Script do PowerShell – Copie várias tabelas em massa usando Azure Data Factory

Este script de exemplo do PowerShell copia dados de várias tabelas em um banco de dados SQL do Azure para um Azure SQL data warehouse.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Consulte [tutorial: cópia em massa](../tutorial-bulk-copy.md#prerequisites) para obter os pré-requisitos para executar este exemplo.

## <a name="sample-script"></a>Script de exemplo

> [!IMPORTANT]
> Esse script cria arquivos JSON que definem entidades de Data Factory (serviço vinculado, conjunto de serviços e Pipeline) em seu disco rígido em c:\ pasta.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, você pode usar o seguinte comando para remover o grupo de recursos e todos os recursos associados a ele:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Para remover o data factory do grupo de recursos, execute o seguinte comando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Criar uma fábrica de dados. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Cria um serviço vinculado no data factory. Um serviço vinculado vincula um armazenamento de dados ou computa a um data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Cria um conjunto de um DataSet no data factory. Um conjunto de dados representa entrada/saída para uma atividade em um pipeline. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Cria um pipeline no data factory. Um pipeline contém uma ou mais atividades que executam uma determinada operação. Nesse pipeline, uma atividade de cópia copia dados de um local para outro local em um armazenamento de BLOBs do Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Cria uma execução para o pipeline. Em outras palavras, o executa o pipeline. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | Obtém detalhes sobre a execução da atividade (execução da atividade) no pipeline. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script do Azure Data Factory PowerShell podem ser encontrados nos [scripts do Azure data Factory PowerShell](../samples-powershell.md).

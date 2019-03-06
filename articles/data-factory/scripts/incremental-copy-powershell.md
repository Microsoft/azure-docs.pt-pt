---
title: 'Script do PowerShell: Carregar dados de forma incremental com o Azure Data Factory | Documentos da Microsoft'
description: Este script do PowerShell mostra como utilizar o Azure Data Factory para copiar dados incrementalmente uma base de dados do SQL do Azure para um armazenamento de Blobs do Azure....
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
ms.openlocfilehash: 5ae6d6ed06aa5734dc601e6e72ba55ec8ddf7bcf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440595"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>Script do PowerShell - incrementalmente carregar dados com o Azure Data Factory
Este script do PowerShell de exemplo carrega registos novos ou atualizados apenas de um arquivo de dados de origem para um arquivo de dados de sink após a cópia inicial completa dos dados da origem para o sink.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Ver [tutorial: cópia incremental](../tutorial-incremental-copy-powershell.md#prerequisites) para as pré-requisitos para executar este exemplo. 

## <a name="sample-script"></a>Script de exemplo

> [!IMPORTANT]
> Este script cria ficheiros JSON que definem as entidades do Data Factory (serviço ligado, conjunto de dados e pipeline) no seu disco rígido na pasta c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1 "Incremental copy from Azure SQL Database to Azure Blob Storage")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode utilizar o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Para remover a fábrica de dados do grupo de recursos, execute o seguinte comando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Criar uma fábrica de dados. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Cria um serviço ligado da fábrica de dados. Os serviços ligados ligam um arquivo de dados ou a computação para uma fábrica de dados. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Cria um conjunto de dados da fábrica de dados. Um conjunto de dados representa a entrada/saída de uma atividade num pipeline. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Cria um pipeline na fábrica de dados. Um pipeline que contém uma ou mais atividades que executa uma operação de determinados. Este pipeline, uma atividade de cópia copia dados de uma localização para outra localização num armazenamento de Blobs do Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Cria uma execução do pipeline. Em outras palavras, executa o pipeline. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Obtém os detalhes sobre a execução da atividade (execução de atividade) no pipeline. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos do script do PowerShell de fábrica de dados do Azure adicionais podem ser encontrados no [scripts do PowerShell do Azure Data Factory](../samples-powershell.md).

---
title: Copiar dados na nuvem usando o PowerShell
description: Este script do PowerShell copia dados de um local em um armazenamento de BLOBs do Azure para outro local no mesmo armazenamento de BLOBs.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: 78314a5255613068ac73d88c734ee5e35e2e6f4b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977467"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Usar o PowerShell para criar um pipeline de data factory para copiar dados na nuvem

Este script do PowerShell de exemplo cria um pipeline no Azure Data Factory que copia dados de um local para outro em um armazenamento de BLOBs do Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Conta de Armazenamento do Azure**. Pode utilizar o armazenamento de blobs como arquivo de dados de **origem** e de **sink**. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md), que explica como criar uma. 
* Crie um **contentor de blobs** no Armazenamento de Blobs, crie uma **pasta** de entrada no contentor e carregue alguns ficheiros para a pasta. Pode utilizar ferramentas como o [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para ligar ao Armazenamento de Blobs do Azure, criar contentores de blobs, carregar o ficheiro de entrada e verificar o ficheiro de saída.

## <a name="sample-script"></a>Script de exemplo

> [!IMPORTANT]
> Esse script cria arquivos JSON que definem entidades de Data Factory (serviço vinculado, conjunto de serviços e Pipeline) em seu disco rígido em c:\ pasta.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Criar uma fábrica de dados. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Cria um serviço vinculado no data factory. Um serviço vinculado vincula um armazenamento de dados ou computa a um data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Cria um conjunto de um DataSet no data factory. Um conjunto de dados representa entrada/saída para uma atividade em um pipeline. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Cria um pipeline no data factory. Um pipeline contém uma ou mais atividades que executam uma determinada operação. Nesse pipeline, uma atividade de cópia copia dados de um local para outro local em um armazenamento de BLOBs do Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Cria uma execução para o pipeline. Em outras palavras, o executa o pipeline. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Obtém detalhes sobre a execução da atividade (execução da atividade) no pipeline.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script Azure Data Factory PowerShell podem ser encontrados nos [exemplos do Azure data Factory PowerShell](../samples-powershell.md).

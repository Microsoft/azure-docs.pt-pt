---
title: Copiar dados na nuvem usando powerShell
description: Este script PowerShell copia dados de um local num Armazém Azure Blob para outro local no mesmo Armazenamento Blob.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 2e289f30ef1c1883c38884eb563a41bccc841329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79462656"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Use o PowerShell para criar um oleoduto de fábrica de dados para copiar dados na nuvem

Esta amostra de script PowerShell cria um pipeline na Azure Data Factory que copia dados de um local para outro local num Armazenamento De Blob Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Conta de Armazenamento Azure.** Pode utilizar o armazenamento de blobs como arquivo de dados de **origem** e de **sink**. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md), que explica como criar uma. 
* Crie um **contentor de blobs** no Armazenamento de Blobs, crie uma **pasta** de entrada no contentor e carregue alguns ficheiros para a pasta. Pode utilizar ferramentas como o [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para ligar ao Armazenamento de Blobs do Azure, criar contentores de blobs, carregar o ficheiro de entrada e verificar o ficheiro de saída.

## <a name="sample-script"></a>Script de exemplo

> [!IMPORTANT]
> Este script cria ficheiros JSON que definem entidades da Data Factory (serviço ligado, conjunto de dados e pipeline) no seu disco rígido no c:\ pasta.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Criar uma fábrica de dados. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Cria um serviço ligado na fábrica de dados. Um serviço ligado liga uma loja de dados ou uma computação a uma fábrica de dados. |
| [Conjunto-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Cria um conjunto de dados na fábrica de dados. Um conjunto de dados representa a entrada/saída para uma atividade num gasoduto. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Cria um oleoduto na fábrica de dados. Um oleoduto contém uma ou mais atividades que realizam uma determinada operação. Neste pipeline, uma atividade de cópia copia dados de um local para outro local num Armazém Azure Blob. |
| [Invocar-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Cria uma corrida para o oleoduto. Por outras palavras, corre o oleoduto. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Obtém detalhes sobre o execução da atividade (execução de atividade) no oleoduto.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script da Fábrica de Dados Azure PowerShell podem ser encontradas nas [amostras de PowerShell da Fábrica](../samples-powershell.md)de Dados Azure .

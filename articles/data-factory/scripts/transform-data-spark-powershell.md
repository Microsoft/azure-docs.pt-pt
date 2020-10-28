---
title: Transforme dados em nuvem usando PowerShell
description: Este script PowerShell transforma dados na nuvem executando o programa Spark num cluster Azure HDInsight Spark.
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: 3af38f4cae53807c7268f00017f02f1325116fb6
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637059"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell script - transforme dados em nuvem usando Azure Data Factory

Esta amostra De script PowerShell cria um pipeline que transforma dados na nuvem executando o programa Spark num cluster Azure HDInsight Spark. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Conta de Armazenamento Azure** . Crie um script python e um ficheiro de entrada e faça o upload para o armazenamento Azure. A saída do programa Spark é armazenada nesta conta de armazenamento. O cluster do Spark a pedido utiliza a mesma conta de armazenamento como o respetivo armazenamento primário.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Carregar o script Python para a conta de Armazenamento de Blobs
1. Crie um ficheiro Python com o nome **WordCount_Spark.py** com o seguinte conteúdo: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Substitua **&lt; o armazenamentoAme &gt; de contagem** de armazenamento pelo nome da sua conta de Armazenamento Azure. Em seguida, guarde o ficheiro. 
3. No Armazenamento de Blobs do Azure, crie um contentor com o nome **adftutorial** , caso ainda não exista. 
4. Crie uma pasta com o nome **spark** .
5. Crie uma subpasta com o nome **script** na pasta **spark** . 
6. Carregue o ficheiro **WordCount_Spark.py** para a subpasta **script** . 


### <a name="upload-the-input-file"></a>Carregue o ficheiro de entrada
1. Crie um ficheiro com o nome **minecraftstory.txt** com algum texto. O programa Spark conta o número de palavras neste texto. 
2. Crie uma sub-página nomeada `inputfiles` na pasta do recipiente `spark` blob. 
3. Carregue o ficheiro `minecraftstory.txt` para a subpasta `inputfiles`. 

## <a name="sample-script"></a>Script de exemplo
> [!IMPORTANT]
> Este script cria ficheiros JSON que definem as entidades da Data Factory (serviço ligado, conjunto de dados e pipeline) no seu disco rígido no c:\ pasta.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script da amostra, pode utilizar o seguinte comando para remover o grupo de recursos e todos os recursos que lhe estão associados:

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
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Cria um serviço ligado na fábrica de dados. Um serviço ligado liga uma loja de dados ou um cálculo a uma fábrica de dados. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Cria um oleoduto na fábrica de dados. Um oleoduto contém uma ou mais atividades que realizam uma determinada operação. Neste oleoduto, uma atividade de faísca transforma dados executando um programa num cluster Azure HDInsight Spark. |
| [Invocar-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Cria uma corrida para o oleoduto. Por outras palavras, corre o oleoduto. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Obtém detalhes sobre o funcionação da atividade (atividade executada) no oleoduto. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).

Amostras adicionais de scripts powerShell da Fábrica de Dados Azure podem ser encontradas nas [amostras powerShell da Azure Data Factory](../samples-powershell.md).
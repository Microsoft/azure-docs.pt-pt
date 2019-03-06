---
title: PowerShell script-transformar dados na cloud com o Data Factory | Documentos da Microsoft
description: Este script do PowerShell transforma os dados na cloud ao executar o programa Spark num cluster do Azure HDInsight Spark.
services: data-factory
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
ms.author: shlo
ms.openlocfilehash: bfec4ffa4d8a9f41b9c9c55ab0d84f4133bd2445
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451711"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>Script do PowerShell - transformar dados na cloud com o Azure Data Factory

Este script do PowerShell de exemplo cria um pipeline que transforma os dados na cloud ao executar o programa Spark num cluster do Azure HDInsight Spark. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Conta de Armazenamento do Azure**. Crie um python script e um ficheiro de entrada e carregá-los para o armazenamento do Azure. A saída do programa Spark é armazenada nesta conta de armazenamento. O cluster do Spark a pedido utiliza a mesma conta de armazenamento como o respetivo armazenamento primário.  

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
2. Substitua **&lt;storageAccountName&gt;** pelo nome da sua conta de Armazenamento do Azure. Em seguida, guarde o ficheiro. 
3. No Armazenamento de Blobs do Azure, crie um contentor com o nome **adftutorial**, caso ainda não exista. 
4. Crie uma pasta com o nome **spark**.
5. Crie uma subpasta com o nome **script** na pasta **spark**. 
6. Carregue o ficheiro **WordCount_Spark.py** para a subpasta **script**. 


### <a name="upload-the-input-file"></a>Carregue o ficheiro de entrada
1. Crie um ficheiro com o nome **minecraftstory.txt** com algum texto. O programa Spark conta o número de palavras neste texto. 
2. Crie uma subpasta com o nome `inputfiles` no `spark` pasta do contentor de Blobs. 
3. Carregue o ficheiro `minecraftstory.txt` para a subpasta `inputfiles`. 

## <a name="sample-script"></a>Script de exemplo
> [!IMPORTANT]
> Este script cria ficheiros JSON que definem as entidades do Data Factory (serviço ligado, conjunto de dados e pipeline) no seu disco rígido na pasta c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

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
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Cria um serviço ligado da fábrica de dados. Os serviços ligados ligam um arquivo de dados ou a computação para uma fábrica de dados. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Cria um pipeline na fábrica de dados. Um pipeline que contém uma ou mais atividades que executa uma operação de determinados. Neste pipeline, uma atividade do spark transforma os dados ao executar um programa de um cluster do Azure HDInsight Spark. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Cria uma execução do pipeline. Em outras palavras, executa o pipeline. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Obtém os detalhes sobre a execução da atividade (execução de atividade) no pipeline. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos do script do PowerShell de fábrica de dados do Azure adicionais podem ser encontrados no [exemplos do PowerShell do Azure Data Factory](../samples-powershell.md).

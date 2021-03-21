---
title: Dados da amostra no Azure Blob Storage - Processo de Ciência de Dados de Equipa
description: Dados de amostragem armazenados no Azure Blob Storage, descarregando-os programáticamente e, em seguida, amostrando-os usando procedimentos escritos em Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c5827a0e07e537b66684f852d8f3e1500cd9febb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788846"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Dados da amostra no armazenamento de blob Azure

Este artigo cobre os dados de amostragem armazenados no Azure Blob Storage, descarregando-os programáticamente e, em seguida, amostrando-os utilizando procedimentos escritos em Python.

**Por que provar os seus dados?**
Se o conjunto de dados que pretende analisar é grande, é geralmente uma boa ideia reduzir a amostragem dos dados para reduzi-los a um tamanho menor, mas representativo e mais manejável. A amostragem facilita a compreensão de dados, a exploração e a engenharia de recursos. O seu papel no Processo de Análise cortana é permitir uma rápida prototipagem das funções de processamento de dados e modelos de machine learning.

Esta tarefa de amostragem é um passo no Processo de Ciência de Dados de [Equipa (TDSP)](./index.yml).

## <a name="download-and-down-sample-data"></a>Dados de descarregamento e amostra de amostra
1. Descarregue os dados do Azure Blob Storage utilizando o serviço Blob a partir do seguinte código Python da amostra: 

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>        
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

2. Leia os dados num quadro de dados do Pandas a partir do ficheiro descarregado acima.

    ```python
    import pandas as pd

    #directly ready from file on disk
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

3. Descodumos os dados utilizando os `numpy` `random.choice` seguintes:

    ```python
    # A 1 percent sample
    sample_ratio = 0.01 
    sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]
    ```

Agora pode trabalhar com o quadro de dados acima com a amostra de um por cento para mais exploração e geração de recursos.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Faça upload de dados e leia-os em Azure Machine Learning
Pode utilizar o seguinte código de amostra para recolher amostras dos dados e usá-lo diretamente no Azure Machine Learning:

1. Escreva o quadro de dados para um ficheiro local

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Faça o upload do ficheiro local para uma bolha Azure utilizando o seguinte código de amostra:

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading to the blob:"+ BLOBNAME)
    ```

3. Leia os dados da bolha Azure utilizando [dados de importação de aprendizagem de](/azure/machine-learning/studio-module-reference/import-data) máquina azure, como mostrado na imagem abaixo:

![bolha leitor](./media/sample-data-blob/reader_blob.png)

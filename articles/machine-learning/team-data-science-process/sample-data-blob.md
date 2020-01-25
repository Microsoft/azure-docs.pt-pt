---
title: Armazenamento - processo de ciência de dados de equipa de BLOBs de dados de exemplo no Azure
description: Dados armazenados no armazenamento de Blobs do Azure ao baixá-lo por meio de programação e, em seguida, a amostragem usando procedimentos escritos em Python de amostragem.
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
ms.openlocfilehash: 4832762a88073f4d819925659bf9078e18f60c2d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720287"
---
# <a name="heading"></a>Armazenamento de BLOBs de dados de exemplo no Azure

Este artigo aborda os dados de amostragem armazenados no armazenamento de Blobs do Azure ao baixá-lo por meio de programação e, em seguida, a amostragem usando procedimentos escritos em Python.

**Por que os dados de exemplo?**
Se o conjunto de dados que pretende analisar for grande, normalmente, é uma boa idéia para dimensionar os dados para reduzi-lo para um tamanho mais pequeno, mas representativo e mais gerenciável. A amostragem facilita a compreensão de dados, a exploração e a engenharia de recursos. Sua função no Cortana Analytics Process é ativar a criação de protótipos rápida das funções de processamento de dados e modelos de machine learning.

Esta tarefa de amostragem é uma etapa na [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="download-and-down-sample-data"></a>Transferir e dimensionar dados
1. Descarregue os dados do armazenamento de blob Azure utilizando o serviço Blob a partir do seguinte código Python da amostra: 
   
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

2. Ler os dados num quadro de dados Pandas do arquivo baixado anteriormente.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Dimensionar os dados com o `numpy`do `random.choice` da seguinte forma:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Agora você pode trabalhar com o quadro de dados acima com a amostra de um por cento para mais exploração e geração de recursos.

## <a name="heading"></a>Carregar dados e lê-lo no Azure Machine Learning
Pode utilizar o seguinte código de exemplo para dimensionar os dados e utilizá-lo diretamente no Azure Machine Learning:

1. Escrever o quadro de dados para um ficheiro local
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Carregar o ficheiro local para um blob do Azure com o seguinte código de exemplo:
   
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

3. Ler os dados de blob do Azure com o Azure Machine Learning [importar dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) conforme mostrado na imagem abaixo:

![blob de leitor](./media/sample-data-blob/reader_blob.png)


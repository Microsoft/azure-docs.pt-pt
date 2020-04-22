---
title: Explore dados no armazenamento de blob Azure com pandas - Team Data Science Process
description: Como explorar dados que são armazenados em recipiente de blob Azure usando o pacote pandas Python.
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
ms.openlocfilehash: e429dce497411305964cb1ec5298228dc4093b1f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685960"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Explore dados no armazenamento de blob Azure com pandas

Este artigo abrange como explorar dados que são armazenados em recipiente de blob Azure usando o pacote [Pandas](https://pandas.pydata.org/) Python.

Esta tarefa é um passo no Processo de Ciência de Dados da [Equipa.](overview.md)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criei uma conta de armazenamento Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento Azure](../../storage/common/storage-account-create.md)
* Guardei os seus dados numa conta de armazenamento de blob Azure. Se precisar de instruções, consulte [os dados em movimento de e para o Armazenamento Azure](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Carregue os dados num DataFrame pandas
Para explorar e manipular um conjunto de dados, deve primeiro ser descarregado da fonte blob para um ficheiro local, que pode ser carregado num DataFrame pandas. Aqui estão os passos a seguir para este procedimento:

1. Descarregue os dados da blob Azure com a seguinte amostra de código Python utilizando o serviço Blob. Substitua a variável no seguinte código com os seus valores específicos:

    ```python
    from azure.storage.blob import BlockBlobService
    import pandas as pd
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

1. Leia os dados num DataFrame pandas a partir do ficheiro descarregado.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

Agora está pronto para explorar os dados e gerar funcionalidades neste conjunto de dados.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Exemplos de exploração de dados usando pandas
Aqui estão alguns exemplos de formas de explorar dados usando pandas:

1. Inspecione o **número de linhas e colunas**

    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```

1. **Inspecione** as primeiras ou **últimas linhas** no conjunto de dados seguinte:

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Verifique o tipo de **dados de** cada coluna importada como utilizando o seguinte código de amostra

    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```

1. Verifique as **estatísticas básicas** das colunas no conjunto de dados da seguinte forma

    ```python
    dataframe_blobdata.describe()
    ```

1. Veja o número de entradas para cada valor de coluna da seguinte forma

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **Contar valores em falta** contra o número real de entradas em cada coluna utilizando o seguinte código de amostra

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```

1. Se tiver **valores em falta** para uma coluna específica nos dados, pode deixá-los cair da seguinte forma:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    Outra forma de substituir os valores em falta é a função de modo:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. Criar um enredo **histograma** usando o número variável de caixotes para traçar a distribuição de uma variável

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Veja **as correlações** entre variáveis usando uma trama de dispersão ou usando a função de correlação incorporada

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```

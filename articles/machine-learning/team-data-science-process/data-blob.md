---
title: Processe dados blob do Azure com análise avançada - Processo de Ciência de Dados de Equipa
description: Explore dados e gere recursos a partir de dados armazenados no armazenamento Azure Blob usando análises avançadas.
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
ms.openlocfilehash: 5a088d5918a957036b905db9136f9b16e5f0527e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307160"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>Processar dados de blobs do Azure com a análise avançada
Este documento abrange a exploração de dados e funcionalidades de geração a partir de dados armazenados no armazenamento de Azure Blob. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Carregue os dados num quadro de dados do Pandas
Para explorar e manipular um conjunto de dados, deve ser descarregado da fonte blob para um ficheiro local que pode ser carregado num quadro de dados do Pandas. Aqui estão os passos a seguir para este procedimento:

1. Descarregue os dados da blob Azure com a seguinte amostra do código Python utilizando o serviço Blob. Substitua a variável no código abaixo pelos seus valores específicos: 
   
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
2. Leia os dados num quadro de dados do Pandas a partir do ficheiro descarregado.
   
    ```python
    #LOCALFILE is the file path    
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

Agora está pronto para explorar os dados e gerar funcionalidades neste conjunto de dados.

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>Exploração de Dados
Aqui estão alguns exemplos de formas de explorar dados usando pandas:

1. Inspecione o número de linhas e colunas 
   
    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```
2. Inspecione as primeiras ou últimas linhas no conjunto de dados como abaixo:
   
    ```python
    dataframe_blobdata.head(10)
   
    dataframe_blobdata.tail(10)
    ```
3. Verifique se o tipo de dados de cada coluna foi importado como utilizando o seguinte código de amostra
   
    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```
4. Verifique as estatísticas básicas das colunas no conjunto de dados da seguinte forma
   
    ```python
    dataframe_blobdata.describe()
    ```
5. Veja o número de entradas para cada valor de coluna da seguinte forma
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```
6. Conte os valores em falta contra o número real de entradas em cada coluna utilizando o seguinte código de amostra
   
    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```
7. Se tiver valores em falta para uma coluna específica nos dados, pode larhá-los da seguinte forma:
   
    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```
   
   Outra forma de substituir os valores em falta é com a função de modo:
   
    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})  
    ```      
8. Crie um lote de histograma usando o número variável de caixotes para traçar a distribuição de uma variável    
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```
9. Veja as correlações entre variáveis usando uma dispersão ou usando a função de correlação incorporada
   
    ```python
    #relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
    #correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```

## <a name="feature-generation"></a><a name="blob-featuregen"></a>Geração de Recursos
Podemos gerar funcionalidades usando Python da seguinte forma:

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Geração de recursos baseada em valor indicador
As características categóricas podem ser criadas da seguinte forma:

1. Inspecione a distribuição da coluna categórica:
   
    ```python
    dataframe_blobdata['<categorical_column>'].value_counts()
    ```
2. Gerar valores indicadores para cada um dos valores da coluna
   
    ```python
    #generate the indicator column
    dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
    ```
3. Junte a coluna indicadora com o quadro de dados original 
   
    ```python
    #Join the dummy variables back to the original data frame
    dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
    ```
4. Remova a variável original em si:
   
    ```python
    #Remove the original column rate_code in df1_with_dummy
    dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    ```

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>Geração de Recursos de Binário
Para gerar características 44, procedemos da seguinte forma:

1. Adicione uma sequência de colunas para bin uma coluna numérica
   
    ```python
    bins = [0, 1, 2, 4, 10, 40]
    dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
    ```
2. Converter o binário numa sequência de variáveis booleanas
   
    ```python
    dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    ```
3. Finalmente, junte as variáveis falsas de volta ao quadro de dados original
   
    ```python
    dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)  
    ```  

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Escrever dados de volta à bolha de Azure e consumir em Azure Machine Learning
Depois de ter explorado os dados e criado as funcionalidades necessárias, pode enviar os dados (amostrados ou caracterizados) para uma bolha Azure e consumi-lo em Azure Machine Learning usando os seguintes passos: Funcionalidades adicionais podem ser criadas no Azure Machine Learning Studio (clássico) também. 

1. Escreva o quadro de dados para o arquivo local
   
    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```
2. Faça o upload dos dados para a bolha Azure da seguinte forma:
   
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
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```
3. Agora os dados podem ser lidos a partir da bolha utilizando o módulo de [dados de importação de aprendizagem de][import-data] máquina Azure, como mostrado no ecrã abaixo:

![bolha leitor][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data
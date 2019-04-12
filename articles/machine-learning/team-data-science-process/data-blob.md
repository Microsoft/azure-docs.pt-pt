---
title: Processar dados de Blobs do Azure com a análise avançada - Team Data Science Process
description: Explorar dados e gerar recursos a partir dos dados armazenados no armazenamento de Blobs do Azure através de análises avançadas.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a91c4d9f5dcdcee436f2dbf012eb5485b7a92192
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495622"
---
# <a name="heading"></a>Processar dados de Blobs do Azure com a análise avançada
Este documento aborda a explorar dados e as funcionalidades da geração de dados armazenados no armazenamento de Blobs do Azure. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Carregar os dados para um quadro de dados Pandas
Para explorar e manipular um conjunto de dados,-lo deverão ser transferido da origem de blob, para um ficheiro local que, em seguida, pode ser carregado num quadro de dados Pandas. Aqui estão as etapas a seguir para este procedimento:

1. Transferência dos dados do Azure de Blobs com o seguinte código de Python de exemplo com o serviço de Blobs. Substitua os valores específicos a variável no código abaixo: 
   
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
2. Ler os dados num quadro de dados Pandas do ficheiro transferido.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Agora, está pronto para explorar os dados e gerar recursos neste conjunto de dados.

## <a name="blob-dataexploration"></a>Exploração de dados
Aqui estão alguns exemplos de formas para explorar dados com o Pandas:

1. Inspecione o número de linhas e colunas 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Inspecione as primeiros ou últimas algumas linhas no conjunto de dados, conforme mostrado a seguir:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Verifique o tipo de dados que cada coluna foi importada como usando o seguinte código de exemplo
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Verifique as estatísticas básicas para as colunas no conjunto de dados da seguinte forma
   
        dataframe_blobdata.describe()
5. Veja o número de entradas para cada valor de coluna da seguinte forma
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Contar valores em falta em comparação com o número real de entradas em cada coluna com o seguinte código de exemplo
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Se tiver valores em falta para uma coluna de específica nos dados, pode soltá-los da seguinte forma:
   
        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape
   
   Outra maneira de substituir valores em falta é com a função de modo:
   
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Criar um desenho de histograma com um número variável de discretizações para desenhar a distribuição de uma variável    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Examinar as correlações entre as variáveis usando um gráfico de dispersão ou usando a função incorporada de correlação
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Geração de funcionalidade
Podemos gerar recursos com o Python da seguinte forma:

### <a name="blob-countfeature"></a>Valor do indicador com base em geração de funcionalidade
Funcionalidades categóricas podem ser criadas da seguinte forma:

1. Inspecione a distribuição da coluna categórica:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Gerar valores de indicador para cada um dos valores de coluna
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Junte-se a coluna de indicador com o quadro de dados original 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Remova a variável original em si:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Discretização de funcionalidade de geração
Para gerar recursos compartimentados, podemos proceda da seguinte forma:

1. Adicionar uma sequência de colunas a uma coluna numérica de discretização
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Converter a discretização para uma seqüência das variáveis booleanas
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Por fim, Junte-se as variáveis fictícias para o quadro de dados original
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Escrever dados blob do Azure e consumir no Azure Machine Learning
Depois de ter explorado os dados e criar os recursos necessários, pode carregar os dados (objeto de amostragem ou caracterizadas) a Azure blob e consumi-las no Azure Machine Learning através dos seguintes passos: Tenha em atenção que as funcionalidades adicionais podem ser criadas no Azure Machine Learning Studio também. 

1. Escrever o quadro de dados para o ficheiro local
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Carregar os dados para BLOBs do Azure da seguinte forma:
   
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
3. Agora, os dados podem ser lidos a partir do blob com o Azure Machine Learning [importar dados] [ import-data] módulo, conforme mostrado no ecrã abaixo:

![blob de leitor][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/


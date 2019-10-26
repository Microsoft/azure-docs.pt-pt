---
title: Acessar dados nos serviços de armazenamento do Azure
titleSuffix: Azure Machine Learning
description: Saiba como usar os armazenamentos de armazenamento para acessar os serviços de repositório do Azure durante o treinamento com o Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: 07c23d3b93c85e3409814ab0eb635a51344e2ab2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72929252"
---
# <a name="access-data-in-azure-storage-services"></a>Acessar dados nos serviços de armazenamento do Azure

Neste artigo, saiba como acessar facilmente seus dados nos serviços de armazenamento do Azure por meio de repositórios de Azure Machine Learning. Os armazenamentos de dados são usados para armazenar informações de conexão, como sua ID de assinatura e autorização de token. O uso de armazenamentos de dados permite que você acesse seu armazenamento sem precisar codificar informações de conexão em seus scripts. Você pode criar repositórios de armazenamento a partir dessas [soluções de armazenamento do Azure](#matrix). Para soluções de armazenamento sem suporte, para economizar o custo de egresso de dados durante experimentos de Machine Learning, recomendamos que você mova seus dados para nossas soluções de armazenamento do Azure com suporte. [Saiba como mover seus dados](#move). 

Este "como" mostra exemplos das seguintes tarefas:
* [Registrar repositórios de armazenamento](#access)
* [Obter repositórios de armazenamento do espaço de trabalho](#get)
* [Carregar e baixar dados usando armazenamentos](#up-and-down)
* [Acessar dados durante o treinamento](#train)
* [Mover dados para o Azure](#move)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

- Uma conta de armazenamento do Azure com um [contêiner de blob do](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) Azure ou [compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- O [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou acesso à [página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com/).

- Uma área de trabalho do Azure Machine Learning. 
    - [Crie um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md) ou use um existente usando o SDK do Python.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Criar e registrar repositórios de armazenamento

Ao registrar uma solução de armazenamento do Azure como um repositório de armazenamento, você cria automaticamente esse repositório de armazenamento em um espaço de trabalho específico. Você pode criar e registrar repositórios de armazenamento em um espaço de trabalho usando o SDK do Python ou a página de aterrissagem do espaço de trabalho.

### <a name="using-the-python-sdk"></a>Utilizar o SDK Python

Todos os métodos de registro estão na classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) e têm o formato register_azure_ *.

As informações necessárias para popular o método Register () podem ser encontradas por meio do [portal do Azure](https://ms.portal.azure.com). Selecione **contas de armazenamento** no painel esquerdo e escolha a conta de armazenamento que você deseja registrar. A página **visão geral** fornece informações como o nome da conta e o contêiner ou o nome do compartilhamento de arquivos. Para obter informações de autenticação, como chave de conta ou token SAS, navegue até **chaves de conta** no painel **configurações** à esquerda. 

Os exemplos a seguir mostram que você deve registrar um contêiner de blob do Azure ou um compartilhamento de arquivos do Azure como um repositório de armazenamento.

+ Para um **armazenamento de contêiner de blob do Azure**, use [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

    O código a seguir cria e registra o repositório de armazenamento, `my_datastore`, no espaço de trabalho, `ws`. Esse repositório de armazenamento acessa o contêiner de blob do Azure, `my_blob_container`, na conta de armazenamento do Azure, `my_storage_account` usando a chave de conta fornecida.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```
    Se sua conta de armazenamento estiver em uma VNET, somente a criação de armazenamento de blob do Azure será suportada. Defina o parâmetro `grant_workspace_access` como `True` para conceder ao seu espaço de trabalho acesso à sua conta de armazenamento.

+ Para um **repositório de armazenamento de arquivos do Azure**, use [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

    O código a seguir cria e registra o repositório de armazenamento, `my_datastore`, no espaço de trabalho, `ws`. Esse repositório de armazenamento acessa o compartilhamento de arquivos do Azure, `my_file_share`, na conta de armazenamento do Azure, `my_storage_account` usando a chave de conta fornecida.

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>Orientações de armazenamento

Recomendamos o contêiner de blob do Azure. Tanto o armazenamento Standard quanto o Premium estão disponíveis para BLOBs. Embora sejam mais caros, sugerimos o armazenamento Premium devido a velocidades de taxa de transferência mais rápidas que podem melhorar a velocidade de suas execuções de treinamento, especialmente se você treinar em um grande conjunto de dados. Consulte a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) para obter informações de custo da conta de armazenamento.

### <a name="using-the-workspace-landing-page"></a>Usando a página de aterrissagem do espaço de trabalho 

Crie um novo repositório de armazenamento em algumas etapas na página de aterrissagem do espaço de trabalho.

1. Entre na página de [aterrissagem do espaço de trabalho](https://ml.azure.com/).
1. Selecione **repositórios de armazenamento** no painel esquerdo em **gerenciar**.
1. Selecione **+ novo repositório de armazenamento**.
1. Preencha o formulário novo repositório de armazenamento. O formulário é atualizado de forma inteligente com base no tipo de armazenamento do Azure e nas seleções de tipo de autenticação.
  
As informações necessárias para popular o formulário podem ser encontradas por meio do [portal do Azure](https://ms.portal.azure.com). Selecione **contas de armazenamento** no painel esquerdo e escolha a conta de armazenamento que você deseja registrar. A página **visão geral** fornece informações como o nome da conta e o contêiner ou o nome do compartilhamento de arquivos. Para itens de autenticação, como chave de conta ou token SAS, navegue até **chaves de conta** no painel **configurações** à esquerda.

O exemplo a seguir demonstra a aparência do formulário para criar um repositório de armazenamento de blob do Azure. 
    
 ![Novo repositório de armazenamento](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Obter repositórios de armazenamento do seu espaço de trabalho

Para obter um repositório de armazenamento específico registrado no espaço de trabalho atual, use o método estático [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) na classe datastore:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Para obter a lista de armazenamentos de dados registrados com um determinado espaço de trabalho, você pode usar a propriedade [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) em um objeto de espaço de trabalho:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Quando você cria um espaço de trabalho, um contêiner de BLOBs do Azure e um compartilhamento de arquivos do Azure são registrados no espaço de trabalho chamado `workspaceblobstore` e `workspacefilestore`, respectivamente. Eles armazenam as informações de conexão do contêiner de BLOB e o compartilhamento de arquivos que é provisionado na conta de armazenamento anexada ao espaço de trabalho. O `workspaceblobstore` é definido como o repositório de armazenamento padrão.

Para obter o armazenamento de datastore padrão do espaço de trabalho:

```Python
datastore = ws.get_default_datastore()
```

Para definir um repositório de armazenamento padrão diferente para o espaço de trabalho atual, use [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) método no objeto de espaço de trabalho:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Carregar & Baixar dados
Os métodos [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) e [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) descritos nos exemplos a seguir são específicos do e operam de forma idêntica para as classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Carregar

 Carregue um diretório ou arquivos individuais no repositório de armazenamento usando o SDK do Python.

Para carregar um diretório em um repositório de armazenamento `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

O parâmetro `target_path` especifica o local no compartilhamento de arquivos (ou contêiner de BLOB) a ser carregado. O padrão é `None`, caso em que os dados são carregados para a raiz. Quando `overwrite=True` os dados existentes em `target_path` são substituídos.

Ou carregue uma lista de arquivos individuais no repositório de armazenamento por meio do método `upload_files()`.

### <a name="download"></a>Transferir

Da mesma forma, faça o download de dados de um datastore para o sistema de arquivos local.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

O parâmetro `target_path` é o local do diretório local para o qual baixar os dados. Para especificar um caminho para a pasta no compartilhamento de arquivos (ou contêiner de BLOB) a ser baixado, forneça esse caminho para `prefix`. Se `prefix` for `None`, todo o conteúdo do compartilhamento de arquivos (ou contêiner de BLOB) será baixado.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Acesse seus dados durante o treinamento

> [!IMPORTANT]
> Usar [Azure Machine Learning conjuntos de dados (versão prévia)](how-to-create-register-datasets.md) é a nova maneira recomendada de acessar os seus dados no treinamento. Os conjuntos de dados fornecem funções que carregam dados tabulares em pandas ou Spark dataframe e a capacidade de baixar ou montar arquivos de qualquer formato do blob do Azure, arquivo do Azure, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL, PostgreSQL do Azure. Saiba mais sobre [como treinar com conjuntos de](how-to-train-with-datasets.md)os.

A tabela a seguir lista os métodos que dizem ao destino de computação como usar os repositórios de armazenamento durante as execuções. 

Aparência|Método|Descrição|
----|-----|--------
Montar| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Use para montar o repositório de armazenamento no destino de computação.
Transferir|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Use para baixar o conteúdo do seu repositório de armazenamento para o local especificado por `path_on_compute`. <br><br> Esse download ocorre antes da execução.
Carregar|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Use para carregar um arquivo do local especificado por `path_on_compute` para seu repositório de armazenamento. <br><br> Esse carregamento ocorre após a execução.

Para fazer referência a uma pasta ou arquivo específico em seu repositório de armazenamento e disponibilizá-lo no destino de computação, use o método [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) do repositório de armazenamento.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Qualquer objeto `datastore` ou `datastore.path` especificado é resolvido para um nome de variável de ambiente do formato `"$AZUREML_DATAREFERENCE_XXXX"`, cujo valor representa o caminho de montagem/download na computação de destino. O caminho do repositório de armazenamento na computação de destino pode não ser o mesmo que o caminho de execução para o script de treinamento.

### <a name="examples"></a>Exemplos 

Os exemplos de código a seguir são específicos para a classe [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) para acessar dados durante o treinamento. 

`script_params` é um dicionário que contém parâmetros para o entry_script. Use-o para passar um armazenamento de dados e descrever como os dados são disponibilizados no destino de computação. Saiba mais em nosso [tutorial](tutorial-train-models-with-aml.md)de ponta a ponta.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Você também pode passar uma lista de armazenamentos de dados para o parâmetro `inputs` do Construtor estimador para montar ou copiar e de seus repositórios. Este exemplo de código:
* Baixa todo o conteúdo em `datastore1` para o destino de computação antes que o script de treinamento `train.py` seja executado
* Baixa a pasta `'./foo'` em `datastore2` para o destino de computação antes que `train.py` seja executado
* Carrega o arquivo `'./bar.pkl'` do destino de computação para o `datastore3` após a execução do script

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Matriz de computação e armazenamento de datastore

Atualmente, os armazenamentos de dados dão suporte ao armazenamento de informações de conexão para os serviços de armazenamento listados na matriz a seguir. Essa matriz exibe as funcionalidades de acesso a dados disponíveis para os diferentes destinos de computação e cenários de armazenamento de Datastore. Saiba mais sobre os [destinos de computação para Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Computação|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Local|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Computação Azure Machine Learning |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml&nbsp;pipelines](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| Máquinas virtuais               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload (](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-) )                            |N/A         |N/A                                                                         |
| HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload (](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-) )                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload (](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-) )                            |N/A         |N/A                                                                         |
| Transferência de dados                  |[Pipelines de&nbsp;ML](concept-ml-pipelines.md)                                               |N/A                                           |[Pipelines de&nbsp;ML](concept-ml-pipelines.md)            |[Pipelines de&nbsp;ML](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Pipelines de&nbsp;ML](concept-ml-pipelines.md)                                              |N/A                                           |[Pipelines de&nbsp;ML](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[Pipelines de&nbsp;ML](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure datalake Analytics       |N/A                                           |N/A                                           |[Pipelines de&nbsp;ML](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> Pode haver cenários em que processos de dados grandes altamente iterativos são executados mais rapidamente usando `as_download()` em vez de `as_mount()`; Isso pode ser validado experimentalmente.

### <a name="accessing-source-code-during-training"></a>Acessando o código-fonte durante o treinamento

O armazenamento de BLOBs do Azure tem velocidades de taxa de transferência maiores que o compartilhamento de arquivos do Azure e será dimensionado para um grande número de trabalhos iniciados em paralelo Por esse motivo, é recomendável configurar suas execuções para usar o armazenamento de BLOBs para transferir arquivos de código-fonte.

O exemplo de código a seguir especifica na configuração de execução qual repositório de armazenamento de blob usar para transferências de código-fonte.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Acessar dados durante a Pontuação

Azure Machine Learning fornece várias maneiras de usar seus modelos para pontuação. Alguns desses métodos não fornecem acesso a repositórios de armazenamento. Use a tabela a seguir para entender quais métodos permitem que você acesse os repositórios de armazenamento durante a Pontuação:

| Método | Acesso ao repositório de armazenamento | Descrição |
| ----- | :-----: | ----- |
| [Previsão de lote](how-to-run-batch-predictions.md) | ✔ | Faça previsões em grandes quantidades de dados de forma assíncrona. |
| [Serviço Web](how-to-deploy-and-where.md) | &nbsp; | Implantar modelo (s) como um serviço Web. |
| [Módulo IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implantar modelo (s) para IoT Edge dispositivos. |

Para situações em que o SDK não fornece acesso a repositórios de dados, você poderá criar um código personalizado usando o SDK do Azure relevante para acessar o dado. Por exemplo, o [SDK do armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python) é uma biblioteca de cliente que você pode usar para acessar dados armazenados em BLOBs ou arquivos.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Mover dados para soluções de armazenamento do Azure com suporte

O serviço Azure Machine Learning dá suporte ao acesso a dados do blob do Azure, arquivo do Azure, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL. Para armazenamento sem suporte, para economizar o custo de egresso de dados durante experimentos de Machine Learning, recomendamos que você mova seus dados para nossas soluções de armazenamento do Azure com suporte usando Azure Data Factory. Azure Data Factory fornece transferência de dados eficiente e resiliente com mais de 80 conectores predefinidos, incluindo serviços de dados do Azure, fontes de dados locais, Amazon S3 e redshift e Google BigQuery, sem custo adicional. [Siga o guia passo a passo para mover seus dados usando Azure data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Passos seguintes

* [Treinar um modelo](how-to-train-ml-models.md)

* [Implementar um modelo](how-to-deploy-and-where.md)

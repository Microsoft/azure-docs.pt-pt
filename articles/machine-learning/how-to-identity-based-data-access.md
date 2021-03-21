---
title: Acesso de dados baseados em identidade aos serviços de armazenamento em Azure
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o acesso de dados baseados na identidade para ligar aos serviços de armazenamento em Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 68d07481e228b1d1b2f4571a783f925add261cff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520018"
---
# <a name="connect-to-storage-with-identity-based-data-access-preview"></a>Conecte-se ao armazenamento com acesso de dados baseado em identidade (pré-visualização)

>[!IMPORTANT]
> As funcionalidades apresentadas neste artigo encontram-se em pré-visualização, devendo ser consideradas funcionalidades [experimentais](/python/api/overview/azure/ml/#stable-vs-experimental) de pré-visualização que podem ser alteradas a qualquer momento.

Neste artigo, você aprende a conectar-se aos serviços de armazenamento em Azure com acesso de dados baseado em identidade e lojas de dados Azure Machine Learning através do [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro).  

Normalmente, as lojas de dados utilizam o acesso de dados baseado em credenciais para confirmar que tem permissão para aceder ao serviço de armazenamento. Eles mantêm informações de ligação, como o seu ID de subscrição e autorização simbólica, no seu [Cofre chave](https://azure.microsoft.com/services/key-vault/) que está associado ao espaço de trabalho. Quando cria uma loja de dados que utiliza acesso a dados baseados na identidade, o seu login Azure[(Azure Ative Directory token)](../active-directory/fundamentals/active-directory-whatis.md)é utilizado para confirmar que tem permissão para aceder ao serviço de armazenamento. Neste cenário, não são guardadas credenciais de autenticação e apenas a informação da conta de armazenamento é armazenada na dataria. 

Para criar lojas de dados que utilizem a autenticação baseada em credenciais, como com chaves de acesso ou princípios de serviço, consulte [Connect to storage services on Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Acesso de dados baseados em identidade em Azure Machine Learning

Existem duas áreas para aplicar o acesso de dados baseados na identidade no Azure Machine Learning. Especialmente, ao trabalhar com dados confidenciais e precisar de mais gestão de acesso a dados granulares. 

1. Acedendo aos serviços de armazenamento.
1. Formação de modelos de aprendizagem automática com dados privados.

### <a name="accessing-storage-services"></a>Acesso aos serviços de armazenamento

Pode ligar-se aos serviços de armazenamento através do acesso de dados baseados na identidade com as datas de aprendizagem da máquina [Azure ou com os conjuntos de dados Azure Machine Learning](how-to-create-register-datasets.md). 

Normalmente, as suas credenciais de autenticação são guardadas numa datas-tore, que é usada para garantir que tem permissão para aceder ao serviço de armazenamento. Quando estas credenciais são registadas nas datastores, qualquer utilizador com a função de *leitor* de espaço de trabalho é capaz de recuperá-las -- o que pode ser uma preocupação de segurança para algumas organizações. [Saiba mais sobre o papel de *workspace Reader*](how-to-assign-roles.md#default-roles). 

Quando utiliza o acesso de dados baseado na identidade, o Azure Machine Learning solicita-lhe o seu token Azure Ative Directory para autenticação de acesso a dados, em vez de manter as suas credenciais na datastore. O que permite a gestão do acesso a dados ao nível do armazenamento e mantém as credenciais confidenciais. 

O mesmo comportamento aplica-se quando tu,

* [Crie um conjunto de dados diretamente a partir de urls de armazenamento](#use-data-in-storage). 
* Trabalhe com dados interativamente através de um caderno Jupyter na sua máquina local ou [caso de computação](concept-compute-instance.md).

> [!NOTE]
> As credenciais armazenadas com base na autenticação baseada em credenciais incluem: ID de assinatura partilhada (SAS), chaves de acesso ao armazenamento e informações principais de serviço como, ID do cliente e ID do inquilino.

### <a name="model-training-on-private-data"></a>Formação de modelos em dados privados

Certos cenários de aprendizagem automática envolvem modelos de formação com dados privados. Nesses casos, os cientistas de dados precisam de executar fluxos de trabalho de formação sem exposição aos dados confidenciais de entrada. Neste cenário, é utilizada uma identidade gerida do computação de formação para a autenticação de acesso a dados. Desta forma, os administradores de armazenamento podem conceder ao **Storage Blob Data Reader** acesso à identidade gerida que o computamento de formação usa para executar o trabalho de formação, em vez dos cientistas de dados individuais. Saiba como [configurar a identidade gerida num cálculo.](how-to-create-attach-compute-cluster.md#managed-identity)


## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Uma conta de armazenamento Azure com um tipo de armazenamento suportado. Os seguintes tipos de armazenamento são suportados na pré-visualização. 
    - [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Gen 1](../data-lake-store/index.yml)
    - [Azure Data Lake Gen 2](../storage/blobs/data-lake-storage-introduction.md)
    - [Base de dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md)

- [O Azure Machine Learning SDK para Python.](/python/api/overview/azure/ml/install)

- Uma área de trabalho do Azure Machine Learning.
  
  Ou [cria um espaço de trabalho de aprendizagem automática Azure](how-to-manage-workspace.md) ou utiliza um existente através do Python [SDK](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Permissões de acesso ao armazenamento

Para garantir uma ligação segura ao seu serviço de armazenamento no Azure, a Azure Machine Learning requer que tenha permissão para aceder ao armazenamento de dados correspondente.

O acesso de dados baseados na identidade apenas suporta ligações aos seguintes serviços de armazenamento:

* Armazenamento de Blobs do Azure
* Azure Data Lake Geração 1
* Azure Data Lake Geração 2
* Base de dados SQL do Azure

Para aceder a estes serviços de armazenamento, tem de ter no mínimo acesso **ao Leitor de Dados Blob de Armazenamento.** Saiba mais sobre [o Storage Blob Data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Apenas os proprietários de conta de armazenamento podem [alterar o seu nível de acesso através do portal Azure](../storage/common/storage-auth-aad-rbac-portal.md).

Se estiver a treinar um modelo num alvo de computação remota, a identidade do cálculo deve ser concedida com, pelo menos, o papel de Leitor de **Dados Blob de Armazenamento** do serviço de armazenamento. Saiba como configurar a [identidade gerida no cálculo.](how-to-create-attach-compute-cluster.md#managed-identity)

## <a name="work-with-virtual-networks"></a>Trabalhar com redes virtuais

Por predefinição, o Azure Machine Learning não consegue comunicar com uma conta de armazenamento que está por trás de uma firewall ou dentro de uma rede virtual.

As contas de armazenamento podem ser configuradas para permitir o acesso apenas a partir de redes virtuais específicas, o que requer configurações adicionais para garantir que os dados não são vazados fora da rede. Este comportamento é o mesmo para o acesso de dados baseado em credenciais, ver [que configurações são necessárias e como aplicá-los para cenários de rede virtual](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Criar e registar datastores

Quando regista um serviço de armazenamento no Azure como uma datastore, cria e regista automaticamente essa datastore para um espaço de trabalho específico. Reveja estas secções: [permissões de acesso](#storage-access-permissions) ao armazenamento para orientação sobre os tipos de permissão exigidos e [trabalhe com rede virtual](#work-with-virtual-networks) para obter detalhes sobre como se conectar ao armazenamento de dados por trás de redes virtuais.

No código seguinte, note-se a ausência de parâmetros de autenticação, `sas_token` `account_key` como, `subscription_id` , , ou o principal de serviço `client_id` . Esta omissão indica que o Azure Machine Learning deve utilizar o acesso de dados baseados na identidade para autenticação. Como a criação de datas normalmente acontece interativamente num bloco de notas ou através do estúdio, o seu token Azure Ative Directory é utilizado para a autenticação de acesso a dados.

> [!NOTE]
> Os nomes da datastore devem consistir apenas em letras minúsculas, dígitos e sublinhados. 

### <a name="azure-blob-container"></a>Recipiente de bolhas Azure

Para registar um recipiente de bolhas Azure como uma loja de dados, utilize [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

O código que se segue cria e regista a `credentialless_blob` datastore para o `ws` espaço de trabalho e atribui-o à variável, `blob_datastore` . Esta loja de dados acede ao `my_container_name` recipiente blob na `my-account-name` conta de armazenamento.

```Python
# create blob datastore without credentials
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-generation-1"></a>Azure Data Lake Storage Generation 1

Para uma data-loja de dados Azure Data Lake Storage Generation 1 (ADLS Gen 1), utilize [register_azure_data_lake](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) para registar uma loja de dados que se conecta a um armazenamento Azure DataLake Generation 1.

O código que se segue cria e regista a `credentialless_adls1` datastore para o `workspace` espaço de trabalho e atribui-o à variável, `adls_dstore` . Esta loja de dados acede à `adls_storage` conta de armazenamento Azure Data Lake Store.

```Python
# create adls gen1 without credentials
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Para uma data-loja de dados Azure Data Lake Storage Generation 2 (ADLS Gen 2), utilize [register_azure_data_lake_gen2](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) para registar uma loja de dados que se conecta a um armazenamento Azure DataLake Gen 2.

O código que se segue cria e regista a `credentialless_adls2` datastore para o `ws` espaço de trabalho e atribui-o à variável, `adls2_dstore` . Esta loja de dados acede ao sistema de ficheiros `tabular` na `myadls2` conta de armazenamento.  

```python
# createn adls2 datastore without credentials
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Utilizar dados no armazenamento

[Os conjuntos de dados Azure Machine Learning](how-to-create-register-datasets.md) são a forma recomendada de interagir com os seus dados em armazenamento com a Azure Machine Learning. 

Os conjuntos de dados embalam os seus dados num objeto consumível avaliado preguiçosamente para tarefas de aprendizagem automática, como o treino. Além disso, com conjuntos de dados pode [descarregar ou montar](how-to-train-with-datasets.md#mount-vs-download) ficheiros de qualquer formato de serviços de armazenamento Azure, como, armazenamento Azure Blob e Azure Data Lakes, para um alvo de computação.


**Para criar conjuntos de dados com acesso de dados baseados na identidade,** tem as seguintes opções. Este tipo de criação de conjuntos de dados emprega o seu token Azure Ative Directory para a autenticação de acesso a dados. 

*  Percursos de referência a partir de datastores que também utilizam o acesso de dados baseados na identidade. 
<br>No exemplo seguinte, `blob_datastore` foi previamente criado usando o acesso de dados baseado na identidade.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Ignore a criação de datastore e crie conjuntos de dados diretamente a partir de urls de armazenamento. Atualmente esta funcionalidade suporta apenas Azure Blobs e Azure Data Lake Storage Generations 1 e 2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

**No entanto, quando se submete um trabalho de formação que consome um conjunto de dados criado com acesso de dados baseado na identidade,** a identidade gerida do computação de formação é utilizada para a autenticação de acesso a dados, em vez do seu token Azure Ative. Para este cenário, certifique-se de que a identidade gerida do cálculo é concedida com, pelo menos, o papel de Leitor de **Dados Blob de Armazenamento** do serviço de armazenamento. Saiba como configurar a [identidade gerida no cálculo.](how-to-create-attach-compute-cluster.md#managed-identity) 

## <a name="next-steps"></a>Passos seguintes

* [Criar um conjunto de dados de aprendizagem automática Azure](how-to-create-register-datasets.md).
* [Treine com conjuntos de dados.](how-to-train-with-datasets.md)
* [Crie uma loja de dados com acesso a dados baseado em chaves.](how-to-access-data.md)

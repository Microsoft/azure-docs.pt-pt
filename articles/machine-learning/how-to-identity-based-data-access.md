---
title: Acesso de dados baseados em identidade aos serviços de armazenamento em Azure
titleSuffix: Machine Learning
description: Saiba como utilizar o acesso de dados baseado na identidade para ligar aos serviços de armazenamento em Azure com as lojas de dados Azure Machine Learning e o Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: a46f54bd037dcf8d71ba3fbafb2ba0fd961a32cc
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210657"
---
# <a name="connect-to-storage-by-using-identity-based-data-access-preview"></a>Conecte-se ao armazenamento utilizando o acesso de dados baseado na identidade (pré-visualização)

>[!IMPORTANT]
> As funcionalidades apresentadas neste artigo estão em pré-visualização. Devem ser considerados funcionalidades [experimentais](/python/api/overview/azure/ml/#stable-vs-experimental) de pré-visualização que podem mudar a qualquer momento.

Neste artigo, você aprende a conectar-se aos serviços de armazenamento em Azure, utilizando o acesso de dados baseado na identidade e as lojas de dados Azure Machine Learning através do [Azure Machine Learning SDK para Python.](/python/api/overview/azure/ml/intro)  

Normalmente, as lojas de dados utilizam o acesso de dados baseado em credenciais para confirmar que tem permissão para aceder ao serviço de armazenamento. Eles mantêm informações de ligação, como o seu ID de subscrição e autorização simbólica, no [cofre chave](https://azure.microsoft.com/services/key-vault/) que está associado ao espaço de trabalho. Quando cria uma loja de dados que utiliza o acesso de dados baseado na identidade, a sua conta Azure Ative[(Token Azure Ative Directory)](../active-directory/fundamentals/active-directory-whatis.md)é utilizada para confirmar que tem permissão para aceder ao serviço de armazenamento. Neste cenário, não são guardadas credenciais de autenticação. Apenas a informação da conta de armazenamento é armazenada na datastore. 

Para criar datastores que utilizem a autenticação baseada em credenciais, como chaves de acesso ou princípios de serviço, consulte [Connect para serviços de armazenamento no Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Acesso de dados baseados em identidade em Azure Machine Learning

Existem dois cenários em que pode aplicar o acesso de dados baseado em identidade no Azure Machine Learning. Estes cenários são um bom ajuste para o acesso baseado na identidade quando você está trabalhando com dados confidenciais e precisa de mais gestão de acesso a dados granulares:

- Acesso aos serviços de armazenamento
- Modelos de aprendizagem automática de formação com dados privados

### <a name="accessing-storage-services"></a>Acesso aos serviços de armazenamento

Pode ligar-se aos serviços de armazenamento através do acesso de dados baseados na identidade com as datas de aprendizagem da máquina [Azure ou com os conjuntos de dados Azure Machine Learning](how-to-create-register-datasets.md). 

As suas credenciais de autenticação são geralmente guardadas numa datas-loja, que é usada para garantir que tem permissão para aceder ao serviço de armazenamento. Quando estas credenciais são registadas através de datastores, qualquer utilizador com a função de leitor de espaço de trabalho pode recuperá-las. Esta escala de acesso pode ser uma preocupação de segurança para algumas organizações. [Saiba mais sobre o papel de workspace Reader.](how-to-assign-roles.md#default-roles) 

Quando utiliza o acesso de dados baseado na identidade, o Azure Machine Learning solicita-lhe o seu token Azure Ative Directory para autenticação de acesso a dados em vez de manter as suas credenciais na loja de dados. Esta abordagem permite a gestão do acesso a dados ao nível do armazenamento e mantém as credenciais confidenciais. 

O mesmo comportamento aplica-se quando:

* [Criar um conjunto de dados diretamente a partir de URLs de armazenamento](#use-data-in-storage). 
* Trabalhe com dados interativamente através de um Caderno Jupyter no seu computador local ou [caso de computação.](concept-compute-instance.md)

> [!NOTE]
> As credenciais armazenadas através da autenticação baseada em credenciais incluem IDs de subscrição, fichas de assinatura de acesso partilhado (SAS) e chave de acesso ao armazenamento e informações principais de serviço, como IDs de cliente e IDs de inquilino.

### <a name="model-training-on-private-data"></a>Formação de modelos em dados privados

Certos cenários de aprendizagem automática envolvem modelos de formação com dados privados. Nesses casos, os cientistas de dados precisam de executar fluxos de trabalho de formação sem serem expostos aos dados confidenciais de entrada. Neste cenário, é utilizada uma identidade gerida do computação de formação para a autenticação de acesso a dados. Esta abordagem permite que os administradores de armazenamento concedam ao Storage Blob Data Reader acesso à identidade gerida que o compute de formação utiliza para executar o trabalho de formação. Os cientistas individuais de dados não precisam de ter acesso. Para obter mais informações, consulte [Configurar a identidade gerida num cluster de cálculo.](how-to-create-attach-compute-cluster.md#managed-identity)


## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Uma conta de armazenamento Azure com um tipo de armazenamento suportado. Estes tipos de armazenamento são suportados na pré-visualização: 
    - [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-overview.md)
    - [Armazenamento do Azure Data Lake Ger1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (Armazenamento do Azure Data Lake Gen2)
    - [Base de Dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md)

- [O Azure Machine Learning SDK para Python.](/python/api/overview/azure/ml/install)

- Uma área de trabalho do Azure Machine Learning.
  
  Ou [cria um espaço de trabalho de aprendizagem automática Azure](how-to-manage-workspace.md) ou utiliza um existente através do Python [SDK](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Permissões de acesso ao armazenamento

Para ajudar a garantir que se conecta de forma segura ao seu serviço de armazenamento no Azure, a Azure Machine Learning requer que tenha permissão para aceder ao armazenamento de dados correspondente.

O acesso a dados baseados na identidade suporta ligações apenas aos seguintes serviços de armazenamento:

* Armazenamento de Blobs do Azure
* Armazenamento do Azure Data Lake Ger1
* Armazenamento do Azure Data Lake Ger2
* Base de Dados SQL do Azure

Para aceder a estes serviços de armazenamento, tem de ter pelo menos acesso [ao Storage Blob Data Reader.](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Apenas os proprietários de conta de armazenamento podem [alterar o seu nível de acesso através do portal Azure](../storage/common/storage-auth-aad-rbac-portal.md).

Se estiver a treinar um modelo num alvo de computação remota, a identidade do cálculo deve ser concedida pelo menos a função de Leitor de Dados blob de armazenamento do serviço de armazenamento. Saiba como configurar a [identidade gerida num cluster de computação.](how-to-create-attach-compute-cluster.md#managed-identity)

## <a name="work-with-virtual-networks"></a>Trabalhar com redes virtuais

Por padrão, o Azure Machine Learning não consegue comunicar com uma conta de armazenamento que está por trás de uma firewall ou numa rede virtual.

Pode configurar contas de armazenamento para permitir o acesso apenas a partir de redes virtuais específicas. Esta configuração requer medidas adicionais para garantir que os dados não são vazados fora da rede. Este comportamento é o mesmo para o acesso de dados baseado em credenciais. Para obter mais informações, consulte [Como configurar cenários de rede virtual](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Criar e registar datastores

Quando regista um serviço de armazenamento no Azure como uma datastore, cria e regista automaticamente essa datastore para um espaço de trabalho específico. Consulte [as permissões de acesso](#storage-access-permissions) ao Armazenamento para obter orientação sobre os tipos de permissões necessários. Consulte [o Trabalho com redes virtuais](#work-with-virtual-networks) para obter detalhes sobre como ligar-se ao armazenamento de dados por trás de redes virtuais.

No código seguinte, note-se a ausência de parâmetros de autenticação `sas_token` `account_key` como, `subscription_id` e o principal de `client_id` serviço. Esta omissão indica que o Azure Machine Learning utilizará o acesso de dados baseado na identidade para a autenticação. A criação de datas-tores normalmente acontece interativamente num caderno ou através do estúdio. Assim, o seu token Azure Ative Directory é utilizado para a autenticação de acesso a dados.

> [!NOTE]
> Os nomes da datastore devem consistir apenas em letras, números e sublinhados minúsculos. 

### <a name="azure-blob-container"></a>Recipiente de bolhas Azure

Para registar um recipiente de bolhas Azure como uma loja de dados, utilize [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

O código a seguir cria a `credentialless_blob` datastore, regista-o no `ws` espaço de trabalho e atribui-o à `blob_datastore` variável. Esta loja de dados acede ao `my_container_name` recipiente blob na `my-account-name` conta de armazenamento.

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Armazenamento do Azure Data Lake Ger1

Utilize [register_azure_data_lake para](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) registar uma datastore que se conecta ao Azure Data Lake Storage Gen1.

O código a seguir cria a `credentialless_adls1` datastore, regista-o no `workspace` espaço de trabalho e atribui-o à `adls_dstore` variável. Esta loja de dados acede à `adls_storage` conta de armazenamento do Lago de Dados Azure.

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Armazenamento do Azure Data Lake Ger2

Utilize [register_azure_data_lake_gen2 para](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) registar uma datastore que se conecta ao Azure Data Lake Storage Gen2.

O código a seguir cria a `credentialless_adls2` datastore, regista-o no `ws` espaço de trabalho e atribui-o à `adls2_dstore` variável. Esta loja de dados acede ao sistema de ficheiros `tabular` na `myadls2` conta de armazenamento.  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Utilizar dados no armazenamento

Recomendamos que utilize [conjuntos de dados de Aprendizagem automática Azure](how-to-create-register-datasets.md) quando interagir com os seus dados armazenados com a Azure Machine Learning. 

Os conjuntos de dados embalam os seus dados num objeto consumível avaliado preguiçosamente para tarefas de aprendizagem automática como o treino. Além disso, com conjuntos de dados pode [descarregar ou montar](how-to-train-with-datasets.md#mount-vs-download) ficheiros de qualquer formato de serviços de armazenamento Azure, como O Azure Blob Storage e Azure Data Lake Storage para um alvo de computação.


Para criar conjuntos de dados com acesso de dados baseados na identidade, tem as seguintes opções. Este tipo de criação de conjuntos de dados utiliza o seu token Azure Ative Directory para autenticação de acesso a dados. 

*  Percursos de referência a partir de datastores que também utilizam o acesso de dados baseados na identidade. 
<br>No exemplo seguinte, `blob_datastore` já existe e utiliza o acesso de dados baseado na identidade.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Ignore a criação de datastore e crie conjuntos de dados diretamente a partir de URLs de armazenamento. Atualmente, esta funcionalidade suporta apenas blobs Azure e Azure Data Lake Storage Gen1 e Gen2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

Quando submete um trabalho de formação que consome um conjunto de dados criado com acesso de dados baseado na identidade, a identidade gerida do computação de formação é utilizada para a autenticação de acesso a dados. O seu token do Diretório Ativo Azure não é usado. Para este cenário, certifique-se de que a identidade gerida do cálculo é concedida pelo menos a função de Leitor de Dados blob de armazenamento do serviço de armazenamento. Para obter mais informações, consulte [Configurar a identidade gerida em clusters de computação.](how-to-create-attach-compute-cluster.md#managed-identity) 

## <a name="next-steps"></a>Passos seguintes

* [Criar um conjunto de dados de aprendizagem de máquinas Azure](how-to-create-register-datasets.md)
* [Preparar com conjuntos de dados](how-to-train-with-datasets.md)
* [Criar uma loja de dados com acesso a dados baseado em chaves](how-to-access-data.md)

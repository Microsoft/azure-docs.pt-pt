---
title: Alterar chaves de acesso à conta de armazenamento
titleSuffix: Azure Machine Learning
description: Saiba como alterar as teclas de acesso da conta Azure Storage utilizadas pelo seu espaço de trabalho. A Azure Machine Learning utiliza uma conta de Armazenamento Azure para armazenar dados e modelos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f4ae4890d28236db493909243d66e28d308e2002
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434642"
---
# <a name="regenerate-storage-account-access-keys"></a>Chaves de acesso à conta de armazenamento regenerar
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como alterar as teclas de acesso para as contas de Armazenamento Azure utilizadas pela Azure Machine Learning. O Azure Machine Learning pode usar contas de armazenamento para armazenar dados ou modelos treinados.

Por razões de segurança, poderá ter de alterar as chaves de acesso para uma conta de Armazenamento Azure. Ao regenerar a chave de acesso, o Azure Machine Learning deve ser atualizado para utilizar a nova chave. O Azure Machine Learning pode estar a usar a conta de armazenamento tanto para o armazenamento do modelo como para uma loja de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte o [Artigo Criar um espaço de trabalho.](how-to-manage-workspace.md)

* [O Azure Machine Learning SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

* A [extensão CLI de aprendizagem automática Azure](reference-azure-machine-learning-cli.md).

> [!NOTE]
> Os fragmentos de código neste documento foram testados com a versão 1.0.83 do Python SDK.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>O que precisa de ser atualizado

As contas de armazenamento podem ser utilizadas pelo espaço de trabalho Azure Machine Learning (armazenamento de troncos, modelos, instantâneos, etc.) e como uma datastore. O processo de atualização do espaço de trabalho é um único comando Azure CLI, e pode ser executada após a atualização da chave de armazenamento. O processo de atualização das lojas de dados está mais envolvido e requer descobrir quais as datas que as lojas estão atualmente a usar a conta de armazenamento e depois re-registrá-los.

> [!IMPORTANT]
> Atualize o espaço de trabalho utilizando o CLI Azure e as datastores usando Python, ao mesmo tempo. Atualizar apenas um ou outro não é suficiente e pode causar erros até que ambos sejam atualizados.

Para descobrir as contas de armazenamento que são utilizadas pelas suas datastores, utilize o seguinte código:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Este código procura quaisquer datastores registados que utilizem o Azure Storage e lista as seguintes informações:

* Nome da datastore: O nome da datastore em que a conta de armazenamento está registada.
* Nome da conta de armazenamento: O nome da conta Azure Storage.
* Recipiente: O recipiente na conta de armazenamento que é utilizado por este registo.

Também indica se a datastore é para uma Azure Blob ou uma partilha de FicheiroS Azure, uma vez que existem diferentes métodos para re-registar cada tipo de datastore.

Se existir uma entrada para a conta de armazenamento para a que planeia regenerar as chaves de acesso, guarde o nome da datastore, o nome da conta de armazenamento e o nome do recipiente.

## <a name="update-the-access-key"></a>Atualizar a chave de acesso

Para atualizar a Azure Machine Learning para utilizar a nova chave, utilize os seguintes passos:

> [!IMPORTANT]
> Execute todas as etapas, atualizando tanto o espaço de trabalho utilizando o CLI, como as lojas de dados utilizando python. A atualização de apenas um ou outro pode causar erros até que ambos sejam atualizados.

1. Regenerar a chave. Para obter informações sobre a regeneração de uma chave de acesso, consulte [as teclas de acesso à conta de armazenamento](../storage/common/storage-account-keys-manage.md). Guarde a nova chave.

1. Para atualizar o espaço de trabalho para utilizar a nova chave, utilize os seguintes passos:

    1. Para iniciar súm na subscrição Azure que contém o seu espaço de trabalho utilizando o seguinte comando Azure CLI:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Para atualizar o espaço de trabalho para utilizar a nova chave, utilize o seguinte comando. Substitua `myworkspace` o nome do espaço de trabalho Azure Machine Learning e substitua-o pelo nome do grupo de recursos `myresourcegroup` Azure que contém o espaço de trabalho.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Este comando sincroniza automaticamente as novas teclas da conta de armazenamento Azure utilizadas pelo espaço de trabalho.

1. Para re-registar os dados que utilizam a conta de armazenamento, utilize os valores da secção [What need to updateed](#whattoupdate) e a chave do passo 1 com o seguinte código:

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    Uma vez `overwrite=True` especificado, este código substitui o registo existente e atualiza-o para utilizar a nova chave.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o registo de datastores, consulte a [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) referência de classe.

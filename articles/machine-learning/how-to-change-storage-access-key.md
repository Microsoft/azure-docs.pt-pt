---
title: Alterar as chaves de acesso à conta de armazenamento
titleSuffix: Azure Machine Learning
description: Saiba como alterar as chaves de acesso da conta De armazenamento Azure utilizada pelo seu espaço de trabalho. O Azure Machine Learning utiliza uma conta de Armazenamento Azure para armazenar dados e modelos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f1541c177cea2d223a5e7df576d95fab7eafb310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296922"
---
# <a name="regenerate-storage-account-access-keys"></a>Chaves de acesso à conta de armazenamento regenerar
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como alterar as chaves de acesso para as contas de Armazenamento Azure utilizadas pelo Azure Machine Learning. O Azure Machine Learning pode utilizar contas de armazenamento para armazenar dados ou modelos treinados.

Para fins de segurança, poderá ter de alterar as chaves de acesso para uma conta de Armazenamento Azure. Quando regenerar a chave de acesso, o Azure Machine Learning tem de ser atualizado para utilizar a nova tecla. O Azure Machine Learning pode estar a utilizar a conta de armazenamento tanto para o armazenamento do modelo como para uma loja de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte o artigo [Criar um espaço de trabalho.](how-to-manage-workspace.md)

* O [SDK de Aprendizagem automática Azure.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

* A [extensão CLI de aprendizagem automática Azure](reference-azure-machine-learning-cli.md).

> [!NOTE]
> Os fragmentos de código neste documento foram testados com a versão 1.0.83 do Python SDK.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>O que precisa de ser atualizado

As contas de armazenamento podem ser utilizadas pelo espaço de trabalho Azure Machine Learning (armazenar registos, modelos, instantâneos, etc.) e como uma loja de dados. O processo de atualização do espaço de trabalho é um único comando Azure CLI, e pode ser decorrido após a atualização da chave de armazenamento. O processo de atualização das lojas de dados está mais envolvido e requer a descoberta de que lojas de dados estão atualmente a usar a conta de armazenamento e, em seguida, reregistrá-las.

> [!IMPORTANT]
> Atualize o espaço de trabalho utilizando o Azure CLI e as lojas de dados utilizando python, ao mesmo tempo. A atualização apenas de um ou outro não é suficiente e pode causar erros até que ambos sejam atualizados.

Para descobrir as contas de armazenamento que são utilizadas pelas suas lojas de dados, utilize o seguinte código:

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

Este código procura quaisquer lojas de dados registadas que utilizem o Armazenamento Azure e lista as seguintes informações:

* Nome da loja de dados: O nome da loja de dados em que a conta de armazenamento está registada.
* Nome da conta de armazenamento: O nome da conta De armazenamento Azure.
* Recipiente: O recipiente na conta de armazenamento que é utilizado por este registo.

Também indica se a loja de dados é para uma participação do Azure Blob ou de um Ficheiro Azure, uma vez que existem diferentes métodos para reregistar cada tipo de datastore.

Se existir uma entrada para a conta de armazenamento para a que planeia regenerar as chaves de acesso, guarde o nome da loja de dados, o nome da conta de armazenamento e o nome do recipiente.

## <a name="update-the-access-key"></a>Atualizar a chave de acesso

Para atualizar o Azure Machine Learning para utilizar a nova tecla, utilize os seguintes passos:

> [!IMPORTANT]
> Execute todas as etapas, atualizando tanto o espaço de trabalho utilizando o CLI, como as lojas de dados utilizando python. A atualização apenas de um ou outro pode causar erros até que ambos sejam atualizados.

1. Regenerar a chave. Para obter informações sobre a regeneração de uma chave de acesso, consulte [Gerir as chaves](../storage/common/storage-account-keys-manage.md)de acesso à conta de armazenamento . Guarde a nova chave.

1. Para atualizar o espaço de trabalho para utilizar a nova tecla, utilize os seguintes passos:

    1. Para iniciar sessão na subscrição Azure que contenha o seu espaço de trabalho utilizando o seguinte comando Azure CLI:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Para atualizar o espaço de trabalho para utilizar a nova tecla, utilize o seguinte comando. Substitua-o `myworkspace` pelo seu nome de `myresourcegroup` espaço de trabalho Azure Machine Learning e substitua-o pelo nome do grupo de recursos Azure que contém o espaço de trabalho.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Este comando sincroniza automaticamente as novas teclas para a conta de armazenamento Azure utilizada pelo espaço de trabalho.

1. Para reregistar a(s) datastore(s) que utilizam a conta de armazenamento, utilize os valores da secção [O que precisa de ser atualizado](#whattoupdate) e a chave da parte 1 com o seguinte código:

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

    Uma `overwrite=True` vez especificado, este código substitui o registo existente e atualiza-o para utilizar a nova tecla.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) o registo de lojas de dados, consulte a referência da classe.

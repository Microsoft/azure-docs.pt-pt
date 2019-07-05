---
title: Alterar as chaves de acesso da conta de armazenamento
titleSuffix: Azure Machine Learning service
description: Saiba como alterar as chaves de acesso para a conta de armazenamento do Azure utilizada pela sua área de trabalho. O serviço do Azure Machine Learning utiliza uma conta de armazenamento do Azure para armazenar dados e modelos. Quando voltar a gerar a chave de acesso para a conta de armazenamento, tem de atualizar o serviço de Azure Machine Learning para utilizar as novas chaves.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: 488a032e177897caf2897ba6335f4e7f64dc0e4d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543834"
---
# <a name="regenerate-storage-account-access-keys"></a>Regenerar chaves de acesso da conta de armazenamento

Saiba como alterar as chaves de acesso para contas de armazenamento do Azure utilizadas pelo serviço Azure Machine Learning. O Azure Machine Learning pode utilizar contas de armazenamento para armazenar dados ou modelos de formação.

Por motivos de segurança, terá de alterar as chaves de acesso para uma conta de armazenamento do Azure. Ao regenerar a chave de acesso, o Azure Machine Learning tem de ser atualizado para utilizar a nova chave. O Azure Machine Learning podem estar usando a conta de armazenamento para ambos os armazenamento de modelo e como um arquivo de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho de serviço do Azure Machine Learning. Para obter mais informações, consulte a [criar uma área de trabalho](setup-create-workspace.md) artigo.

* O [do Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* O [extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>O que precisa ser atualizado

Contas de armazenamento podem ser utilizadas pela área de trabalho de serviço do Azure Machine Learning (armazenamento de registos, modelos, instantâneos, etc.) e como um arquivo de dados. O processo para atualizar a área de trabalho é um único comando da CLI do Azure e pode ser executado depois de atualizar a chave de armazenamento. O processo de atualização de arquivos de dados é mais envolvido e requer a descobrir quais arquivos de dados estão atualmente a utilizar a conta de armazenamento e, em seguida, registe-los novamente.

> [!IMPORTANT]
> Atualize a área de trabalho com a CLI do Azure e os arquivos de dados com o Python, ao mesmo tempo. A atualizar apenas um ou outro não é suficiente e pode causar erros até que ambos sejam atualizadas.

Para detetar as contas de armazenamento que são utilizadas pelos seus arquivos de dados, utilize o seguinte código:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " + default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " + ds.account_name + ", container name: " + ds.container_name)
```

Esse código procura por quaisquer arquivos de dados registados que utilizem o armazenamento do Azure e lista as informações seguintes:

* Nome do arquivo de dados: O nome do arquivo de dados que a conta de armazenamento está registada.
* Nome da conta de armazenamento: O nome da conta do Storage do Azure.
* Contentor: O contentor na conta de armazenamento que é utilizada por esse Registro.

Se houver uma entrada para a conta de armazenamento que pretende regenerar chaves de acesso para, guarde o nome de arquivo de dados, o nome da conta de armazenamento e o nome de contentor.

## <a name="update-the-access-key"></a>Atualizar a chave de acesso

Para atualizar o serviço Azure Machine Learning para utilizar a nova chave, utilize os seguintes passos:

> [!IMPORTANT]
> Execute todos os passos, ambos atualizar área de trabalho utilizando a CLI e os arquivos de dados com o Python. A atualizar apenas um ou outro, pode provocar erros até que ambos sejam atualizadas.

1. Regenere a chave. Para obter informações sobre a regenerar uma chave de acesso, consulte a [gerir uma conta de armazenamento](/azure/storage/common/storage-account-manage#access-keys) artigo. Guarde a nova chave.

1. Para atualizar a área de trabalho para utilizar a nova chave, utilize os seguintes passos:

    1. Para iniciar sessão para a subscrição do Azure que contém a área de trabalho utilizando o seguinte comando da CLI do Azure:

        ```azurecli-interactive
        az login
        ```

    1. Para instalar a extensão do Azure Machine Learning, utilize o seguinte comando:

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. Para atualizar a área de trabalho para utilizar a nova chave, utilize o seguinte comando. Substitua `myworkspace` com o seu nome de área de trabalho do Azure Machine Learning e substitua `myresourcegroup` com o nome do grupo de recursos do Azure que contém a área de trabalho.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        Este comando sincroniza automaticamente as novas chaves para a conta de armazenamento do Azure utilizada pela área de trabalho.

1. Para voltar a registar datastore(s) que usam a conta de armazenamento, utilize os valores do [o que precisa ser atualizado](#whattoupdate) secção e a chave do passo 1 com o código a seguir:

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    Uma vez que `overwrite=True` for especificado, este código substitui o registo existente e atualiza-o para utilizar a nova chave.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o registo de arquivos de dados, consulte a [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) referência de classe.

---
title: Use a identidade AD da Azure com o seu serviço web
titleSuffix: Azure Machine Learning
description: Utilize a identidade Azure AD com o seu serviço web no Serviço Azure Kubernetes para aceder aos recursos da nuvem durante a pontuação.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b6e6288f125da2a29a8eff56b64f327914f90cb4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520477"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Utilizar a identidade do Azure Active Directory com o serviço Web de aprendizagem automática no Azure Kubernetes Service

Neste modo de como, aprende a atribuir uma identidade do Azure Ative Directory (AD) ao seu modelo de aprendizagem automática implantado no Serviço Azure Kubernetes. O projeto [Azure AD Pod Identity](https://github.com/Azure/aad-pod-identity) permite que as aplicações acedam aos recursos em nuvem de forma segura com a AZure AD utilizando uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) e primitivos Kubernetes. Isto permite que o seu serviço web aceda de forma segura aos seus recursos Azure sem ter de incorporar credenciais ou gerir fichas diretamente dentro do seu `score.py` script. Este artigo explica os passos para criar e instalar uma Identidade Azure no seu cluster de Serviço Azure Kubernetes e atribuir a identidade ao seu serviço web implantado.

## <a name="prerequisites"></a>Pré-requisitos

- A [extensão Azure CLI para o serviço machine learning](reference-azure-machine-learning-cli.md), o [Azure Machine Learning SDK para Python](/python/api/overview/azure/ml/intro), ou a extensão do Código do Estúdio Visual de [Aprendizagem de Máquinas Azure.](tutorial-setup-vscode-extension.md)

- Acesso ao seu cluster AKS utilizando o `kubectl` comando. Para mais informações, consulte [Connect to the cluster](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)

- Um serviço web Azure Machine Learning implantado no seu cluster AKS.

## <a name="create-and-install-an-azure-identity"></a>Criar e instalar uma Identidade Azure

1. Para determinar se o seu cluster AKS está ativado por Kubernetes RBAC, utilize o seguinte comando:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Este comando devolve um valor de `true` se o RBAC de Kubernetes estiver ativado. Este valor determina o comando a utilizar no passo seguinte.

1. Instale [a identidade do pod AD Azure](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/) no seu cluster AKS.

1. [Crie uma Identidade no Azure](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#2-create-an-identity-on-azure) seguindo os passos mostrados na página do projeto Azure AD Pod Identity.

1. [Implemente a AzureIdentity](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#3-deploy-azureidentity) seguindo os passos mostrados na página do projeto Azure AD Pod Identity.

1. [Implementar AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding) seguindo os passos mostrados na página do projeto Azure AD Pod Identity.

1. Se a Identidade Azure criada no passo anterior não estiver no mesmo grupo de recursos de nó para o seu cluster AKS, siga os passos [de Atribuição de Função](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/#user-assigned-identities-that-are-not-within-the-node-resource-group) mostrados na página do projeto Azure AD Pod Identity.

## <a name="assign-azure-identity-to-web-service"></a>Atribuir identidade azul ao serviço web

Os seguintes passos utilizem a Identidade Azure criada na secção anterior e atribuam-na ao seu serviço web AKS através de uma **etiqueta seletora**.

Em primeiro lugar, identifique o nome e o espaço de nome da sua implantação no seu cluster AKS que pretende atribuir a Identidade Azure. Pode obter esta informação executando o seguinte comando. Os espaços de nome devem ser o nome do espaço de trabalho Azure Machine Learning e o seu nome de implantação deve ser o seu nome de ponto final, tal como mostrado no portal.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Adicione o rótulo de seletor de identidade Azure à sua implementação editando a especificação de implementação. O valor do seletor deve ser o que definiu no passo 5 da [Deploy AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Edite a implementação para adicionar a etiqueta seletor de identidade Azure. Aceda à seguinte secção em `/spec/template/metadata/labels` . Deve ver valores como `isazuremlapp: “true”` . Adicione o rótulo de identidade aad-pod como mostrado abaixo.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Para verificar se a etiqueta foi corretamente adicionada, verifique o seguinte comando. Também deve ver os estatutos das cápsulas recém-criadas.

```azurecli-interactive
   kubectl get pod -n azureml-<name of workspace> --show-labels
```

Uma vez que as cápsulas estejam em funcionamento, os serviços web para esta implementação poderão agora aceder aos recursos do Azure através da sua Identidade Azure sem ter de incorporar as credenciais no seu código.

## <a name="assign-roles-to-your-azure-identity"></a>Atribuir funções à sua Identidade Azure

[Atribua a sua Identidade Gerida Azure com funções adequadas](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) para aceder a outros recursos da Azure. Certifique-se de que as funções que está a atribuir têm as **ações de dados corretas**. Por exemplo, a [Função de leitor de dados de armazenamento blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) terá lido permissões para o seu Bloco de Armazenamento enquanto a [Função](../role-based-access-control/built-in-roles.md#reader) de Leitor genérico não poderá.

## <a name="use-azure-identity-with-your-web-service"></a>Use a identidade Azure com o seu serviço web

Implemente um modelo no seu cluster AKS. O `score.py` script pode conter operações que apontam para os recursos Azure a que a sua Identidade Azure tem acesso. Certifique-se de que instalou as dependências necessárias da biblioteca do cliente para o recurso a que está a tentar aceder. Abaixo estão alguns exemplos de como pode usar a sua Identidade Azure para aceder a diferentes recursos Azure a partir do seu serviço.

### <a name="access-key-vault-from-your-web-service"></a>Acesso Key Vault a partir do seu serviço web

Se deu o seu Azure Identity leia o acesso a um segredo dentro de um **Cofre de Chaves,** `score.py` pode aceder-lhe utilizando o seguinte código.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name)
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> Este exemplo utiliza o DefaultAzureCredential. Para conceder o acesso à sua identidade utilizando uma política de acesso específica, consulte [atribuir uma política de acesso ao Cofre de Chaves utilizando o CLI Azure](../key-vault/general/assign-access-policy-cli.md).

### <a name="access-blob-from-your-web-service"></a>Aceder Blob a partir do seu serviço web

Se tiver dado o seu Azure Identity leia o acesso aos dados dentro de uma **Bolha de Armazenamento,** `score.py` pode aceder-lhe através do seguinte código.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre como usar a biblioteca de clientes Python Azure Identity, consulte o [repositório](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) no GitHub.
* Para obter um guia detalhado sobre a implementação de modelos para os clusters de serviços Azure Kubernetes, consulte o [como fazer](how-to-deploy-azure-kubernetes-service.md).
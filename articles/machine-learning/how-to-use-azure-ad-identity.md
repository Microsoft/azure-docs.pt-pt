---
title: Use a identidade AAD com o seu serviço web
titleSuffix: Azure Machine Learning
description: Utilize a identidade AAD com o seu serviço web no Serviço Azure Kubernetes para aceder aos recursos da nuvem durante a pontuação.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 02/10/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: f76e149339e80ddeba8431afffbd677a4b595ec3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319478"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Utilize a identidade AZure AD com o seu serviço web de aprendizagem automática no Serviço Azure Kubernetes

Neste modo de fazer, aprende a atribuir uma identidade do Azure Ative Directory (AAD) ao seu modelo de machine learning implantado no Serviço Azure Kubernetes. O projeto [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) permite que as aplicações acedam a recursos em nuvem de forma segura com a AAD utilizando uma [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) e primitivos Kubernetes. Isto permite que o seu serviço web aceda de forma segura aos seus recursos Azure sem ter de incorporar credenciais ou gerir fichas diretamente dentro do seu `score.py` script. Este artigo explica os passos para criar e instalar uma Identidade Azure no seu cluster de Serviço Azure Kubernetes e atribuir a identidade ao seu serviço web implantado.

## <a name="prerequisites"></a>Pré-requisitos

- A [extensão Azure CLI para o serviço machine learning](reference-azure-machine-learning-cli.md), o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), ou a extensão do Código do Estúdio Visual de [Aprendizagem de Máquinas Azure.](tutorial-setup-vscode-extension.md)

- Acesso ao seu cluster AKS utilizando o `kubectl` comando. Para mais informações, consulte [Connect to the cluster](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- Um serviço web Azure Machine Learning implantado no seu cluster AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Crie e instale uma Identidade Azure no seu cluster AKS

1. Para determinar se o seu cluster AKS está ativado no RBAC, utilize o seguinte comando:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Este comando devolve um valor `true` de se o RBAC estiver ativado. Este valor determina o comando a utilizar no passo seguinte.

1. Para instalar [a Identidade do Pod AAD](https://github.com/Azure/aad-pod-identity#getting-started) no seu cluster AKS, utilize um dos seguintes comandos:

    * Se o seu cluster AKS tiver **o RBAC ativado,** utilize o seguinte comando:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Se o seu cluster AKS **não tiver RBAC ativado,** utilize o seguinte comando:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        A saída do comando é semelhante ao seguinte texto:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Crie uma Identidade Azure](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) seguindo os passos mostrados na página do projeto AAD Pod Identity.

1. [Instale a Identidade Azure](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) seguindo os passos indicados na página do projeto AAD Pod Identity.

1. [Instale a Ligação de Identidade Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) seguindo os passos indicados na página do projeto AAD Pod Identity.

1. Se a Identidade Azure criada no passo anterior não estiver no mesmo grupo de recursos que o seu cluster AKS, siga [as Permissões definidas para MIC](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) seguindo os passos mostrados na página do projeto AAD Pod Identity.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Atribuir identidade azul ao serviço web de machine learning

Os seguintes passos utilizem a Identidade Azure criada na secção anterior e atribuam-na ao seu serviço web AKS através de uma **etiqueta seletora**.

Em primeiro lugar, identifique o nome e o espaço de nome da sua implantação no seu cluster AKS que pretende atribuir a Identidade Azure. Pode obter esta informação executando o seguinte comando. Os espaços de nome devem ser o nome do espaço de trabalho Azure Machine Learning e o seu nome de implantação deve ser o seu nome de ponto final, tal como mostrado no portal.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Adicione o rótulo de seletor de identidade Azure à sua implementação editando a especificação de implementação. O valor do seletor deve ser o que definiu no passo 5 da [Instalação da Ligação de Identidade Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

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

Para verificar se a etiqueta foi corretamente adicionada, verifique o seguinte comando.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Para ver todos os estados da cápsula, executar o seguinte comando.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Uma vez que as cápsulas estejam em funcionamento, os serviços web para esta implementação poderão agora aceder aos recursos do Azure através da sua Identidade Azure sem ter de incorporar as credenciais no seu código. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Atribua as funções adequadas à sua Identidade Azure

[Atribua a sua Identidade Gerida Azure com funções adequadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) para aceder a outros recursos da Azure. Certifique-se de que as funções que está a atribuir têm as **ações de dados corretas**. Por exemplo, a [Função de leitor de dados de armazenamento blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) terá lido permissões para o seu Bloco de Armazenamento enquanto a [Função](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) de Leitor genérico não poderá.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Use a identidade Azure com o seu serviço web de aprendizagem automática

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
> Este exemplo utiliza o DefaultAzureCredential. Para conceder o acesso à sua identidade utilizando uma política de acesso específica, consulte [a Parte 4: Recupere o segredo de si Azure Key Vault](../key-vault/general/authentication.md#part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python).

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

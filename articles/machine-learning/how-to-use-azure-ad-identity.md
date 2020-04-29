---
title: Use identidade AAD com o seu serviço web
titleSuffix: Azure Machine Learning
description: Utilize a identidade AAD com o seu serviço web no Serviço Azure Kubernetes para aceder a recursos na nuvem durante a pontuação.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77122847"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Utilize a identidade Azure AD com o seu serviço web de aprendizagem automática no Serviço Azure Kubernetes

Neste como-fazer, você aprende a atribuir uma identidade Azure Ative Directory (AAD) ao seu modelo de aprendizagem automática implantado no Serviço Azure Kubernetes. O projeto [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) permite que as aplicações acedam de forma segura aos recursos em nuvem com a AAD utilizando uma [Identidade Gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) e primitivos Kubernetes. Isto permite ao seu serviço web aceder de forma segura aos seus recursos Azure `score.py` sem ter de incorporar credenciais ou gerir fichas diretamente dentro do seu script. Este artigo explica os passos para criar e instalar uma Identidade Azure no seu cluster de Serviço Azure Kubernetes e atribuir a identidade ao seu serviço web implantado.

## <a name="prerequisites"></a>Pré-requisitos

- A [extensão Azure CLI para o serviço de Machine Learning,](reference-azure-machine-learning-cli.md)o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a extensão do Código visual de [aprendizagem automática Azure.](tutorial-setup-vscode-extension.md)

- Acesso ao seu cluster `kubectl` AKS usando o comando. Para mais informações, consulte [Connect to the cluster](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- Um serviço web Azure Machine Learning implantado no seu cluster AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Crie e instale uma Identidade Azure no seu cluster AKS

1. Para determinar se o seu cluster AKS está ativado por RBAC, utilize o seguinte comando:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Este comando devolve `true` um valor de se o RBAC estiver ativado. Este valor determina o comando a utilizar no próximo passo.

1. Para instalar a [Identidade aAD](https://github.com/Azure/aad-pod-identity#getting-started) Pod no seu cluster AKS, utilize um dos seguintes comandos:

    * Se o seu cluster AKS tiver **rBAC ativado** utilize o seguinte comando:
    
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

1. [Instale a Identidade Azure](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) seguindo os passos mostrados na página do projeto AAD Pod Identity.

1. [Instale a Ligação de Identidade Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) seguindo os passos mostrados na página do projeto AAD Pod Identity.

1. Se a Identidade Azure criada no passo anterior não estiver no mesmo grupo de recursos que o seu cluster AKS, siga [as Permissões definidas para o MIC](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) seguindo os passos mostrados na página do projeto AAD Pod Identity.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Atribuir identidade azure ao serviço web de aprendizagem automática

Os seguintes passos utilizam a Identidade Azure criada na secção anterior e atribuem-na ao seu serviço web AKS através de uma **etiqueta seletora**.

Primeiro, identifique o nome e o espaço de nome da sua implantação no seu cluster AKS que pretende atribuir à Identidade Azure. Pode obter esta informação executando o seguinte comando. Os espaços de nome devem ser o seu nome de espaço de trabalho Azure Machine Learning e o seu nome de implementação deve ser o seu nome final, como mostrado no portal.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Adicione a etiqueta do seletor de identidade Azure à sua implementação editando a especificação de implementação. O valor do seletor deve ser aquele que definiu no passo 5 de [Instalação da Ligação de Identidade Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Editar a implementação para adicionar a etiqueta do seletor de identidade Azure. Vá para a `/spec/template/metadata/labels`secção seguinte abaixo. Devia ver valores `isazuremlapp: “true”`como. Adicione a etiqueta de identidade aad-pod como mostrado abaixo.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Para verificar se a etiqueta foi corretamente adicionada, execute o seguinte comando.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Para ver todos os estados da cápsula, execute o seguinte comando.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Uma vez que as cápsulas estejam em funcionamento, os serviços web para esta implementação poderão agora aceder aos recursos azure através da sua Identidade Azure sem ter de incorporar as credenciais no seu código. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Atribuir as funções adequadas à sua Identidade Azure

[Atribuir a sua Identidade Gerida Azure com funções adequadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) para aceder a outros recursos Azure. Certifique-se de que as funções que está a atribuir têm as **corretas Ações de Dados**. Por exemplo, o Papel de Leitor de [Dados blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) de armazenamento terá lido permissões para o seu Blob de Armazenamento enquanto o [genérico Papel de Leitor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) pode não.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Use identidade Azure com o seu serviço web de aprendizagem automática

Implante um modelo para o seu cluster AKS. O `score.py` script pode conter operações que apontem para os recursos Azure a que a sua Identidade Azure tem acesso. Certifique-se de que instalou as dependências necessárias da biblioteca do cliente para o recurso a que está a tentar aceder. Abaixo estão alguns exemplos de como pode usar a sua Identidade Azure para aceder a diferentes recursos Azure do seu serviço.

### <a name="access-key-vault-from-your-web-service"></a>Aceder ao Cofre chave do seu serviço web

Se deu à sua Identidade Azure o acesso a `score.py` um segredo dentro de um Cofre de **Chaves,** pode aceder-lhe usando o seguinte código.

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

### <a name="access-blob-from-your-web-service"></a>Aceda ao Blob a partir do seu serviço web

Se tiver dado à sua Identidade Azure ler o `score.py` acesso aos dados dentro de um Blob de **Armazenamento,** pode aceder-lhe utilizando o seguinte código.

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

* Para obter mais informações sobre como utilizar a biblioteca de clientes Python Azure Identity, consulte o [repositório](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) no GitHub.
* Para obter um guia detalhado sobre a implantação de modelos para os clusters de serviço seletiva seletiva seletiva, consulte o [como .](how-to-deploy-azure-kubernetes-service.md)
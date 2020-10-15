---
title: Utilize identidades geridas para controlo de acesso (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a usar identidades geridas para controlar o acesso aos recursos Azure a partir do espaço de trabalho Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 6bcc4ac5561a8bdb721018aa05bf2376579b627b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079675"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Utilizar identidades geridas com Azure Machine Learning (pré-visualização)

[As identidades geridas](/azure/active-directory/managed-identities-azure-resources/overview) permitem-lhe configurar o seu espaço de trabalho com as *permissões mínimas necessárias para aceder aos recursos.* 

Ao configurar o espaço de trabalho Azure Machine Learning de forma fidedigna, é importante garantir que diferentes serviços associados ao espaço de trabalho tenham o nível de acesso correto. Por exemplo, durante o fluxo de trabalho de aprendizagem automática, o espaço de trabalho necessita de acesso ao Registo de Contentores Azure (ACR) para imagens docker, e contas de armazenamento para dados de formação. 

Além disso, as identidades geridas permitem o controlo fino sobre permissões, por exemplo, pode conceder ou revogar o acesso de recursos computacional específicos a um ACR específico.

Neste artigo, você vai aprender a usar identidades geridas para:

 * Configure e utilize o ACR para o seu espaço de trabalho Azure Machine Learning sem ter de permitir o acesso do utilizador administrativo ao ACR.
 * Aceda a um ACR privado externo ao seu espaço de trabalho, para retirar imagens base para treino ou inferência.
 * Aceder a conjuntos de dados para formação utilizando identidades geridas em vez de chaves de acesso ao armazenamento.

> [!IMPORTANT]
> A utilização de identidades geridas para controlar o acesso a recursos com a Azure Machine Learning está atualmente em pré-visualização. A funcionalidade de pré-visualização é fornecida "as-is", sem garantia de suporte ou acordo de nível de serviço. Para obter mais informações, consulte os [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)
- [A extensão Azure CLI para o serviço de aprendizagem automática](reference-azure-machine-learning-cli.md)
- [A máquina azul aprendendo Python SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
- Para atribuir funções, o login para a sua subscrição Azure deve ter a função [de Operador de Identidade Gerida,](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou outra função que conceda as ações necessárias (como o __Proprietário).__
- Deve estar familiarizado com a criação e o trabalho com [identidades geridas.](/azure/active-directory/managed-identities-azure-resources/overview)

## <a name="configure-managed-identities"></a>Configurar identidades geridas

Em algumas situações, é necessário proibir o acesso do utilizador administrativo ao Registo de Contentores Azure. Por exemplo, o ACR pode ser partilhado e é necessário não permitir o acesso administrativo por outros utilizadores. Ou, a criação de ACR com o utilizador administrativo ativado é proibida por uma política de nível de subscrição.

> [!IMPORTANT]
> Ao utilizar a Azure Machine Learning para inferência em Azure Container Instance (ACI), é __necessário__o acesso do utilizador administrativo em ACR . Não o desative se planeia implementar modelos para ACI para inferência.

Quando cria acr sem permitir o acesso do utilizador administrativo, as identidades geridas são usadas para aceder ao ACR para construir e retirar imagens do Docker.

Pode trazer o seu próprio ACR com o utilizador administrativo desativado quando criar o espaço de trabalho. Em alternativa, deixe o Azure Machine Learning criar espaço de trabalho ACR e desativar o utilizador administrativo posteriormente.

### <a name="bring-your-own-acr"></a>Traga o seu próprio ACR

Se o utilizador administrador ACR for inde ser inde ser inde ser inde ser indesenhado pela política de subscrição, deve primeiro criar ACR sem utilizador administrativo e, em seguida, associá-lo ao espaço de trabalho. Além disso, se tiver ACR existente com o utilizador administrativo desativado, pode anexá-lo ao espaço de trabalho.

[Crie ACR a partir do Azure CLI](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli) sem definir ```--admin-enabled``` o argumento, ou a partir do portal Azure sem permitir o utilizador de administração. Em seguida, ao criar o espaço de trabalho Azure Machine Learning, especifique o ID de recurso Azure do ACR. O exemplo a seguir demonstra a criação de um novo espaço de trabalho Azure ML que utiliza um ACR existente:

> [!TIP]
> Para obter o valor do `--container-registry` parâmetro, utilize o comando [de exibição az acr](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az_acr_show) para mostrar informações para o seu ACR. O `id` campo contém o ID de recursos para o seu ACR.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Deixe o serviço de aprendizagem automática Azure criar espaço de trabalho ACR

Se não trouxer o seu próprio ACR, o serviço Azure Machine Learning criará um para si quando realizar uma operação que precise de uma. Por exemplo, submeter uma corrida de formação para Machine Learning Compute, construir um ambiente ou implementar um ponto final de serviço web. O ACR criado pelo espaço de trabalho terá o utilizador administrativo ativado e é necessário desativar manualmente o utilizador administrativo.

1. Criar uma nova área de trabalho

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. Realizar uma ação que requer ACR. Por exemplo, o [tutorial sobre a formação de um modelo.](tutorial-train-models-with-aml.md)

1. Obtenha o nome ACR criado pelo cluster:

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    Este comando devolve um valor semelhante ao seguinte texto. Só quer a última parte do texto, que é o nome da instância ACR:

    ```text
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. Atualizar o ACR para desativar o utilizador administrativo:

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>Criar computação com identidade gerida para aceder a imagens do Docker para treino

Para aceder ao espaço de trabalho ACR, crie um cluster de cálculo de machine learning com identidade gerida atribuída pelo sistema. Pode ativar a identidade a partir do portal Azure ou estúdio ao criar o cálculo, ou a partir de Azure CLI usando

# <a name="python"></a>[Python](#tab/python)

Ao criar um cluster de computação com a [Configuração de Configuração AmlComputeProvisioning,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration?view=azure-ml-py)utilize o `identity_type` parâmetro para definir o tipo de identidade gerido.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para obter informações sobre a configuração da identidade gerida ao criar um cluster de computação em estúdio, consulte [Configurar identidade gerida](how-to-create-attach-compute-studio.md#managed-identity).

---

Uma identidade gerida é automaticamente concedida a ACRPull no espaço de trabalho ACR para permitir a retirada de imagens de Docker para o treino.

> [!NOTE]
> Se criar o cálculo primeiro, antes de o espaço de trabalho ACR ter sido criado, tem de atribuir manualmente a função ACRPull.

## <a name="access-base-images-from-private-acr"></a>Aceda a imagens base de ACR privada

Por padrão, a Azure Machine Learning utiliza imagens base do Docker que provêm de um repositório público gerido pela Microsoft. Em seguida, constrói o seu ambiente de treino ou inferência nessas imagens. Para mais informações, veja [o que são ambientes ML?](concept-environments.md)

Para utilizar uma imagem base personalizada interna da sua empresa, pode utilizar identidades geridas para aceder ao seu ACR privado. Existem dois casos de uso:

 * Use a imagem base para treinar como está.
 * Construa a imagem gerida pela Azure Machine Learning com imagem personalizada como base.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>Puxe a imagem base de Docker para o cluster de cálculo de aprendizagem automática para o treino como é

Crie um cluster de cálculo de machine learning com identidade gerida atribuída pelo sistema, ativada como descrito anteriormente. Então, determine a identificação principal da identidade gerida.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

Opcionalmente, pode atualizar o cluster de cálculo para atribuir uma identidade gerida atribuída pelo utilizador:

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

Para permitir que o cluster de computação puxe as imagens de base, conceda o papel de ACRPull de identidade de serviço gerido no ACR privado

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

Por último, ao submeter uma formação, especifique a localização da imagem base na [definição](how-to-use-environments.md#use-existing-environments)de ambiente .

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> Para garantir que a imagem base é puxada diretamente para o recurso compute, desenrida `user_managed_dependencies = True` e não especifique um Dockerfile. Caso contrário, o serviço Azure Machine Learning tentará construir uma nova imagem de Docker e falhará, porque só o cluster de computação tem acesso para retirar a imagem base do ACR.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>Construa o ambiente gerido pela Azure Machine Learning em imagem base a partir de ACR privado para treino ou inferência

Neste cenário, o serviço Azure Machine Learning constrói o ambiente de formação ou inferência em cima de uma imagem base que fornece a partir de um ACR privado. Como a tarefa de construção de imagens acontece no espaço de trabalho ACR utilizando tarefas ACR, deve executar passos adicionais para permitir o acesso.

1. Crie __identidade gerida atribuída ao utilizador__ e conceda à identidade ACRPull acesso ao __ACR privado__.  
1. Conceder ao sistema de trabalho atribuído a identidade gerida uma __função__ de Operador de Identidade Gerida na __identidade gerida atribuída pelo utilizador__ a partir do passo anterior. Esta função permite que o espaço de trabalho atribua a identidade gerida atribuída ao utilizador para a Tarefa ACR para a construção do ambiente gerido. 

    1. Obtenha a identificação principal da identidade gerida atribuída pelo sistema de espaço de trabalho:

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. Conceda o papel de Operador de Identidade Gerida:

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        O ID de recurso UAI é identificação de recurso Azure da identidade atribuída ao utilizador, no formato `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` .

1. Especifique o ACR externo e o ID do cliente da __identidade gerida atribuída pelo utilizador__ nas ligações do espaço de trabalho utilizando Workspace.set_connection [método:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-)

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

Uma vez concluída a configuração, pode utilizar as imagens base do ACR privado ao construir ambientes para treino ou inferência. O seguinte corte de código demonstra como especificar a imagem base ACR e o nome da imagem numa definição de ambiente:

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

Opcionalmente, pode especificar o URL de recursos de identidade gerido e o ID do cliente na própria definição de ambiente, utilizando [a Entidade RegistryId.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.container_registry.registryidentity?view=azure-ml-py) Se utilizar explicitamente a identidade do registo, substitui quaisquer ligações de espaço de trabalho especificadas anteriormente:

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="access-training-data"></a>Aceder a dados de formação

Uma vez criado um cluster de cálculo de machine learning com identidade gerida como descrito anteriormente, pode usar essa identidade para aceder a dados de treino sem chaves de conta de armazenamento. Pode utilizar a identidade gerida atribuída pelo sistema ou pelo utilizador para este cenário.

### <a name="grant-compute-managed-identity-access-to-storage-account"></a>Grant compute gerido acesso de identidade à conta de armazenamento

[Conceda à identidade gerida um papel de leitor](https://docs.microsoft.com/azure/storage/common/storage-auth-aad#assign-azure-roles-for-access-rights) na conta de armazenamento na qual armazena os seus dados de formação.

### <a name="register-data-store-with-workspace"></a>Registar loja de dados com espaço de trabalho

Depois de atribuir a identidade gerida, pode criar uma loja de dados sem ter de especificar credenciais de armazenamento.

```python
from azureml.core import Datastore

blob_dstore = Datastore.register_azure_blob_container(workspace=workspace,
                                                      datastore_name='my-datastore',
                                                      container_name='my-container',
                                                      account_name='my-storage-account')
```

### <a name="submit-training-run"></a>Submeter execução de preparação

Quando submete uma corrida de formação utilizando a loja de dados, o cálculo de machine learning utiliza a sua identidade gerida para aceder aos dados.

## <a name="use-docker-images-for-inference"></a>Use imagens docker para inferência

Depois de configurar o ACR sem o utilizador administrativo, como descrito anteriormente, pode aceder às imagens do Docker para inferência sem as teclas de administração do seu serviço Azure Kubernetes (AKS). Quando cria ou liga a AKS ao espaço de trabalho, o principal de serviço do cluster é automaticamente atribuído ao ACRPull acesso ao espaço de trabalho ACR.

> [!NOTE]
> Se você trouxer o seu próprio cluster AKS, o cluster deve ter o principal de serviço habilitado em vez de identidade gerida.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [segurança empresarial em Azure Machine Learning.](concept-enterprise-security.md)

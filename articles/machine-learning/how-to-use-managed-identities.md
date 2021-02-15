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
ms.date: 10/22/2020
ms.openlocfilehash: 014c592713a8568b3bbc7e8e536f81b203271ccc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388078"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Utilizar identidades geridas com Azure Machine Learning (pré-visualização)

[As identidades geridas](../active-directory/managed-identities-azure-resources/overview.md) permitem-lhe configurar o seu espaço de trabalho com as *permissões mínimas necessárias para aceder aos recursos.* 

Ao configurar o espaço de trabalho Azure Machine Learning de forma fidedigna, é importante garantir que diferentes serviços associados ao espaço de trabalho tenham o nível de acesso correto. Por exemplo, durante o fluxo de trabalho de aprendizagem automática, o espaço de trabalho necessita de acesso ao Registo de Contentores Azure (ACR) para imagens docker, e contas de armazenamento para dados de formação. 

Além disso, as identidades geridas permitem o controlo fino sobre permissões, por exemplo, pode conceder ou revogar o acesso de recursos computacional específicos a um ACR específico.

Neste artigo, você vai aprender a usar identidades geridas para:

 * Configure e utilize o ACR para o seu espaço de trabalho Azure Machine Learning sem ter de permitir o acesso do utilizador administrativo ao ACR.
 * Aceda a um ACR privado externo ao seu espaço de trabalho, para retirar imagens base para treino ou inferência.
 * Criar espaço de trabalho com identidade gerida atribuída ao utilizador para aceder a recursos associados.

> [!IMPORTANT]
> A utilização de identidades geridas para controlar o acesso a recursos com a Azure Machine Learning está atualmente em pré-visualização. A funcionalidade de pré-visualização é fornecida "as-is", sem garantia de suporte ou acordo de nível de serviço. Para obter mais informações, consulte os [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)
- [A extensão Azure CLI para o serviço de aprendizagem automática](reference-azure-machine-learning-cli.md)
- [A máquina azul aprendendo Python SDK.](/python/api/overview/azure/ml/intro?view=azure-ml-py)
- Para atribuir funções, o login para a sua subscrição Azure deve ter a função [de Operador de Identidade Gerida,](../role-based-access-control/built-in-roles.md#managed-identity-operator) ou outra função que conceda as ações necessárias (como o __Proprietário).__
- Deve estar familiarizado com a criação e o trabalho com [identidades geridas.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="configure-managed-identities"></a>Configurar identidades geridas

Em algumas situações, é necessário proibir o acesso do utilizador administrativo ao Registo de Contentores Azure. Por exemplo, o ACR pode ser partilhado e é necessário não permitir o acesso administrativo por outros utilizadores. Ou, a criação de ACR com o utilizador administrativo ativado é proibida por uma política de nível de subscrição.

> [!IMPORTANT]
> Ao utilizar a Azure Machine Learning para inferência em Azure Container Instance (ACI), é __necessário__ o acesso do utilizador administrativo em ACR . Não o desative se planeia implementar modelos para ACI para inferência.

Quando cria acr sem permitir o acesso do utilizador administrativo, as identidades geridas são usadas para aceder ao ACR para construir e retirar imagens do Docker.

Pode trazer o seu próprio ACR com o utilizador administrativo desativado quando criar o espaço de trabalho. Em alternativa, deixe o Azure Machine Learning criar espaço de trabalho ACR e desativar o utilizador administrativo posteriormente.

### <a name="bring-your-own-acr"></a>Traga o seu próprio ACR

Se o utilizador administrador ACR for inde ser indesenhado pela política de subscrição, deve primeiro criar ACR sem utilizador administrativo e, em seguida, associá-lo ao espaço de trabalho. Além disso, se tiver ACR existente com o utilizador administrativo desativado, pode anexá-lo ao espaço de trabalho.

[Crie ACR a partir do Azure CLI](../container-registry/container-registry-get-started-azure-cli.md) sem definir ```--admin-enabled``` o argumento, ou a partir do portal Azure sem permitir o utilizador de administração. Em seguida, ao criar o espaço de trabalho Azure Machine Learning, especifique o ID de recurso Azure do ACR. O exemplo a seguir demonstra a criação de um novo espaço de trabalho Azure ML que utiliza um ACR existente:

> [!TIP]
> Para obter o valor do `--container-registry` parâmetro, utilize o comando [de exibição az acr](/cli/azure/acr#az_acr_show) para mostrar informações para o seu ACR. O `id` campo contém o ID de recursos para o seu ACR.

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

    ```output
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. Atualizar o ACR para desativar o utilizador administrativo:

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>Criar computação com identidade gerida para aceder a imagens do Docker para treino

Para aceder ao espaço de trabalho ACR, crie um cluster de cálculo de machine learning com identidade gerida atribuída pelo sistema. Pode ativar a identidade a partir do portal Azure ou estúdio ao criar o cálculo, ou a partir de Azure CLI usando o seguinte. Para obter mais informações, consulte [a utilização da identidade gerida com clusters compute](how-to-create-attach-compute-cluster.md#managed-identity).

# <a name="python"></a>[Python](#tab/python)

Ao criar um cluster de computação com a [Configuração de Configuração AmlComputeProvisioning,](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration?view=azure-ml-py)utilize o `identity_type` parâmetro para definir o tipo de identidade gerido.

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

Neste cenário, o serviço Azure Machine Learning constrói o ambiente de formação ou inferência em cima de uma imagem base que fornece a partir de um ACR privado. Como a tarefa de construção de imagens acontece no espaço de trabalho ACR utilizando tarefas ACR, deve executar mais passos para permitir o acesso.

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

1. Especifique o ACR externo e o ID do cliente da __identidade gerida atribuída pelo utilizador__ nas ligações do espaço de trabalho utilizando Workspace.set_connection [método:](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-)

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

Opcionalmente, pode especificar o URL de recursos de identidade gerido e o ID do cliente na própria definição de ambiente, utilizando [a Entidade RegistryId.](/python/api/azureml-core/azureml.core.container_registry.registryidentity?view=azure-ml-py) Se utilizar explicitamente a identidade do registo, substitui quaisquer ligações de espaço de trabalho especificadas anteriormente:

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>Use imagens docker para inferência

Depois de configurar o ACR sem o utilizador administrativo, como descrito anteriormente, pode aceder às imagens do Docker para inferência sem as teclas de administração do seu serviço Azure Kubernetes (AKS). Quando cria ou liga a AKS ao espaço de trabalho, o principal de serviço do cluster é automaticamente atribuído ao ACRPull acesso ao espaço de trabalho ACR.

> [!NOTE]
> Se você trouxer o seu próprio cluster AKS, o cluster deve ter o principal de serviço habilitado em vez de identidade gerida.

## <a name="create-workspace-with-user-assigned-managed-identity"></a>Criar espaço de trabalho com identidade gerida atribuída pelo utilizador

Ao criar espaço de trabalho, pode especificar uma identidade gerida atribuída ao utilizador que será usada para aceder aos recursos associados: ACR, KeyVault, Storage e App Insights.

Primeiro [crie uma identidade gerida atribuída ao utilizador](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli]), e tome nota do ID de recursos ARM da identidade gerida.

Em seguida, use Azure CLI ou Python SDK para criar o espaço de trabalho. Ao utilizar o CLI, especifique o ID utilizando o `--primary-user-assigned-identity` parâmetro. Quando utilizar o SDK, `primary_user_assigned_identity` utilize. Seguem-se exemplos de utilização do Azure CLI e python para criar um novo espaço de trabalho utilizando estes parâmetros:

__CLI do Azure__

```azurecli-interactive
az ml workspace create -w <workspace name> -g <resource group> --primary-user-assigned-identity <managed identity ARM ID>
```

__Python__

```python
from azureml.core import Workspace

ws = Workspace.create(name="workspace name", 
    subscription_id="subscription id", 
    resource_group="resource group name",
    primary_user_assigned_identity="managed identity ARM ID")
```

Também pode utilizar [um modelo ARM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) para criar um espaço de trabalho com identidade gerida atribuída pelo utilizador.

> [!IMPORTANT]
> Se você trouxer os seus próprios recursos associados, em vez de ter o serviço Azure Machine Learning criá-los, você deve conceder as funções de identidade geridas nesses recursos. Utilize o [modelo ARM de atribuição de funções](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) para esbudê-lo.

Para um espaço de trabalho com (chaves geridas pelo cliente para encriptação)[], https://docs.microsoft.com/azure/machine-learning/concept-data-encryption pode passar numa identidade gerida pelo utilizador para autenticar do armazenamento para o Key Vault. Utilize o argumento __de identidade-para-cmk-encriptação__ (CLI) ou __user_assigned_identity_for_cmk_encryption__ (SDK) para passar na identidade gerida. Esta identidade gerida pode ser a mesma ou diferente do utilizador primário do espaço de trabalho atribuído à identidade gerida.

Se tiver um espaço de trabalho existente, pode atualizá-lo a partir do sistema atribuído à identidade gerida atribuída pelo utilizador utilizando ```az ml workspace update``` o comando CLI ou ```Workspace.update``` o método Python SDK.


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [segurança empresarial em Azure Machine Learning.](concept-enterprise-security.md)

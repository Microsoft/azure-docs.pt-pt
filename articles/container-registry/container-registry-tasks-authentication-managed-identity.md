---
title: Utilizar uma identidade gerida com tarefas de registo de contentor do Azure
description: Fornece um acesso de tarefas do Azure Container Registry para recursos do Azure, incluindo outros registos de contentores privados ao atribuir uma identidade gerida para recursos do Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5b60727472a06aaac8ccd3dce8609461e8972311
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148033"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Utilizar um Azure identidade em tarefas de ACR gerido 

Utilize um [identidade de recursos do Azure gerida](../active-directory/managed-identities-azure-resources/overview.md) a autenticação do ACR tarefas para um registo de contentor do Azure ou outros recursos do Azure, sem necessidade de fornecer ou gerir as credenciais no código. Por exemplo, utilize uma identidade gerida para extrair ou enviar imagens de contentor para o outro registo como um passo numa tarefa.

Neste artigo, aprender mais sobre identidades geridas e como:

> [!div class="checklist"]
> * Ativar uma identidade atribuída de sistema ou uma identidade de utilizador atribuída numa tarefa ACR
> * Conceder o acesso de identidade para recursos do Azure, como outros registos de contentores do Azure
> * Utilizar a identidade gerida para aceder aos recursos a partir de uma tarefa 

Para criar os recursos do Azure, este artigo requer que execute a CLI do Azure versão 2.0.66 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerida?

Uma identidade gerida para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory (Azure AD). Pode configurar [determinados recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), incluindo tarefas do ACR, com uma identidade gerida. Em seguida, utilize a identidade para aceder a outros recursos do Azure, sem passar credenciais no código ou em scripts.

Identidades geridas são de dois tipos:

* *Identidiades atribuídas*, que pode atribuir a vários recursos e persistir durante mais tempo pretendido. Identidiades atribuídas estão atualmente em pré-visualização.

* R *gerenciados pelo sistema de identidade*, que é exclusivo a um recurso específico, como uma tarefa ACR e tem uma duração de tempo de vida desse recurso.

Depois de configurar um recurso do Azure com uma identidade gerida, conceda o acesso de identidade para outro recurso, tal como qualquer entidade de segurança. Por exemplo, atribua uma identidade gerida uma função com a solicitação, push e pull ou outras permissões para um registo de contentor privado no Azure. (Para obter uma lista completa de funções de registo, consulte [permissões e funções do Azure Container Registry](container-registry-roles.md).) Pode conceder um acesso de identidade para um ou mais recursos.

## <a name="create-container-registries"></a>Criar registos de contentores

Para este tutorial, terá três registos de contentores:

* Utilizar o registo primeiro para criar e executar tarefas do ACR. Neste artigo, este registo de origem é denominado *myregistry*. 
* Os segundo e terceiro registos são registos de destino para a primeira tarefa de exemplo enviar uma imagem que ele se baseia. Neste artigo, os registos de destino são nomeados *customregistry1* e *customregistry2*.

Substitua por seus próprios nomes de registo em passos posteriores.

Se ainda não tiver os registos de contentores do Azure necessários, consulte o artigo [início rápido: Criar um registo de contentor privado com a CLI do Azure](container-registry-get-started-azure-cli.md). Não precisa de enviar imagens para o registo ainda.

## <a name="example-task-with-a-system-assigned-identity"></a>Exemplo: Tarefa com uma identidade atribuída de sistema

Este exemplo mostra como criar uma [tarefas de vários passos](container-registry-tasks-multi-step.md) com uma identidade atribuída de sistema. A tarefa cria uma imagem e, em seguida, utiliza a identidade para autenticar com dois registos de destino para enviar a imagem.

Os passos para esta tarefa de exemplo são definidos num [ficheiro YAML](container-registry-tasks-reference-yaml.md) com o nome `testtask.yaml`. O ficheiro está localizado no diretório multipleRegistries do [acr-tarefas](https://github.com/Azure-Samples/acr-tasks) repositório de exemplos. O ficheiro está reproduzido aqui:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Criar tarefas com a identidade atribuída de sistema

Criar a tarefa *vários reg* executando o seguinte [az acr tarefa criar] [ az-acr-task-create] comando. O contexto de tarefas é a pasta de multipleRegistries do repositório de exemplos e o comando faz referência ao arquivo `testtask.yaml` no repositório. O `--assign-identity` parâmetro sem valor adicional cria uma identidade de atribuído de sistema de mensagens em fila para a tarefa. Esta tarefa está a ser configurada para ter dispará-lo manualmente, mas pode configurá-lo para ser executada quando confirmações são enviados por push ao repositório ou é efetuado um pedido pull. 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

No resultado do comando, o `identity` seção mostra uma identidade do tipo `SystemAssigned` está definida para a tarefa. O `principalId` é o ID de principal de serviço da identidade:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

Utilizar o [show de tarefa az acr] [ az-acr-task-show] comando para armazenar o `principalId` numa variável, para utilizar nos comandos posteriores:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Conceder permissões de push de identidade para dois registos de contentores de destino

Nesta secção, conceder as permissões de identidade atribuída de sistema para enviar por push para os registos de destino de duas, com o nome *customregistry1* e *customregistry2*.

Utilizar pela primeira vez o [show do az acr] [ az-acr-show] comando para obter o ID de recurso de cada registo e armazenar os IDs nas variáveis:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Utilize o [criação da atribuição de função de az] [ az-role-assignment-create] comando para atribuir a identidade do `acrpush` função para cada registo. Esta função tem permissões para imagens de push e pull para um registo de contentor.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>Adicionar credenciais de registo de destino a tarefa

Agora utilizar o [adicionar credenciais de tarefa az acr] [ az-acr-task-credential-add] comando para adicionar credenciais da identidade para a tarefa, de modo que ele possa ser autenticado com ambos os registos de destino.

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>Executar manualmente a tarefa

Utilize o [execução da tarefa az acr] [ az-acr-task-run] comando para acionar a tarefa manualmente. O `--set` parâmetro é utilizado para transmitir os nomes de servidor de início de sessão dos registos de duas destino como valores para as variáveis de tarefas `REGISTRY1` e `REGISTRY2`.

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

A saída é semelhante a:

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>Exemplo: Tarefa com uma identidade de utilizador atribuído

Neste exemplo cria uma identidade atribuído ao utilizador com permissões para ler segredos a partir de um cofre de chave do Azure. Atribuir esta identidade para uma tarefa de vários passos que lê o segredo, cria uma imagem e inicia sessão na CLI do Azure para ler a tag de imagem.

### <a name="create-a-key-vault-and-store-a-secret"></a>Criar um cofre de chaves e armazenar um segredo

Primeiro, se for necessário, crie um grupo de recursos chamado *myResourceGroup* no *eastus* localização com o seguinte [criar grupo az] [ az-group-create]comando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utilize o [az keyvault crie] [ az-keyvault-create] comando para criar um cofre de chaves. Certifique-se de que especifique um nome de Cofre de chaves exclusivas. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Store um segredo de exemplo no Cofre de chaves com o [conjunto de segredos de keyvault de az] [ az-keyvault-secret-set] comando:

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Por exemplo, pode querer armazenar as credenciais para autenticar com um registo privado do Docker, para que pode extrair uma imagem privada.

### <a name="create-an-identity"></a>Criar uma identidade

Criar uma identidade com o nome *myACRTasksId* na sua subscrição com o [criar a identidade de az] [ az-identity-create] comando. Pode utilizar o mesmo grupo de recursos que utilizou anteriormente para criar um registo de contentor ou o Cofre de chaves ou num diferente.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Para configurar a identidade nos passos seguintes, utilize o [show de identidade az] [ az-identity-show] comando para armazenar o recurso com a identidade ID e o ID de principal de serviço em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Conceder acesso de identidade para o Cofre de chaves para ler um segredo

Execute o seguinte [az keyvault conjunto-policy] [ az-keyvault-set-policy] comando para definir uma política de acesso no Cofre de chaves. O exemplo a seguir permite que a identidade de utilizador atribuída obter segredos a partir do Cofre de chaves. Este acesso é necessário mais tarde para executar uma tarefa de vários passo com êxito.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Conceder acesso de leitor de identidade para o grupo de recursos para o registo

Execute o seguinte [criação da atribuição de função de az] [ az-role-assignment-create] comando para atribuir a identidade de uma função de leitor, nesse caso para o grupo de recursos que contém o registo de origem. Esta função é necessária mais tarde para executar uma tarefa de vários passo com êxito.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Criar tarefas com a identidade de utilizador atribuído

Agora, crie uma [tarefas de vários passos](container-registry-tasks-multi-step.md) e atribua-a identidade atribuída por utilizador. Para esta tarefa de exemplo, criar um [ficheiro YAML](container-registry-tasks-reference-yaml.md) com o nome `managed-identities.yaml` num diretório de trabalho local e cole o seguinte conteúdo. Certifique-se de que substitua o nome do Cofre de chaves no ficheiro com o nome do seu Cofre de chaves

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

Esta tarefa faz o seguinte:

* Verifica-se de que ele pode acessar o segredo no Cofre de chaves. Este passo é para fins de demonstração. Num cenário do mundo real, poderá ser necessário um passo de tarefa para obter as credenciais para aceder um repositório privado do Docker Hub.
* Cria e envia o `mywebsite` imagem para o registo de origem.
* Os registos para a CLI do Azure à lista o `my-website` etiquetas no registo de origem da imagem.

Criar uma tarefa chamada *msitask* e transmita-o ID de recurso da identidade atribuído ao utilizador que criou anteriormente. Esta tarefa de exemplo é criada a partir do `managed-identities.yaml` ficheiro guardado no diretório de trabalho local, para que tenha de dispará-lo manualmente.

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

No resultado do comando, o `identity` seção mostra uma identidade do tipo `UserAssigned` está definida para a tarefa. O `principalId` é o ID de principal de serviço da identidade:

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>Executar manualmente a tarefa

Utilize o [execução da tarefa az acr] [ az-acr-task-run] comando para acionar a tarefa manualmente. O `--set` parâmetro é utilizado para transmitir o nome do registo de origem para a tarefa:

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

Saída mostra o segredo é resolvido, a imagem é criada e enviados por push com êxito e a tarefa inicia sessão na CLI do Azure com a identidade para ler a tag de imagem no registo de origem:

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre a utilização de identidades geridas com tarefas de registo de contentor do Azure e como:

> [!div class="checklist"]
> * Ativar uma identidade atribuída de sistema ou atribuído ao utilizador numa tarefa ACR
> * Conceder o acesso de identidade para recursos do Azure, como outros registos de contentores do Azure
> * Utilizar a identidade gerida para aceder aos recursos a partir de uma tarefa  

* Saiba mais sobre [geridos identidades para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/).

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy

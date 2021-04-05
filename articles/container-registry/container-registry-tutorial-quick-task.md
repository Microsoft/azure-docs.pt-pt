---
title: Tutorial - Construção rápida da imagem do recipiente
description: Neste tutorial, vai aprender a compilar uma imagem do contentor de Docker no Azure com o Azure Container Registry Tasks (ACR Tasks) e, em seguida, implementá-la no Azure Container Instances.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: b218f47348d5a26297f14c4bc788a6cf6b78cc60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96030339"
---
# <a name="tutorial-build-and-deploy-container-images-in-the-cloud-with-azure-container-registry-tasks"></a>Tutorial: Compilar e implementar imagens de contentor na cloud com Tarefas do Azure Container Registry

O [ACR Tasks](container-registry-tasks-overview.md) é um conjunto de funcionalidades no Azure Container Registry que fornece compilações de imagens do contentor de Docker simplificadas e eficientes no Azure. Neste artigo, vai aprender a utilizar a funcionalidade *tarefa rápida* do ACR Tasks.

O ciclo de desenvolvimento de "ciclo interno" é o processo iterativo de escrita de código, compilar e testar a sua aplicação antes de a consolidar para o controlo de origem. Uma tarefa rápida expande o seu “ciclo interno” para a cloud, fornecendo-lhe a validação da compilação com êxito e o envio automático de imagens compiladas com êxito para o registo de contentor. As imagens são compiladas de forma nativa na cloud, junto do seu registo, o que permite uma implementação mais rápida.

Todos os seus conhecimentos do Dockerfile podem ser transferidos diretamente para o ACR Tasks. Não tem de alterar o Dockerfiles para compilar na cloud com o ACR Tasks, apenas o comando que está a executar. 

Neste tutorial, a primeira parte de uma série:

> [!div class="checklist"]
> * Obter o código de origem da aplicação de exemplo
> * Compilar uma imagem do contentor no Azure
> * Implementar um contentor no Azure Container Instances

Nos tutoriais subsequentes, vai aprender a utilizar o ACR Tasks para obter compilações automatizadas de imagens do contentor após a consolidação do código e a atualização da imagem de base. As tarefas ACR também podem executar [tarefas em várias etapas,](container-registry-tasks-multi-step.md)utilizando um ficheiro YAML para definir passos para construir, empurrar e testar opcionalmente vários recipientes.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="github-account"></a>Conta do GitHub

Crie uma conta em https://github.com, se ainda não tiver uma. Esta série de tutoriais utiliza um repositório de GitHub para demonstrar as compilações de imagens automatizadas no ACR Tasks.

### <a name="fork-sample-repository"></a>Bifurcar um repositório de exemplo

Em seguida, utilize a IU do GitHub para bifurcar o repositório de exemplo na conta do GitHub. Neste tutorial, vai criar uma imagem do contentor a partir da origem no repositório e, no tutorial seguinte, vai enviar uma consolidação para o fork do repositório para iniciar uma tarefa automatizada.

Bifurcar este repositório: https://github.com/Azure-Samples/acr-build-helloworld-node

![Captura de ecrã do botão Bifurcar (realçado) no GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Clonar a bifurcação

Depois de concluir a bifurcação do repositório, clone a bifurcação e introduza o diretório que contém o clone local.

Clone o repo com `git` , **\<your-github-username\>** substitua-o pelo seu nome de utilizador GitHub:

```console
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Introduza o diretório que contém o código de origem:

```console
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Shell do Bash

Os comandos nesta série de tutoriais estão formatados para a shell do Bash. Se preferir utilizar o PowerShell, a Linha de Comandos ou outra shell, poderá ter de ajustar o formato da continuação de linha e da variável de ambiente em conformidade.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="build-in-azure-with-acr-tasks"></a>Compilar no Azure com o ACR Tasks

Agora que já descarregou o código de origem para o seu computador, siga estes passos para criar um registo do contentor e compilar a imagem do contentor com o ACR Tasks.

Para a execução dos comandos de exemplo ser mais fácil, os tutoriais nesta série utilizam variáveis de ambiente da shell. Execute o comando seguinte para definir a variável `ACR_NAME`. **\<registry-name\>** Substitua-o por um nome único para o seu novo registo de contentores. O nome do registo deve ser único dentro de Azure, conter apenas letras minúsculas e conter 5-50 caracteres alfanuméricos. Os outros recursos que criar no tutorial baseiam-se neste nome, pelo que necessita de modificar apenas esta primeira variável.

```console
ACR_NAME=<registry-name>
```

Com a variável de ambiente de registo de contentor preenchida, deve agora poder copiar e colar o resto dos comandos no tutorial sem editar nenhum dos valores. Execute os comandos seguintes para criar um grupo de recursos e um registo de contentor:

```azurecli
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Agora que tem um registo, utilize o ACR Tasks para compilar uma imagem do contentor a partir do código de exemplo. Execute o comando [az acr build][az-acr-build] para realizar uma *tarefa rápida*:

```azurecli
az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
```

O resultado do comando [az acr build][az-acr-build] é semelhante ao seguinte. Pode ver o carregamento do código de origem (o “contexto”) para o Azure e os detalhes da operação `docker build` que o ACR Tasks executa na cloud. Uma vez que o ACR Tasks utiliza `docker build` para compilar as suas imagens, não são necessárias alterações ao Dockerfiles para começar a utilizar imediatamente o ACR Tasks.

```output
Packing source code into tar file to upload...
Sending build context (4.813 KiB) to ACR...
Queued a build with build ID: da1
Waiting for build agent...
2020/11/18 18:31:42 Using acb_vol_01185991-be5f-42f0-9403-a36bb997ff35 as the home volume
2020/11/18 18:31:42 Setting up Docker configuration...
2020/11/18 18:31:43 Successfully set up Docker configuration
2020/11/18 18:31:43 Logging in to registry: myregistry.azurecr.io
2020/11/18 18:31:55 Successfully logged in
Sending build context to Docker daemon   21.5kB
Step 1/5 : FROM node:15-alpine
15-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:15-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 88087d7e709a
Step 3/5 : RUN cd /src && npm install
 ---> Running in e80e1263ce9a
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.1s
Removing intermediate container e80e1263ce9a
 ---> 26aac291c02e
Step 4/5 : EXPOSE 80
 ---> Running in 318fb4c124ac
Removing intermediate container 318fb4c124ac
 ---> 113e157d0d5a
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in fe7027a11787
Removing intermediate container fe7027a11787
 ---> 20a27b90eb29
Successfully built 20a27b90eb29
Successfully tagged myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:11 Pushing image: myregistry.azurecr.io/helloacrtasks:v1, attempt 1
The push refers to repository [myregistry.azurecr.io/helloacrtasks]
6428a18b7034: Preparing
c44b9827df52: Preparing
172ed8ca5e43: Preparing
8c9992f4e5dd: Preparing
8dfad2055603: Preparing
c44b9827df52: Pushed
172ed8ca5e43: Pushed
8dfad2055603: Pushed
6428a18b7034: Pushed
8c9992f4e5dd: Pushed
v1: digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1 size: 1366
2020/11/18 18:32:43 Successfully pushed image: myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:43 Step ID acb_step_0 marked as successful (elapsed time in seconds: 15.648945)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloacrtasks
    tag: v1
    digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git: {}

Run ID: da1 was successful after 1m9.970148252s
```

Perto do final do resultado, o ACR Tasks mostra as dependências que detetou para a imagem. Estas dependências permitem que o ACR Tasks automatize as compilações de imagens ao atualizar as imagens de base, por exemplo, quando uma imagem de base é atualizada com as correções do SO ou da estrutura. Mais adiante nesta série de tutoriais, vai obter informações sobre o suporte do ACR Tasks para as atualizações das imagens de base.

## <a name="deploy-to-azure-container-instances"></a>Implementar no Azure Container Instances

Por predefinição, o ACR Tasks envia automaticamente imagens compiladas com êxito para o seu registo, permitindo-lhe implementá-las de imediato a partir do registo.

Nesta secção, vai criar um Azure Key Vault e um principal de serviço e, em seguida, VAI implementar o contentor no Azure Container Instances (ACI) ao utilizar as credenciais de um principal de serviço.

### <a name="configure-registry-authentication"></a>Configurar a autenticação do registo

Todos os cenários de produção devem utilizar [principais de serviço][service-principal-auth] para aceder aos registos de contentor do Azure. Os principais de serviço permitem-lhe ter controlo de acesso baseado em funções sobre as imagens de contentor. Por exemplo, pode configurar um principal de serviço com acesso a um registo apenas por pedido.

#### <a name="create-a-key-vault"></a>Criar um cofre de chaves

Se ainda não tiver um cofre no [Azure Key Vault](../key-vault/index.yml), crie um com a CLI do Azure através dos comandos seguintes.

```azurecli
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-a-service-principal-and-store-credentials"></a>Criar um principal de serviço e armazenar as credenciais

Agora tem de criar um principal de serviço e armazenar as credenciais no cofre de chaves.

Utilize o comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar o principal de serviço e [az keyvault secret set][az-keyvault-secret-set] para armazenar a **palavra-passe** do principal de serviço no cofre:

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

O `--role` argumento no comando anterior configura o diretor de serviço com a função *acrpull,* que lhe concede acesso apenas ao registo. Para conceder tanto o impulso como o acesso, mude o `--role` argumento para *acrpush*.

Em seguida, armazene o *appId* do principal de serviço no cofre, que é o **nome de utilizador** que passa para o Azure Container Registry para autenticação:

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Acabou de criar um Azure Key Vault e de armazenar dois segredos nele:

* `$ACR_NAME-pull-usr`: o ID do principal de serviço, para utilização como o **nome de utilizador** do registo de contentor.
* `$ACR_NAME-pull-pwd`: a palavra-passe do principal de serviço, para utilização como a **palavra-passe** do registo de contentor.

Agora, pode referenciar estes segredos por nome quando você ou as suas aplicações e serviços solicitarem imagens do registo.

### <a name="deploy-a-container-with-azure-cli"></a>Implementar um contentor com a CLI do Azure

Agora que as credenciais do principal de serviço estão armazenadas como segredos no Azure Key Vault, as aplicações e os serviços podem utilizá-las para aceder ao registo privado.

Execute o comando seguinte [az container create][az-container-create] para implementar uma instância de contentor. O comando utiliza as credenciais do principal de serviço armazenadas no Azure Key Vault para autenticar no registo de contentor.

```azurecli
az container create \
    --resource-group $RES_GROUP \
    --name acr-tasks \
    --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-tasks-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

O valor `--dns-name-label` tem de ser exclusivo no Azure, para que o comando anterior acrescente o nome do registo de contentor à etiqueta de nome DNS do contentor. O resultado do comando apresenta o nome de domínio completamente qualificado (FQDN) do contentor, por exemplo:

```output
FQDN
----------------------------------------------
acr-tasks-myregistry.eastus.azurecontainer.io
```

Tome nota do FQDN do contentor, vai utilizá-lo na próxima secção.

### <a name="verify-the-deployment"></a>Verificar a implementação

Para ver o processo de arranque do contentor, utilize o comando [az container attach][az-container-attach]:

```azurecli
az container attach --resource-group $RES_GROUP --name acr-tasks
```

A `az container attach` saída apresenta primeiro o estado do recipiente à medida que puxa a imagem e começa, ligando depois o STDOUT e o STDERR da consola local ao do recipiente.

```output
Container 'acr-tasks' is in state 'Running'...
(count: 1) (last timestamp: 2020-11-18 18:39:10+00:00) pulling image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:15+00:00) Successfully pulled image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Created container
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Started container

Start streaming logs:
Server running at http://localhost:80
```

Quando `Server running at http://localhost:80` for apresentado, navegue para o FQDN do contentor no browser para ver a aplicação em execução. O FQDN devia ter sido apresentado como resultado do comando `az container create` que executou na secção anterior.

:::image type="content" source="media/container-registry-tutorial-quick-build/quick-build-02-browser.png" alt-text="Aplicação de amostra em execução no navegador":::

Para desanexar a consola do contentor, prima `Control+C`.

## <a name="clean-up-resources"></a>Limpar os recursos

Pare a instância de contentor com o comando [az container delete][az-container-delete]:

```azurecli
az container delete --resource-group $RES_GROUP --name acr-tasks
```

Para remover *todos* os recursos que criou neste tutorial, incluindo o registo de contentor, o cofre de chaves e o principal de serviço, emita os comandos seguintes. No entanto, estes recursos serão utilizados no [próximo tutorial](container-registry-tutorial-build-task.md) da série, por isso, poderá querer mantê-los se avançar diretamente para o próximo tutorial.

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Passos seguintes

Agora que testou o ciclo interno com uma tarefa rápida, configure uma **tarefa de compilação** para acionar compilações de imagens do contentor quando consolida código de origem para um repositório de Git:

> [!div class="nextstepaction"]
> [Acionar compilações automáticas com tarefas](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-login]: /cli/azure/reference-index#az-login
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png

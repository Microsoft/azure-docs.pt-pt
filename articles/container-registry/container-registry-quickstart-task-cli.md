---
title: Quickstart - Construa uma imagem de contentor a pedido em Azure
description: Utilize comandos de registo de contentores Azure para construir, empurrar e executar rapidamente uma imagem de contentor Docker a pedido, na nuvem Azure.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: c6fe1fc246d112218b492072155175b2db99c8c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97032954"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Quickstart: Construa e execute uma imagem de contentor usando tarefas de registo de contentores Azure

Neste arranque rápido, você usa [comandos de registo de contentores Azure][container-registry-tasks-overview] para construir, empurrar e executar uma imagem de recipiente Docker nativamente dentro de Azure, sem uma instalação local do Docker. ACR Tasks é um conjunto de funcionalidades dentro do Registo do Contentor de Azure para ajudá-lo a gerir e modificar imagens de contentores através do ciclo de vida do contentor. Este exemplo mostra como descarregar o seu ciclo de desenvolvimento de imagem de contentor "inner-loop" para a nuvem com construções a pedido usando um Dockerfile local. 

Após este arranque rápido, explore funcionalidades mais avançadas de Tarefas ACR utilizando os [tutoriais.](container-registry-tutorial-quick-task.md) As tarefas ACR podem automatizar as construções de imagem com base em códigos comprometedores ou atualizações de imagem base, ou testar vários contentores, em paralelo, entre outros cenários. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
    
- Este arranque rápido requer a versão 2.0.58 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se ainda não tiver um registo de contentores, primeiro crie um grupo de recursos com o [grupo az criar][az-group-create] comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registo de contentor

Crie um registo de contentores utilizando o comando [az acr create.][az-acr-create] O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. No exemplo seguinte, é utilizado *o myContainerRegistry008.* Atualize para um valor exclusivo.

```azurecli-interactive
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry008 --sku Basic
```

Este exemplo cria um registo *básico,* uma opção otimizada para os custos para os desenvolvedores que aprendem sobre o Registo de Contentores Azure. Para obter mais informações sobre os níveis de serviço disponíveis, consulte [os níveis de serviço de registo de contentores][container-registry-skus].

## <a name="build-and-push-image-from-a-dockerfile"></a>Construa e empurre a imagem de um Dockerfile

Agora use o registo do contentor Azure para construir e empurrar uma imagem. Primeiro, crie um diretório de trabalho local e, em seguida, crie um Dockerfile chamado *Dockerfile* com uma única linha: `FROM mcr.microsoft.com/hello-world` . Este é um exemplo simples para construir uma imagem de contentor Linux a partir da `hello-world` imagem hospedada no Microsoft Container Registry. Pode criar o seu próprio Dockerfile padrão e construir imagens para outras plataformas. Se estiver a trabalhar numa concha de pancada, crie o Dockerfile com o seguinte comando:

```bash
echo FROM mcr.microsoft.com/hello-world > Dockerfile
```

Executar o comando [de construção az acr,][az-acr-build] que constrói a imagem e, depois de a imagem ser construída com sucesso, empurra-a para o seu registo. O exemplo a seguir constrói e empurra a `sample/hello-world:v1` imagem. O `.` fim do comando define a localização do Dockerfile, neste caso o atual diretório.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

A saída de uma construção e impulso bem sucedidos é semelhante ao seguinte:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM mcr.microsoft.com/hello-world
latest: Pulling from hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>Executar a imagem

Agora, rapidamente, execute a imagem que construiu e empurrou para o seu registo. Aqui você usa [a az acr run][az-acr-run] para executar o comando do contentor. No seu fluxo de trabalho de desenvolvimento de contentores, este pode ser um passo de validação antes de implementar a imagem, ou pode incluir o comando num ficheiro YAML em [várias etapas][container-registry-tasks-multi-step]. 

O exemplo a seguir `$Registry` utiliza-se para especificar o registo onde executou o comando:

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

O `cmd` parâmetro neste exemplo executa o recipiente na sua configuração predefinição, mas suporta `cmd` parâmetros adicionais `docker run` ou mesmo `docker` outros comandos.

O resultado é semelhante ao seguinte:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar o comando de exclusão do [grupo az][az-group-delete] para remover o grupo de recursos, o registo do contentor e as imagens do contentor aí armazenadas.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, usou funcionalidades de Tarefas ACR para construir, empurrar e executar rapidamente uma imagem de contentor Docker nativamente dentro de Azure, sem uma instalação local do Docker. Continue para os tutoriais de Tarefas de Registo de Contentores Azure para aprender sobre a utilização de tarefas ACR para automatizar construções e atualizações de imagem.

> [!div class="nextstepaction"]
> [Tutoriais de tarefas de registo de contentores Azure][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli

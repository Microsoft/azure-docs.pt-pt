---
title: Início rápido-compilar & executar a imagem de contêiner no registro de contêiner do Azure
description: Execute rapidamente tarefas com o registro de contêiner do Azure para compilar e executar uma imagem de contêiner sob demanda na nuvem.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: b97249aa61916975fa641d4620179be33e1d5276
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931540"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Início rápido: criar e executar uma imagem de contêiner usando tarefas do registro de contêiner do Azure

Neste guia de início rápido, você usa comandos de tarefas do registro de contêiner do Azure para criar, enviar por push e executar rapidamente uma imagem de contêiner do Docker nativamente no Azure, mostrando como descarregar o ciclo de desenvolvimento de "loop interno" para a nuvem. [As tarefas ACR][container-registry-tasks-overview] são um conjunto de recursos no registro de contêiner do Azure para ajudá-lo a gerenciar e modificar imagens de contêiner no ciclo de vida do contêiner. 

Após este guia de início rápido, explore recursos mais avançados de tarefas ACR. As tarefas do ACR podem automatizar as compilações de imagem com base em confirmações de código ou atualizações de imagem de base, ou testar vários contêineres, em paralelo, entre outros cenários. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pode utilizar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir este início rápido. Se você quiser usá-lo localmente, a versão 2.0.58 ou posterior é recomendada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Se você ainda não tiver um registro de contêiner, primeiro crie um grupo de recursos com o comando [AZ Group Create][az-group-create] . Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Crie um registro de contêiner usando o comando [AZ ACR Create][az-acr-create] . O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. No exemplo a seguir, *myContainerRegistry008* é usado. Atualize para um valor exclusivo.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry008 --sku Basic
```

Este exemplo cria um registro *básico* , uma opção com otimização de custo para os desenvolvedores aprenderem sobre o registro de contêiner do Azure. Para obter detalhes sobre as camadas de serviço disponíveis, consulte [SKUs de registro de contêiner][container-registry-skus].

## <a name="build-an-image-from-a-dockerfile"></a>Criar uma imagem de um Dockerfile

Agora, use o registro de contêiner do Azure para criar uma imagem. Primeiro, crie um diretório de trabalho e, em seguida, crie um Dockerfile chamado *Dockerfile* com o conteúdo a seguir. Este é um exemplo simples para criar uma imagem de contêiner do Linux, mas você pode criar suas próprias Dockerfile padrão e imagens de Build para outras plataformas.

```bash
echo FROM hello-world > Dockerfile
```

Execute o comando [AZ ACR Build][az-acr-build] para criar a imagem. Quando criada com êxito, a imagem é enviada por push para o registro. O exemplo a seguir envia por push a imagem de `sample/hello-world:v1`. O `.` no final do comando define o local do Dockerfile, neste caso, o diretório atual.

```azurecli-interactive
az acr build --image sample/hello-world:v1 --registry myContainerRegistry008 --file Dockerfile . 
```

A saída de um Build e Push com êxito é semelhante à seguinte:

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
Step 1/1 : FROM hello-world
latest: Pulling from library/hello-world
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

Agora, execute rapidamente a imagem criada e enviada por push ao registro. No fluxo de trabalho de desenvolvimento de contêiner, essa pode ser uma etapa de validação antes de implantar a imagem.

Crie um arquivo *quickrun. YAML* em um diretório de trabalho local com o seguinte conteúdo para uma única etapa. Substitua o nome do servidor de logon do registro por *\<acrLoginServer\>* . O nome do servidor de logon está no formato *\<nome-do-registro\>. azurecr.Io* (todas as letras minúsculas), por exemplo, *mycontainerregistry008.azurecr.Io*. Este exemplo pressupõe que você criou e enviou por push a imagem de `sample/hello-world:v1` na seção anterior:

```yml
steps:
  - cmd: <acrLoginServer>/sample/hello-world:v1
```

A etapa de `cmd` neste exemplo executa o contêiner em sua configuração padrão, mas `cmd` dá suporte a parâmetros de `docker run` adicionais ou até mesmo outros comandos de `docker`.

Execute o contêiner com o seguinte comando:

```azurecli-interactive
az acr run --registry myContainerRegistry008 --file quickrun.yaml .
```

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

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar o comando [AZ Group Delete][az-group-delete] para remover o grupo de recursos, o registro de contêiner e as imagens de contêiner armazenadas nele.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você usou recursos de tarefas ACR para criar, enviar e executar rapidamente uma imagem de contêiner do Docker nativamente no Azure. Continue com os tutoriais do registro de contêiner do Azure para saber mais sobre como usar tarefas ACR para automatizar compilações e atualizações de imagem.

> [!div class="nextstepaction"]
> [Tutoriais do registro de contêiner do Azure][container-registry-tutorial-quick-task]

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
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli

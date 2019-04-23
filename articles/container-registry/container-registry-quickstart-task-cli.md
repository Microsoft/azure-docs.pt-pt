---
title: Início rápido - compilar e executar uma imagem de contentor no Azure Container Registry
description: Execute rapidamente tarefas com o Azure Container Registry para criar e executar uma contentor imagem sob demanda, na cloud.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 61a17842158326c927f049af893a00818f3acc55
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793391"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Início rápido: Criar e executar uma imagem de contentor com tarefas de registo de contentor do Azure

Neste início rápido, vai utilizar comandos de tarefas de registo de contentor do Azure para criar rapidamente, push e executar um Docker imagem de contentor nativamente no Azure, que mostra como descarregar o ciclo de desenvolvimento de "loop interno" para a cloud. [Tarefas de ACR] [ container-registry-tasks-overview] é um conjunto de recursos no Azure Container Registry para o ajudar a gerir e modificar imagens de contentor o ciclo de vida do contentor. 

Depois deste guia de introdução, explore funcionalidades mais avançadas das tarefas do ACR. Tarefas de ACR pode automatizar compilações de imagem com base nas consolidações de código ou atualizações de imagem base, ou teste de vários contentores, em paralelo, entre outros cenários. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pode utilizar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir este início rápido. Se gostaria de usá-lo localmente, versão 2.0.58 ou posterior é recomendado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se ainda não tiver um registo de contentor, primeiro de criar um grupo de recursos com o [criar grupo az] [ az-group-create] comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Crie um registo de contentor com o [criar az acr] [ az-acr-create] comando. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. No exemplo a seguir *myContainerRegistry008* é utilizado. Atualize para um valor exclusivo.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry008 --sku Basic
```

Este exemplo cria um *básica* registro, uma opção com otimização de custos para os desenvolvedores a aprender sobre o Azure Container Registry. Para obter detalhes sobre os escalões de serviço disponíveis, consulte [SKUs do registo de contentor][container-registry-skus].

## <a name="build-an-image-from-a-dockerfile"></a>Crie uma imagem a partir de um Dockerfile

Utilize o Azure Container Registry para criar uma imagem. Em primeiro lugar, crie um diretório de trabalho e, em seguida, crie um Dockerfile com o nome *Dockerfile* com o seguinte conteúdo. Esse é um exemplo simple para criar uma imagem de contentor do Linux, mas pode criar seu próprio padrão Dockerfile e criar imagens para outras plataformas.

```bash
echo "FROM hello-world" > Dockerfile
```

Executar o [compilação do az acr] [ az-acr-build] comando para criar a imagem. Quando criada com êxito, a imagem é enviada por push para o seu registo. O exemplo seguinte envia por push o `sample/hello-world:v1` imagem. O `.` no final do comando define a localização do Dockerfile, neste caso, o diretório atual.

```azurecli-interactive
az acr build --image sample/hello-world:v1 --registry myContainerRegistry008 --file Dockerfile . 
```

Resultado de uma compilação bem-sucedida e push é semelhante ao seguinte:

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

## <a name="run-the-image"></a>Execute a imagem

Agora execute rapidamente a imagem criada e enviadas para o seu registo. No seu fluxo de trabalho de desenvolvimento do contentor, pode ser uma etapa de validação antes de implementar a imagem.

Crie um ficheiro *quickrun.yaml* num diretório de trabalho local com o seguinte conteúdo para um único passo. Substitua o nome do servidor de início de sessão do seu registo para  *\<acrLoginServer\>*. O nome do servidor de início de sessão está no formato  *\<nome do registo\>. azurecr.io* (em minúsculas), por exemplo, *mycontainerregistry008.azurecr.io*. Este exemplo assume que criada e enviados por push o `sample/hello-world:v1` imagem na secção anterior:

```yml
steps:
  - cmd: <acrLoginServer>/sample/hello-world:v1
```

O `cmd` passo neste exemplo é executado o contentor na configuração predefinida, mas `cmd` suporta adicionais `docker run` parâmetros ou, inclusivamente, noutras `docker` comandos.

Execute o contentor com o seguinte comando:

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

Quando já não for necessário, pode utilizar o [eliminação do grupo de az] [ az-group-delete] comando para remover o grupo de recursos, o registo de contentor e as imagens de contentor armazenadas nele.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, utilizou funcionalidades de tarefas do ACR para rapidamente criar, push e executar um Docker imagem de contentor nativamente no Azure. Avance para os tutoriais de registo de contentor do Azure para saber como utilizar o ACR tarefas para automatizar compilações de imagem e atualizações.

> [!div class="nextstepaction"]
> [Tutoriais de registo de contentor do Azure][container-registry-tutorial-quick-task]

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

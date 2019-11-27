---
title: Criar imagem com a nuvem nativa Buildpack
description: Use o comando AZ ACR Pack Build para criar uma imagem de contêiner de um aplicativo e enviar por push para o registro de contêiner do Azure, sem usar um Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 9cd1ae464213027cba3012c93c0ca3894c804750
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456118"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Criar e enviar por push uma imagem de um aplicativo usando um Buildpack nativo de nuvem

O comando CLI do Azure `az acr pack build` usa a ferramenta CLI do [`pack`](https://github.com/buildpack/pack) , de [Buildpacks](https://buildpacks.io/), para criar um aplicativo e enviar por push sua imagem para um registro de contêiner do Azure. Esse recurso fornece uma opção para criar rapidamente uma imagem de contêiner do código-fonte do aplicativo em node. js, Java e em outras linguagens sem precisar definir um Dockerfile.

Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure para executar os exemplos neste artigo. Se você quiser usá-lo localmente, a versão 2.0.70 ou posterior será necessária. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="use-the-build-command"></a>Usar o comando de Build

Para criar e enviar por push uma imagem de contêiner usando a nuvem nativa Buildpacks, execute o comando [AZ ACR Pack Build][az-acr-pack-build] . Enquanto o comando [AZ ACR Build][az-acr-build] cria e envia uma imagem de uma fonte Dockerfile e de um código relacionado, com `az acr pack build` você especifica uma árvore de origem do aplicativo diretamente.

No mínimo, especifique o seguinte ao executar `az acr pack build`:

* Um registro de contêiner do Azure em que você executa o comando
* Um nome de imagem e uma marca para a imagem resultante
* Um dos [locais de contexto com suporte](container-registry-tasks-overview.md#context-locations) para tarefas ACR, como um diretório local, um repositório GitHub ou um tarball remoto
* O nome de uma imagem do Buildpack Builder adequada para seu aplicativo. Caches do registro de contêiner do Azure as imagens do Construtor, como `cloudfoundry/cnb:0.0.34-cflinuxfs3` para compilações mais rápidas.  

o `az acr pack build` dá suporte a outros recursos de comandos de tarefas ACR, incluindo a [execução de variáveis](container-registry-tasks-reference-yaml.md#run-variables) e logs de [execução de tarefas](container-registry-tasks-overview.md#view-task-logs) que são transmitidos e também salvos para recuperação posterior.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Exemplo: criar imagem do node. js com o construtor de Cloud Foundry

O exemplo a seguir cria uma imagem de contêiner de um aplicativo node. js no repositório [Azure-Samples/NodeJS-docs-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) , usando o construtor de `cloudfoundry/cnb:0.0.34-cflinuxfs3`. Esse construtor é armazenado em cache pelo registro de contêiner do Azure, portanto, um parâmetro de `--pull` não é necessário:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Este exemplo cria a imagem `node-app` com a marca `1.0` e a envia para o registro de contêiner *myregistry* . Neste exemplo, o nome do registro de destino é explicitamente anexado ao nome da imagem. Se não for especificado, o nome do servidor de logon do registro será automaticamente anexado ao nome da imagem.

A saída do comando mostra o progresso da criação e do envio por push da imagem. 

Depois que a imagem for criada com êxito, você poderá executá-la com o Docker, se ela estiver instalada. Primeiro entre no registro:

```azurecli
az acr login --name myregistry
```

Execute a imagem:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Navegue até `localhost:1337` em seu navegador favorito para ver o aplicativo Web de exemplo. Pressione `[Ctrl]+[C]` para parar o contêiner.

## <a name="example-build-java-image-with-heroku-builder"></a>Exemplo: Compilar imagem Java com o Heroku Builder

O exemplo a seguir cria uma imagem de contêiner do aplicativo Java no repositório [buildpack/Sample-java-app](https://github.com/buildpack/sample-java-app) , usando o construtor de `heroku/buildpacks:18`. O parâmetro `--pull` especifica que o comando deve efetuar pull da imagem mais recente do construtor. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Este exemplo cria a imagem `java-app` marcada com a ID de execução do comando e a envia para o registro de contêiner *myregistry* .

A saída do comando mostra o progresso da criação e do envio por push da imagem. 

Depois que a imagem for criada com êxito, você poderá executá-la com o Docker, se ela estiver instalada. Primeiro entre no registro:

```azurecli
az acr login --name myregistry
```

Execute a imagem, substituindo sua marca de imagem para *RunId*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Navegue até `localhost:8080` em seu navegador favorito para ver o aplicativo Web de exemplo. Pressione `[Ctrl]+[C]` para parar o contêiner.


## <a name="next-steps"></a>Passos Seguintes

Depois de criar e enviar por push uma imagem de contêiner com `az acr pack build`, você pode implantá-la como qualquer imagem para um destino de sua escolha. As opções de implantação do Azure incluem executá-lo no [serviço de aplicativo](../app-service/containers/tutorial-custom-docker-image.md) ou no [serviço kubernetes do Azure](../aks/tutorial-kubernetes-deploy-cluster.md), entre outros.

Para obter mais informações sobre recursos de tarefas de ACR, consulte [automatizar compilações de imagem de contêiner e manutenção com tarefas ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

---
title: Construa imagem com Buildpack Nativo nuvem
description: Utilize o comando de construção de az acr para construir uma imagem de contentor a partir de uma aplicação e empurre para o Registo do Contentor de Azure, sem usar um Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 264fc7314c78088ebfefb9ddb8edbe38fa16581a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92736637"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Construa e empurre uma imagem a partir de uma aplicação usando um Cloud Native Buildpack

O comando Azure CLI `az acr pack build` utiliza a [`pack`](https://github.com/buildpack/pack) ferramenta CLI, a partir de [Buildpacks,](https://buildpacks.io/)para construir uma app e empurrar a sua imagem para um registo de contentores Azure. Esta funcionalidade oferece uma opção para construir rapidamente uma imagem de recipiente a partir do código fonte da sua aplicação em Node.js, Java e outros idiomas sem ter que definir um Dockerfile.

Pode utilizar o Azure Cloud Shell ou uma instalação local do Azure CLI para executar os exemplos neste artigo. Se quiser usá-lo localmente, a versão 2.0.70 ou posterior é necessária. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="use-the-build-command"></a>Use o comando de construção

Para construir e empurrar uma imagem de recipiente usando cloud native buildpacks, executar o comando de construção de [pacote az acr.][az-acr-pack-build] Enquanto o comando [de construção az acr][az-acr-build] constrói e empurra uma imagem a partir de uma fonte dockerfile e código relacionado, `az acr pack build` com você especificar uma árvore de origem de aplicação diretamente.

No mínimo, especifique o seguinte quando `az acr pack build` correr:

* Um registo de contentores Azure onde você dirige o comando
* Um nome de imagem e etiqueta para a imagem resultante
* Um dos locais de [contexto suportado para tarefas](container-registry-tasks-overview.md#context-locations) ACR, como um diretório local, um repo GitHub, ou uma bola de tarball remoto
* O nome de uma imagem de construtor buildpack adequada para a sua aplicação. Azure Container Registry caches imagens de construtora, tais como `cloudfoundry/cnb:0.0.34-cflinuxfs3` para construções mais rápidas.  

`az acr pack build` suporta outras funcionalidades dos comandos ACR Tasks, incluindo variáveis de [execução](container-registry-tasks-reference-yaml.md#run-variables) e [registos de execução](container-registry-tasks-logs.md) de tarefas que são transmitidos e também guardados para posterior recuperação.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Exemplo: Construa Node.js imagem com o construtor cloud foundry

O exemplo a seguir constrói uma imagem de recipiente a partir de uma aplicação de Node.js no [Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) repo, utilizando o `cloudfoundry/cnb:0.0.34-cflinuxfs3` construtor. Este construtor é cached pelo Registo de Contentores Azure, por isso não é necessário um `--pull` parâmetro:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Este exemplo constrói a `node-app` imagem com a etiqueta e `1.0` empurra-a para o registo do contentor *do miocizador.* Neste exemplo, o nome do registo-alvo é explicitamente preparado para o nome de imagem. Se não for especificado, o nome do servidor de login do registo é automaticamente pré-preparado para o nome de imagem.

A saída do comando mostra o progresso da construção e da pressão da imagem. 

Depois de a imagem ser construída com sucesso, pode executá-la com o Docker, se a instalar. Primeiro sinal no seu registo:

```azurecli
az acr login --name myregistry
```

Executar a imagem:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Navegue no `localhost:1337` seu navegador favorito para ver a aplicação web da amostra. Pressione `[Ctrl]+[C]` para parar o recipiente.

## <a name="example-build-java-image-with-heroku-builder"></a>Exemplo: Construa a imagem de Java com o construtor Heroku

O exemplo a seguir constrói uma imagem de contentor a partir da app Java no [repo buildpack/sample-java-app,](https://github.com/buildpack/sample-java-app) utilizando o `heroku/buildpacks:18` construtor. O `--pull` parâmetro especifica que o comando deve puxar a imagem mais recente do construtor. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Este exemplo constrói a `java-app` imagem marcada com a identificação de execução do comando e empurra-a para o registo do contentor de *miogresso.*

A saída do comando mostra o progresso da construção e da pressão da imagem. 

Depois de a imagem ser construída com sucesso, pode executá-la com o Docker, se a instalar. Primeiro sinal no seu registo:

```azurecli
az acr login --name myregistry
```

Executar a imagem, substituindo a sua etiqueta de imagem por *runid*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Navegue no `localhost:8080` seu navegador favorito para ver a aplicação web da amostra. Pressione `[Ctrl]+[C]` para parar o recipiente.


## <a name="next-steps"></a>Passos seguintes

Depois de construir e empurrar uma imagem de recipiente `az acr pack build` com, pode implantá-la como qualquer imagem para um alvo à sua escolha. As opções de implementação do Azure incluem executá-lo no [Serviço de Aplicações](../app-service/tutorial-custom-container.md) ou [no Serviço Azure Kubernetes,](../aks/tutorial-kubernetes-deploy-cluster.md)entre outros.

Para obter mais informações sobre as funcionalidades de Tarefas ACR, consulte [a construção e manutenção de imagens de recipientes de automatização com tarefas ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

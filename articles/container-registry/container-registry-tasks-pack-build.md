---
title: Construa imagem com Cloud Native Buildpack
description: Utilize o comando de construção de az acr para construir uma imagem de recipiente a partir de uma aplicação e empurre para o Registo de Contentores Azure, sem usar um Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087069"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Construa e empurre uma imagem de uma aplicação usando um Cloud Native Buildpack

O comando `az acr pack build` Azure CLI utiliza a [`pack`](https://github.com/buildpack/pack) ferramenta CLI, a partir da [Buildpacks,](https://buildpacks.io/)para construir uma aplicação e empurrar a sua imagem para um registo de contentores Azure. Esta funcionalidade fornece uma opção para construir rapidamente uma imagem de recipiente a partir do seu código fonte de aplicação em Node.js, Java e outras línguas sem ter que definir um Dockerfile.

Pode utilizar a Casca de Nuvem Azure ou uma instalação local do Azure CLI para executar os exemplos deste artigo. Se quiser usá-lo localmente, a versão 2.0.70 ou posterior é necessária. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="use-the-build-command"></a>Use o comando de construção

Para construir e empurrar uma imagem de recipiente usando Cloud Native Buildpacks, executar o comando de construção de [az acr pack.][az-acr-pack-build] Enquanto o comando de [construção az acr][az-acr-build] constrói e empurra uma `az acr pack build` imagem de uma fonte de Dockerfile e código relacionado, com você especifica uma árvore de origem de aplicação diretamente.

No mínimo, especifique `az acr pack build`o seguinte quando executar:

* Um registo de contentores Azure onde dirige o comando
* Um nome de imagem e etiqueta para a imagem resultante
* Um dos [locais de contexto suportado](container-registry-tasks-overview.md#context-locations) para Tarefas ACR, como um diretório local, um repo GitHub, ou uma bola de tarball remota
* O nome de uma imagem de construtor buildpack adequada para a sua aplicação. O registo de contentores Azure `cloudfoundry/cnb:0.0.34-cflinuxfs3` caches imagens de construtores, como para construções mais rápidas.  

`az acr pack build`suporta outras funcionalidades dos comandos aCR Tasks, incluindo variáveis de execução e [registos](container-registry-tasks-reference-yaml.md#run-variables) de execução de [tarefas](container-registry-tasks-logs.md) que são transmitidos e também guardados para posterior recuperação.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Exemplo: Construa imagem nonódeo com o construtor cloud foundry

O exemplo seguinte constrói uma imagem de recipiente a partir de uma app Node.js no [Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) repo, usando o `cloudfoundry/cnb:0.0.34-cflinuxfs3` construtor. Este construtor é cacheed pelo Registo de `--pull` Contentores Azure, por isso não é necessário um parâmetro:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Este exemplo constrói `node-app` a `1.0` imagem com a etiqueta e empurra-a para o registo de *contentores do meu registo.* Neste exemplo, o nome do registo-alvo é explicitamente preparado para o nome da imagem. Se não especificado, o nome do servidor de login do registo é automaticamente preparado para o nome da imagem.

A saída de comando mostra o progresso da construção e do impulso da imagem. 

Depois de a imagem ser construída com sucesso, pode executá-la com o Docker, se a tiver instalada. Primeiro sinal no seu registo:

```azurecli
az acr login --name myregistry
```

Executar a imagem:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Navegue `localhost:1337` no seu navegador favorito para ver a aplicação web da amostra. Pressione `[Ctrl]+[C]` para parar o recipiente.

## <a name="example-build-java-image-with-heroku-builder"></a>Exemplo: Construa a imagem de Java com o construtor Heroku

O exemplo seguinte constrói uma imagem de recipiente da aplicação Java no [repo buildpack/sample-java-app,](https://github.com/buildpack/sample-java-app) utilizando o `heroku/buildpacks:18` construtor. O `--pull` parâmetro especifica que o comando deve puxar a última imagem do construtor. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Este exemplo constrói `java-app` a imagem marcada com a identificação do comando e empurra-a para o registo do contentor *do meu registo.*

A saída de comando mostra o progresso da construção e do impulso da imagem. 

Depois de a imagem ser construída com sucesso, pode executá-la com o Docker, se a tiver instalada. Primeiro sinal no seu registo:

```azurecli
az acr login --name myregistry
```

Executar a imagem, substituindo a sua etiqueta de imagem por *runid:*

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Navegue `localhost:8080` no seu navegador favorito para ver a aplicação web da amostra. Pressione `[Ctrl]+[C]` para parar o recipiente.


## <a name="next-steps"></a>Passos seguintes

Depois de construir e empurrar `az acr pack build`uma imagem de recipiente com, pode implantá-la como qualquer imagem para um alvo à sua escolha. As opções de implementação do Azure incluem executá-lo no [App Service](../app-service/containers/tutorial-custom-docker-image.md) ou [no Azure Kubernetes Service,](../aks/tutorial-kubernetes-deploy-cluster.md)entre outros.

Para obter mais informações sobre as funcionalidades de Tarefas ACR, consulte a construção e manutenção de [imagens de contentores Automate com Tarefas ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

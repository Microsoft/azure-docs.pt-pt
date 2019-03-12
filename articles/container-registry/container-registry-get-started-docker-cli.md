---
title: Enviar imagem do Docker para registo de contentores do Azure privado
description: Enviar e extrair imagens do Docker para um registo privado de contentor do Azure com a CLI do Docker
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: d2aa05d6648a76b02e09c660d7e6f6f30988db53
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542170"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Enviar a sua primeira imagem para um registo privado de contentor Docker com a CLI do Docker

Os registos de contentores do Azure armazenam e gerem imagens de contentores do [Docker](https://hub.docker.com) privadas, de forma semelhante a como o [Docker Hub](https://hub.docker.com/) armazena imagens do Docker públicas. Pode utilizar o [interface de linha de comandos do Docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) para [início de sessão](https://docs.docker.com/engine/reference/commandline/login/), [push](https://docs.docker.com/engine/reference/commandline/push/), [pull](https://docs.docker.com/engine/reference/commandline/pull/)e outras operações no seu contentor registo.

Nas etapas a seguir, baixar um oficial [imagem da Nginx](https://store.docker.com/images/nginx) partir do registo do Docker Hub público, identifique-a para o seu registo de contentores do Azure privado, enviá-la para o seu registo e, em seguida, tirá-lo a partir do registo.

## <a name="prerequisites"></a>Pré-requisitos

* **Registo de contentores do Azure** - crie um registos de contentores na sua subscrição do Azure. Por exemplo, utilizar o [portal do Azure](container-registry-get-started-portal.md) ou o [CLI do Azure](container-registry-get-started-azure-cli.md).
* **Docker CLI** -também tem de ter o Docker instalado localmente. Docker disponibiliza pacotes que configuram facilmente em qualquer [macOS] [docker-mac], [Windows] [docker windows] ou [Linux] [docker-linux] system.

## <a name="log-in-to-a-registry"></a>Iniciar sessão num registo

Existem [várias formas de autenticar](container-registry-authentication.md) para o seu registo de contentor privado. O método recomendado ao trabalhar numa linha de comandos é com o comando da CLI do Azure [início de sessão az acr](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Por exemplo iniciar sessão para um registo denominado *myregistry*:

```azurecli
az acr login --name myregistry
```

Também pode iniciar sessão com [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/). Por exemplo, poderá ter [atribuído um principal de serviço](container-registry-authentication.md#service-principal) para o seu registo para um cenário de automatização. Quando executar o seguinte comando, forneça interativamente o appID principal de serviço (nome de utilizador) e a palavra-passe quando lhe for pedido. Para melhores práticas gerir as credenciais de início de sessão, consulte a [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/) referência do comando:

```
docker login myregistry.azurecr.io
```

Os dois comandos devolvem `Login Succeeded` depois de concluir.

> [!TIP]
> Sempre especificar o nome do registo completamente qualificado (em minúsculas) quando utiliza `docker login` e quando marca imagens para enviar por push para o seu registo. Nos exemplos neste artigo, é o nome completamente qualificado *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Extraia a imagem oficial do Nginx

Em primeiro lugar, extrair a imagem pública da Nginx para o computador local.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Executar o contentor localmente

Executar o seguinte [docker run](https://docs.docker.com/engine/reference/run/) comando para iniciar uma instância local do contentor de Nginx interativamente (`-it`) na porta 8080. O `--rm` argumento especifica que o contêiner deve ser removido quando, impedi-lo.

```
docker run -it --rm -p 8080:80 nginx
```

Navegue até [ http://localhost:8080 ](http://localhost:8080) para ver a página da web padrão servidos pelo Nginx no contentor em execução. Deverá ver uma página semelhante ao seguinte:

![Nginx no computador local](./media/container-registry-get-started-docker-cli/nginx.png)

Uma vez que iniciou o contentor de forma interativa com `-it`, pode ver o servidor Nginx o resultado na linha de comando depois de navegar para o mesmo no seu browser.

Para parar e remover o contentor, prima `Control` + `C`.

## <a name="create-an-alias-of-the-image"></a>Criar um alias da imagem

Uso [etiqueta do docker](https://docs.docker.com/engine/reference/commandline/tag/) para criar um alias da imagem com o caminho totalmente qualificado para o seu registo. Este exemplo especifica o espaço de nomes `samples` para evitar sobrepovoar a raiz do registo.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Para obter mais informações sobre a marcação com espaços de nomes, consulte a [espaços de nomes do repositório](container-registry-best-practices.md#repository-namespaces) secção [melhores práticas do Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Enviar a imagem para o seu registo

Agora que já marcou a imagem com o caminho totalmente qualificado para o seu registo privado, pode enviá-la para o registo com [push do docker](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Extrair a imagem do seu registo

Utilize o [solicitação docker](https://docs.docker.com/engine/reference/commandline/pull/) comando para extrair a imagem a partir do registo:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Iniciar o contentor de Nginx

Utilize o [docker run](https://docs.docker.com/engine/reference/run/) comando para executar a imagem tiver obtido a partir do registo:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Navegue até [ http://localhost:8080 ](http://localhost:8080) para ver o contentor em execução.

Para parar e remover o contentor, prima `Control` + `C`.

## <a name="remove-the-image-optional"></a>Remova a imagem (opcional)

Se já não precisar da imagem da Nginx, pode eliminá-lo localmente com o [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) comando.

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Para remover as imagens do seu registo de contentor do Azure, pode utilizar o comando da CLI do Azure [eliminação de repositório az acr](/cli/azure/acr/repository#az-acr-repository-delete). Por exemplo, o comando seguinte elimina o manifesto referenciado pelo `samples/nginx:latest` marca, quaisquer dados de camada única além de todas as outras etiquetas que referencia o manifesto.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe as noções básicas, está pronto para começar a utilizar o seu registo! Por exemplo, implemente imagens de contentor a partir do registo para:

* [Serviço Kubernetes do Azure (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

---
title: Empurre & puxar a imagem do Docker
description: Enviar e extrair imagens do Docker para um registo privado de contentor do Azure com a CLI do Docker
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 6751a04c3c1bfe826334161704c20c1ba2e5a6d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74456366"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Enviar a sua primeira imagem para um registo privado de contentor Docker com a CLI do Docker

Os registos de contentores do Azure armazenam e gerem imagens de contentores do [Docker](https://hub.docker.com) privadas, de forma semelhante a como o [Docker Hub](https://hub.docker.com/) armazena imagens do Docker públicas. Pode utilizar a interface de [linha de comando Docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) para iniciar [sessão,](https://docs.docker.com/engine/reference/commandline/login/) [empurrar,](https://docs.docker.com/engine/reference/commandline/push/) [puxar](https://docs.docker.com/engine/reference/commandline/pull/)e outras operações no registo do seu contentor.

Nos seguintes passos, você descarrega uma imagem oficial [nginx](https://store.docker.com/images/nginx) do registo público do Docker Hub, marque-a para o seu registo privado de contentores Azure, empurre-a para o seu registo e, em seguida, puxe-a do registo.

## <a name="prerequisites"></a>Pré-requisitos

* **Registo de contentores do Azure** - crie um registos de contentores na sua subscrição do Azure. Por exemplo, utilize o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI** - Também deve ter o Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="log-in-to-a-registry"></a>Iniciar sessão num registo

Existem [várias formas](container-registry-authentication.md) de autenticar o seu registo de contentores privados. O método recomendado ao trabalhar numa linha de comando é com o [login az acr acr](/cli/azure/acr?view=azure-cli-latest#az-acr-login)comando Azure CLI . Por exemplo, fazer login num registo chamado *myregistry:*

```azurecli
az acr login --name myregistry
```

Também pode iniciar sessão com login de [estivador](https://docs.docker.com/engine/reference/commandline/login/). Por exemplo, pode ter [atribuído um diretor](container-registry-authentication.md#service-principal) de serviço ao seu registo para um cenário de automação. Quando executar o seguinte comando, forneça interativamente o appID principal do serviço (nome de utilizador) e a palavra-passe quando solicitado. Para obter as melhores práticas para gerir as credenciais de login, consulte a referência do comando de login do [estivador:](https://docs.docker.com/engine/reference/commandline/login/)

```
docker login myregistry.azurecr.io
```

Ambos os `Login Succeeded` comandos regressam uma vez concluídos.

> [!TIP]
> Especifique sempre o nome de registo totalmente `docker login` qualificado (toda a minúscula) quando utilizar e quando marcar imagens para empurrar para o seu registo. Nos exemplos deste artigo, o nome totalmente qualificado é *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Puxe a imagem oficial de Nginx

Primeiro, puxe a imagem pública de Nginx para o seu computador local.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Executar o contentor localmente

Execute o comando de execução de [estivadores](https://docs.docker.com/engine/reference/run/) seguintes`-it`para iniciar uma instância local do contentor Nginx interactivamente () na porta 8080. O `--rm` argumento especifica que o recipiente deve ser removido quando o parar.

```
docker run -it --rm -p 8080:80 nginx
```

Navegue `http://localhost:8080` para ver a página web predefinida servida pela Nginx no recipiente de corrida. Deve ver uma página semelhante à seguinte:

![Nginx no computador local](./media/container-registry-get-started-docker-cli/nginx.png)

Como iniciou o contentor `-it`interativamente com, pode ver a saída do servidor Nginx na linha de comando depois de navegar para ele no seu navegador.

Para parar e retirar `Control` + `C`o recipiente, prima .

## <a name="create-an-alias-of-the-image"></a>Criar um pseudónimo da imagem

Utilize a [etiqueta de estiva](https://docs.docker.com/engine/reference/commandline/tag/) para criar um pseudónimo da imagem com o caminho totalmente qualificado para o seu registo. Este exemplo especifica o espaço de nomes `samples` para evitar sobrepovoar a raiz do registo.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Para mais informações sobre a marcação com espaços de nome, consulte a secção de espaços de [nomes repositórios](container-registry-best-practices.md#repository-namespaces) das [melhores práticas para o Registo de Contentores Azure](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Enviar a imagem para o registo

Agora que marcou a imagem com o caminho totalmente qualificado para o seu registo privado, pode empurrá-la para o registo com pressão de [estivador:](https://docs.docker.com/engine/reference/commandline/push/)

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Extrair a imagem do registo

Utilize o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para retirar a imagem do seu registo:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Iniciar o contentor de Nginx

Use o comando de execução do [estivador](https://docs.docker.com/engine/reference/run/) para executar a imagem que tirou do seu registo:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Navegue `http://localhost:8080` para ver o recipiente de corrida.

Para parar e retirar `Control` + `C`o recipiente, prima .

## <a name="remove-the-image-optional"></a>Remover a imagem (opcional)

Se já não precisar da imagem Nginx, pode eliminá-la localmente com o comando [docker rmi.](https://docs.docker.com/engine/reference/commandline/rmi/)

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Para remover imagens do registo do seu contentor Azure, pode utilizar o comando Azure CLI [az repositório .](/cli/azure/acr/repository#az-acr-repository-delete) Por exemplo, o seguinte comando elimina o `samples/nginx:latest` manifesto referenciado pela etiqueta, quaisquer dados únicos de camada, e todas as outras etiquetas referentes ao manifesto.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe o básico, está pronto para começar a usar o seu registo! Por exemplo, coloque imagens de contentores do seu registo para:

* [Serviço de Kubernetes do Azure (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Instale opcionalmente a [extensão do Docker para código](https://code.visualstudio.com/docs/azure/docker) de estúdio visual e a extensão [da Conta Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com os registos de contentores Azure. Puxe e empurre as imagens para um registo de contentores Azure, ou execute tarefas ACR, todas dentro do Código do Estúdio Visual.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

---
title: Enviar por push & imagem do Docker pull
description: Enviar e extrair imagens do Docker para um registo privado de contentor do Azure com a CLI do Docker
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 6751a04c3c1bfe826334161704c20c1ba2e5a6d2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456366"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Enviar a sua primeira imagem para um registo privado de contentor Docker com a CLI do Docker

Os registos de contentores do Azure armazenam e gerem imagens de contentores do [Docker](https://hub.docker.com) privadas, de forma semelhante a como o [Docker Hub](https://hub.docker.com/) armazena imagens do Docker públicas. Você pode usar a [interface de linha de comando do Docker](https://docs.docker.com/engine/reference/commandline/cli/) (CLI do Docker) para operações de [logon](https://docs.docker.com/engine/reference/commandline/login/), [Push](https://docs.docker.com/engine/reference/commandline/push/), [pull](https://docs.docker.com/engine/reference/commandline/pull/)e outras no registro de contêiner.

Nas etapas a seguir, você baixa uma [imagem do Nginx](https://store.docker.com/images/nginx) oficial do registro do Hub do Docker público, marca-a para seu registro de contêiner do Azure privado, envia-a por push para o registro e, em seguida, a extrai do registro.

## <a name="prerequisites"></a>Pré-requisitos

* **Registo de contentores do Azure** - crie um registos de contentores na sua subscrição do Azure. Por exemplo, use o [portal do Azure](container-registry-get-started-portal.md) ou o [CLI do Azure](container-registry-get-started-azure-cli.md).
* **CLI do Docker** -você também deve ter o Docker instalado localmente. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [MacOS][docker-mac], [Windows][docker-windows]ou [Linux][docker-linux] .

## <a name="log-in-to-a-registry"></a>Iniciar sessão num registo

Há [várias maneiras de se autenticar](container-registry-authentication.md) no registro de contêiner privado. O método recomendado ao trabalhar em uma linha de comando é com o comando CLI do Azure [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Por exemplo, para fazer logon em um registro chamado *myregistry*:

```azurecli
az acr login --name myregistry
```

Você também pode fazer logon com o [logon do Docker](https://docs.docker.com/engine/reference/commandline/login/). Por exemplo, você pode ter [atribuído uma entidade de serviço](container-registry-authentication.md#service-principal) ao registro para um cenário de automação. Quando você executar o comando a seguir, forneça de forma interativa a appID (nome de usuário) e a senha da entidade de serviço quando solicitado. Para obter as práticas recomendadas para gerenciar as credenciais de logon, consulte a referência do comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) :

```
docker login myregistry.azurecr.io
```

Os dois comandos retornam `Login Succeeded` uma vez concluídos.

> [!TIP]
> Sempre especifique o nome totalmente qualificado do registro (todas as letras minúsculas) ao usar `docker login` e ao marcar imagens para enviar por push para o registro. Nos exemplos deste artigo, o nome totalmente qualificado é *myregistry.azurecr.Io*.

## <a name="pull-the-official-nginx-image"></a>Efetuar pull da imagem oficial do Nginx

Primeiro, receba a imagem Nginx pública em seu computador local.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Executar o contentor localmente

Execute o comando do [Docker execute](https://docs.docker.com/engine/reference/run/) a seguir para iniciar uma instância local do contêiner Nginx interativamente (`-it`) na porta 8080. O argumento `--rm` especifica que o contêiner deve ser removido quando você o interrompe.

```
docker run -it --rm -p 8080:80 nginx
```

Navegue até `http://localhost:8080` para exibir a página da Web padrão servida pelo Nginx no contêiner em execução. Você deverá ver uma página semelhante à seguinte:

![Nginx no computador local](./media/container-registry-get-started-docker-cli/nginx.png)

Como você iniciou o contêiner interativamente com `-it`, você pode ver a saída do servidor Nginx na linha de comando depois de navegar para ele no navegador.

Para parar e remover o contêiner, pressione `Control`+`C`.

## <a name="create-an-alias-of-the-image"></a>Criar um alias da imagem

Use a [marca do Docker](https://docs.docker.com/engine/reference/commandline/tag/) para criar um alias da imagem com o caminho totalmente qualificado para o registro. Este exemplo especifica o espaço de nomes `samples` para evitar sobrepovoar a raiz do registo.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Para obter mais informações sobre como marcar com namespaces, consulte a seção [namespaces do repositório](container-registry-best-practices.md#repository-namespaces) de [práticas recomendadas para o registro de contêiner do Azure](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Enviar a imagem por push para o registro

Agora que você marcou a imagem com o caminho totalmente qualificado para o registro privado, poderá enviá-la por push ao registro com o [push do Docker](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Efetuar pull da imagem do registro

Use o comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para efetuar pull da imagem do registro:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Iniciar o contêiner Nginx

Use o comando [Docker execute](https://docs.docker.com/engine/reference/run/) para executar a imagem que você recebeu do registro:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Navegue até `http://localhost:8080` para exibir o contêiner em execução.

Para parar e remover o contêiner, pressione `Control`+`C`.

## <a name="remove-the-image-optional"></a>Remover a imagem (opcional)

Se você não precisar mais da imagem Nginx, poderá excluí-la localmente com o comando [Docker RMI](https://docs.docker.com/engine/reference/commandline/rmi/) .

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Para remover imagens do registro de contêiner do Azure, você pode usar o comando CLI do Azure [AZ ACR Repository Delete](/cli/azure/acr/repository#az-acr-repository-delete). Por exemplo, o comando a seguir exclui o manifesto referenciado pela marca `samples/nginx:latest`, quaisquer dados de camada exclusivos e todas as outras marcas que fazem referência ao manifesto.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Passos seguintes

Agora que você conhece os conceitos básicos, está pronto para começar a usar o registro! Por exemplo, implante imagens de contêiner do registro para:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Opcionalmente, instale a [extensão do Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e a extensão de [conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com seus registros de contêiner do Azure. Efetuar pull e enviar imagens por push para um registro de contêiner do Azure ou executar tarefas de ACR, tudo no Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

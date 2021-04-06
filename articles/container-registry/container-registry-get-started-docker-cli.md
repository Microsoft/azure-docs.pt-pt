---
title: Empurre & puxar imagem do recipiente
description: Empurre e puxe as imagens do Docker para o seu registo privado de contentores em Azure usando o Docker CLI
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 83ef385313b035f5e5d7d993e7948725906c75a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99987761"
---
# <a name="push-your-first-image-to-your-azure-container-registry-using-the-docker-cli"></a>Empurre a sua primeira imagem para o seu registo de contentores Azure usando o Docker CLI

Um registo de contentores Azure armazena e gere imagens privadas de contentores e outros artefactos, semelhantes à forma como o [Docker Hub](https://hub.docker.com/) armazena imagens públicas de contentores Docker. Pode utilizar a [interface da linha de comando Do docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) para [login,](https://docs.docker.com/engine/reference/commandline/login/) [push,](https://docs.docker.com/engine/reference/commandline/push/) [pull](https://docs.docker.com/engine/reference/commandline/pull/)e outras operações de imagem de contentores no registo do seu contentor.

Nos passos seguintes, você descarrega uma imagem pública [Nginx,](https://store.docker.com/images/nginx)marque-a para o seu registo privado de contentores Azure, empurre-a para o seu registo e, em seguida, retire-a do registo.

## <a name="prerequisites"></a>Pré-requisitos

* **Registo de contentores do Azure** - crie um registos de contentores na sua subscrição do Azure. Por exemplo, utilize o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI** - Também deve ter Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="log-in-to-a-registry"></a>Iniciar sessão num registo

Existem [várias formas de autenticar](container-registry-authentication.md) o seu registo privado de contentores. O método recomendado ao trabalhar numa linha de comando é com o login Azure CLI [az acr](/cli/azure/acr#az-acr-login). Por exemplo, para iniciar sessão num registo denominado *miogrísta,* inicie sessão no CLI Azure e, em seguida, autense no seu registo:

```azurecli
az login
az acr login --name myregistry
```

Também pode iniciar sessão com [o login do Docker.](https://docs.docker.com/engine/reference/commandline/login/) Por exemplo, pode ter [atribuído um principal de serviço](container-registry-authentication.md#service-principal) ao seu registo para um cenário de automação. Quando executar o seguinte comando, forneça interativamente o appID principal do serviço (nome de utilizador) e a palavra-passe quando solicitado. Para obter as melhores práticas para gerir as credenciais de login, consulte a referência do comando de login do [estivador:](https://docs.docker.com/engine/reference/commandline/login/)

```
docker login myregistry.azurecr.io
```

Ambos os comandos regressam `Login Succeeded` uma vez concluídos.
> [!NOTE]
>* Você pode querer usar Código de Estúdio Visual com extensão Docker para um login mais rápido e conveniente.

> [!TIP]
> Especifique sempre o nome de registo totalmente qualificado (todos os minúsculos) quando utilizar `docker login` e quando marca imagens para empurrar para o seu registo. Nos exemplos deste artigo, o nome totalmente qualificado é *myregistry.azurecr.io*.

## <a name="pull-a-public-nginx-image"></a>Puxe uma imagem pública de Nginx

Primeiro, puxe uma imagem pública do Nginx para o seu computador local. Este exemplo retira uma imagem do Registo de Contentores da Microsoft.

```
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

## <a name="run-the-container-locally"></a>Executar o contentor localmente

Execute o seguinte comando de execução do [estivador](https://docs.docker.com/engine/reference/run/) para iniciar uma instância local do contentor Nginx interativamente `-it` () na porta 8080. O `--rm` argumento especifica que o recipiente deve ser removido quando o parar.

```
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Navegue para `http://localhost:8080` ver a página web padrão servida por Nginx no recipiente de execução. Deve ver uma página semelhante à seguinte:

![Nginx no computador local](./media/container-registry-get-started-docker-cli/nginx.png)

Como iniciou o contentor interativamente `-it` com, pode ver a saída do servidor Nginx na linha de comando depois de navegar para o mesmo no seu navegador.

Para parar e retirar o recipiente, prima `Control` + `C` .

## <a name="create-an-alias-of-the-image"></a>Criar um pseudónimo da imagem

Use [etiqueta de estivador](https://docs.docker.com/engine/reference/commandline/tag/) para criar um pseudónimo da imagem com o caminho totalmente qualificado para o seu registo. Este exemplo especifica o espaço de nomes `samples` para evitar sobrepovoar a raiz do registo.

```
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx
```

Para obter mais informações sobre a marcação com espaços de nome, consulte a secção de espaços de [nomes repositórios](container-registry-best-practices.md#repository-namespaces) das [melhores práticas para o Registo de Contentores Azure.](container-registry-best-practices.md)

## <a name="push-the-image-to-your-registry"></a>Enviar a imagem para o registo

Agora que marcou a imagem com o caminho totalmente qualificado para o seu registo privado, pode empurrá-la para o registo com [o estivador:](https://docs.docker.com/engine/reference/commandline/push/)

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Extrair a imagem do registo

Use o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para retirar a imagem do seu registo:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Iniciar o contentor de Nginx

Use o comando de execução do [estivador](https://docs.docker.com/engine/reference/run/) para executar a imagem que tirou do seu registo:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Procure para `http://localhost:8080` ver o recipiente de funcionamento.

Para parar e retirar o recipiente, prima `Control` + `C` .

## <a name="remove-the-image-optional"></a>Remova a imagem (opcional)

Se já não precisar da imagem Nginx, pode eliminá-la localmente com o comando [rmi do docker.](https://docs.docker.com/engine/reference/commandline/rmi/)

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Para remover imagens do seu registo de contentores Azure, pode utilizar o [repositório de comando Azure](/cli/azure/acr/repository#az-acr-repository-delete)CLI az acr . Por exemplo, o seguinte comando elimina o manifesto referenciado pela `samples/nginx:latest` etiqueta, quaisquer dados de camadas únicos e todas as outras tags que referenciam o manifesto.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe o básico, está pronto para começar a usar o seu registo! Por exemplo, desloque imagens de contentores do seu registo para:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Instale opcionalmente a [extensão do Docker para o Código do Estúdio Visual](https://code.visualstudio.com/docs/azure/docker) e a extensão da Conta [Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com os registos dos seus contentores Azure. Puxe e empurre as imagens para um registo de contentores Azure, ou execute tarefas ACR, tudo dentro do Código do Estúdio Visual.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

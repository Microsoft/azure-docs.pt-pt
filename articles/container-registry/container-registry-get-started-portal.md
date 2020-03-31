---
title: Quickstart - Criar registo no portal
description: Aprenda rapidamente a criar um registo privado de Docker no Registo de Contentores Azure com o portal Azure.
ms.topic: quickstart
ms.date: 03/03/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 6fe6358655f50ab783b4017efa8ee1db351cd018
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79409288"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Quickstart: Criar um registo de contentores privados utilizando o portal Azure

Um registo de contentores Azure é um registo privado do Docker em Azure onde pode armazenar e gerir imagens privadas de contentores Docker e artefactos relacionados. Neste guia de introdução, irá criar um registo de contentor com o portal do Azure. Em seguida, use os comandos do Docker para empurrar uma imagem de recipiente para o registo, e finalmente puxe e execute a imagem do seu registo.

Para iniciar sessão no registo para trabalhar com imagens de contentores, este arranque rápido requer que esteja a executar o Azure CLI (versão 2.0.55 ou posteriormente recomendado). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

Também tem de ter o Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Selecione **Criar um** > registo de**contentores** > **Container Registry**de recursos .

![Criar um registo de contentor no portal do Azure][qs-portal-01]

No separador **Basics,** introduza valores para **o grupo de recursos** e o nome do **registo**. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. Para este início rápido, crie um novo grupo de recursos na localização `West US` com o nome `myResourceGroup`, e para o **SKU**, selecione "Básico". 

![Criar registo de contentores no portal Azure][qs-portal-03]

Aceite os valores predefinidos para as definições restantes. Em seguida, selecione **Rever + criar**. Depois de rever as definições, selecione **Criar**.

Neste quickstart cria-se um registo *Básico,* que é uma opção otimizada para os desenvolvedores que aprendem sobre o Registo de Contentores Azure. Para mais informações sobre os níveis de serviço disponíveis, consulte o [registo de contentores SKUs][container-registry-skus].

Quando a mensagem **de implantação tiver sucesso,** selecione o registo do recipiente no portal. 

![Visão geral do registo de contentores no portal Azure][qs-portal-05]

Tome nota do valor do **servidor Iniciar sessão**. Usa este valor nos seguintes passos quando empurra saqueias e puxas imagens com o Docker.

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Antes de emitir e solicitar imagens de contentor, tem de iniciar sessão na instância do ACR. Abra uma concha de comando no seu sistema operativo e utilize o comando de [login az acr][az-acr-login] no Azure CLI. (Especifique apenas o nome do registo ao iniciar sessão. Não inclua o sufixo 'azurecr.io'.)

```azurecli
az acr login --name <acrName>
```

O comando devolve `Login Succeeded` depois de estar concluído. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Listar imagens de contentor

Para listar as imagens no seu registo, navegue para o seu registo no portal e `docker push`selecione **Repositórios,** em seguida, selecione o repositório com que criou .

Neste exemplo, selecionamos o repositório do mundo `v1` **olá,** e podemos ver a imagem marcada sob **tags**.

![Liste imagens de contentores no portal Azure][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os seus recursos, navegue para o grupo de recursos **myResourceGroup** no portal. Assim que o grupo de recursos for carregado clique no **grupo de recursos Delete** para remover o grupo de recursos, o registo do contentor e as imagens do recipiente armazenadas lá.

![Eliminar grupo de recursos no portal do Azure][qs-portal-08]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um Registo de Contentores Azure com o portal Azure, empurrou uma imagem de contentor, puxou e executou a imagem do registo. Continue aos tutoriais do Registo de Contentores Azure para uma olhada mais profunda na ACR.

> [!div class="nextstepaction"]
> [Tutoriais do Registo de Contentores de Azure][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login

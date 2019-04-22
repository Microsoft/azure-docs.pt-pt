---
title: Início rápido - criar um registo privado do Docker no Azure - portal do Azure
description: Aprenda rapidamente a criar um registo do contentor do Docker com o portal do Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: f41d51981c4da9ee089282da8b8d4cc5f37a4aed
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682989"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Início rápido: Criar um registo de contentor privado com o portal do Azure

Um registo de contentor do Azure é um registo do Docker privado no Azure, onde pode armazenar e gerir as imagens privadas de contentor do Docker. Neste guia de introdução, irá criar um registo de contentor com o portal do Azure. Em seguida, utilize os comandos do Docker para enviar por push uma imagem de contentor para o registro e finalmente extrair e executar a imagem a partir do registo.

Para iniciar sessão no registo para trabalhar com imagens de contentor, este início rápido requer que está a executar a CLI do Azure (versão 2.0.55 ou recomendados mais tarde). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

Também tem de ter o Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Selecione **Criar um recurso** > **Contentores** > **Container Registry**.

![Criar um registo de contentor no portal do Azure][qs-portal-01]

Introduza os valores para o **Nome do registo** e o **Grupo de recursos**. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. Para este início rápido, crie um novo grupo de recursos na localização `West US` com o nome `myResourceGroup`, e para o **SKU**, selecione "Básico". Selecione **Criar** para implementar a instância ACR.

![Criar registo de contentor no portal do Azure][qs-portal-03]

Neste início rápido de criar uma *básica* registo, o que é uma opção com otimização de custos para os desenvolvedores a aprender sobre o Azure Container Registry. Para obter detalhes sobre os escalões de serviço disponíveis, consulte [SKUs do registo de contentor][container-registry-skus].

Quando o **implementação concluída com êxito** mensagem for apresentada, selecione o registo de contentor no portal. 

![Descrição geral de registo de contentor no portal do Azure][qs-portal-05]

Tome nota do valor do **servidor de início de sessão**. Utilize este valor nos passos seguintes ao trabalhar com o registo com a CLI do Azure e Docker.

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Antes de emitir e solicitar imagens de contentor, tem de iniciar sessão na instância do ACR. Abra uma shell de comando no seu sistema operativo e utilize o [início de sessão az acr] [ az-acr-login] comando na CLI do Azure.

```azurecli
az acr login --name <acrName>
```

O comando devolve `Login Succeeded` depois de estar concluído. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Listar imagens de contentor

Para listar as imagens no seu registo, navegue para o registo no portal e selecione **repositórios**, em seguida, selecione o repositório que criou com `docker push`.

Neste exemplo, selecionamos o **hello-world** repositório e podemos ver a `v1`-imagem etiquetada sob **etiquetas**.

![Lista de imagens de contentor no portal do Azure][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os seus recursos, navegue para o **myResourceGroup** grupo de recursos no portal. Quando o grupo de recursos estiver carregado, clique em **eliminar grupo de recursos** remover o grupo de recursos, o registo de contentor e as imagens de contentor armazenadas nele.

![Eliminar grupo de recursos no portal do Azure][qs-portal-08]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um Azure Container Registry com o portal do Azure, enviou uma imagem de contentor e extraídos e executou a imagem do registo. Avance para os tutoriais de registo de contentor do Azure para uma análise mais profunda ACR.

> [!div class="nextstepaction"]
> [Tutoriais de registo de contentor do Azure][container-registry-tutorial-quick-task]

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

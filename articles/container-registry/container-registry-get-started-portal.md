---
title: Início rápido-criar um registro privado do Docker no Azure-portal do Azure
description: Aprenda rapidamente a criar um registo do contentor do Docker com o portal do Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: c9e8c7fe4d32a44e8c0831154f02eda1f82aaff3
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309475"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Início rápido: Criar um registro de contêiner privado usando o portal do Azure

Um registo de contentor do Azure é um registo do Docker privado no Azure, onde pode armazenar e gerir as imagens privadas de contentor do Docker. Neste guia de introdução, irá criar um registo de contentor com o portal do Azure. Em seguida, use os comandos do Docker para enviar uma imagem de contêiner para o registro e, por fim, efetuar pull e executar a imagem do registro.

Para fazer logon no registro para trabalhar com imagens de contêiner, este início rápido requer que você esteja executando a CLI do Azure (versão 2.0.55 ou posterior recomendada). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli].

Também tem de ter o Docker instalado localmente. O Docker fornece pacotes que configuram facilmente o Docker em qualquer [Mac][docker-mac], [Windows][docker-windows] [ou no]sistema Linux[Docker-Linux] .

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Selecione **Criar um recurso** > **Contentores** > **Container Registry**.

![Criar um registo de contentor no portal do Azure][qs-portal-01]

Introduza os valores para o **Nome do registo** e o **Grupo de recursos**. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. Para este início rápido, crie um novo grupo de recursos na localização `West US` com o nome `myResourceGroup`, e para o **SKU**, selecione "Básico". Selecione **Criar** para implementar a instância ACR.

![Criar registro de contêiner no portal do Azure][qs-portal-03]

Neste início rápido, você cria um registro *básico* , que é uma opção com otimização de custo para os desenvolvedores aprenderem sobre o registro de contêiner do Azure. Para obter detalhes sobre as camadas de serviço disponíveis, consulte [SKUs de registro de contêiner][container-registry-skus].

Quando a mensagem **implantação bem-sucedida** for exibida, selecione o registro de contêiner no Portal. 

![Visão geral do registro de contêiner no portal do Azure][qs-portal-05]

Anote o valor do **servidor de logon**. Você usa esse valor nas etapas a seguir enquanto trabalha com o registro com o CLI do Azure e o Docker.

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Antes de emitir e solicitar imagens de contentor, tem de iniciar sessão na instância do ACR. Abra um shell de comando em seu sistema operacional e use o comando [AZ ACR login][az-acr-login] no CLI do Azure.

```azurecli
az acr login --name <acrName>
```

O comando devolve `Login Succeeded` depois de estar concluído. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Listar imagens de contentor

Para listar as imagens no registro, navegue até o registro no portal **e selecione**repositórios e, em seguida, selecione o Repository que você `docker push`criou com.

Neste exemplo, selecionamos o repositório **Hello-World** e podemos ver a `v1`imagem marcada em **marcas**.

![Listar imagens de contêiner no portal do Azure][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar seus recursos, navegue até o grupo  de recursos MyResource Group no Portal. Depois que o grupo de recursos for carregado, clique em **excluir grupo de recursos** para remover o grupo de recursos, o registro de contêiner e as imagens de contêiner armazenadas nele.

![Eliminar grupo de recursos no portal do Azure][qs-portal-08]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um registro de contêiner do Azure com o portal do Azure, enviou por push uma imagem de contêiner e puxau e executou a imagem do registro. Continue nos tutoriais do registro de contêiner do Azure para obter uma análise mais profunda do ACR.

> [!div class="nextstepaction"]
> [Tutoriais do registro de contêiner do Azure][container-registry-tutorial-quick-task]

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

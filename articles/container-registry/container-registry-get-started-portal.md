---
title: Quickstart - Criar registo no portal
description: Aprenda rapidamente a criar um registo privado de contentores Azure utilizando o portal Azure.
ms.date: 08/04/2020
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 4618a83a43d9c27f44432d5d346fdb053cee7054
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773590"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Quickstart: Criar um registo de contentores Azure utilizando o portal Azure

Um registo de contentores Azure é um registo privado do Docker em Azure, onde pode armazenar e gerir imagens privadas de contentores Docker e artefactos relacionados. Neste guia de introdução, irá criar um registo de contentor com o portal do Azure. Em seguida, use os comandos do Docker para empurrar uma imagem do contentor para o registo, e finalmente puxe e execute a imagem do seu registo.

Para iniciar sessão no registo para trabalhar com imagens de contentores, este arranque rápido requer que esteja a executar o Azure CLI (versão 2.0.55 ou posteriormente recomendado). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

Também tem de ter o Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-container-registry"></a>Criar um registo de contentor

**Selecione Criar um** registo  >  **de contentores de**  >  recursos.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Navegue para o registo de contentores no portal":::

No **separador Básicos,** introduza valores para **o grupo de Recursos** e nome do **registo.** O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. Para este início rápido, crie um novo grupo de recursos na localização `West US` com o nome `myResourceGroup`, e para o **SKU**, selecione "Básico".

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Criar registo de contentores no portal":::

Aceite valores predefinidos para as definições restantes. Em seguida, **selecione Rever + criar**. Depois de rever as definições, selecione **Criar**.

Neste quickstart você cria um registo *Básico,* que é uma opção otimizada para custos para desenvolvedores aprendendo sobre o Registo de Contentores Azure. Para obter mais informações sobre os níveis de serviço disponíveis (SKUs), consulte [os níveis de serviço de registo de contentores][container-registry-skus].

Quando a mensagem **de implementação tiver sido bem sucedida,** selecione o registo do contentor no portal. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Visão geral do registo de contentores no portal":::

Tome nota do nome do registo e do valor do **servidor Login**. Usa estes valores nos seguintes passos quando empurra e puxa imagens com o Docker.

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Antes de empurrar e puxar imagens do contentor, deve iniciar sessão na instância de registo. [Inscreva-se no CLI Azure][get-started-with-azure-cli] na sua máquina local e, em seguida, faça o comando [de login az acr.][az-acr-login] Especifique apenas o nome do registo ao iniciar sessão com o Azure CLI. Não utilize o nome do servidor de login, que inclui um sufixo de domínio como `azurecr.io` .

```azurecli
az acr login --name <registry-name>
```

Exemplo:

```azurecli
az acr login --name mycontainerregistry
```

O comando devolve `Login Succeeded` depois de estar concluído. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Listar imagens de contentor

Para listar as imagens no seu registo, navegue para o seu registo no portal e selecione **Repositórios,** selecione o repositório  **hello-world** com o que `docker push` criou.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Listar imagens de contentores no portal":::

Ao selecionar o repositório **hello-world,** você vê a `v1` imagem marcada em **Tags**.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os seus recursos, navegue para o grupo de recursos **myResourceGroup** no portal. Assim que o grupo de recursos estiver carregado, clique no **grupo de recursos Delete** para remover o grupo de recursos, o registo do contentor e as imagens do contentor aí armazenadas.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Eliminar grupo de recursos no portal":::


## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um Registo de Contentores Azure com o portal Azure, empurrou uma imagem de contentor, puxou e executou a imagem a partir do registo. Continue os tutoriais do Registo do Contentor de Azure para uma olhada mais profunda no ACR.

> [!div class="nextstepaction"]
> [Tutoriais do Registo de Contentores de Azure][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Tutoriais de tarefas de registo de contentores Azure][container-registry-tutorial-quick-task]

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
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az_acr_login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md

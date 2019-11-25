---
title: Quickstart - Create registry in portal
description: Quickly learn to create a private Docker registry in Azure Container Registry with the Azure portal.
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c5e6de0fdb7d0e46fa9be28483fd894289baf1a3
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455213"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Quickstart: Create a private container registry using the Azure portal

Um registo de contentor do Azure é um registo do Docker privado no Azure, onde pode armazenar e gerir as imagens privadas de contentor do Docker. Neste guia de introdução, irá criar um registo de contentor com o portal do Azure. Then, use Docker commands to push a container image into the registry, and finally pull and run the image from your registry.

To log in to the registry to work with container images, this quickstart requires that you are running the Azure CLI (version 2.0.55 or later recommended). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli].

Também tem de ter o Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-container-registry"></a>Create a container registry (Criar um registo de contentores)

Selecione **Criar um recurso** > **Contentores** > **Container Registry**.

![Criar um registo de contentor no portal do Azure][qs-portal-01]

Introduza os valores para o **Nome do registo** e o **Grupo de recursos**. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. Para este início rápido, crie um novo grupo de recursos na localização `West US` com o nome `myResourceGroup`, e para o **SKU**, selecione "Básico". Selecione **Criar** para implementar a instância ACR.

![Create container registry in the Azure portal][qs-portal-03]

In this quickstart you create a *Basic* registry, which is a cost-optimized option for developers learning about Azure Container Registry. For details on available service tiers, see [Container registry SKUs][container-registry-skus].

When the **Deployment succeeded** message appears, select the container registry in the portal. 

![Container registry Overview in the Azure portal][qs-portal-05]

Take note of the value of the **Login server**. You use this value in the following steps while working with your registry with the Azure CLI and Docker.

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Antes de emitir e solicitar imagens de contentor, tem de iniciar sessão na instância do ACR. Open a command shell in your operating system, and use the [az acr login][az-acr-login] command in the Azure CLI.

```azurecli
az acr login --name <acrName>
```

O comando devolve `Login Succeeded` depois de estar concluído. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Listar imagens de contentor

To list the images in your registry, navigate to your registry in the portal and select **Repositories**, then select the repository you created with `docker push`.

In this example, we select the **hello-world** repository, and we can see the `v1`-tagged image under **TAGS**.

![List container images in the Azure portal][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar recursos

To clean up your resources, navigate to the **myResourceGroup** resource group in the portal. Once the resource group is loaded click on **Delete resource group** to remove the resource group, the container registry, and the container images stored there.

![Eliminar grupo de recursos no portal do Azure][qs-portal-08]

## <a name="next-steps"></a>Passos seguintes

In this quickstart, you created an Azure Container Registry with the Azure portal, pushed a container image, and pulled and ran the image from the registry. Continue to the Azure Container Registry tutorials for a deeper look at ACR.

> [!div class="nextstepaction"]
> [Azure Container Registry tutorials][container-registry-tutorial-quick-task]

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

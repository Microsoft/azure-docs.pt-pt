---
title: Quickstart - Criar registo geo-replicado - Modelo de Gestor de Recursos Azure
description: Saiba como criar um registo de contentores Azure geo-replicado utilizando um modelo de Gestor de Recursos Azure.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 05/26/2020
ms.openlocfilehash: 2cfce37ff63a8321f40843ced2a7b786bcfc013e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88649624"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>Quickstart: Criar um registo de contentores geo-replicado utilizando um modelo ARM

Este quickstart mostra como criar uma instância de registo de contentores Azure usando um modelo de Gestor de Recursos Azure (modelo ARM). O modelo configura um registo [geo-replicado,](container-registry-geo-replication.md) que sincroniza automaticamente o conteúdo do registo em mais de uma região de Azure. A geo-replicação permite o acesso próximo da rede a imagens de implementações regionais, proporcionando ao mesmo tempo uma experiência de gestão única. É uma característica do nível de serviço de registo [Premium.](container-registry-skus.md)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). O modelo estabelece um registo e uma réplica regional adicional.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

Os seguintes recursos são definidos no modelo:

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)**: criar um registo de contentores Azure
* **[Microsoft.ContainerRegistry/registries/replicações](/azure/templates/microsoft.containerregistry/registries/replications)**: criar uma réplica de registo de contentores

Mais amostras de modelo de registo de contentores Azure podem ser encontradas na [galeria de modelos quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

 1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 2. Selecione ou introduza os seguintes valores.

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **Criar novo,** insira um nome único para o grupo de recursos e, em seguida, selecione **OK**.
    * **Localização**: selecione uma localização para o grupo de recursos. Exemplo: **Central US**.
    * **Nome Acr**: aceite o nome gerado para o registo ou introduza um nome. Deve ser globalmente único.
    * **Localização**: aceite a localização gerada para a réplica da casa do registo, ou entre num local como **o Central US**. 
    * **Localização da réplica do Acr**: introduza um local para a réplica do registo, utilizando o nome curto da região. Deve ser diferente do local do registo domiciliário. Exemplo: **westeurope**.
    * **Concordo com os termos e condições acima indicados:** Selecione.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Propriedades do modelo":::

 3. Se aceitar os termos e condições, selecione **Comprar**. Após a criação do registo com sucesso, recebe uma notificação:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Propriedades do modelo":::

 O portal do Azure é utilizado para implementar o modelo. Além do portal Azure, pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Utilize o portal Azure ou uma ferramenta como o Azure CLI para rever as propriedades do registo do contentor.

1. No portal, procure registos de contentores e selecione o registo de contentores que criou.

1. Na página **'Vista Geral',** note o **servidor de Login** do registo. Use este URI quando usar o Docker para marcar e empurrar imagens para o seu registo. Para obter informações, consulte [a sua primeira imagem utilizando o Docker CLI](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Propriedades do modelo":::

1. Na página **replicações,** confirme as localizações da réplica doméstica e a réplica adicionada através do modelo. Se desejar, adicione mais réplicas nesta página.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Propriedades do modelo":::

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar deles, elimine o grupo de recursos, o registo e a réplica do registo. Para tal, vá ao portal Azure, selecione o grupo de recursos que contém o registo e, em seguida, **selecione Delete resource group**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um registo de contentores Azure com um modelo ARM e configura uma réplica de registo noutro local. Continue os tutoriais do Registo do Contentor de Azure para uma olhada mais profunda no ACR.

> [!div class="nextstepaction"]
> [Tutoriais do Registo de Contentores de Azure](container-registry-tutorial-prepare-registry.md)

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

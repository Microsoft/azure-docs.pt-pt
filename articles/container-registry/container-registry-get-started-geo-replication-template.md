---
title: Quickstart - Criar registo geo-replicado - Modelo de Gestor de Recursos
description: Aprenda a criar um registo de contentores Azure geo-replicado utilizando um modelo de Gestor de Recursos Azure.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 05/26/2020
ms.openlocfilehash: 1345cc67137a4fb3b6d54443e71f1a8813b5b06c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122815"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-a-resource-manager-template"></a>Quickstart: Criar um registo de contentores geo-replicado utilizando um modelo de Gestor de Recursos

Este quickstart mostra como criar uma instância de registo de contentores Azure usando um modelo de Gestor de Recursos Azure. O modelo estabelece um registo [geo-replicado,](container-registry-geo-replication.md) que sincroniza automaticamente o conteúdo do registo em mais de uma região do Azure. A geo-replicação permite o acesso próximo da rede a imagens de destacamentos regionais, proporcionando uma única experiência de gestão. É uma característica do nível de serviço de registo [Premium.](container-registry-skus.md) 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Nenhum.

## <a name="create-a-geo-replicated-registry"></a>Criar um registo geo-replicado

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). O modelo estabelece um registo e uma réplica regional adicional.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json" range="1-81" highlight="45-74" :::

Os seguintes recursos são definidos no modelo:

* **[Microsoft.ContainerRegistry/registros](/azure/templates/microsoft.containerregistry/registries)**: criar um registo de contentores Azure
* **[Microsoft.ContainerRegistry/registros/replications](/azure/templates/microsoft.containerregistry/registries/replications)**: criar uma réplica de registo de contentores

Mais amostras de modelo de registo de contentores azure podem ser encontradas na galeria do [modelo de arranque rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Implementar o modelo

 1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 2. Selecione ou introduza os seguintes valores.

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **Criar novo,** introduza um nome único para o grupo de recursos e, em seguida, selecione **OK**.
    * **Localização**: selecione uma localização para o grupo de recursos. Exemplo: **Centro dos EUA**.
    * **Nome Acr**: aceite o nome gerado para o registo ou introduza um nome. Deve ser globalmente único.
    * **Localização**: aceitar a localização gerada para a réplica do registo ou introduzir um local como o **Centro dos EUA**. 
    * Localização de **réplica Acr**: insira um local para a réplica do registo, utilizando o nome curto da região. Deve ser diferente do local do registo domiciliário. Exemplo: **Europa ocidental**.
    * **Concordo com os termos e condições acima indicados:** Selecione.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Propriedades do modelo":::

 3. Se aceitar os termos e condições, selecione **Comprar**. Depois de o registo ter sido criado com sucesso, recebe uma notificação:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Notificação do portal":::

 O portal Azure é usado para implantar o modelo. Além do portal Azure, pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implantação](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Rever os recursos implantados

Utilize o portal Azure ou uma ferramenta como o Azure CLI para rever as propriedades do registo do contentor.

1. No portal, procure registos de contentores e selecione o registo de contentores que criou.

1. Na página **'Visão Geral',** note o **servidor de Login** do registo. Use este URI quando utilizar o Docker para etiquetar e empurrar imagens para o seu registo. Para obter informações, consulte [Empurre a sua primeira imagem utilizando o Docker CLI](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Visão geral do registo":::

1. Na página **de Replicações,** confirme as localizações da réplica da casa e a réplica adicionada através do modelo. Se desejar, adicione mais réplicas nesta página.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Replicações de registo":::

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar deles, elimine o grupo de recursos, o registo e a réplica do registo. Para tal, vá ao portal Azure, selecione o grupo de recursos que contém o registo e, em seguida, **selecione eliminar o grupo de recursos**.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, criou um Registo de Contentores Azure com um modelo de Gestor de Recursos, e configurauma réplica de registo noutro local. Continue aos tutoriais do Registo de Contentores Azure para uma olhada mais profunda na ACR.

> [!div class="nextstepaction"]
> [Tutoriais do Registo de Contentores de Azure](container-registry-tutorial-prepare-registry.md)

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Crie e implante o seu primeiro modelo de Gestor de Recursos Azure](/azure/azure-resource-manager/templates/template-tutorial-create-first-template.md)
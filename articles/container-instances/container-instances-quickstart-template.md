---
title: Quickstart - Criar uma instância de contentor - Modelo de Gestor de Recursos Azure
description: Neste quickstart, você usa um modelo de Gestor de Recursos Azure para implementar rapidamente uma aplicação web contentorizada que funciona em uma instância isolada do recipiente Azure.
services: azure-resource-manager
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-js
ms.date: 04/30/2020
ms.openlocfilehash: 621e3e1cef39e34656c094a39d218d5d5866fa26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91309086"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-an-arm-template"></a>Quickstart: Implementar uma instância de contentor em Azure usando um modelo ARM

Utilize instâncias de contentores Azure para executar recipientes Docker sem servidor em Azure com simplicidade e velocidade. Implemente uma aplicação para uma instância de contentor a pedido quando não precisar de uma plataforma completa de orquestração de contentores como o Serviço Azure Kubernetes. Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure (modelo ARM) para implantar um recipiente estivador isolado e disponibilizar a sua aplicação web com um endereço IP público.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-aci-linuxcontainer-public-ip/).

:::code language="json" source="~/quickstart-templates/101-aci-linuxcontainer-public-ip/azuredeploy.json":::

O seguinte recurso é definido no modelo:

* **[Microsoft.ContainerInstance/containerGroups:](/azure/templates/microsoft.containerinstance/containergroups)** criar um grupo de contentores Azure. Este modelo define um grupo composto por uma única instância de contentor.

Mais amostras de modelo de casos de recipientes Azure podem ser encontradas na [galeria de modelos quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerinstance&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

 1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um registo e uma réplica em outro local.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

 2. Selecione ou introduza os seguintes valores.

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **Criar novo,** insira um nome único para o grupo de recursos e, em seguida, selecione **OK**.
    * **Localização**: selecione uma localização para o grupo de recursos. Exemplo: **Central US**.
    * **Nome:** aceite o nome gerado, por exemplo, ou insira um nome.
    * **Imagem:** aceite o nome de imagem padrão. Esta amostra de imagem Linux embala uma pequena aplicação web escrita em Node.js que serve uma página html estática. 

    Aceite valores predefinidos para as propriedades restantes.

    Reveja os termos e condições. Se concordar, **selecione concordo com os termos e condições acima indicados.**

    ![Propriedades do modelo](media/container-instances-quickstart-template/template-properties.png)

 3. Após a criação do caso com sucesso, recebe uma notificação:

    ![Notificação do portal](media/container-instances-quickstart-template/deployment-notification.png)

 O portal do Azure é utilizado para implementar o modelo. Além do portal Azure, pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Utilize o portal Azure ou uma ferramenta como o [Azure CLI](container-instances-quickstart.md) para rever as propriedades da instância do recipiente.

1. No portal, procure por Instâncias de Contentores e selecione a instância do recipiente que criou.

1. Na página **'Visão Geral',** note o **Estado** da ocorrência e o seu **endereço IP**.

    ![Descrição geral da instância](media/container-instances-quickstart-template/aci-overview.png)

2. Uma vez que o seu estado esteja *funcionando,* navegue para o endereço IP no seu browser. 

    ![Aplicação implementada com o Azure Container Instances vista no browser](media/container-instances-quickstart-template/view-application-running-in-an-azure-container-instance.png)

### <a name="view-container-logs"></a>Ver registos de contentor

Ver os registos de uma instância de contentor é útil quando estiver a resolver problemas no contentor ou na aplicação nele executada.

Para visualizar os registos do contentor, em **Definições,** selecione   >  **Registos de Contentores**. Deverá ver o pedido HTTP GET gerado quando visualizou a aplicação no seu browser.

![Registos de contentor no portal do Azure](media/container-instances-quickstart-template/aci-logs.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar o recipiente, na página **'Visão Geral'** para a instância do recipiente, selecione **Delete**. Quando lhe for perguntado, confirme a eliminação.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma instância de contentores Azure a partir de uma imagem pública da Microsoft. Se quiser criar uma imagem de contentor e implementá-la partir de um registo de contentor privado do Azure, prossiga para o tutorial do Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial: Criar uma imagem de recipiente para implantação em Instâncias de Contentores Azure](./container-instances-tutorial-prepare-app.md)

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
---
title: Quickstart - Criar exemplo de gestão API Azure usando o modelo ARM
description: Aprenda a criar uma instância de Gestão API Azure no nível de Desenvolvedor utilizando um modelo de Gestor de Recursos Azure (modelo ARM).
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: 1610c51b613712c06410247e2eb673a50a9988b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792247"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>Quickstart: Criar uma nova instância de serviço de gestão API Azure usando um modelo ARM

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma instância de serviço AZure API Management (APIM). A APIM ajuda as organizações a publicar APIs a desenvolvedores externos, parceiros e internos para desbloquear o potencial dos seus dados e serviços. A Gestão de API fornece as competências essenciais para garantir um programa de API bem-sucedido através do envolvimento dos programadores, das informações de negócio, da análise, da segurança e da proteção. A APIM permite-lhe criar e gerir os modernos gateways API para serviços de backend existentes hospedados em qualquer lugar. Para obter mais informações, consulte [Descrição Geral](api-management-key-concepts.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azure-api-management-create/).

:::code language="json" source="~/quickstart-templates/101-azure-api-management-create/azuredeploy.json":::

O seguinte recurso é definido no modelo:

- **[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service)**

Mais amostras de modelo de gestão AZure API podem ser encontradas em [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma instância de serviço de Gestão API com um nome gerado automaticamente.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

    Neste exemplo, o caso está configurado no nível de Desenvolvedor, uma opção económica para avaliar a Gestão API da Azure. Este nível não é para uso de produção. Para obter mais informações sobre o dimensionamento dos escalões da Gestão de API, consulte [Atualização de versão do software e dimensionamento](upgrade-and-scale.md).

1. Selecione ou introduza os seguintes valores.
    - **Subscrição**: selecione uma subscrição do Azure.
    - **Grupo de recursos**: selecione **Criar novo,** insira um nome único para o grupo de recursos e, em seguida, selecione **OK**.
    - **Região**: selecione uma localização para o grupo de recursos. Exemplo: **Central US**.
    - **Email do Editor**: insira um endereço de e-mail para receber notificações.
    - **Nome do editor**: insira um nome que escolher para a editora API.
    - **Sku**: aceite o valor padrão do **Desenvolvedor.**
    - **Sku Count**: aceite o valor predefinido.
    - **Localização**: aceite a localização gerada para o serviço de Gestão da API.

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="Propriedades do modelo de gestão da API":::

1. Selecione **Rever + Criar** e, em seguida, rever os termos e condições. Se estiver de acordo, **selecione Criar**.

    > [!TIP]
    >  Pode levar entre 30 a 40 minutos para criar e ativar um serviço de Gestão API no nível de Desenvolvedor.

1. Após a criação do caso com sucesso, recebe uma notificação:

    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="Notificação de implantação":::

 O portal do Azure é utilizado para implementar o modelo. Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Utilize o portal Azure para verificar os recursos implantados ou utilize ferramentas como o Azure CLI ou a Azure PowerShell para listar os recursos implantados.

1. No [portal Azure,](https://portal.azure.com)procure e selecione **os serviços de Gestão da API** e selecione a instância de serviço que criou.
1. Reveja as propriedades do seu serviço na página **'Vista Geral'.**

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="Página geral do serviço":::

Quando o seu serviço de gestão API estiver online, está pronto para usá-lo. Comece com o tutorial para [importar e publicar](import-and-publish.md) a sua primeira API.

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia continuar a trabalhar com tutoriais subsequentes, talvez queira deixar a instância de Gestão da API no lugar. Quando já não for necessário, elimine o grupo de recursos, que elimina os recursos do grupo de recursos.

1. No [portal Azure,](https://portal.azure.com)procure e selecione **grupos de Recursos.** Também pode selecionar **grupos de recursos** na página **inicial.**
1. Na página **dos grupos de recursos,** selecione o seu grupo de recursos.
1. Na página do grupo de recursos, selecione **Eliminar o grupo de recursos**.

    :::image type="content" source="media/quickstart-arm-template/delete-resource-group.png" alt-text="Eliminar grupo de recursos":::
1. Digite o nome do seu grupo de recursos e, em seguida, **selecione Delete**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Importe e publique a sua primeira API](import-and-publish.md)

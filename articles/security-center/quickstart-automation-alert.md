---
title: Crie uma automatização de segurança para alertas de segurança específicos utilizando um modelo Azure Resource Manager (ARM)
description: Saiba como criar uma automatização do Azure Security Center para desencadear uma aplicação lógica, que será desencadeada por alertas específicos do Centro de Segurança utilizando um modelo de Gestor de Recursos Azure (ARM)
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: ecef318978194ac3773c54e2d9c960781086de65
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691810"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-azure-resource-manager-template-arm-template"></a>Quickstart: Criar uma resposta automática a um alerta de segurança específico utilizando um modelo de Gestor de Recursos Azure (modelo ARM)

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma automatização de fluxo de trabalho que desencadeie uma aplicação lógica quando alertas de segurança específicos são recebidos pelo Azure Security Center.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)


## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para obter uma lista das funções e permissões necessárias para trabalhar com a funcionalidade de automatização de fluxo de trabalho do Azure Security Center, consulte [a automatização do fluxo de trabalho](workflow-automation.md).


## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/).

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>Recursos relevantes

- [**Microsoft.Security/automations**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftsecurity-resource-provider): A automatização que irá desencadear a aplicação lógica, ao receber um alerta do Azure Security Center que contém uma cadeia específica.
- [**Microsoft.Logic/workflows**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftlogic-resource-provider): Uma aplicação lógica despoletável vazia.

Para outros modelos de arranque rápido do Security Center, consulte estes [modelos de contribuições comunitárias.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security)

## <a name="deploy-the-template"></a>Implementar o modelo

  - **PowerShell:**
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **CLI:**
    ```azurecli-interactive
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **Portal:**

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

    Para obter mais informações sobre esta opção de implementação, consulte [utilize um botão de implementação para implementar modelos do repositório GitHub](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-to-azure-button).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Utilize o portal Azure para verificar se a automatização do fluxo de trabalho foi implantada. 

1. A partir [do portal Azure,](https://portal.azure.com) **Abrir Centro de Segurança.**
1. A partir da barra de menu superior, selecione o ícone do filtro e selecione a subscrição específica na qual implementou a nova automatização do fluxo de trabalho.
1. Da barra lateral do Security Center, abra **a automatização do fluxo de trabalho** e verifique a sua nova automatização.

    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="Texto alt imagem." lightbox="./media/quickstart-automation-alert/validating-template-run.png":::

    >[!TIP]
    > Se tiver muitas automatizações de fluxo de trabalho na sua subscrição, utilize o filtro por opção **nome.** 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine a automatização do fluxo de trabalho utilizando o portal Azure.

1. A partir [do portal Azure,](https://portal.azure.com) **Abrir Centro de Segurança.**
1. A partir da barra de menu superior, selecione o ícone do filtro e selecione a subscrição específica na qual implementou a nova automatização do fluxo de trabalho.
1. Da barra lateral do Security Center, abra **a automatização do fluxo de trabalho** e encontre a automatização a ser eliminada.

    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="Texto alt imagem." lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::

1. Selecione a caixa de verificação para que o item seja eliminado.
1. A partir da barra de ferramentas, selecione eliminar.


## <a name="next-steps"></a>Passos seguintes

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Crie e implemente o seu primeiro modelo ARM](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
---
title: Crie uma automatização de segurança para alertas de segurança específicos utilizando um modelo de Gestor de Recursos Azure (modelo ARM)
description: Aprenda a criar uma automatização do Azure Security Center para desencadear uma aplicação lógica, que será desencadeada por alertas específicos do Security Center utilizando um modelo de Gestor de Recursos Azure (modelo ARM).
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: e53a5c4840cee0651090bec0b68cc53e13102299
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705494"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-arm-template"></a>Quickstart: Criar uma resposta automática a um alerta de segurança específico usando um modelo ARM

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

- [**Microsoft.Security/automations**](/azure/templates/microsoft.security/automations): A automatização que irá desencadear a aplicação lógica, ao receber um alerta do Azure Security Center que contém uma cadeia específica.
- [**Microsoft.Logic/workflows**](/azure/templates/microsoft.logic/workflows): Uma aplicação lógica despoletável vazia.

Para outros modelos de arranque rápido do Security Center, consulte estes [modelos de contribuições comunitárias.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Implementar o modelo

- **PowerShell:**

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **CLI:**

  ```azurecli-interactive
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **Portal:**

  [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

  Para obter mais informações sobre esta opção de implementação, consulte [utilize um botão de implementação para implementar modelos do repositório GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Utilize o portal Azure para verificar se a automatização do fluxo de trabalho foi implantada.

1. A partir do [portal Azure,](https://portal.azure.com) **Abrir Centro de Segurança.**
1. A partir da barra de menu superior, selecione o ícone do filtro e selecione a subscrição específica na qual implementou a nova automatização do fluxo de trabalho.
1. Da barra lateral do Security Center, abra **a automatização do fluxo de trabalho** e verifique a sua nova automatização.
    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="Lista de automações configuradas" lightbox="./media/quickstart-automation-alert/validating-template-run.png":::
    >[!TIP]
    > Se tiver muitas automatizações de fluxo de trabalho na sua subscrição, utilize o filtro por opção **nome.**

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine a automatização do fluxo de trabalho utilizando o portal Azure.

1. A partir do [portal Azure,](https://portal.azure.com) **Abrir Centro de Segurança.**
1. A partir da barra de menu superior, selecione o ícone do filtro e selecione a subscrição específica na qual implementou a nova automatização do fluxo de trabalho.
1. Da barra lateral do Security Center, abra **a automatização do fluxo de trabalho** e encontre a automatização a ser eliminada.
    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="Passos para a remoção de uma automatização do fluxo de trabalho" lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::
1. Selecione a caixa de verificação para que o item seja eliminado.
1. A partir da barra de ferramentas, **selecione Delete**.

## <a name="next-steps"></a>Passos seguintes

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

---
title: Implementar aplicações lógicas com modelos do Azure Resource Manager - Azure Logic Apps
description: Implementar aplicações lógicas com modelos Azure Resource Manager
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: 7543859a916de97d471db2894887e640db51dfc2
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893429"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>Implementar aplicações lógicas com modelos Azure Resource Manager

Depois de criar um modelo do Azure Resource Manager para implementar a aplicação lógica, pode implementar o modelo nas seguintes formas:

* [Portal do Azure](#portal)
* [Azure PowerShell](#powershell)
* [CLI do Azure](#cli)
* [API de REST do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Pipelines do Azure de DevOps do Azure](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Implementar através do portal do Azure

Para implementar automaticamente um modelo de aplicação lógica no Azure, pode escolher as seguintes **implementar no Azure** botão, que inicia sessão no portal do Azure e pede-lhe informações sobre a sua aplicação lógica. Pode, em seguida, faça as alterações necessárias para o modelo de aplicação lógica ou parâmetros.

[![Deploy para o Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Por exemplo, a lhe for pedido para obter estas informações depois de iniciar sessão portal do Azure:

* Nome da subscrição do Azure
* Grupo de recursos que pretende utilizar
* Localização da aplicação lógica
* O nome para a aplicação lógica
* Um teste de URI
* Aceitação dos termos especificados e condições

Para obter mais informações, consulte [implementar recursos com modelos Azure Resource Manager e o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="authorize-oauth-connections"></a>Autorizar ligações de OAuth

Após a implementação, a aplicação lógica funciona ponto a ponto com parâmetros válidos. No entanto, tem de autorizar ainda ligações de OAuth para gerar um token de acesso válido. Para as Implantações automatizadas, pode usar um script que dá consentimento a cada conexão de OAuth, como esta [script de exemplo no projeto do GitHub LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth). Pode também autorizar OAuth ligações através do portal do Azure ou no Visual Studio ao abrir a aplicação lógica no Designer de aplicações lógicas.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Implementar com o Azure PowerShell

Para implementar um específico *grupo de recursos do Azure*, utilize este comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

Para implementar para uma subscrição do Azure específica, utilize este comando:

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Implementar recursos com modelos do Resource Manager e do Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)
* [`New-AzDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Implementar com a CLI do Azure

Para implementar um específico *grupo de recursos do Azure*, utilize este comando:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para implementar para uma subscrição do Azure específica, utilize este comando:

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para obter mais informações, veja estes tópicos: 

* [Implementar recursos com modelos do Resource Manager e do CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Implementar com o DevOps do Azure

Para implementar modelos de aplicações lógicas e gerir ambientes, as equipes normalmente usam uma ferramenta como [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) na [do Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Pipelines do Azure fornece um [tarefas de implementação de grupo de recursos do Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) que pode adicionar a qualquer compilação ou pipeline de versões.
Para obter autorização implementar e gerar o pipeline de lançamento, também tem um Azure Active Directory (AD) [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md). Saiba mais sobre [utilizar principais de serviço com Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

Seguem-se passos de alto nível gerais para a utilização de Pipelines do Azure:

1. Pipelines do Azure, crie um pipeline vazio.

1. Escolha os recursos que necessários para o pipeline, como o seu modelo de aplicação lógica e os ficheiros de parâmetros de modelo, o que gerar manualmente ou como parte do processo de compilação.

1. Para a sua tarefa de agente, localize e adicione a **implementação de grupo de recursos do Azure** tarefas.

   ![Adicionar tarefa "Implementação de grupo de recursos do Azure"](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. Configurar com um [principal de serviço](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

1. Adicione referências a seus modelo da aplicação lógica e os ficheiros de parâmetros de modelo.

1. Continue a criar passos no processo de liberação para qualquer outro ambiente, teste automatizado ou aprovadores conforme necessário.

## <a name="get-support"></a>Obter suporte

Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Monitorizar aplicações lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md)

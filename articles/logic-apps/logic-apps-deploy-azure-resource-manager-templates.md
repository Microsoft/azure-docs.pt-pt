---
title: Implementar modelos de aplicação lógica
description: Saiba como implementar modelos de Gestor de Recursos Azure criados para aplicações lógicas do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 95bfa7b1918da09e4f5913eeb2b57c290b093efe
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78162386"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Implementar modelos de Gestor de Recursos Azure para aplicações lógicas azure

Depois de criar um modelo de Gestor de Recursos Azure para a sua aplicação lógica, pode implementar o seu modelo desta forma:

* [Portal do Azure](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [CLI do Azure](#cli)
* [API REST API, gestor de recursos azure](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Implementar através do portal Azure

Para implementar automaticamente um modelo de aplicação lógica para o Azure, pode escolher o seguinte **botão Deploy para Azure,** que o inscreve no portal Azure e o solicita informações sobre a sua aplicação lógica. Em seguida, pode efetuar quaisquer alterações necessárias ao modelo ou parâmetros da aplicação lógica.

[![Implementar no Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Por exemplo, é solicitado para obter as seguintes informações depois de iniciar sessão no portal Azure:

* Nome de subscrição azure
* Grupo de recursos que pretende utilizar
* Localização da aplicação lógica
* O nome para a aplicação lógica
* Um uri de teste
* Aceitação dos termos e condições especificados

Para mais informações, consulte estes tópicos:

* [Visão geral: Implementação automatizada para aplicações lógicas com modelos de Gestor de Recursos Azure](logic-apps-azure-resource-manager-templates-overview.md)
* [Implementar recursos com modelos de Gestor de Recursos Azure e o portal Azure](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Implementar com o Visual Studio

Para implementar um modelo de aplicação lógica a partir de um projeto do Grupo de Recursos Azure que criou através do Visual Studio, siga estes [passos para implementar manualmente a sua aplicação lógica](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) para o Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Implementar com o Azure PowerShell

Para implantar num grupo específico de *recursos Azure,* utilize o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para mais informações, consulte estes tópicos:

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Implementar com a CLI do Azure

Para implantar num grupo específico de *recursos Azure,* utilize o seguinte comando:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para mais informações, consulte estes tópicos:

* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/templates/deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Implementar com Azure DevOps

Para implementar modelos de aplicações lógicas e gerir ambientes, as equipas geralmente usam uma ferramenta como [o Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) em [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). A Azure Pipelines fornece uma tarefa de implantação do Grupo de [Recursos Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) que pode adicionar a qualquer pipeline de construção ou lançamento. Para autorização para implantar e gerar o gasoduto de libertação, também precisa de um diretor de [serviço](../active-directory/develop/app-objects-and-service-principals.md)sinuoso (AD) azure Ative Directy . Saiba mais sobre [a utilização de diretores de serviço com pipelines Azure.](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)

Para obter mais informações sobre integração contínua e implantação contínua (CI/CD) para modelos de Gestor de Recursos Azure com Pipelines Azure, consulte estes tópicos e amostras:

* [Integrar modelos de Gestor de Recursos com Pipelines Azure](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Tutorial: Integração contínua para modelos de Gestor de Recursos Azure com Pipelines Azure](../azure-resource-manager/templates/template-tutorial-use-azure-pipelines.md)
* [Amostra: Ligue-se às filas de ônibus de serviço azure de Aplicações Da Lógica Azure e implemente com pipelines Azure em Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Ligue-se a contas de armazenamento azure de Aplicações Lógicas Azure e implante com pipelines Azure em Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Criar uma aplicação de função para apps azure logic e implementar com pipelines Azure em Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Ligue-se a uma conta de integração da Azure Logic Apps e implemente com pipelines Azure em Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Amostra: Orquestrar gasodutos Azure utilizando aplicações da Azure Logic](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Aqui estão os passos gerais de alto nível para a utilização de gasodutos Azure:

1. Em Azure Pipelines, crie um oleoduto vazio.

1. Escolha os recursos necessários para o pipeline, como o modelo de aplicação lógica e os ficheiros de parâmetros de modelo, que gera manualmente ou como parte do processo de construção.

1. Para o seu trabalho de agente, encontre e adicione a tarefa de implantação do **Grupo de Recursos Azure.**

   ![Adicionar tarefa de implantação do grupo de recursos azure](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Configure com um diretor de [serviço](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Adicione referências ao modelo de aplicação lógica e aos ficheiros de parâmetros de modelo.

1. Continue a criar passos no processo de liberação para qualquer outro ambiente, teste automatizado ou aprovadores conforme necessário.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorizar ligações de OAuth

Após a implementação, a sua aplicação lógica funciona de ponta a ponta com parâmetros válidos. No entanto, deve ainda autorizar quaisquer ligações OAuth para gerar fichas de acesso válidas para [autenticar as suas credenciais](../active-directory/develop/authentication-scenarios.md). Aqui estão as formas de autorizar as ligações OAuth:

* Para implementações automatizadas, pode utilizar um script que proporciona consentimento para cada ligação OAuth. Aqui está um exemplo de script no GitHub no projeto [LogicAppConnectionAuth.](https://github.com/logicappsio/LogicAppConnectionAuth)

* Para autorizar manualmente as ligações OAuth, abra a sua aplicação lógica no Logic App Designer, quer no portal Azure quer no Visual Studio. No designer, autorize quaisquer ligações necessárias.

Se utilizar um diretor de [serviço](../active-directory/develop/app-objects-and-service-principals.md) azure Ative Directory (Azure AD) em vez de autorizar ligações, aprenda a [especificar os parâmetros principais](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)do serviço no seu modelo de aplicação lógica .

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Monitorizar aplicações lógicas](../logic-apps/monitor-logic-apps.md)

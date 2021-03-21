---
title: Implementar modelos de aplicação lógica
description: Saiba como implementar modelos de Gestor de Recursos Azure criados para apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/25/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5f5db3fd88f04e7fe569cd675936445dcf730288
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705341"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Implementar modelos do Azure Resource Manager para o Azure Logic Apps

Depois de criar um modelo de Gestor de Recursos Azure para a sua aplicação lógica, pode implementar o seu modelo desta forma:

* [Portal do Azure](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [CLI do Azure](#cli)
* [API REST do Azure Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Implementar através do portal Azure

Para implementar automaticamente um modelo de aplicação lógica para OZure, pode escolher o seguinte botão **Implementar para Azure,** que o inscreve no portal Azure e lhe pede informações sobre a sua aplicação lógica. Em seguida, pode estoisar quaisquer alterações necessárias ao modelo ou parâmetros de aplicação lógica.

[![Implementar no Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Por exemplo, é solicitado para as seguintes informações depois de iniciar sação no portal Azure:

* Nome de assinatura Azure
* Grupo de recursos que pretende utilizar
* Localização de aplicativo lógico
* O nome para a aplicação lógica
* Um URI de teste
* Aceitação dos termos e condições especificados

Para obter mais informações, veja estes tópicos:

* [Visão geral: Automatizar a implementação de apps lógicas com modelos de Gestor de Recursos Azure](logic-apps-azure-resource-manager-templates-overview.md)
* [Implementar recursos com modelos de Gestor de Recursos Azure e o portal Azure](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Implementar com o Visual Studio

Para implementar um modelo de aplicação lógica a partir de um projeto do Azure Resource Group que criou usando o Visual Studio, siga estes [passos para implementar manualmente a sua aplicação lógica](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) para o Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Implementar com o Azure PowerShell

Para implantar num grupo específico de *recursos Azure,* utilize o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para obter mais informações, veja estes tópicos:

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Implementar com a CLI do Azure

Para implantar num grupo específico de *recursos Azure,* utilize o seguinte comando:

```azurecli
az deployment group create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para obter mais informações, veja estes tópicos:

* [Implementar recursos com modelos do Resource Manager e do CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)
* [`az deployment group create`](/cli/azure/deployment/group#az_deployment_group_create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Implementar com Azure DevOps

Para implementar modelos de aplicativos lógicos e gerir ambientes, as equipas geralmente usam uma ferramenta como [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) em [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services). A Azure Pipelines fornece uma [tarefa de implementação do Grupo de Recursos Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) que pode adicionar a qualquer oleoduto de construção ou libertação. Para autorização para implantar e gerar o gasoduto de libertação, também precisa de um diretor de [serviço Azure](../active-directory/develop/app-objects-and-service-principals.md)Ative (AD). Saiba mais sobre [a utilização de princípios de serviço com a Azure Pipelines](/azure/devops/pipelines/library/connect-to-azure).

Para obter mais informações sobre integração contínua e implementação contínua (CI/CD) para modelos Azure Resource Manager com Pipelines Azure, consulte estes tópicos e amostras:

* [Integrar modelos de Gestor de Recursos com Pipelines Azure](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Tutorial: Integração contínua de modelos do Gestor de Recursos Azure com Pipelines Azure](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Amostra: Ligue-se às filas de autocarros da Azure Service de Azure Logic Apps e implemente com a Azure Pipelines em Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Ligue-se às contas de armazenamento Azure a partir de Azure Logic Apps e implemente com a Azure Pipelines em Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Crie uma ação de aplicação de função para apps Azure Logic e implemente com a Azure Pipelines em Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Ligue-se a uma conta de integração a partir de Azure Logic Apps e implemente com a Azure Pipelines em Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Amostra: Orquestrar gasodutos Azure usando apps Azure Logic](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Aqui estão os passos gerais de alto nível para a utilização de Gasodutos Azure:

1. Em Azure Pipelines, crie um oleoduto vazio.

1. Escolha os recursos necessários para o pipeline, como o modelo de aplicação lógica e os ficheiros de parâmetros do modelo, que gera manualmente ou como parte do processo de construção.

1. Para o seu trabalho de agente, encontre e adicione a tarefa **de implantação do Grupo de Recursos Azure.**

   ![Adicionar a tarefa "Azure Resource Group Deployment"](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Configure com um [diretor de serviço.](/azure/devops/pipelines/library/connect-to-azure)

1. Adicione referências ao seu modelo de aplicação lógica e ficheiros de parâmetros de modelo.

1. Continue a construir etapas no processo de libertação para qualquer outro ambiente, teste automatizado ou aprovadores, se necessário.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorizar ligações OAuth

Após a implementação, a sua aplicação lógica funciona de ponta a ponta com parâmetros válidos, mas para gerar fichas de acesso válidas para [autenticar as suas credenciais,](../active-directory/develop/authentication-vs-authorization.md)ainda tem de autorizar ou utilizar ligações OAuth pré-autorizadas. No entanto, só tem de implementar e autenticar os recursos de ligação API uma vez, o que significa que não tem de incluir esses recursos de conexão em implementações subsequentes, a menos que tenha de atualizar as informações de ligação. Se utilizar um gasoduto de integração contínua e de implementação contínua, implementaria apenas recursos de Aplicações Lógicas atualizados e não teria de reautorizar as ligações todas as vezes.

Aqui ficam algumas sugestões para lidar com ligações autorizadas:

* Pré-autorize e partilhe recursos de conexão API através de aplicações lógicas que estão na mesma região. As ligações API existem como recursos Azure independentemente de aplicações lógicas. Embora as aplicações lógicas tenham dependências de recursos de conexão API, os recursos de conexão API não têm dependências de aplicações lógicas e permanecem depois de eliminar as aplicações lógicas dependentes. Além disso, as aplicações lógicas podem usar ligações API que existem em outros grupos de recursos. No entanto, o Logic App Designer suporta a criação de ligações API apenas no mesmo grupo de recursos que as suas aplicações lógicas.

  > [!NOTE]
  > Se estiver a considerar partilhar ligações API, certifique-se de que a sua solução pode [lidar com potenciais problemas de estrangulamento](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling). O estrangulamento acontece ao nível da ligação, pelo que reutilizar a mesma ligação através de várias aplicações lógicas pode aumentar o potencial de estrangulamento de problemas.

* A menos que o seu cenário envolva serviços e sistemas que exijam autenticação multi-factor, pode utilizar um script PowerShell para fornecer consentimento para cada ligação OAuth, executando um trabalhador de integração contínua como uma conta de utilizador normal numa máquina virtual que tem sessões de navegador ativas com as autorizações e consentimento já fornecidos. Por exemplo, pode reutilizar o script de amostra fornecido pelo [projeto LogicAppConnectionAuth no banco de aplicações lógicas GitHub repo](https://github.com/logicappsio/LogicAppConnectionAuth).

* Autorize manualmente as ligações OAuth abrindo a sua aplicação lógica no Logic App Designer, quer no portal Azure, quer no Visual Studio.

* Se utilizar um diretor de [serviço](../active-directory/develop/app-objects-and-service-principals.md) Azure Ative (Azure AD) em vez de autorizar ligações, aprenda a [especificar os parâmetros principais do serviço no seu modelo de aplicação lógica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Monitorizar aplicações lógicas](../logic-apps/monitor-logic-apps.md)

---
title: Implantar modelos de aplicativo lógico – aplicativos lógicos do Azure
description: Saiba como implantar modelos de Azure Resource Manager criados para aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 083c908609ebcf1bdc6dcad5e37ae03daa41758f
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706510"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Implantar modelos de Azure Resource Manager para aplicativos lógicos do Azure

Depois de criar um modelo de Azure Resource Manager para seu aplicativo lógico, você pode implantar o modelo das seguintes maneiras:

* [Azure portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [CLI do Azure](#cli)
* [API REST do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure Pipelines DevOps do Azure](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Implantar por meio do portal do Azure

Para implantar automaticamente um modelo de aplicativo lógico no Azure, você pode escolher o botão **implantar no Azure** a seguir, que entra no portal do Azure e solicita informações sobre seu aplicativo lógico. Em seguida, você pode fazer as alterações necessárias no modelo ou parâmetros do aplicativo lógico.

[![Implementar no Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Por exemplo, as seguintes informações serão solicitadas depois que você entrar no portal do Azure:

* Nome da assinatura do Azure
* Grupo de recursos que você deseja usar
* Local do aplicativo lógico
* O nome para a aplicação lógica
* Um URI de teste
* Aceitação dos termos e condições especificados

Para obter mais informações, consulte estes tópicos:

* [Sobre Automatizar a implantação de aplicativos lógicos com modelos de Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)
* [Implantar recursos com modelos de Azure Resource Manager e o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Implementar com o Visual Studio

Para implantar um modelo de aplicativo lógico de um projeto de grupo de recursos do Azure que você criou usando o Visual Studio, siga estas [etapas para implantar manualmente seu aplicativo lógico](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) no Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Implementar com o Azure PowerShell

Para implantar em um *grupo de recursos específico do Azure*, use o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para obter mais informações, consulte estes tópicos:

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Implementar com a CLI do Azure

Para implantar em um *grupo de recursos específico do Azure*, use o seguinte comando:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para obter mais informações, consulte estes tópicos:

* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Implantar com o Azure DevOps

Para implantar modelos de aplicativo lógico e gerenciar ambientes, as equipes normalmente usam uma ferramenta como [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) no [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines fornece uma [tarefa de implantação de grupo de recursos do Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) que você pode adicionar a qualquer pipeline de compilação ou versão. Para autorização para implantar e gerar o pipeline de lançamento, você também precisa de uma entidade de [serviço](../active-directory/develop/app-objects-and-service-principals.md)Azure Active Directory (AD). Saiba mais sobre como [usar entidades de serviço com Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

Para obter mais informações sobre integração contínua e implantação contínua (CI/CD) para modelos de Azure Resource Manager com Azure Pipelines, consulte estes tópicos e exemplos:

* [Integrar modelos do Resource Manager com o Azure Pipelines](../azure-resource-manager/vs-resource-groups-project-devops-pipelines.md)
* [Tutorial: Integração contínua para modelos de Azure Resource Manager com Azure Pipelines](../azure-resource-manager/resource-manager-tutorial-use-azure-pipelines.md)
* [Amostra: Conectar-se às filas do barramento de serviço do Azure de aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Conectar-se às contas de armazenamento do Azure de aplicativos lógicos do Azure e implantá-las com o Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Configurar uma ação do aplicativo de funções para aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Conectar-se a uma conta de integração de aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

Aqui estão as etapas gerais de alto nível para usar Azure Pipelines:

1. Em Azure Pipelines, crie um pipeline vazio.

1. Escolha os recursos necessários para o pipeline, como o modelo do aplicativo lógico e os arquivos de parâmetros de modelo, que você gera manualmente ou como parte do processo de compilação.

1. Para seu trabalho do Agent, localize e adicione a tarefa de **implantação do grupo de recursos do Azure** .

   ![Adicionar a tarefa "implantação de grupo de recursos do Azure"](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Configure com uma [entidade de serviço](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Adicione referências ao modelo de aplicativo lógico e aos arquivos de parâmetros de modelo.

1. Continue a criar passos no processo de liberação para qualquer outro ambiente, teste automatizado ou aprovadores conforme necessário.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorizar ligações de OAuth

Após a implantação, seu aplicativo lógico funciona de ponta a ponta com parâmetros válidos. No entanto, você ainda deve autorizar quaisquer conexões OAuth para gerar tokens de acesso válidos para [autenticar suas credenciais](../active-directory/develop/authentication-scenarios.md). Aqui estão as maneiras como você pode autorizar conexões OAuth:

* Para implantações automatizadas, você pode usar um script que fornece consentimento para cada conexão OAuth. Aqui está um script de exemplo no GitHub no projeto [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) .

* Para autorizar manualmente as conexões OAuth, abra seu aplicativo lógico no designer de aplicativo lógico, seja na portal do Azure ou no Visual Studio. No designer, autorize todas as conexões necessárias.

Se você usar uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (AD do Azure) em vez de autorizar conexões, saiba como [especificar parâmetros de entidade de serviço em seu modelo de aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Monitorizar aplicações lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md)

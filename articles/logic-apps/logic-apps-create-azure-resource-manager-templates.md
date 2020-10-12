---
title: Criar modelos de aplicativos lógicos para implantação
description: Saiba como criar modelos de Gestor de Recursos Azure para automatizar a implementação em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 4535e6bf11f8c2abf20b1b323925c3fc3299d362
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971786"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Criar modelos do Azure Resource Manager para automatizar a implementação do Azure Logic Apps

Para o ajudar a automatizar a criação e implementação da sua aplicação lógica, este artigo descreve as formas de criar um [modelo de Gestor de Recursos Azure](../azure-resource-manager/management/overview.md) para a sua aplicação lógica. Para uma visão geral sobre a estrutura e sintaxe para um modelo que inclua a definição de fluxo de trabalho e outros recursos necessários para a implementação, consulte [a visão geral: Automatize a implementação para aplicações lógicas com modelos de Gestor de Recursos Azure](logic-apps-azure-resource-manager-templates-overview.md).

A Azure Logic Apps fornece uma [aplicação lógica pré-incorporada Modelo Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) que pode reutilizar, não só para criar aplicações lógicas, mas também para definir os recursos e parâmetros a utilizar para implementação. Pode usar este modelo para os seus cenários de negócio ou personalizar o modelo para satisfazer os seus requisitos.

> [!IMPORTANT]
> Certifique-se de que as ligações no seu modelo utilizam o mesmo grupo de recursos Azure e localização que a sua aplicação lógica.

Para mais informações sobre os modelos do Gestor de Recursos Azure, consulte estes tópicos:

* [Estrutura e sintaxe do gestor de recursos Azure](../azure-resource-manager/templates/template-syntax.md)
* [Modelos de gestor de recursos do autor Azure](../azure-resource-manager/templates/template-syntax.md)
* [Desenvolver modelo do Azure Resource Manager para consistência da cloud](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Criar modelos com Estúdio Visual

Para a forma mais fácil de criar modelos de aplicações lógicas parametrizadas válidas que estejam maioritariamente prontos para implantação, utilize o Visual Studio (edição comunitária gratuita ou superior) e as Ferramentas de Aplicações Lógicas Azure para o Estúdio Visual. Em seguida, pode [criar a sua aplicação lógica no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) ou encontrar e transferir uma [aplicação lógica existente do portal Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Ao descarregar a sua aplicação lógica, obtém um modelo que inclui as definições para a sua aplicação lógica e outros recursos, como ligações. O modelo também *parametriza,* ou define parâmetros para os valores utilizados para a implementação da sua aplicação lógica e outros recursos. Pode fornecer os valores destes parâmetros num ficheiro de parâmetros separados. Desta forma, pode alterar mais facilmente estes valores com base nas suas necessidades de implantação. Para obter mais informações, veja estes tópicos:

* [Criar aplicativos lógicos com o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Gerir aplicações lógicas com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Criar modelos com Azure PowerShell

Pode criar modelos de Gestor de Recursos utilizando o Azure PowerShell com o [módulo LogicAppTemplate](https://github.com/jeffhollan/LogicAppTemplateCreator). Este módulo de código aberto avalia primeiro a sua aplicação lógica e quaisquer ligações que a aplicação lógica utilize. O módulo gera então recursos de modelo com os parâmetros necessários para a implantação.

Por exemplo, suponha que tem uma aplicação lógica que recebe uma mensagem de uma fila de autocarros do Azure Service bus e envia dados para a Base de Dados Azure SQL. O módulo preserva toda a lógica de orquestração e parametriza as cordas de ligação SQL e Service Bus para que possa fornecer e alterar esses valores com base nas suas necessidades de implantação.

Estas amostras mostram como criar e implementar aplicações lógicas utilizando modelos de Gestor de Recursos Azure, Pipelines Azure em Azure DevOps e Azure PowerShell:

* [Amostra: Ligue-se às filas de autocarros da Azure Service de Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Ligue-se às contas de armazenamento Azure de Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Crie uma ação de aplicação de função para apps Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Conecte-se a uma conta de integração da Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>Instalar módulos PowerShell

1. Se ainda não o fez, instale [a Azure PowerShell](/powershell/azure/install-az-ps).

1. Para a forma mais fácil de instalar o módulo LogicAppTemplate a partir da [PowerShell Gallery,](https://www.powershellgallery.com/packages/LogicAppTemplate)execute este comando:

   ```powershell
   Install-Module -Name LogicAppTemplate
   ```

   Para atualizar para a versão mais recente, executar este comando:

   ```powershell
   Update-Module -Name LogicAppTemplate
   ```

Ou, para instalar manualmente, siga os passos no GitHub para [o Criador de Modelos de Aplicações Lógicas.](https://github.com/jeffhollan/LogicAppTemplateCreator)

### <a name="install-azure-resource-manager-client"></a>Instalar cliente Azure Resource Manager

Para que o módulo LogicAppTemplate funcione com qualquer inquilino da Azure e ficha de acesso a subscrição, instale a [ferramenta cliente Azure Resource Manager](https://github.com/projectkudu/ARMClient), que é uma simples ferramenta de linha de comando que chama a API do Gestor de Recursos Azure.

Quando executar o `Get-LogicAppTemplate` comando com esta ferramenta, o comando obtém primeiro um token de acesso através da ferramenta ARMClient, canaliza o símbolo para o script PowerShell e cria o modelo como um ficheiro JSON. Para obter mais informações sobre a ferramenta, consulte este [artigo sobre a ferramenta cliente Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Gerar modelo com PowerShell

Para gerar o seu modelo após a instalação do módulo LogicAppTemplate e [do Azure CLI,](/cli/azure/)executar este comando PowerShell:

```powershell
$parameters = @{
    Token = (az account get-access-token | ConvertFrom-Json).accessToken
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

Para seguir a recomendação para a tubagem num símbolo da [ferramenta cliente Azure Resource Manager,](https://github.com/projectkudu/ARMClient)executar este comando em vez de onde `$SubscriptionId` está o seu ID de subscrição Azure:

```powershell
$parameters = @{
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

armclient token $SubscriptionId | Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

Após a extração, pode então criar um ficheiro de parâmetros a partir do seu modelo executando este comando:

```powershell
Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Para extração com referências Azure Key Vault (apenas estática), executar este comando:

```powershell
Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parâmetros | Obrigatório | Descrição |
|------------|----------|-------------|
| ModeloFile | Sim | O caminho do arquivo para o seu arquivo de modelo |
| KeyVault | Não | Um enum que descreve como lidar com possíveis valores do cofre chave. A predefinição é `None`. |
||||

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implementar modelos de aplicação lógica](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

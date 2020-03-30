---
title: Criar modelos de aplicativológicológico para implementação
description: Saiba como criar modelos de Gestor de Recursos Azure para automatizar implantação em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972636"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Crie modelos de Gestor de Recursos Azure para automatizar implementação para aplicações lógicas azure

Para ajudá-lo a automatizar a criação e implementação da sua aplicação lógica, este artigo descreve as formas como pode criar um modelo de Gestor de [Recursos Azure](../azure-resource-manager/management/overview.md) para a sua aplicação lógica. Para uma visão geral sobre a estrutura e sintaxe para um modelo que inclui a definição de fluxo de trabalho e outros recursos necessários para a implementação, consulte [a visão geral: Implementação automática para aplicações lógicas com modelos de Gestor de Recursos Azure](logic-apps-azure-resource-manager-templates-overview.md).

As Aplicações Lógicas Azure fornecem um modelo de gestor de recursos de [aplicação lógica pré-construída](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) que pode reutilizar, não só para criar aplicações lógicas, mas também para definir os recursos e parâmetros a utilizar para a implementação. Pode usar este modelo para os seus próprios cenários de negócio ou personalizar o modelo para satisfazer os seus requisitos.

> [!IMPORTANT]
> Certifique-se de que as ligações no seu modelo utilizam o mesmo grupo de recursos Azure e localização que a sua aplicação lógica.

Para saber mais sobre os modelos do Gestor de Recursos Azure, consulte estes tópicos:

* [Estrutura do modelo do Gestor de Recursos Azure e sintaxe](../azure-resource-manager/templates/template-syntax.md)
* [Modelos de Gestor de Recursos Do Autor Azure](../azure-resource-manager/templates/template-syntax.md)
* [Desenvolver modelo do Azure Resource Manager para consistência da cloud](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Criar modelos com Estúdio Visual

Para a forma mais fácil de criar modelos de aplicações lógicas parametrizadas válidas que estejam na sua maioria prontas para ser implantadas, utilize o Visual Studio (edição comunitária gratuita ou maior) e as Ferramentas de Aplicações lógicas Azure para o Estúdio Visual. Em seguida, pode [criar a sua aplicação lógica no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) ou encontrar e descarregar uma [aplicação lógica existente do portal Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Ao descarregar a sua aplicação lógica, obtém um modelo que inclui as definições para a sua aplicação lógica e outros recursos, como ligações. O modelo também *parametriza,* ou define parâmetros para, os valores utilizados para a implementação da sua aplicação lógica e outros recursos. Pode fornecer os valores para estes parâmetros num ficheiro de parâmetros separados. Dessa forma, pode alterar mais facilmente estes valores com base nas suas necessidades de implantação. Para obter mais informações, veja estes tópicos:

* [Criar aplicativos lógicos com o Estúdio Visual](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Gerir aplicações lógicas com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Criar modelos com Azure PowerShell

Pode criar modelos de Gestor de Recursos utilizando o Azure PowerShell com o [módulo LogicAppTemplate](https://github.com/jeffhollan/LogicAppTemplateCreator). Este módulo de código aberto avalia primeiro a sua aplicação lógica e quaisquer ligações que a aplicação lógica utiliza. O módulo gera então recursos de modelo com os parâmetros necessários para a implantação.

Por exemplo, suponha que tenha uma aplicação lógica que recebe uma mensagem de uma fila de ônibus de serviço Azure e envia dados para uma base de dados Azure SQL. O módulo preserva toda a lógica da orquestração e parametriza as cordas de ligação SQL e Service Bus para que possa fornecer e alterar esses valores com base nas suas necessidades de implantação.

Estas amostras mostram como criar e implementar aplicações lógicas utilizando modelos de Gestor de Recursos Azure, Pipelines Azure em Azure DevOps e Azure PowerShell:

* [Amostra: Ligue-se às filas de ônibus de serviço azure de aplicações da Azure Logic](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Ligar às contas de armazenamento azure de aplicações da Lógica Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Configurar uma ação de aplicação de função para aplicações da Lógica Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Ligar a uma conta de integração de Aplicações Lógicas Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>Instalar módulos PowerShell

1. Se ainda não o fez, [instale o Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-az-ps)

1. Para a forma mais fácil de instalar o módulo LogicAppTemplate a partir da [Galeria PowerShell,](https://www.powershellgallery.com/packages/LogicAppTemplate)execute este comando:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   Para atualizar para a versão mais recente, executar este comando:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

Ou, para instalar manualmente, siga os passos no GitHub para criador de modelos de [aplicação lógica](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Instalar o cliente do Gestor de Recursos Azure

Para que o módulo LogicAppTemplate trabalhe com qualquer inquilino azure e acesso de subscrição, instale a [ferramenta cliente do Gestor de Recursos Azure,](https://github.com/projectkudu/ARMClient)que é uma simples ferramenta de linha de comando que chama a API do Gestor de Recursos Azure.

Quando executa `Get-LogicAppTemplate` o comando com esta ferramenta, o comando recebe primeiro um sinal de acesso através da ferramenta ARMClient, canaliza o símbolo para o script PowerShell, e cria o modelo como um ficheiro JSON. Para mais informações sobre a ferramenta, consulte este artigo sobre a ferramenta cliente do [Gestor de Recursos Azure.](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)

### <a name="generate-template-with-powershell"></a>Gerar modelo com PowerShell

Para gerar o seu modelo depois de instalar o módulo LogicAppTemplate e [o Azure CLI,](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)execute este comando PowerShell:

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Para seguir a recomendação de tubagem num símbolo da ferramenta cliente do `$SubscriptionId` Gestor de [Recursos Azure,](https://github.com/projectkudu/ARMClient)execute este comando em vez de onde está o seu ID de subscrição Azure:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Após a extração, pode então criar um ficheiro de parâmetros a partir do seu modelo executando este comando:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Para extração com referências azure key vault (apenas estática), executar este comando:

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parâmetros | Necessário | Descrição |
|------------|----------|-------------|
| ModeloFile | Sim | O caminho de arquivo para o seu ficheiro de modelo |
| KeyVault | Não | Um enum que descreve como lidar com possíveis valores chave do cofre. A predefinição é `None`. |
||||

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implementar modelos de aplicação lógica](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

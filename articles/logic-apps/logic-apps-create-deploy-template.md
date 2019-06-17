---
title: Criar modelos de implementação para o Azure Logic Apps | Documentos da Microsoft
description: Criar modelos do Azure Resource Manager para implementar aplicações lógicas
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128605"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Criar modelos do Azure Resource Manager para implementar aplicações lógicas

Quando cria uma aplicação lógica, pode expandir a definição da sua aplicação lógica num [modelo Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Em seguida, pode utilizar este modelo para automatizar as implementações ao definir os recursos e parâmetros que pretende utilizado para a implantação e fornecer os valores de parâmetro por meio de um [ficheiro de parâmetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Dessa forma, pode implementar aplicações lógicas mais facilmente e a qualquer ambiente ou o grupo de recursos do Azure que pretende. 

O Azure Logic Apps fornece um [modelo de Gestor de recursos do Azure de aplicações de lógica pré-criados](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) que pode reutilizar, não apenas para a criação de aplicações lógicas, mas também para definir os recursos e os parâmetros a utilizar para a implementação. Pode utilizar este modelo para os seus próprios cenários de negócios ou personalizar o modelo para satisfazer os seus requisitos. Saiba mais sobre [estrutura de modelo do Resource Manager e a sintaxe](../azure-resource-manager/resource-group-authoring-templates.md). Para a sintaxe JSON e propriedades, consulte [tipos de recursos do Microsoft](/azure/templates/microsoft.logic/allversions).

Para obter mais informações sobre os modelos Azure Resource Manager, veja estes artigos:

* [Criar modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Desenvolver modelos Azure Resource Manager para consistência de cloud](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>Estrutura de aplicação lógica

A definição da aplicação lógica tem nestas secções básicas, o que pode ver ao mudar de "designer view" para "vista de código" ou com uma ferramenta como [Explorador de recursos do Azure](http://resources.azure.com). Definições de aplicação lógica utilizar Javascript Object Notation (JSON), então, para obter mais informações sobre a sintaxe JSON e propriedades, consulte [tipos de recursos do Microsoft](/azure/templates/microsoft.logic/allversions).

* **Recurso de aplicação lógica**: Descreve as informações como a localização da sua aplicação lógica ou a região, preços do plano e a definição de fluxo de trabalho.

* **Definição de fluxo de trabalho**: Descreve a sua aplicação lógica acionadores e ações e como o serviço do Azure Logic Apps é executado o fluxo de trabalho. Na vista de código, pode encontrar a definição de fluxo de trabalho no `definition` secção.

* **Ligações**: Se utilizar conectores geridos na sua aplicação lógica, o `$connections` seção faz referência a outros recursos que armazenam metadados sobre estas ligações entre a sua aplicação lógica e outros sistemas ou serviços, como cadeias de ligação e tokens de acesso em segurança. Dentro de sua definição da aplicação lógica, as referências a estas ligações aparecem dentro de sua definição de aplicação de lógica `parameters` secção.

Para criar um modelo de aplicação lógica que pode usar com implementações do grupo de recursos do Azure, tem de definir os recursos e parametrizar conforme necessário. Por exemplo, se estiver a implementar para um desenvolvimento, teste e ambiente de produção, provavelmente pretende utilizar cadeias de ligação diferente para uma base de dados SQL em cada ambiente.
Em alternativa, pode querer implementar em diferentes subscrições ou grupos de recursos.

## <a name="create-logic-app-deployment-templates"></a>Criar modelos de implementação de aplicação de lógica

Para a forma mais fácil criar um modelo de implementação de aplicação lógica válida, utilize o Visual Studio e as ferramentas do Azure Logic Apps para a extensão do Visual Studio. Ao transferir a aplicação lógica no portal do Azure para o Visual Studio, obtém um modelo de implementação válida que pode utilizar com qualquer subscrição do Azure e o local. Além disso, o download automaticamente a sua aplicação lógica parametriza a definição da aplicação lógica que está incorporada no modelo.
Para obter mais informações sobre como criar e gerir aplicações lógicas no Visual Studio, consulte [criar aplicações lógicas com o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) e [gerir aplicações lógicas com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Além do Visual Studio ou a criação manual de seu modelo e os parâmetros necessários ao seguir as orientações neste tópico, também pode utilizar o [módulo do PowerShell para criar modelos de aplicações de lógica](https://github.com/jeffhollan/LogicAppTemplateCreator). Este módulo de código-fonte aberto avalia, em primeiro lugar, a aplicação lógica e todas as ligações que utiliza a aplicação lógica. O módulo, em seguida, gera recursos de modelo com os parâmetros necessários para a implementação. Por exemplo, suponha que tem uma aplicação lógica que recebe uma mensagem de uma fila do Service bus do Azure e adiciona dados a uma base de dados SQL do Azure. A ferramenta de módulo preserva toda a lógica de orquestração e parametriza as cadeias de caracteres de conexão SQL e barramento de serviço para que pode definir esses valores em implementação.

> [!IMPORTANT]
> Conexões têm de existir no mesmo grupo de recursos do Azure que a aplicação lógica.
> Para o módulo do PowerShell trabalhar com qualquer utilização do Azure e subscrição um inquilino do acesso token, o módulo com o [ferramenta de cliente do Azure Resource Manager](https://github.com/projectkudu/ARMClient). Para obter mais informações, consulte esta [artigo sobre a ferramenta de cliente do Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) aborda ARMClient mais detalhadamente.

### <a name="install-powershell-module-for-logic-app-templates"></a>Instalar o módulo do PowerShell para modelos de aplicações lógicas

Para a forma mais fácil instalar o módulo a partir da [galeria do PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), utilize este comando:

`Install-Module -Name LogicAppTemplate`

Pode instalar manualmente o módulo do PowerShell:

1. Baixe a versão mais recente [criador de modelo de aplicação lógica](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).

1. Extraia a pasta na pasta de módulo do PowerShell, que é normalmente `%UserProfile%\Documents\WindowsPowerShell\Modules`.

### <a name="generate-logic-app-template---powershell"></a>Gerar modelo de aplicação lógica - PowerShell

Após a instalação do PowerShell, pode gerar um modelo ao utilizar o seguinte comando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` é o ID de subscrição do Azure. Esta linha primeiro obtém um acesso token através do ARMClient, em seguida, encaminha-lo por meio de para o script do PowerShell e, em seguida, cria o modelo num ficheiro JSON.

## <a name="parameters-in-logic-app-templates"></a>Parâmetros de modelos de aplicações lógicas

Depois de criar o modelo de aplicação lógica, pode adicionar e editar todos os parâmetros necessários. O seu modelo tem mais do que um `parameters` secção, por exemplo: 

* Definição de fluxo de trabalho da sua aplicação lógica tem a sua própria [ `parameters` secção](../logic-apps/logic-apps-workflow-definition-language.md#parameters) onde pode definir todos os parâmetros que a aplicação lógica utiliza para aceitar entradas na implementação.

* Modelo do Resource Manager tem a sua própria [ `parameters` secção](../azure-resource-manager/resource-group-authoring-templates.md#parameters), em separado da sua aplicação lógica `parameters` secção. Por exemplo:

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Por exemplo, suponha que a sua definição da aplicação lógica faz referência um ID de recurso que representa uma função do Azure ou um fluxo de trabalho de aplicação lógica aninhada e pretender implementar esse ID de recurso, juntamente com a sua aplicação lógica como uma única implementação. Pode adicionar esse identificador como um recurso no seu modelo e parametrizar esse ID. Pode usar essa mesma abordagem para referências a pontos finais personalizados de APIs ou OpenAPI (anteriormente conhecido como "Swagger") que quer implementado com cada grupo de recursos do Azure.

Ao utilizar parâmetros no seu modelo de implementação, siga estas orientações para que o estruturador de aplicações lógicas podem apresentar corretamente esses parâmetros:

* Utilize parâmetros apenas nestes acionadores e ações:

  * Aplicação de funções do Azure
  * Aninhadas ou fluxo de trabalho de aplicação de lógica de subordinados
  * Chamada de gestão de API
  * URL do runtime de ligação de API
  * Caminho de ligação de API

* Quando define parâmetros, certifique-se de que fornecer valores padrão usando o `defaultValue` valor de propriedade, por exemplo:

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* Para proteger ou ocultar informações confidenciais em modelos, proteger os seus parâmetros. Saiba mais sobre [como utilizar o protegido parâmetros](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow).

Eis um exemplo que mostra como é possível parametrizar a ação "Send message" do Service bus do Azure:

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>Recursos dependentes de referência

Se a sua aplicação lógica exigir referências a recursos dependentes, pode usar [funções de modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) no modelo de implementação da sua aplicação lógica. Por exemplo, suponha que pretende que a aplicação lógica para fazer referência a uma função do Azure ou uma conta de integração com definições para parceiros, contratos e outros artefatos que desejar implementados juntamente com a sua aplicação lógica.
Pode utilizar funções de modelo do Resource Manager, tal como `parameters`, `variables`, `resourceId`, `concat`e assim por diante.

Eis um exemplo que mostra como pode substituir o ID de recurso para uma função do Azure ao definir estes parâmetros:

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

Eis como utilizar estes parâmetros ao referenciar a função do Azure:

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>Adicionar a aplicação lógica ao projeto do grupo de recursos

Se tiver um projeto do grupo de recursos do Azure existente, pode adicionar a aplicação lógica para esse projeto, utilizando a janela contorno de JSON. Também pode adicionar outra aplicação de lógica juntamente com a aplicação que criou anteriormente.

1. No Explorador de soluções, abra o `<template>.json` ficheiro.

2. Do **View** menu, selecione **Other Windows** > **contorno de JSON**.

3. Para adicionar um recurso para o ficheiro de modelo, escolha **adicionar recurso** na parte superior da janela contorno de JSON. Ou na janela contorno de JSON, com o botão direito **recursos**e selecione **adicionar novo recurso**.

   ![Janela contorno de JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. Na **adicionar recursos** caixa de diálogo, localize e selecione **aplicação lógica**. Nomeie a sua aplicação lógica e escolha **adicionar**.

   ![Adicionar recurso](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>Obter suporte

Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Implementar modelos de aplicações lógicas](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)

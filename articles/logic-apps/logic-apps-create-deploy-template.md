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
ms.openlocfilehash: 3d32b180f7d841c36f8ae03aa94956c6da00c6fe
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57883445"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Criar modelos do Azure Resource Manager para implementar aplicações lógicas

Depois de ter sido criada uma aplicação lógica, pode querer criá-la como um modelo Azure Resource Manager.
Dessa forma, pode facilmente implementar a aplicação lógica para qualquer ambiente ou o grupo de recursos em que poderá ser necessário.
Para mais informações sobre modelos do Resource Manager, consulte [criar modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) e [implementar recursos com modelos do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Modelo de implementação de aplicação lógica

Uma aplicação lógica possui três componentes básicos:

* **Recurso de aplicação lógica**: Contém informações sobre coisas como preços do plano, localização e a definição de fluxo de trabalho.
* **Definição de fluxo de trabalho**: Descreve os passos de fluxo de trabalho da sua aplicação lógica e como o motor do Logic Apps deve executar o fluxo de trabalho.
Pode ver essa definição na sua aplicação lógica **vista de código** janela.
No recurso de aplicação lógica, pode encontrar esta definição no `definition` propriedade.
* **Ligações**: Refere-se para separar os recursos que armazenam metadados sobre todas as ligações conector, como uma cadeia de ligação e um token de acesso em segurança.
No recurso da aplicação lógica, a aplicação lógica faz referência a esses recursos no `parameters` secção.

Pode ver todas essas peças de logic apps existentes utilizando uma ferramenta como o [Explorador de recursos do Azure](http://resources.azure.com). Para a sintaxe JSON e propriedades, consulte [tipos de recursos do Microsoft](/azure/templates/microsoft.logic/allversions).

Para tornar um modelo para uma aplicação lógica para utilizar com implementações do grupo de recursos, tem de definir os recursos e parametrizar conforme necessário.
Por exemplo, se estiver a implementar para um desenvolvimento, teste e ambiente de produção, provavelmente pretende utilizar cadeias de ligação diferente para uma base de dados SQL em cada ambiente.
Em alternativa, pode querer implementar em diferentes subscrições ou grupos de recursos.  

## <a name="create-a-logic-app-deployment-template"></a>Criar um modelo de implementação de aplicação lógica

A maneira mais fácil de ter um modelo de implementação de aplicação lógica válida é utilizar o [Visual Studio Tools para o Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
As ferramentas do Visual Studio geram um modelo de implementação válida que possa ser utilizado em qualquer subscrição ou localização.

Algumas outras ferramentas podem ajudá-lo à medida que cria um modelo de implementação de aplicação lógica.
Pode criar manualmente, ou seja, utilizando os recursos já discutidos aqui para criar os parâmetros conforme necessário.
Outra opção é usar um [criador de modelo de aplicação lógica](https://github.com/jeffhollan/LogicAppTemplateCreator) módulo do PowerShell. Este módulo de código-fonte aberto avalia, em primeiro lugar, a aplicação lógica e todas as ligações que está a utilizar e, em seguida, gera recursos de modelo com os parâmetros necessários para a implementação.
Por exemplo, se tiver uma aplicação lógica que recebe uma mensagem de uma fila do Service bus do Azure e adiciona dados a uma base de dados SQL do Azure, a ferramenta preserva toda a lógica de orquestração e parametriza a ligação de SQL e o Service Bus cadeias de caracteres para que eles podem ser definidos ao implementar genda da atribuição.

> [!NOTE]
> Ligações tem de estar no mesmo grupo de recursos que a aplicação lógica.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Instalar o módulo de PowerShell do modelo de aplicação lógica
A maneira mais fácil para instalar o módulo é através da [galeria do PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), com o comando `Install-Module -Name LogicAppTemplate`.  

Também pode instalar o módulo do PowerShell manualmente:

1. Baixe a versão mais recente dos [criador de modelo de aplicação lógica](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Extraia a pasta na sua pasta de módulo do PowerShell (normalmente `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Para o módulo trabalhar com qualquer acesso de subscrição e um inquilino do token, recomendamos que utilize-o com o [ARMClient](https://github.com/projectkudu/ARMClient) ferramenta da linha de comandos.  Isso [mensagem de blogue](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) aborda ARMClient mais detalhadamente.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Gerar um modelo de aplicação lógica com o PowerShell
Após a instalação do PowerShell, pode gerar um modelo ao utilizar o seguinte comando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` é o ID de subscrição do Azure. Esta linha primeiro obtém um acesso token através do ARMClient, em seguida, encaminha-lo por meio de para o script do PowerShell e, em seguida, cria o modelo num ficheiro JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Adicionar parâmetros a um modelo de aplicação lógica
Depois de criar o modelo de aplicação lógica, pode continuar a adicionar ou modificar os parâmetros que poderá precisar. Por exemplo, se a definição de incluir um ID de recurso para uma função do Azure ou um fluxo de trabalho aninhado, que planeja implantar numa implementação única, pode adicionar mais recursos ao seu modelo e parametrizar IDs, conforme necessário. O mesmo se aplica a quaisquer referências a APIs personalizadas ou de Swagger pontos de extremidade que espera implantar com cada grupo de recursos.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Adicionar referências para recursos dependentes para modelos de implementação do Visual Studio

Quando pretender que a aplicação lógica para fazer referência a recursos dependentes, pode utilizar [funções de modelo do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) no seu modelo de implementação de aplicação lógica. Por exemplo, poderá a aplicação lógica para fazer referência a uma conta de função do Azure ou integração que pretende implementar em conjunto com a sua aplicação lógica. Siga estas orientações sobre como utilizar parâmetros no seu modelo de implementação para que o Estruturador da aplicação lógica processa corretamente. 

Pode usar parâmetros de aplicação lógica nesses tipos de acionadores e ações:

*   Fluxo de trabalho subordinado
*   Function app
*   Chamada APIM
*   URL do runtime de ligação de API
*   Caminho de ligação de API

E pode usar funções de modelo como parâmetros, variáveis, resourceId, concat, etc. Por exemplo, eis como pode substituir o ID de recurso de função do Azure:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

E, em que usaria parâmetros:

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
Como outro exemplo, é possível parametrizar a operação de mensagem de envio do Service Bus:

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl é opcional e pode ser removido do seu modelo, se estiver presente.
> 


> [!NOTE] 
> Para o Estruturador da aplicação lógica funcionar ao utilizar parâmetros, tem de indicar valores predefinidos, por exemplo:
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Adicionar a sua aplicação lógica a um projeto do grupo de recursos existente

Se tiver um projeto do grupo de recursos existente, pode adicionar a sua aplicação lógica para esse projeto na janela contorno de JSON. Também pode adicionar outra aplicação de lógica juntamente com a aplicação que criou anteriormente.

1. Abra o ficheiro `<template>.json`.

2. Para abrir a janela contorno de JSON, aceda a **View** > **Other Windows** > **contorno de JSON**.

3. Para adicionar um recurso para o ficheiro de modelo, clique em **adicionar recurso** na parte superior da janela contorno de JSON. Ou na janela contorno de JSON, com o botão direito **recursos**e selecione **adicionar novo recurso**.

    ![Janela contorno de JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)
    
4. Na **adicionar recursos** caixa de diálogo, localize e selecione **aplicação lógica**. Nomeie a sua aplicação lógica e escolha **adicionar**.

    ![Adicionar recurso](./media/logic-apps-create-deploy-template/addresource.png)


## <a name="deploy-a-logic-app-template"></a>Implementar um modelo de aplicação lógica

Pode implementar o modelo ao utilizar quaisquer ferramentas como o PowerShell, REST API, [Pipelines do Azure DevOps do Azure](#team-services)e a implementação do modelo através do portal do Azure.
Além disso, para armazenar os valores para parâmetros, recomendamos que crie uma [ficheiro de parâmetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Saiba como [implementar recursos com modelos Azure Resource Manager e PowerShell](../azure-resource-manager/resource-group-template-deploy.md) ou [implementar recursos com modelos Azure Resource Manager e o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Autorizar ligações de OAuth

Após a implementação, a aplicação lógica funciona ponto a ponto com parâmetros válidos.
No entanto, tem de autorizar ainda ligações de OAuth para gerar um token de acesso válido.
Para autorizar OAuth ligações, abra a aplicação lógica no Designer de aplicações lógicas e autorizar estas ligações. Ou, para implementação automatizada, pode usar um script para dar consentimento a cada conexão de OAuth.
Existe um script de exemplo no GitHub sob o [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projeto.

<a name="team-services"></a>
## <a name="azure-devops-azure-pipelines"></a>Pipelines do Azure de DevOps do Azure

Um cenário comum para implantar e gerenciar um ambiente é usar uma ferramenta como Pipelines do Azure no DevOps do Azure, com um modelo de implementação de aplicação lógica. DevOps do Azure inclui um [implementar o grupo de recursos do Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) tarefas que pode adicionar a qualquer compilação ou pipeline de versões. Tem de ter uma [principal de serviço](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) para autorização para implementar e, em seguida, pode gerar o pipeline de lançamento.

1. Nos Pipelines do Azure, selecione **vazio** para que criar um pipeline vazio.

    ![Criar o pipeline vazio][1]

2. Escolha quaisquer recursos de que precisa para isso, provavelmente, incluindo o modelo de aplicação lógica que é gerado manualmente ou como parte do processo de compilação.
3. Adicionar uma **implementação de grupo de recursos do Azure** tarefas.
4. Configurar com um [principal de serviço](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)e os arquivos de modelo e parâmetros de modelo de referência.
5. Continue a criar passos no processo de liberação para qualquer outro ambiente, teste automatizado ou aprovadores conforme necessário.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png

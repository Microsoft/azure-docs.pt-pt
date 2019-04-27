---
title: Criar e gerir fluxos de trabalho automatizados com o Visual Studio Code - Azure Logic Apps | Documentos da Microsoft
description: Início rápido para saber como criar e gerir aplicações lógicas com JSON no Visual Studio Code (código de VS)
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: article
ms.reviewer: klam, deli, LADocs
ms.suite: integration
ms.date: 10/05/2018
ms.openlocfilehash: 0fec590523fa130af2e5670a92914c056df289d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60512374"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Início rápido: Criar e gerir fluxos de aplicação trabalho automatizada lógicas - Visual Studio Code

Com o [do Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o Visual Studio Code, pode criar e gerir aplicações lógicas que ajudá-lo a automatizam tarefas, os fluxos de trabalho e processos para integrar aplicações, dados, sistemas e serviços entre organizações e empresas. Este início rápido mostra como pode criar e editar definições de fluxo de trabalho de aplicação lógica, trabalhando com o esquema de definição de fluxo de trabalho em JavaScript Object Notation (JSON) através de uma experiência baseada em código. Também pode trabalhar no logic apps existentes já implementadas <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> na cloud. 

Embora seja possível executar estas mesmas tarefas no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> e no Visual Studio, pode começar a utilizar mais rapidamente no Visual Studio Code quando já estiver familiarizado com definições de aplicação lógica e quiser trabalhar diretamente no código. Por exemplo, pode desativar, ativar, eliminar e atualizar aplicações lógicas já criadas. Além disso, pode trabalhar no logic apps e contas de integração a partir de qualquer plataforma de desenvolvimento em que o Visual Studio Code é executado, como Linux, Windows e Mac.

Neste artigo, pode criar a mesma aplicação lógica, como mostra a [início rápido para criar uma aplicação lógica no portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), que se concentra mais nos conceitos básicos. No Visual Studio Code, a aplicação lógica é semelhante a este exemplo:

![Aplicação lógica concluída](./media/create-logic-apps-visual-studio-code/overview.png)

Antes de começar, certifique-se de que dispõe destes itens:

* Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

* Conhecimento básico sobre [definições de fluxo de trabalho de aplicação lógica](../logic-apps/logic-apps-workflow-definition-language.md) e suas estruturas, que usa o JavaScript Object Notation (JSON) 

  Se estiver familiarizado com o Logic Apps, experimente o guia de introdução orienta [como criar a sua primeira aplicação lógica no portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), que se concentra mais nos conceitos básicos. 

* Acesso à web para iniciar sessão no Azure e a sua subscrição do Azure

* Transfira e instale estas ferramentas, se ainda não as tiver: 

  * <a href="https://code.visualstudio.com/" target="_blank">Versão do Visual Studio Code 1.25.1 ou posterior</a>, que é gratuita

  * Extensão do Visual Studio Code para o Azure Logic Apps

    Pode transferir e instalar esta extensão a partir da [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) ou diretamente a partir de dentro do Visual Studio Code. 
    Certifique-se de que recarregar o Visual Studio Code após a instalação. 

    ![Localizar "Extensão do Visual Studio Code para o Azure Logic Apps"](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Para verificar se a extensão instalado corretamente, o Azure o ícone é apresentado na sua barra de ferramentas do Visual Studio Code. 

    ![Extensão instalada](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Para obter mais informações, consulte <a href="https://code.visualstudio.com/docs/editor/extension-gallery" target="_blank">Marketplace de extensão</a>. Também pode ver e envie contribuições para a versão de código-fonte aberto esta extensão ao visitar a [extensão do Azure Logic Apps para o Visual Studio Code, no GitHub](https://github.com/Microsoft/vscode-azurelogicapps). 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Abra o Visual Studio Code. Na barra de ferramentas do Visual Studio Code, selecione o ícone do Azure. 

   ![Selecione o ícone do Azure](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. Na janela do Azure, em **Logic Apps**, selecione **iniciar sessão no Azure**. 

   ![Selecione "Iniciar sessão no Azure"](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Agora lhe for pedido para iniciar sessão com o código de autenticação fornecido. 

1. Copie o código de autenticação e, em seguida, escolha **Copiar & abrir**, que abre uma nova janela do browser.

   ![Início de sessão de linha de comandos](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Introduza o código de autenticação. Quando lhe for pedido, escolha **continuar**.

   ![Introduzir código](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Selecione a sua conta do Azure. Depois de iniciar sessão, pode fechar o navegador e retornar ao Visual Studio Code.

   Na janela do Azure, o painel de Logic Apps e o painel de contas de integração agora mostram as subscrições do Azure na sua conta. 

   ![Selecionar subscrição](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Se não vir as subscrições é de esperar, junto a **Logic Apps** da etiqueta, escolha **selecionar subscrições** (ícone de filtro). Localize e selecione as subscrições que pretende.

1. Para ver qualquer logic apps existentes ou contas de integração na sua subscrição do Azure, expanda a sua subscrição.

   ![Ver o logic apps e contas de integração](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Criar uma aplicação lógica

1. Se ainda não iniciou sessão sua subscrição do Azure de dentro do Visual Studio Code, siga os passos neste artigo para [iniciar sessão agora](#sign-in-azure).

1. No menu de contexto da sua subscrição, selecione **criar**.

   ![Selecione "Criar"](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. Na lista que mostra os grupos de recursos do Azure na sua subscrição, selecione um grupo de recursos existente ou **criar um novo grupo de recursos**. 

   Este exemplo cria um novo grupo de recursos:

   ![Criar um novo grupo de recursos](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Forneça um nome para o seu grupo de recursos do Azure e, em seguida, prima ENTER.

   ![Nome do seu grupo de recursos](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Selecione a localização do datacenter para onde pretende guardar os metadados da sua aplicação lógica.

   ![Selecionar localização](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Forneça um nome para a aplicação lógica e, em seguida, prima ENTER.

   ![Nome da sua aplicação lógica](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Sua nova aplicação lógica aparece agora na janela do Azure, na sua subscrição do Azure. Agora pode começar a criar definição de fluxo de trabalho da sua aplicação lógica.

1. No menu de atalho da sua aplicação lógica, selecione **aberto num Editor de**. 

   ![Aplicação de lógica aberto no editor](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   Código do Visual Studio abre um modelo de definição de fluxo de trabalho de aplicação lógica (. ficheiro de logicapp) para que possa começar a criar o fluxo de trabalho da sua aplicação lógica.

   ![Nova definição de fluxo de trabalho de aplicação lógica](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. No ficheiro de modelo definição fluxo de trabalho do logic app, comece a criar definição de fluxo de trabalho da sua aplicação lógica. Para referência técnica, consulte a [esquema de linguagem de definição de fluxo de trabalho para o Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   Aqui está uma definição de lógica de exemplo. Normalmente, os elementos JSON são apresentados por ordem alfabética em cada secção, mas este exemplo mostra aproximadamente destes elementos na ordem em que aparecem passos da aplicação lógica no estruturador.

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. Quando tiver terminado, guarde o ficheiro de definição de aplicação lógica. Quando pede-lhe para confirmar a carregar a definição da aplicação lógica à sua subscrição do Azure, escolha o Visual Studio Code **carregar**.

   ![Carregar a sua nova aplicação lógica](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Depois do código do Visual Studio publica a aplicação lógica no Azure, pode localizar a aplicação agora em direto e em execução no portal do Azure. 

   ![Aplicação lógica publicado no portal do Azure](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Editar a aplicação lógica

Para trabalhar numa aplicação de lógica existente que já tenha sido implementada no Azure, é possível abrir o ficheiro de definição de fluxo de trabalho dessa aplicação no Visual Studio Code.

1. Se ainda não iniciou sessão sua subscrição do Azure de dentro do Visual Studio Code, siga os passos neste artigo para [iniciar sessão agora](#sign-in-azure).

1. Na janela do Azure, em **Logic Apps**, expanda a sua subscrição do Azure e selecione a aplicação lógica que pretende. 

1. No menu da sua aplicação lógica, selecione **aberto num Editor de**. Em alternativa, junto ao nome da sua aplicação lógica, escolha o ícone de edição.

   ![Editor aberto para a aplicação de lógica existente](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code é aberto o. ficheiro de logicapp para definição de fluxo de trabalho da sua aplicação lógica.

   ![Definição de fluxo de trabalho da aplicação de lógica aberta](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Faça as alterações na definição da sua aplicação lógica.

1. Quando tiver terminado, guarde as alterações.

1. Quando o Visual Studio Code pede-lhe para atualizar a definição de aplicação lógica na sua subscrição do Azure, escolha **carregar**. 

   ![Carregar as suas edições](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">fórum do Azure Logic Apps</a>.
* Para submeter ou votar em ideias para funcionalidades, visite o <a href="https://aka.ms/logicapps-wish" target="_blank">site de comentários dos utilizadores do Logic Apps</a>.


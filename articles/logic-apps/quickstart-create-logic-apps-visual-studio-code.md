---
title: Criar e gerenciar fluxos de trabalho automatizados com o Visual Studio Code-aplicativos lógicos do Azure
description: Início rápido sobre como criar e gerenciar aplicativos lógicos com JSON em Visual Studio Code (VS Code)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, deli, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/05/2018
ms.openlocfilehash: f0b568289a50b9883e6e0f1cba6b793c9e6e90e8
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051727"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Início rápido: Criar e gerenciar fluxos de trabalho de aplicativo lógico automatizado-Visual Studio Code

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e Visual Studio Code, você pode criar e gerenciar aplicativos lógicos que ajudam a automatizar tarefas, fluxos de trabalho e processos para integrar aplicativos, dados, sistemas e serviços entre organizações e empresas. Este guia de início rápido mostra como você pode criar e Editar definições de fluxo de trabalho de aplicativo lógico trabalhando com o esquema de definição de fluxo de trabalho no JavaScript Object Notation (JSON) por meio de uma experiência baseada em código. Você também pode trabalhar em aplicativos lógicos existentes que já estão implantados no Azure. 

Embora seja possível executar essas mesmas tarefas na portal do Azure (https://portal.azure.com) e no Visual Studio, você pode começar mais rápido em Visual Studio Code quando já estiver familiarizado com as definições de aplicativos lógicos e quiser trabalhar diretamente no código. Por exemplo, você pode desabilitar, habilitar, excluir e atualizar aplicativos lógicos já criados. Além disso, você pode trabalhar em aplicativos lógicos e contas de integração de qualquer plataforma de desenvolvimento onde o Visual Studio Code for executado, como Linux, Windows e Mac.

Para este artigo, você pode criar o mesmo aplicativo lógico que no [início rápido para criar um aplicativo lógico no portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), que se concentra mais nos conceitos básicos. No Visual Studio Code, o aplicativo lógico é semelhante a este exemplo:

![Aplicação lógica concluída](./media/create-logic-apps-visual-studio-code/overview.png)

Antes de começar, verifique se você tem estes itens:

* Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Conhecimento básico sobre as [definições de fluxo de trabalho do aplicativo lógico](../logic-apps/logic-apps-workflow-definition-language.md) e sua estrutura, que usa JavaScript Object Notation (JSON) 

  Se você for novo em aplicativos lógicos, experimente o guia de início rápido que [explica como criar seu primeiro aplicativo lógico no portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), que se concentra mais nos conceitos básicos. 

* Acesso à Web para entrar no Azure e sua assinatura do Azure

* Transfira e instale estas ferramentas, se ainda não as tiver: 

  * [Visual Studio Code versão 1.25.1 ou posterior]("https://code.visualstudio.com/), que é gratuita

  * Extensão de Visual Studio Code para aplicativos lógicos do Azure

    Você pode baixar e instalar essa extensão do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) ou diretamente de dentro Visual Studio Code. 
    Certifique-se de recarregar Visual Studio Code após a instalação. 

    ![Localizar "extensão de Visual Studio Code para aplicativos lógicos do Azure"](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Para verificar se a extensão foi instalada corretamente, o ícone do Azure aparece na barra de ferramentas Visual Studio Code. 

    ![Extensão instalada](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Para obter mais informações, consulte [Marketplace de extensão](https://code.visualstudio.com/docs/editor/extension-gallery). Você também pode exibir e enviar contribuições para a versão de código-fonte aberto da extensão visitando a [extensão de aplicativos lógicos do Azure para Visual Studio Code no GitHub](https://github.com/Microsoft/vscode-azurelogicapps). 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Abra o Visual Studio Code. Na barra de ferramentas Visual Studio Code, selecione o ícone do Azure. 

   ![Selecione o ícone do Azure](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. Na janela do Azure, em **aplicativos lógicos**, selecione **entrar no Azure**. 

   ![Selecione "entrar no Azure"](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Agora, você será solicitado a entrar usando o código de autenticação fornecido. 

1. Copie o código de autenticação e escolha **copiar & abrir**, que abre uma nova janela do navegador.

   ![Prompt de entrada](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Insira seu código de autenticação. Quando solicitado, escolha **continuar**.

   ![Introduzir código](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Selecione sua conta do Azure. Depois de entrar, você pode fechar o navegador e retornar ao Visual Studio Code.

   Na janela Azure, o painel aplicativos lógicos e o painel contas de integração agora mostram as assinaturas do Azure em sua conta. 

   ![Selecionar subscrição](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Se você não vir as assinaturas esperadas, ao lado de rótulo de **aplicativos lógicos** , escolha **selecionar assinaturas** (ícone de filtro). Localize e selecione as assinaturas desejadas.

1. Para exibir quaisquer aplicativos lógicos existentes ou contas de integração em sua assinatura do Azure, expanda sua assinatura.

   ![Exibir aplicativos lógicos e contas de integração](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Criar uma aplicação lógica

1. Se você não tiver entrado em sua assinatura do Azure de dentro de Visual Studio Code, siga as etapas neste artigo para [entrar agora](#sign-in-azure).

1. No menu de contexto de sua assinatura, selecione **criar**.

   ![Selecione "criar"](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. Na lista que mostra os grupos de recursos do Azure em sua assinatura, selecione um grupo de recursos existente ou **crie um novo grupo de recursos**. 

   Este exemplo cria um novo grupo de recursos:

   ![Criar um novo grupo de recursos](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Forneça um nome para o grupo de recursos do Azure e pressione ENTER.

   ![Nomear seu grupo de recursos](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Selecione o local do datacenter para onde salvar os metadados do aplicativo lógico.

   ![Selecionar localização](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Forneça um nome para seu aplicativo lógico e pressione ENTER.

   ![Nomeie seu aplicativo lógico](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Seu novo aplicativo lógico agora aparece na janela do Azure, em sua assinatura do Azure. Agora você pode começar a criar a definição de fluxo de trabalho do aplicativo lógico.

1. No menu de atalho do aplicativo lógico, selecione **abrir no editor**. 

   ![Abrir o aplicativo lógico no editor](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   Visual Studio Code abre um modelo de definição de fluxo de trabalho do aplicativo lógico (arquivo. logicapp. JSON) para que você possa começar a criar o fluxo de trabalho do aplicativo lógico.

   ![Nova definição de fluxo de trabalho do aplicativo lógico](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. No arquivo de modelo de definição de fluxo de trabalho do aplicativo lógico, comece a criar a definição de fluxo de trabalho do aplicativo lógico. Para referência técnica, consulte o [esquema de linguagem de definição de fluxo de trabalho para aplicativos lógicos do Azure](../logic-apps/logic-apps-workflow-definition-language.md).

   Aqui está um exemplo de definição de lógica. Normalmente, os elementos JSON aparecem em ordem alfabética dentro de cada seção, mas este exemplo mostra esses elementos aproximadamente na ordem em que as etapas do aplicativo lógico aparecem no designer.

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

1. Quando tiver terminado, salve o arquivo de definição do aplicativo lógico. Quando Visual Studio Code solicita que você confirme o carregamento da definição do aplicativo lógico para sua assinatura do Azure, escolha **carregar**.

   ![Carregar seu novo aplicativo lógico](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Depois que Visual Studio Code publica seu aplicativo lógico no Azure, você pode encontrar seu aplicativo agora ao vivo e em execução no portal do Azure. 

   ![Aplicativo lógico publicado no portal do Azure](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Editar aplicativo lógico

Para trabalhar em um aplicativo lógico existente que já está implantado no Azure, você pode abrir o arquivo de definição de fluxo de trabalho do aplicativo em Visual Studio Code.

1. Se você não tiver entrado em sua assinatura do Azure de dentro de Visual Studio Code, siga as etapas neste artigo para [entrar agora](#sign-in-azure).

1. Na janela do Azure, em **aplicativos lógicos**, expanda sua assinatura do Azure e selecione o aplicativo lógico desejado. 

1. No menu do seu aplicativo lógico, selecione **abrir no editor**. Ou, ao lado do nome do aplicativo lógico, escolha o ícone Editar.

   ![Abrir editor para aplicativo lógico existente](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code abre o arquivo. logicapp. JSON para a definição de fluxo de trabalho do aplicativo lógico.

   ![Definição de fluxo de trabalho do aplicativo lógico aberto](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Faça as alterações na definição do aplicativo lógico.

1. Quando tiver terminado, guarde as alterações.

1. Quando Visual Studio Code solicita que você atualize sua definição de aplicativo lógico em sua assinatura do Azure, escolha **carregar**. 

   ![Carregar suas edições](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> * [Criar aplicativos lógicos com o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
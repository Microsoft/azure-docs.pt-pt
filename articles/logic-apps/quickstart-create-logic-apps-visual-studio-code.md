---
title: Automatizar tarefas com o Visual Studio Code-aplicativos lógicos do Azure
description: Criar ou Editar definições JSON subjacentes de aplicativo lógico usando Visual Studio Code (VS Code)
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
ms.date: 10/25/2019
ms.openlocfilehash: 96551d3c7f0d37bc7ed8696666b130fa83dae41b
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063648"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Início rápido: criar e gerenciar definições de fluxo de trabalho de aplicativo lógico usando Visual Studio Code

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e Visual Studio Code, você pode criar e gerenciar aplicativos lógicos que ajudam a automatizar tarefas, fluxos de trabalho e processos para integrar aplicativos, dados, sistemas e serviços entre organizações e empresas. Este guia de início rápido mostra como você pode criar e editar as definições de fluxo de trabalho subjacentes, que usam JavaScript Object Notation (JSON), para aplicativos lógicos por meio de uma experiência baseada em código. Você também pode trabalhar em aplicativos lógicos existentes que já estão implantados no Azure.

Embora seja possível executar essas mesmas tarefas no [portal do Azure](https://portal.azure.com) e no Visual Studio, você pode começar mais rapidamente em Visual Studio Code quando já estiver familiarizado com as definições de aplicativos lógicos e quiser trabalhar diretamente no código. Por exemplo, você pode desabilitar, habilitar, excluir e atualizar aplicativos lógicos já criados. Além disso, você pode trabalhar em aplicativos lógicos e contas de integração de qualquer plataforma de desenvolvimento onde o Visual Studio Code for executado, como Linux, Windows e Mac.

Para este artigo, você pode criar o mesmo aplicativo lógico neste [início rápido](../logic-apps/quickstart-create-first-logic-app-workflow.md), que se concentra mais nos conceitos básicos. No Visual Studio Code, o aplicativo lógico é semelhante a este exemplo:

![Exemplo de definição de fluxo de trabalho do aplicativo lógico](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

Antes de começar, certifique-se de que dispõe destes itens:

* Se você não tiver uma conta e assinatura do Azure, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [definições de fluxo de trabalho do aplicativo lógico](../logic-apps/logic-apps-workflow-definition-language.md) e sua estrutura, conforme descrito com JSON

  Se você for novo em aplicativos lógicos, experimente este guia de [início rápido](../logic-apps/quickstart-create-first-logic-app-workflow.md), que cria seus primeiros aplicativos lógicos na portal do Azure e concentra-se mais nos conceitos básicos.

* Acesso à Web para entrar no Azure e sua assinatura do Azure

* Transfira e instale estas ferramentas, se ainda não as tiver:

  * [Visual Studio Code versão 1.25.1 ou posterior](https://code.visualstudio.com/), que é gratuita

  * Extensão de Visual Studio Code para aplicativos lógicos do Azure

    Você pode baixar e instalar essa extensão do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) ou diretamente de dentro Visual Studio Code. Certifique-se de recarregar Visual Studio Code após a instalação.

    ![Localizar "extensão de Visual Studio Code para aplicativos lógicos do Azure"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Para verificar se a extensão foi instalada corretamente, selecione o ícone do Azure que aparece na barra de ferramentas Visual Studio Code.

    ![Confirmar a extensão instalada corretamente](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Para obter mais informações, consulte [Marketplace de extensão](https://code.visualstudio.com/docs/editor/extension-gallery). Para contribuir com a versão de código-fonte aberto desta extensão, visite a [extensão de aplicativos lógicos do Azure para Visual Studio Code no GitHub](https://github.com/Microsoft/vscode-azurelogicapps).

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Abra o Visual Studio Code. Na barra de ferramentas Visual Studio Code, selecione o ícone do Azure.

   ![Selecione o ícone do Azure na barra de ferramentas Visual Studio Code](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. Na janela do Azure, em **aplicativos lógicos**, selecione **entrar no Azure**. Quando a página de entrada da Microsoft solicitar, entre com sua conta do Azure.

   ![Selecione "entrar no Azure"](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Se a entrada demorar mais do que o normal, Visual Studio Code solicitará que você entre por meio de um site de autenticação da Microsoft fornecendo um código de dispositivo. Para entrar com o código, selecione usar o **código do dispositivo**.

      ![Em vez disso, continue com o código do dispositivo](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. Para copiar o código, selecione **copiar & abrir**.

      ![Copiar o código para entrar no Azure](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Para abrir uma nova janela do navegador e continuar para o site de autenticação, selecione **Abrir link**.

      ![Confirmar a abertura de um navegador e ir para o site de autenticação](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. Na página **entrar na sua conta** , insira seu código de autenticação e selecione **Avançar**.

      ![Insira o código de autenticação para entrar no Azure](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Selecione sua conta do Azure. Depois de entrar, você pode fechar o navegador e retornar ao Visual Studio Code.

   No painel do Azure, as seções **aplicativos lógicos** e **contas de integração** agora mostram as assinaturas do Azure que estão associadas à sua conta. No entanto, se você não vir as assinaturas esperadas ou se as seções mostrarem muitas assinaturas, siga estas etapas:

   1. Mova o ponteiro do mouse sobre o rótulo de **aplicativos lógicos** . Quando a barra de ferramentas for exibida, selecione **selecionar assinaturas** (ícone de filtro).

      ![Localizar ou filtrar assinaturas do Azure](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. Na lista exibida, selecione as assinaturas que você deseja que sejam exibidas.

1. Em **aplicativos lógicos**, selecione a assinatura desejada. O nó de assinatura expande e mostra todos os aplicativos lógicos existentes nessa assinatura.

   ![Selecione sua assinatura do Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > Em **contas de integração**, selecionar sua assinatura mostra todas as contas de integração existentes nessa assinatura.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Criar novo aplicativo lógico

1. Se você ainda não tiver entrado em sua conta e assinatura do Azure, de dentro Visual Studio Code, siga as [etapas anteriores para entrar agora](#sign-in-azure).

1. No Visual Studio Code, em **aplicativos lógicos**, abra o menu de atalho da sua assinatura e selecione **criar aplicativo lógico**.

   ![No menu assinatura, selecione "criar aplicativo lógico"](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   Uma lista é exibida e mostra os grupos de recursos do Azure em sua assinatura.

1. Na lista grupo de recursos, selecione **criar um novo grupo de recursos** ou um grupo de recursos existente. Para este exemplo, crie um novo grupo de recursos.

   ![Criar um novo grupo de recursos do Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Forneça um nome para o grupo de recursos do Azure e pressione ENTER.

   ![Forneça o nome para o grupo de recursos do Azure](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Selecione a região do Azure onde você deseja salvar os metadados do aplicativo lógico.

   ![Selecione o local do Azure para salvar os metadados do aplicativo lógico](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Forneça um nome para seu aplicativo lógico e pressione Enter.

   ![Forneça o nome para seu aplicativo lógico](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Na janela do Azure, em sua assinatura do Azure, seu aplicativo lógico novo e em branco é exibido. O Visual Studio Code também abre um arquivo JSON (. logicapp. JSON), que inclui uma definição de fluxo de trabalho de esqueleto para seu aplicativo lógico. Agora você pode iniciar a criação manual da definição de fluxo de trabalho do aplicativo lógico neste arquivo JSON. Para obter referência técnica sobre a estrutura e a sintaxe de uma definição de fluxo de trabalho, consulte o [esquema de linguagem de definição de fluxo de trabalho para aplicativos lógicos do Azure](../logic-apps/logic-apps-workflow-definition-language.md).

   ![Arquivo JSON de definição de fluxo de trabalho do aplicativo lógico vazio](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Por exemplo, aqui está uma definição de fluxo de trabalho de aplicativo lógico de exemplo, que começa com um gatilho de RSS e uma ação do Outlook do Office 365. Normalmente, os elementos JSON aparecem em ordem alfabética em cada seção. No entanto, este exemplo mostra esses elementos aproximadamente na ordem em que as etapas do aplicativo lógico aparecem no designer.

   > [!IMPORTANT]
   > Se você quiser reutilizar essa definição de aplicativo lógico de exemplo, precisará de uma conta institucional do Office 365, por exemplo, @fabrikam.com. Certifique-se de substituir o endereço de email fictício pelo seu próprio endereço de email. Para usar um conector de email diferente, como Outlook.com ou Gmail, substitua a ação de `Send_an_email_action` por uma ação semelhante disponível de um [conector de email com suporte dos aplicativos lógicos do Azure](../connectors/apis-list.md).

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
         "Send_an_email_(V2)": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "<p>Title: @{triggerBody()?['title']}<br>\n<br>\nDate published: @{triggerBody()?['updatedOn']}<br>\n<br>\nLink: @{triggerBody()?['primaryLink']}</p>",
                  "Subject": "RSS item: @{triggerBody()?['title']}",
                  "To": "sophia-owen@fabrikam.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/v2/Mail"
            }
         }
      },
      "outputs": {}
   }
   ```

1. Quando terminar, salve a definição de fluxo de trabalho do aplicativo lógico. (Menu Arquivo > salvar ou pressione Ctrl + S)

1. Quando for solicitado a carregar seu aplicativo lógico para sua assinatura do Azure, selecione **carregar**.

   Esta etapa publica seu aplicativo lógico no [portal do Azure](https://portal.azure.com), que e torna sua lógica ativa e em execução no Azure.

   ![Carregar novo aplicativo lógico para sua assinatura do Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Exibir aplicativo lógico no designer

No Visual Studio Code, você pode abrir seu aplicativo lógico no modo de exibição de design somente leitura. Embora você não possa editar seu aplicativo lógico no designer, é possível verificar visualmente o fluxo de trabalho do aplicativo lógico usando a exibição do designer.

Na janela do Azure, em **aplicativos lógicos**, abra o menu de atalho do aplicativo lógico e selecione **abrir no designer**.

O designer somente leitura é aberto em uma janela separada e mostra o fluxo de trabalho do aplicativo lógico, por exemplo:

![Exibir aplicativo lógico no designer somente leitura](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Exibir no portal do Azure

Para examinar seu aplicativo lógico no portal do Azure, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) usando a mesma conta do Azure e assinatura associada ao seu aplicativo lógico.

1. Na caixa de pesquisa do portal do Azure, insira o nome do seu aplicativo lógico. Na lista de resultados, selecione seu aplicativo lógico.

   ![Seu novo aplicativo lógico no portal do Azure](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Desabilitar ou habilitar o aplicativo lógico

No Visual Studio Code, se você editar um aplicativo lógico publicado e salvar suas alterações, você *substituirá* seu aplicativo já implantado. Para evitar interromper seu aplicativo lógico em produção e minimizar a interrupção, desative primeiro seu aplicativo lógico. Em seguida, você pode reativar seu aplicativo lógico depois de confirmar que seu aplicativo lógico ainda funciona.

1. Se você ainda não tiver entrado em sua conta e assinatura do Azure, de dentro Visual Studio Code, siga as [etapas anteriores para entrar agora](#sign-in-azure).

1. Na janela do Azure, em **aplicativos lógicos**, expanda sua assinatura do Azure para que você possa exibir todos os aplicativos lógicos nessa assinatura.

   1. Para desabilitar o aplicativo lógico que você deseja, abra o menu do aplicativo lógico e selecione **desabilitar**.

      ![Desabilitar seu aplicativo lógico](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. Quando você estiver pronto para reativar seu aplicativo lógico, abra o menu do aplicativo lógico e selecione **habilitar**.

      ![Habilitar seu aplicativo lógico](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Editar aplicativo lógico implantado

No Visual Studio Code, você pode abrir e editar a definição de fluxo de trabalho para um aplicativo lógico já implantado no Azure.

> [!IMPORTANT] 
> Antes de editar um aplicativo lógico em execução ativamente na produção, evite o risco de interromper o aplicativo lógico e minimizar a interrupção [desabilitando primeiro seu aplicativo lógico](#disable-enable-logic-app).

1. Se você ainda não tiver entrado em sua conta e assinatura do Azure, de dentro Visual Studio Code, siga as [etapas anteriores para entrar agora](#sign-in-azure).

1. Na janela do Azure, em **aplicativos lógicos**, expanda sua assinatura do Azure e selecione o aplicativo lógico desejado.

1. Abra o menu do aplicativo lógico e selecione **abrir no editor**. Ou, ao lado do nome do aplicativo lógico, selecione o ícone Editar.

   ![Abrir editor para aplicativo lógico existente](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code abre o arquivo. logicapp. JSON em sua pasta temporária local para que você possa exibir a definição de fluxo de trabalho do aplicativo lógico.

   ![Exibir definição de fluxo de trabalho para o aplicativo lógico publicado](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Faça as alterações na definição de fluxo de trabalho do aplicativo lógico.

1. Quando tiver terminado, guarde as alterações. (Menu Arquivo > salvar ou pressione Ctrl + S)

1. Quando for solicitado a carregar suas alterações e *substituir* seu aplicativo lógico existente no portal do Azure, selecione **carregar**.

   Esta etapa publica suas atualizações para seu aplicativo lógico no [portal do Azure](https://portal.azure.com).

   ![Carregar edições na definição do aplicativo lógico no Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Exibir ou promover outras versões

No Visual Studio Code, você pode abrir e examinar as versões anteriores para seu aplicativo lógico. Você também pode promover uma versão anterior para a versão atual.

> [!IMPORTANT] 
> Antes de alterar um aplicativo lógico em execução ativamente na produção, evite o risco de interromper o aplicativo lógico e minimizar a interrupção [desabilitando primeiro seu aplicativo lógico](#disable-enable-logic-app).

1. Na janela do Azure, em **aplicativos lógicos**, expanda sua assinatura do Azure para que você possa exibir todos os aplicativos lógicos nessa assinatura.

1. Em sua assinatura, expanda seu aplicativo lógico e expanda **versões**.

   A lista de **versões** mostra as versões anteriores do seu aplicativo lógico, se existir.

   ![As versões anteriores do seu aplicativo lógico](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Para exibir uma versão anterior, selecione uma das etapas:

   * Para exibir a definição de JSON, em **versões**, selecione o número de versão para essa definição. Ou abra o menu de atalho dessa versão e selecione **abrir no editor**.

     Um novo arquivo é aberto no computador local e mostra a definição de JSON da versão.

   * Para exibir a versão no modo de exibição de designer somente leitura, abra o menu de atalho da versão e selecione **abrir no designer**.

1. Para promover uma versão anterior à versão atual, siga estas etapas:

   1. Em **versões**, abra o menu de atalho da versão anterior e selecione **promover**.

      ![Promover versão anterior](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Para continuar depois que Visual Studio Code solicitar a confirmação, selecione **Sim**.

      ![Confirmar promoção da versão anterior](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      Visual Studio Code promove a versão selecionada para a versão atual e atribui um novo número à versão promovida. A versão atual anterior agora aparece sob a versão promovida.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar aplicativos lógicos com o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
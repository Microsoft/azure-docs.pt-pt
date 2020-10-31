---
title: Automatizar tarefas e fluxos de trabalho com o Código do Estúdio Visual
description: Criar ou editar definições de fluxo de aplicativos de lógica utilizando o Código do Estúdio Visual (Código VS)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/28/2020
ms.openlocfilehash: 0b777fdef344ce1a60ed00ee46eeaa8cee23b8c0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099193"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Quickstart: Criar e gerir as definições do fluxo de trabalho das aplicações lógicas com o Visual Studio Code

Com [apps Azure Logic](../logic-apps/logic-apps-overview.md) e Visual Studio Code, pode criar e gerir aplicações lógicas que o ajudam a automatizar tarefas, fluxos de trabalho e processos para integrar apps, dados, sistemas e serviços entre organizações e empresas. Este quickstart mostra como pode criar e editar as definições subjacentes do fluxo de trabalho, que utilizam a Notação de Objetos JavaScript (JSON), para aplicações lógicas através de uma experiência baseada em código. Também pode trabalhar em aplicações lógicas existentes que já estão implantadas no Azure.

Embora possa executar essas mesmas tarefas no [portal Azure](https://portal.azure.com) e no Visual Studio, pode começar mais rapidamente no Código do Estúdio Visual quando já está familiarizado com as definições de aplicações lógicas e quer trabalhar diretamente em código. Por exemplo, pode desativar, ativar, eliminar e atualizar aplicações lógicas já criadas. Além disso, pode trabalhar em aplicações lógicas e contas de integração a partir de qualquer plataforma de desenvolvimento onde o Código do Estúdio Visual é executado, como Linux, Windows e Mac.

Para este artigo, pode criar a mesma aplicação lógica a partir deste [quickstart](../logic-apps/quickstart-create-first-logic-app-workflow.md), que se foca mais nos conceitos básicos. Também pode [aprender a criar a aplicação de exemplo no Visual Studio,](quickstart-create-logic-apps-with-visual-studio.md)e aprender a criar e gerir [aplicações através da Interface Azure Command-Line (Azure CLI)](quickstart-logic-apps-azure-cli.md). No Código do Estúdio Visual, a aplicação lógica parece este exemplo:

![Exemplo de definição de fluxo de trabalho de aplicativos de lógica](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

Antes de começar, certifique-se de que dispõe destes itens:

* Se não tiver uma conta Estri e subscrição, [inscreva-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/)

* Conhecimentos [básicos sobre definições lógicas de fluxo de fluxo de aplicações](../logic-apps/logic-apps-workflow-definition-language.md) e sua estrutura como descrito com JSON

  Se é novo em Aplicações Lógicas, experimente este [quickstart](../logic-apps/quickstart-create-first-logic-app-workflow.md), que cria as suas primeiras aplicações lógicas no portal Azure e foca-se mais nos conceitos básicos.

* Acesso à web para iniciar sessão no Azure e na sua subscrição do Azure

* Transfira e instale estas ferramentas, se ainda não as tiver:

  * [Visual Studio Code versão 1.25.1 ou posterior](https://code.visualstudio.com/), que é gratuito

  * Extensão visual studio code para apps Azure Logic

    Você pode baixar e instalar esta extensão a partir do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) ou diretamente do Código do Estúdio Visual. Certifique-se de que recarrega o Código do Estúdio Visual após a instalação.

    ![Encontre "Extensão visual studio code para apps Azure Logic"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Para verificar se a extensão instalada corretamente, selecione o ícone Azure que aparece na sua barra de ferramentas Visual Studio Code.

    ![Confirme a extensão corretamente instalada](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Para mais informações, consulte [o Mercado de Extensão.](https://code.visualstudio.com/docs/editor/extension-gallery) Para contribuir para a versão aberta desta extensão, visite a [extensão Azure Logic Apps para o Código do Estúdio Visual no GitHub](https://github.com/Microsoft/vscode-azurelogicapps).

<a name="access-azure"></a>

## <a name="access-azure-from-visual-studio"></a>Access Azure do Visual Studio

1. Abra o Visual Studio Code. Na barra de ferramentas Visual Studio Code, selecione o ícone Azure.

   ![Selecione ícone Azure na barra de ferramentas Visual Studio Code](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. Na janela Azure, em **Aplicações Lógicas,** selecione **Iniciar súm em Azure** . Quando a página de sôs-in da Microsoft o solicitar, inscreva-se na sua conta Azure.

   ![Selecione "Iniciar sação no Azure"](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Se o sôs-sinal demorar mais do que o habitual, o Código do Estúdio Visual pede-lhe para iniciar singagem através de um website de autenticação da Microsoft, fornecendo-lhe um código de dispositivo. Para iniciar s dia de saúde com o código, selecione **Use Device Code** .

      ![Continue com o código do dispositivo em vez](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. Para copiar o código, **selecione Copy & Open** .

      ![Código de cópia para a azure iniciar sedura](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Para abrir uma nova janela do navegador e continuar no site de autenticação, selecione **Open Link** .

      ![Confirme a abertura de um navegador e vá para o site de autenticação](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. Na página **'Iniciar sú' na página da sua conta,** insira o seu código de autenticação e selecione **Seguinte** .

      ![Introduza o código de autenticação para o Azure iniciar sação](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Selecione a sua conta Azure. Depois de iniciar sôm, pode fechar o seu navegador e voltar ao Código do Estúdio Visual.

   No painel Azure, as secções **De Aplicações Lógicas** e **Contas de Integração** mostram agora as subscrições do Azure que estão associadas à sua conta. No entanto, se não vir as subscrições que espera, ou se as secções mostrarem demasiadas subscrições, siga estes passos:

   1. Mova o ponteiro sobre a etiqueta **De Aplicações Lógicas.** Quando a barra de ferramentas aparecer, **selecione 'Selecionar Subscrições'** (ícone do filtro).

      ![Localizar ou filtrar assinaturas Azure](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. Na lista que aparece, selecione as subscrições que pretende aparecer.

1. Em **Aplicações Lógicas,** selecione a subscrição que deseja. O nó de subscrição expande-se e mostra quaisquer aplicações lógicas que existam nessa subscrição.

   ![selecione a subscrição do Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > Em **Contas de Integração,** a seleção da sua subscrição mostra quaisquer contas de integração existentes nessa subscrição.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Criar nova aplicação lógica

1. Se ainda não se inscreveu na sua conta Azure e subscrição a partir do Código do Estúdio Visual, siga os [passos anteriores para iniciar seduca.](#access-azure)

1. No código Visual Studio, em **Aplicações Lógicas,** abra o menu de atalho da sua subscrição e selecione **Create Logic App** .

   ![A partir do menu de subscrição, selecione "Create Logic App"](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   Uma lista aparece e mostra quaisquer grupos de recursos Azure na sua subscrição.

1. A partir da lista de grupos de recursos, selecione **criar um novo grupo de recursos** ou um grupo de recursos existente. Para este exemplo, criar um novo grupo de recursos.

   ![Criar um novo grupo de recursos do Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Forneça um nome para o seu grupo de recursos Azure e prima ENTER.

   ![Forneça o nome do seu grupo de recursos Azure](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Selecione a região Azure onde pretende guardar os metadados da sua aplicação lógica.

   ![Selecione a localização Azure para guardar metadados de aplicações lógicas](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Forneça um nome para a sua aplicação lógica e prima Enter.

   ![Forneça o nome para a sua aplicação lógica](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Na janela Azure, sob a subscrição do Azure, aparece a sua nova aplicação lógica em branco. O Visual Studio Code também abre um ficheiro JSON (.logicapp.json), que inclui uma definição de fluxo de trabalho de esqueleto para a sua aplicação lógica. Agora pode começar a autorizar manualmente a definição de fluxo de trabalho da sua aplicação lógica neste ficheiro JSON. Para obter referência técnica sobre a estrutura e sintaxe para uma definição de fluxo de trabalho, consulte o [esquema de linguagem de definição de fluxo de trabalho para apps Azure Logic](../logic-apps/logic-apps-workflow-definition-language.md).

   ![Vazio lógica lógico aplicativo de trabalho de definição JSON arquivo](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Por exemplo, aqui está uma definição de fluxo de fluxo de aplicação lógica de amostra, que começa com um gatilho RSS e uma ação Office 365 Outlook. Normalmente, os elementos JSON aparecem alfabeticamente em cada secção. No entanto, esta amostra mostra estes elementos aproximadamente na ordem que os passos da aplicação lógica aparecem no designer.

   > [!IMPORTANT]
   > Se quiser reutilizar esta definição de aplicação lógica de amostra, precisa de uma conta organizacional, por @fabrikam.com exemplo. Certifique-se de que substitui o endereço de e-mail fictício pelo seu próprio endereço de e-mail. Para utilizar um conector de e-mail diferente, como Outlook.com ou Gmail, substitua a `Send_an_email_action` ação por uma ação semelhante disponível a partir de um conector de [e-mail que é suportado por Azure Logic Apps](../connectors/apis-list.md).
   >
   > Se quiser utilizar o conector do Gmail, apenas as contas de negócios da G-Suite podem utilizar este conector sem restrições em aplicações lógicas. 
   > Se tiver uma conta de consumo do Gmail, pode utilizar este conector apenas com serviços específicos aprovados pela Google, ou pode [criar uma aplicação para clientes da Google para utilizar para autenticação com o seu conector Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) 
   > Para obter mais informações, consulte [as políticas de segurança de dados e privacidade para conectores google em Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

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

1. Quando terminar, guarde a definição de fluxo de trabalho da sua aplicação lógica. (Menu de arquivo > Guardar ou pressionar Ctrl+S)

1. Quando lhe for solicitado que faça o upload da sua aplicação lógica para a sua subscrição Azure, selecione **Upload** .

   Este passo publica a sua aplicação lógica para o [portal Azure](https://portal.azure.com), que e faz a sua lógica ao vivo e a funcionar em Azure.

   ![Faça o upload de uma nova app lógica para a sua subscrição do Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Ver aplicativo de lógica no designer

No Código do Estúdio Visual, pode abrir a sua aplicação lógica na vista de design apenas para leitura. Embora não possa editar a sua aplicação lógica no designer, pode verificar visualmente o fluxo de trabalho da sua aplicação lógica utilizando a vista do designer.

Na janela Azure, em **Aplicações Lógicas,** abra o menu de atalho da sua aplicação lógica e selecione **Open in Designer** .

O designer só de leitura abre numa janela separada e mostra o fluxo de trabalho da sua aplicação lógica, por exemplo:

![Ver aplicativo de lógica no designer apenas de leitura](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Ver no portal do Azure

Para rever a sua aplicação lógica no portal Azure, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando a mesma conta Azure e subscrição que está associada à sua aplicação lógica.

1. Na caixa de pesquisa do portal Azure, insira o nome das suas aplicações lógicas. Na lista de resultados, selecione a sua aplicação lógica.

   ![A sua nova aplicação lógica no portal Azure](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Desativar ou ativar aplicação lógica

No Código do Estúdio Visual, se editar uma aplicação lógica publicada e guardar as suas alterações, *substitui* a sua aplicação já implementada. Para evitar quebrar a sua aplicação lógica na produção e minimizar a perturbação, desative primeiro a sua aplicação lógica. Pode então reativar a sua aplicação lógica depois de ter confirmado que a sua aplicação lógica ainda funciona.

1. Se ainda não se inscreveu na sua conta Azure e subscrição a partir do Código do Estúdio Visual, siga os [passos anteriores para iniciar seduca.](#access-azure)

1. Na janela Azure, no âmbito das **Logic Apps,** expanda a subscrição do Azure para que possa ver todas as aplicações lógicas nessa subscrição.

   1. Para desativar a aplicação lógica que pretende, abra o menu da aplicação lógica e selecione **Disable** .

      ![Desativar a sua aplicação lógica](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. Quando estiver pronto para reativar a sua aplicação lógica, abra o menu da aplicação lógica e selecione **Enable** .

      ![Ativar a sua aplicação lógica](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Editar aplicativo de lógica implementado

No Código do Estúdio Visual, pode abrir e editar a definição de fluxo de trabalho para uma aplicação lógica já implantada em Azure.

> [!IMPORTANT] 
> Antes de editar uma aplicação lógica em execução ativa na produção, evite o risco de quebrar essa aplicação lógica e minimizar a perturbação [desativando primeiro a sua aplicação lógica.](#disable-enable-logic-app)

1. Se ainda não se inscreveu na sua conta Azure e subscrição a partir do Código do Estúdio Visual, siga os [passos anteriores para iniciar seduca.](#access-azure)

1. Na janela Azure, em **Aplicações Lógicas,** expanda a subscrição do Azure e selecione a aplicação lógica que pretende.

1. Abra o menu da sua aplicação lógica e selecione **Open in Editor** . Ou, ao lado do nome da sua aplicação lógica, selecione o ícone de edição.

   ![Editor aberto para app lógica existente](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   O Código do Estúdio Visual abre o .logicapp.jsficheiro na sua pasta temporária local para que possa ver a definição de fluxo de trabalho da sua aplicação lógica.

   ![Ver definição de fluxo de trabalho para aplicação lógica publicada](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Faça as suas alterações na definição de fluxo de trabalho da aplicação lógica.

1. Quando tiver terminado, guarde as alterações. (Menu de arquivo > Guardar ou pressionar Ctrl+S)

1. Quando for solicitado para fazer o upload das suas alterações e *substituir* a sua aplicação lógica existente no portal Azure, selecione **Upload** .

   Este passo publica as suas atualizações para a sua aplicação lógica no [portal Azure.](https://portal.azure.com)

   ![Faça upload de edições para definição de aplicativo lógica em Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Ver ou promover outras versões

No Código do Estúdio Visual, pode abrir e rever as versões anteriores para a sua aplicação lógica. Também pode promover uma versão anterior para a versão atual.

> [!IMPORTANT] 
> Antes de alterar uma aplicação lógica em execução ativa na produção, evite o risco de quebrar essa aplicação lógica e minimizar a perturbação [desativando primeiro a sua aplicação lógica.](#disable-enable-logic-app)

1. Na janela Azure, no âmbito das **Logic Apps,** expanda a subscrição do Azure para que possa ver todas as aplicações lógicas nessa subscrição.

1. Sob a sua subscrição, expanda a sua aplicação lógica e expanda as **versões.**

   A lista **de Versões** mostra as versões anteriores da sua aplicação lógica, se existir alguma.

   ![As versões anteriores da sua aplicação lógica](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Para ver uma versão anterior, selecione qualquer passo:

   * Para visualizar a definição JSON, em **Versões,** selecione o número da versão para essa definição. Ou, abra o menu de atalho dessa versão e selecione **Open in Editor** .

     Um novo ficheiro abre no seu computador local e mostra a definição JSON da versão.

   * Para ver a versão na vista do designer apenas para leitura, abra o menu de atalho da versão e selecione **Open in Designer** .

1. Para promover uma versão anterior à versão atual, siga estes passos:

   1. Em **Versões,** abra o menu de atalho da versão anterior e selecione **Promover** .

      ![Promover versão anterior](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Para continuar depois do Código do Estúdio Visual pedir-lhe confirmação, selecione **Sim** .

      ![Confirmar a promoção da versão anterior](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      O Visual Studio Code promove a versão selecionada para a versão atual e atribui um novo número à versão promovida. A versão anteriormente atual aparece agora na versão promovida.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar aplicações lógicas stateful ou apátridas no Código do Estúdio Visual (Preview)](../logic-apps/create-stateful-stateless-workflows-visual-studio-code.md)

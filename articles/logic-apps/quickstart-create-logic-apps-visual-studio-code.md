---
title: Automatizar tarefas com código de estúdio visual
description: Criar ou editar aplicação lógica subjacente às definições jSON utilizando o Código do Estúdio Visual (Código VS)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/25/2019
ms.openlocfilehash: 3dccb596be2d97dd0f38d680537913a76ce09e93
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147070"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Quickstart: Criar e gerir definições lógicas de fluxo de fluxo de aplicações utilizando o Código do Estúdio Visual

Com [aplicações da Azure Logic](../logic-apps/logic-apps-overview.md) e Código de Estúdio Visual, pode criar e gerir aplicações lógicas que o ajudam a automatizar tarefas, fluxos de trabalho e processos para integrar apps, dados, sistemas e serviços entre organizações e empresas. Este quickstart mostra como pode criar e editar as definições subjacentes ao fluxo de trabalho, que utilizam a Notação de Objetos JavaScript (JSON), para aplicações lógicas através de uma experiência baseada em códigos. Também pode trabalhar em aplicações lógicas existentes que já estão implantadas para o Azure.

Embora possa executar essas mesmas tarefas no [portal Azure](https://portal.azure.com) e no Visual Studio, pode começar mais rapidamente no Visual Studio Code quando já está familiarizado com as definições de aplicações lógicas e quer trabalhar diretamente em código. Por exemplo, pode desativar, ativar, excluir e atualizar aplicações lógicas já criadas. Além disso, pode trabalhar em aplicações lógicas e contas de integração a partir de qualquer plataforma de desenvolvimento onde o Código visual studio é executado, como Linux, Windows e Mac.

Para este artigo, pode criar a mesma aplicação lógica a partir deste [quickstart](../logic-apps/quickstart-create-first-logic-app-workflow.md), que se foca mais nos conceitos básicos. No Visual Studio Code, a aplicação lógica parece este exemplo:

![Definição de fluxo de fluxo de aplicativológica de exemplo](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

Antes de começar, certifique-se de que dispõe destes itens:

* Se não tiver uma conta Azure e uma subscrição, [inscreva-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

* Conhecimentos básicos sobre [definições lógicas](../logic-apps/logic-apps-workflow-definition-language.md) de fluxo de fluxo de aplicações e sua estrutura como descrito com JSON

  Se é novo em Aplicações Lógicas, experimente este [quickstart](../logic-apps/quickstart-create-first-logic-app-workflow.md), que cria as suas primeiras aplicações lógicas no portal Azure e foca-se mais nos conceitos básicos.

* Acesso à web para iniciar sessão no Azure e na sua subscrição azure

* Transfira e instale estas ferramentas, se ainda não as tiver:

  * [Visual Studio Code versão 1.25.1 ou mais tarde](https://code.visualstudio.com/), que é gratuito

  * Extensão do Código do Estúdio Visual para Aplicações Lógicas Azure

    Pode descarregar e instalar esta extensão a partir do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) ou diretamente a partir do Código visual do Estúdio. Certifique-se de que recarrega o Código do Estúdio Visual após a instalação.

    ![Encontre "Extensão do Código do Estúdio Visual para Aplicações Lógicas Azure"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Para verificar se a extensão foi instalada corretamente, selecione o ícone Azure que aparece na sua barra de ferramentas Visual Studio Code.

    ![Confirmar a extensão corretamente instalada](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Para mais informações, consulte [O Mercado de Extensão](https://code.visualstudio.com/docs/editor/extension-gallery). Para contribuir para a versão open-source desta extensão, visite a extensão das [Aplicações Lógicas Azure para o Código do Estúdio Visual no GitHub](https://github.com/Microsoft/vscode-azurelogicapps).

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Abra o Visual Studio Code. Na barra de ferramentas Visual Studio Code, selecione o ícone Azure.

   ![Selecione ícone Azure na barra de ferramentas Visual Studio Code](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. Na janela Azure, sob **Aplicações Lógicas,** selecione **Iniciar sessão no Azure**. Quando a página de entrada da Microsoft lhe pedir, inscreva-se na sua conta Azure.

   ![Selecione "Iniciar sessão no Azure"](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Se o iniciar sessão demorar mais do que o habitual, o Visual Studio Code pede-lhe que aceda através de um website de autenticação da Microsoft, fornecendo-lhe um código de dispositivo. Para iniciar sessão com o código, selecione **Use código do dispositivo**.

      ![Continue com o código do dispositivo em vez disso](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. Para copiar o código, selecione **Copy & Open**.

      ![Código de cópia para sinal de Azure](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Para abrir uma nova janela do navegador e continuar no site de autenticação, selecione **Open Link**.

      ![Confirme a abertura de um navegador e vá para o site de autenticação](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. No **'Iniciar sessão' na página da sua conta,** introduza o código de autenticação e selecione **Next**.

      ![Introduza código de autenticação para o sinal de azure](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Selecione a sua conta Azure. Depois de iniciar sessão, pode fechar o seu navegador e voltar ao Visual Studio Code.

   No painel Azure, as secções **de Aplicações Lógicas** e Contas de **Integração** mostram agora as subscrições do Azure que estão associadas à sua conta. No entanto, se não vir as subscrições que espera, ou se as secções mostrarem muitas subscrições, siga estes passos:

   1. Mova o seu ponteiro sobre a etiqueta **Apps Lógicas.** Quando a barra de ferramentas aparecer, selecione **Selecione Assinaturas (ícone** de filtro).

      ![Localizar ou filtrar assinaturas Azure](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. A partir da lista que aparece, selecione as subscrições que pretende aparecer.

1. Em **Aplicações Lógicas,** selecione a subscrição que deseja. O nó de subscrição expande-se e mostra quaisquer aplicações lógicas que existam nessa subscrição.

   ![selecione a subscrição do Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > Em **Contas integrativas,** a seleção da sua subscrição mostra quaisquer contas de integração que existam nessa subscrição.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Criar uma nova aplicação lógica

1. Se ainda não assinou a sua conta Azure e a sua subscrição a partir de dentro do Visual Studio Code, siga os [passos anteriores para iniciar sessão agora](#sign-in-azure).

1. No código Visual Studio, em **Aplicações Lógicas,** abra o menu de atalho da sua subscrição e selecione **Create Logic App**.

   ![A partir do menu de subscrição, selecione "Create Logic App"](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   Uma lista aparece e mostra quaisquer grupos de recursos Azure na sua subscrição.

1. A partir da lista de grupos de recursos, selecione **criar um novo grupo** de recursos ou um grupo de recursos existente. Para este exemplo, crie um novo grupo de recursos.

   ![Criar um novo grupo de recursos do Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Forneça um nome para o seu grupo de recursos Azure e prima ENTER.

   ![Forneça nome para o seu grupo de recursos Azure](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Selecione a região azure onde pretende guardar os metadados da sua aplicação lógica.

   ![Selecione localização Azure para salvar metadados de aplicações lógicas](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Forneça um nome para a sua aplicação lógica e prima Enter.

   ![Forneça nome para a sua aplicação lógica](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Na janela Azure, sob a sua subscrição Azure, aparece a sua nova e em branco aplicação lógica. O Visual Studio Code também abre um ficheiro JSON (.logicapp.json), que inclui uma definição de fluxo de trabalho de esqueleto para a sua aplicação lógica. Agora pode começar a autorizar manualmente a definição de fluxo de trabalho da sua aplicação lógica neste ficheiro JSON. Para referência técnica sobre a estrutura e sintaxe para uma definição de fluxo de trabalho, consulte o esquema linguístico de definição de fluxo de [trabalho para aplicações lógicas azure](../logic-apps/logic-apps-workflow-definition-language.md).

   ![Arquivo JSON de fluxo de aplicativo sintetísco](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Por exemplo, aqui está uma definição de fluxo de fluxo de aplicações lógica de amostra, que começa com um gatilho RSS e uma ação do Office 365 Outlook. Normalmente, os elementos JSON aparecem alfabeticamente em cada secção. No entanto, esta amostra mostra estes elementos aproximadamente na ordem que os passos da aplicação lógica aparecem no designer.

   > [!IMPORTANT]
   > Se quiser reutilizar esta definição de aplicação lógica de amostra, precisa @fabrikam.comde uma conta organizacional do Office 365, por exemplo, . Certifique-se de que substitui o endereço de e-mail fictício pelo seu próprio endereço de e-mail. Para utilizar um conector de e-mail `Send_an_email_action` diferente, como Outlook.com ou Gmail, substitua a ação por uma ação semelhante disponível a partir de um [conector de e-mail que é suportado por Aplicações lógicas Azure](../connectors/apis-list.md).
   >
   > Se pretender utilizar o conector Gmail, apenas as contas de negócio g-Suite podem utilizar este conector sem restrições em aplicações lógicas. 
   > Se tiver uma conta de consumo do Gmail, pode utilizar este conector apenas com serviços específicos aprovados pela Google, ou pode criar uma aplicação de [cliente da Google para usar para autenticação com o seu conector Gmail](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). 
   > Para mais informações, consulte as políticas de [segurança e privacidade dos dados para os conectores da Google em Aplicações Lógicas Azure](../connectors/connectors-google-data-security-privacy-policy.md).

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

1. Quando terminar, guarde a definição de fluxo de trabalho da sua aplicação lógica. (Menu de ficheiros > Guardar ou prima Ctrl+S)

1. Quando lhe for solicitado que carregue a sua aplicação lógica para a sua subscrição Azure, selecione **Upload**.

   Este passo publica a sua aplicação lógica para o [portal Azure](https://portal.azure.com), que e faz a sua lógica viver e funcionar em Azure.

   ![Faça upload de nova aplicação lógica para a sua subscrição do Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Ver aplicativo lógico em designer

No Visual Studio Code, pode abrir a sua aplicação lógica apenas para ler. Embora não possa editar a sua aplicação lógica no designer, pode verificar visualmente o fluxo de trabalho da sua aplicação lógica utilizando a visão do designer.

Na janela Azure, sob **aplicações lógicas,** abra o menu de atalho da sua aplicação lógica e selecione **Open in Designer.**

O designer só de leitura abre numa janela separada e mostra o fluxo de trabalho da sua aplicação lógica, por exemplo:

![Ver aplicativo lógico em designer só de leitura](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Ver no portal do Azure

Para rever a sua aplicação lógica no portal Azure, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando a mesma conta Azure e subscrição que está associada à sua aplicação lógica.

1. Na caixa de pesquisa do portal Azure, introduza o nome das suas aplicações lógicas. Na lista de resultados, selecione a sua aplicação lógica.

   ![A sua nova aplicação lógica no portal Azure](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Desativar ou ativar aplicação lógica

No Visual Studio Code, se editar uma aplicação lógica publicada e guardar as suas alterações, *substitui* a sua aplicação já implementada. Para evitar quebrar a sua aplicação lógica em produção e minimizar a perturbação, desative primeiro a sua aplicação lógica. Em seguida, pode reativar a sua aplicação lógica depois de ter confirmado que a sua aplicação lógica ainda funciona.

1. Se ainda não assinou a sua conta Azure e a sua subscrição a partir de dentro do Visual Studio Code, siga os [passos anteriores para iniciar sessão agora](#sign-in-azure).

1. Na janela Azure, no âmbito das **Aplicações Lógicas,** expanda a subscrição do Azure para que possa ver todas as aplicações lógicas dessa subscrição.

   1. Para desativar a aplicação lógica que deseja, abra o menu da aplicação lógica e selecione **Disable**.

      ![Desative a sua aplicação lógica](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. Quando estiver pronto para reativar a sua aplicação lógica, abra o menu da aplicação lógica e selecione **Enable**.

      ![Ativar a sua aplicação lógica](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Editar aplicação lógica implementada

No Visual Studio Code, pode abrir e editar a definição de fluxo de trabalho para uma aplicação lógica já implantada no Azure.

> [!IMPORTANT] 
> Antes de editar uma aplicação lógica de execução ativa em produção, evite o risco de quebrar essa aplicação lógica e minimizar a perturbação, [desativando primeiro a sua aplicação lógica](#disable-enable-logic-app).

1. Se ainda não assinou a sua conta Azure e a sua subscrição a partir de dentro do Visual Studio Code, siga os [passos anteriores para iniciar sessão agora](#sign-in-azure).

1. Na janela Azure, sob **aplicações lógicas,** expanda a subscrição do Azure e selecione a aplicação lógica que deseja.

1. Abra o menu da sua aplicação lógica e selecione **Open in Editor.** Ou, ao lado do nome da sua aplicação lógica, selecione o ícone de edição.

   ![Editor aberto para app lógica existente](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   O Visual Studio Code abre o ficheiro .logicapp.json na sua pasta temporária local para que possa ver a definição de fluxo de trabalho da sua aplicação lógica.

   ![Ver definição de fluxo de trabalho para aplicação lógica publicada](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Faça as suas alterações na definição de fluxo de trabalho da aplicação lógica.

1. Quando tiver terminado, guarde as alterações. (Menu de ficheiros > Guardar ou prima Ctrl+S)

1. Quando lhe for solicitado que carregue as suas alterações e *sobreponha* a sua aplicação lógica existente no portal Azure, selecione **Upload**.

   Este passo publica as suas atualizações para a sua aplicação lógica no [portal Azure](https://portal.azure.com).

   ![Upload de edites para definição de aplicações lógicas em Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Ver ou promover outras versões

No Visual Studio Code, pode abrir e rever as versões anteriores para a sua aplicação lógica. Também pode promover uma versão anterior para a versão atual.

> [!IMPORTANT] 
> Antes de alterar uma aplicação lógica de execução ativa em produção, evite o risco de quebrar essa aplicação lógica e minimizar a perturbação, [desativando primeiro a sua aplicação lógica](#disable-enable-logic-app).

1. Na janela Azure, no âmbito das **Aplicações Lógicas,** expanda a subscrição do Azure para que possa ver todas as aplicações lógicas dessa subscrição.

1. Sob a sua subscrição, expanda a sua aplicação lógica e expanda **versões.**

   A lista de **Versões** mostra as versões anteriores da sua aplicação lógica, caso existam.

   ![As versões anteriores da sua aplicação lógica](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Para ver uma versão anterior, selecione qualquer passo:

   * Para ver a definição JSON, em **Versões,** selecione o número da versão para essa definição. Ou, abra o menu de atalho da versão e selecione **Open in Editor.**

     Um novo ficheiro abre no seu computador local e mostra a definição JSON da versão.

   * Para ver a versão na vista de designer apenas para leitura, abra o menu de atalho da versão e selecione **Open in Designer**.

1. Para promover uma versão anterior à versão atual, siga estes passos:

   1. Em **Versões,** abra o menu de atalho da versão anterior e **selecione Promover**.

      ![Promover versão anterior](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Para continuar depois do Código do Estúdio Visual lhe pedir confirmação, selecione **Sim**.

      ![Confirme a promoção da versão anterior](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      O Visual Studio Code promove a versão selecionada para a versão atual e atribui um novo número à versão promovida. A versão anteriormente atual aparece agora na versão promovida.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar aplicativos lógicos com o Estúdio Visual](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
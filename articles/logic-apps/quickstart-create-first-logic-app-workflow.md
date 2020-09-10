---
title: Criar um fluxo de trabalho de integração automatizada
description: Quickstart - Construa o seu primeiro fluxo de trabalho automatizado utilizando apps Azure Logic para a integração de sistemas e soluções de integração de aplicações empresariais (EAI)
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/23/2020
ms.openlocfilehash: c40bec80d9f61cf46221cbfe7dde80f3a7b46f6f
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658311"
---
# <a name="quickstart-create-your-first-automated-integration-workflow-by-using-azure-logic-apps---azure-portal"></a>Quickstart: Crie o seu primeiro fluxo de trabalho de integração automatizada utilizando apps Azure Logic - Portal Azure

Este quickstart introduz os conceitos gerais básicos por trás de como construir o seu primeiro fluxo de trabalho usando [Azure Logic Apps](logic-apps-overview.md), como criar uma aplicação lógica em branco, adicionar um gatilho e uma ação, e depois testar a sua aplicação lógica. Neste quickstart, você constrói uma aplicação lógica que verifica regularmente o feed RSS de um site para novos itens. Se existirem novos itens, a aplicação lógica envia uma mensagem de e-mail para cada item. Quando terminar, a aplicação lógica é semelhante a este fluxo de trabalho a alto nível:

![Arte conceptual mostrando fluxo de trabalho de aplicações lógicas de exemplo de alto nível.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Para este cenário, precisa de uma subscrição do Azure ou [de uma conta Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), uma conta de e-mail de um serviço que é suportado por Azure Logic Apps, como o Office 365 Outlook, Outlook.com ou Gmail. Para outros serviços de e-mail suportados, [reveja a lista de conectores aqui.](/connectors/) Neste exemplo, a aplicação lógica utiliza uma conta de trabalho ou escola. Se utilizar um serviço de e-mail diferente, os passos gerais gerais são os mesmos, mas a interface do utilizador pode diferir ligeiramente.

> [!IMPORTANT]
> Se quiser utilizar o conector do Gmail, apenas as contas de negócios da G-Suite podem utilizar este conector sem restrições em aplicações lógicas. Se tiver uma conta de consumo do Gmail, pode utilizar este conector apenas com serviços específicos aprovados pela Google, ou pode [criar uma aplicação para clientes da Google para utilizar para autenticação com o seu conector Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) Para obter mais informações, consulte [as políticas de segurança de dados e privacidade para conectores google em Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. Na caixa de pesquisa do portal Azure, insira `logic apps` e selecione **Aplicações Lógicas**.

   ![Screenshot mostrando a caixa de pesquisa do portal Azure com "apps lógicas" como o termo de pesquisa e "Logic Apps" como o resultado de pesquisa selecionado.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Na página **'Aplicações lógicas',** selecione **Adicionar**.

   ![Screenshot mostrando lista de aplicações lógicas e botão selecionado, "Add".](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. No painel **da Aplicação Lógica,** forneça detalhes sobre a sua aplicação lógica, como mostrado abaixo.

   ![Screenshot mostrando painel de criação de aplicativos lógicos com detalhes para nova aplicação lógica.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | <*lógica-app-nome*> | O nome da sua aplicação lógica, que deve ser única em todas as regiões e que pode conter apenas letras, números, hífenes `-` (), sublinhados `_` (), parênteses `(` e `)` períodos `.` (). Este exemplo utiliza "My-First-Logic-App". |
   | **Subscrição** | <*Nome de subscrição Azure*> | O seu nome de subscrição Azure |
   | **Grupo de recursos** | <*Nome de grupo Azure-recursos*> | O nome para o [grupo de recursos Azure,](../azure-resource-manager/management/overview.md)que deve ser único em todas as regiões e é usado para organizar recursos relacionados. Este exemplo usa "My-First-LA-RG". |
   | **Localização** | <*Região de Azure*> | A região onde armazenar a sua informação lógica de aplicações. Este exemplo usa "West US". |
   | **Log Analytics** | Desligado | Mantenha a definição **Desativado** para o registo de diagnósticos. |
   ||||

1. Quando estiver pronto, selecione **Review + Create**. Confirme os detalhes que forneceu e selecione **Criar**.

1. Depois de a Azure implementar com sucesso a sua aplicação, selecione **Ir para o recurso**.

   ![Screenshot mostrando a página de implementação de recursos e botão selecionado para "Ir para o recurso".](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Ou, pode encontrar e selecionar a sua aplicação lógica digitando o nome na caixa de pesquisa.

   O Estruturador da Aplicação Lógica é aberto e mostra uma página com um vídeo de introdução e os acionadores habitualmente utilizados. Em **Modelos**, selecione **Aplicação Lógica em Branco**.

   ![Screenshot mostrando galeria de modelos de design de apps lógicas e modelo selecionado, "Blank Logic App".](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Em seguida, adicione um [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts) que é desencadeado quando um novo item do feed RSS é apresentado. Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Cada vez que o gatilho dispara, o motor Azure Logic Apps cria uma instância lógica de aplicações que inicia e executa o seu fluxo de trabalho.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Adicione o gatilho RSS

1. No **Logic App Designer,** sob a caixa de pesquisa, selecione **All**.

1. Para encontrar o conector RSS, na caixa de pesquisa, insira `rss` . A partir da lista de gatilhos, selecione o gatilho RSS, **quando for publicado um item de alimentação**.

   ![Screenshot mostrando o Logic Apps Designer com "rss" na caixa de pesquisa e o gatilho selecionado, "Quando um item de alimentação é publicado".](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Forneça as informações para o seu gatilho, conforme descrito neste passo:

   ![Screenshot mostrando Aplicativos Lógicos Designer com definições de gatilho RSS, incluindo URL RSS, frequência e intervalo.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **O URL do feed RSS** | <*RSS-feed-URL*> | A ligação para o feed RSS que deseja monitorizar. Este exemplo utiliza o feed RSS do Wall Street Journal em `https://feeds.a.dj.com/rss/RSSMarketsMain.xml` , mas se quiser, pode usar o seu próprio URL de feed RSS. |
   | **Intervalo** | 1 | O número de intervalos de espera entre verificações |
   | **Frequência** | Minuto | A unidade de tempo para cada intervalo entre verificações |
   ||||

   Em conjunto, o intervalo e a frequência definem o agendamento para o acionador da aplicação lógica. Esta aplicação lógica verifica o feed a cada minuto.

1. Para colapsar os detalhes do gatilho por enquanto, clique dentro da barra de título do gatilho.

   ![Screenshot mostrando Logic Apps Designer com formato de aplicação lógica em colapso.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

A sua aplicação lógica está agora ativa, mas não faz mais nada que não verificar o feed RSS. Por isso, adicione uma ação que responde quando o acionador é acionado.

## <a name="add-the-send-email-action"></a>Adicione a ação "enviar e-mail"

Adicione agora uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que envia um e-mail quando um novo item aparece no feed RSS.

1. Sob o gatilho **Quando um item de alimentação é publicado,** selecione Novo **passo**.

   ![Screenshot mostrando Logic Apps Designer com "Novo passo".](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Em **Escolha uma ação** e a caixa de pesquisa, selecione **All**.

1. Na caixa de pesquisa, `send an email` insira para que possa encontrar conectores que ofereçam esta ação. Para filtrar a lista de ações para uma aplicação ou serviço específico, pode selecionar primeiro essa aplicação ou serviço.

   Por exemplo, se estiver a utilizar uma conta de trabalho ou escola da Microsoft e quiser utilizar o Office 365 Outlook, selecione **Office 365 Outlook**. Ou, se estiver a utilizar uma conta pessoal da Microsoft, pode selecionar Outlook.com. Este exemplo continua com o Office 365 Outlook:

   ![Screenshot mostrando o Designer de Aplicações Lógicas e o conector do Office 365 Outlook selecionado.](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   Agora pode encontrar e selecionar mais facilmente a ação que pretende utilizar, por `send an email` exemplo:

   ![Screenshot mostrando Logic Apps Designer e lista com ações filtradas.](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Se o conector de e-mail selecionado lhe pedir para autenticar a sua identidade, complete esse passo agora para criar uma ligação entre a sua aplicação lógica e o seu serviço de e-mail.

   > [!NOTE]
   > Neste exemplo específico, autentica manualmente a sua identidade. No entanto, os conectores que necessitam de autenticação diferem nos tipos de autenticação que suportam. Também tem opções para configurar a forma como pretende lidar com a autenticação. Por exemplo, quando utiliza os modelos do Gestor de Recursos Azure para implantação, pode parametrizar e melhorar a segurança nas entradas que pretende alterar com frequência ou facilidade, como informações de ligação. Para obter mais informações, veja estes tópicos:
   >
   > * [Parâmetros do modelo para implantação](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Autorizar ligações OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Autenticar acesso com identidades geridas](../logic-apps/create-managed-service-identity.md)
   > * [Autenticar ligações para implementação de aplicações lógicas](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. No Enviar uma ação **por e-mail,** especifique as informações a incluir no e-mail.

   1. Na caixa **Para**, introduza o endereço de e-mail do destinatário. Para efeitos de teste, pode utilizar o seu endereço de e-mail.

      Por agora, ignore a lista **Adicionar conteúdo dinâmico** que é apresentada. Quando clica em algumas caixas de edição, esta lista aparece e mostra quaisquer saídas disponíveis do passo anterior que pode usar como entradas para a ação atual.

   1. Na caixa **Assunto**, introduza este texto com um espaço em branco à direita: `New RSS item: `

      ![Screenshot mostrando o Logic Apps Designer com ação "Enviar um e-mail" e cursor dentro da caixa de propriedade "Subject".](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. Na lista de **conteúdos dinâmicos Add,** selecione **o título Feed**, que é a saída do gatilho, "Quando um item de alimentação é publicado", que disponibiliza o título de artigo RSS para utilizar.

      ![Screenshot mostrando o Logic Apps Designer com ação "Enviar um e-mail" e cursor dentro da caixa de propriedade "Subject" com uma lista de conteúdos dinâmicos abertos e saída selecionada, "Feed title".](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Na lista de conteúdos dinâmicos, se não aparecerem saídas do gatilho "Quando um item de alimentação é publicado", junto ao cabeçalho da ação, selecione **Ver mais**.
      > 
      > ![Screenshot mostrando Logic Apps Designer com lista de conteúdo dinâmico aberto e "Ver mais" selecionado para o gatilho.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Quando estiver pronto, o assunto do e-mail terá o aspeto deste exemplo:

      ![Screenshot mostrando o Logic Apps Designer com ação "Enviar um e-mail" e um assunto de e-mail exemplo com a propriedade incluída "Feed title".](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Se um ciclo "For each" aparecer no estruturador, significa que selecionou token para uma matriz, como o token **categories-item**. Para estes tipos de tokens, o estruturador adiciona automaticamente este ciclo à volta da ação que faz referência a esse token. Desta forma, a aplicação lógica realiza a mesma ação em cada item da matriz. Para remover o laço, selecione as **elipses** **(...**) na barra de título do loop e, em seguida, selecione **Delete**.

   1. Na caixa **Corpo**, introduza este texto e selecione estes tokens para o corpo da mensagem de e-mail. Para adicionar linhas em branco a uma caixa de edição, prima Shift + Enter.

      ![Screenshot mostrando o Logic Apps Designer com ação "Enviar um e-mail" e propriedades selecionadas dentro da caixa "Body".](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

      | Propriedade | Descrição |
      |----------|-------------|
      | **Título do feed** | O título do item |
      | **Feed publicado em** | A data e hora de publicação do item |
      | **Ligação principal do feed** | O URL do item |
      |||

1. Guarde a sua aplicação lógica.

Em seguida, teste a aplicação lógica.

## <a name="run-your-logic-app"></a>Executar a aplicação lógica

Para iniciar manualmente a sua aplicação lógica, na barra de barras de ferramentas do designer, selecione **Run**. Em alternativa, aguarde que a aplicação lógica verifique o feed RSS de acordo com o agendamento especificado (a cada minuto).

![Screenshot mostrando o Design de Aplicações Lógicas com o botão "Run" selecionado na barra de ferramentas do designer.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Se o feed RSS tiver itens novos, a sua aplicação lógica envia um e-mail para cada item novo. Caso contrário, a aplicação lógica aguarda até o intervalo seguinte antes de verificar novamente. Se não receber nenhum e-mail, veja a pasta de e-mail de lixo.

Por exemplo, eis um exemplo de uma mensagem de e-mail enviada por esta aplicação lógica.

![Screenshot mostrando o e-mail da amostra recebido quando aparece o novo item de alimentação RSS.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Tecnicamente, quando o gatilho verifica o feed RSS e encontra novos itens, o gatilho dispara, e o motor Azure Logic Apps cria uma instância do seu fluxo de trabalho de aplicações lógicas que executa as ações no fluxo de trabalho. Se o acionador não encontrar itens novos, este não é acionado e "ignora" a criação de instâncias do fluxo de trabalho.

Parabéns, já construíste e executou com sucesso a tua primeira aplicação lógica com o portal Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar deste exemplo, elimine o grupo de recursos que contém a aplicação lógica e os recursos relacionados.

1. Na caixa de pesquisa Azure, insira `resource groups` e, em seguida, selecione **grupos de Recursos**.

   ![Screenshot mostrando caixa de pesquisa do portal Azure com o termo de pesquisa, "grupos de recursos".](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Encontre e selecione o grupo de recursos da sua aplicação lógica. No **painel de visão** geral, selecione **Eliminar o grupo de recursos**.

   ![Screenshot mostrando portal Azure com grupo de recursos selecionado e botão para "Eliminar grupo de recursos".](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Quando o painel de confirmação aparecer, insira o nome do grupo de recursos e selecione **Delete**.

   ![Screenshot mostrando portal Azure com painel de confirmação e nome de grupo de recursos introduzido para apagar.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Quando elimina uma aplicação lógica, não são instanciadas novas execuções. Todas as execuções em curso e pendentes são canceladas. Se tiver milhares de execuções, o cancelamento pode demorar muito tempo a concluir.

## <a name="next-steps"></a>Próximos passos

Neste início rápido, criou a sua primeira aplicação lógica que verifica a existência de atualizações RSS com base no seu agendamento especificado (a cada minuto) e realiza uma ação (envia e-mail) quando existem atualizações. Para saber mais, continue com este tutorial que cria fluxos de trabalho mais avançados com base num agendamento:

> [!div class="nextstepaction"]
> [Verificar o tráfego com uma aplicação lógica baseada num agendamento](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

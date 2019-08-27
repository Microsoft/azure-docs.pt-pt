---
title: Início rápido – criar e automatizar seu primeiro fluxo de trabalho com os aplicativos lógicos do Azure
description: Crie seu primeiro aplicativo lógico que automatiza tarefas, processos e fluxos de trabalho usando aplicativos lógicos do Azure. Crie aplicativos lógicos para a integração do sistema e soluções de EAI (integração de aplicativos empresariais) para seus sistemas & serviços de nuvem.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.openlocfilehash: d6c7f04d35a91e30a98ec403b6811a2be0aaee20
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051677"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>Início rápido: Crie seu primeiro fluxo de trabalho automatizado com os aplicativos lógicos do Azure-portal do Azure

Este início rápido mostra como criar o primeiro fluxo de trabalho automatizado com o [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Neste artigo, vai criar uma aplicação lógica que verifica regularmente o feed RSS de um site relativamente à existência de novos itens. Se existirem novos itens, a aplicação lógica envia uma mensagem de e-mail para cada item. Quando terminar, a aplicação lógica é semelhante a este fluxo de trabalho a alto nível:

![Descrição geral - exemplo de aplicação lógica](./media/quickstart-create-first-logic-app-workflow/overview.png)

Para seguir este início rápido, precisa de uma conta de e-mail de um fornecedor suportado pelo Logic Apps, como o Outlook do Office 365, Outlook.com ou Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](https://docs.microsoft.com/connectors/). Esta aplicação lógica utiliza uma conta do Outlook do Office 365. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

Além disso, se não tiver uma subscrição do Azure, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

## <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. No menu principal do Azure, selecione **criar um recurso** > **aplicativo lógico**de**integração** > .

   ![Criar uma aplicação lógica](./media/quickstart-create-first-logic-app-workflow/create-logic-app.png)

1. Em **Criar aplicação lógica**, forneça detalhes sobre a sua aplicação lógica, conforme aqui apresentado. Depois de terminar, selecione **criar**.

   ![Indicar os detalhes da aplicação lógica](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Propriedade | Value | Descrição |
   |----------|-------|-------------|
   | **Name** | <*logic-app-name*> | O nome do aplicativo lógico, que pode conter apenas letras, números, hifens`-`(), sublinhados`_`(), parênteses`(`( `)`,) e pontos (`.`). Este exemplo usa "My-First-Logic-app". |
   | **Subscrição** | <*Azure-subscription-name*> | O nome da sua assinatura do Azure |
   | **Grupo de recursos** | <*Azure-resource-group-name*> | O nome do [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usado para organizar os recursos relacionados. Este exemplo usa "My-First-LA-RG". |
   | **Location** | <*Azure-região*> | A região onde armazenar as informações do aplicativo lógico. Este exemplo usa "oeste dos EUA". |
   | **Log Analytics** | Desativado | Mantenha a definição **Desativado** para o registo de diagnósticos. |
   ||||

1. Depois que o Azure implantar seu aplicativo, na barra de ferramentas do Azure, selecione **notificações** > **ir para o recurso** para o aplicativo lógico implantado.

   ![Ir para recurso](./media/quickstart-create-first-logic-app-workflow/go-to-logic-app.png)

   Ou, você pode encontrar e selecionar seu aplicativo lógico digitando o nome na caixa de pesquisa.

   O Estruturador da Aplicação Lógica é aberto e mostra uma página com um vídeo de introdução e os acionadores habitualmente utilizados. Em **Modelos**, selecione **Aplicação Lógica em Branco**.

   ![Selecionar modelo de aplicativo lógico em branco](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Em seguida, adicione um [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts) que é desencadeado quando um novo item do feed RSS é apresentado. Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que inicia e executa o fluxo de trabalho.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>Verificar o feed RSS com um acionador

1. No designer de aplicativo lógico, na caixa de pesquisa, selecione **tudo**.

1. Na caixa de pesquisa, digite "RSS". Na lista de gatilhos, selecione este gatilho: **Quando um item de feed é publicado-RSS**

   ![Selecionar gatilho: "RSS-quando um item de feed é publicado"](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss.png)

1. Forneça essas informações para seu gatilho, conforme mostrado e descrito aqui:

   ![Configurar o acionador com o feed RSS, a frequência e o intervalo](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss-settings.png)

   | Propriedade | Value | Descrição |
   |----------|-------|-------------|
   | **O URL do feed RSS** | ```http://feeds.reuters.com/reuters/topNews``` | A ligação para o feed RSS que quer monitorizar |
   | **Intervalo** | 1 | O número de intervalos de espera entre verificações |
   | **Frequência** | Minuto | A unidade de tempo para cada intervalo entre verificações  |
   ||||

   Em conjunto, o intervalo e a frequência definem o agendamento para o acionador da aplicação lógica. Esta aplicação lógica verifica o feed a cada minuto.

1. Para ocultar os detalhes do acionador por agora, clique no interior da barra de título do acionador.

   ![Fechar forma para ocultar os detalhes](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

A sua aplicação lógica está agora ativa, mas não faz mais nada que não verificar o feed RSS. Por isso, adicione uma ação que responde quando o acionador é acionado.

## <a name="send-email-with-an-action"></a>Enviar mensagens de e-mail com uma ação

Agora, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que envia uma mensagem de e-mail quando um novo item aparece no feed RSS.

1. No gatilho **quando um item do feed é publicado** , selecione **nova etapa**.

   ![Adicionar uma ação](./media/quickstart-create-first-logic-app-workflow/add-new-action.png)

1. Em **escolher uma ação** e a caixa de pesquisa, selecione **tudo**.

1. Na caixa de pesquisa, digite "enviar um email". Na lista de ações, selecione a ação "enviar um e-mail" para o fornecedor de e-mail que pretende.

   ![Selecione esta ação: "Office 365 Outlook-enviar um email"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Para filtrar a lista de ações para um serviço ou aplicação específico, pode selecionar primeiro essa aplicação ou serviço:

   * Relativamente a contas escolares ou profissionais do Azure, selecione Office 365 Outlook (Outlook do Office 365).
   * Quanto a contas Microsoft pessoais, selecione Outlook.com.

1. Se lhe forem pedidas credenciais, inicie sessão na sua conta de e-mail para que o Logic Apps crie uma ligação para a sua conta de e-mail.

1. Na ação **Enviar uma mensagem de e-mail**, especifique os dados que pretende que a mensagem inclua.

   1. Na caixa **Para**, introduza o endereço de e-mail do destinatário. Para fins de teste, pode utilizar o seu próprio endereço de e-mail.

      Por agora, ignore a lista **Adicionar conteúdo dinâmico** que é apresentada. Quando clica no interior de algumas caixas de edição, esta lista aparece e mostra os parâmetros disponíveis do passo anterior que pode incluir como entradas no fluxo de trabalho.

   1. Na caixa **Assunto**, introduza este texto com um espaço em branco à direita: ```New RSS item:```

      ![Introduza o assunto do e-mail](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. Na lista **Adicionar conteúdo dinâmico**, selecione **Título de feed** para incluir o título do item RSS.

      ![Lista de conteúdo dinâmico - "Título do feed"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Quando estiver pronto, o assunto do e-mail terá o aspeto deste exemplo:

      ![Título do feed adicionado](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Se um ciclo "For each" aparecer no estruturador, significa que selecionou token para uma matriz, como o token **categories-item**. Para estes tipos de tokens, o estruturador adiciona automaticamente este ciclo à volta da ação que faz referência a esse token. Desta forma, a aplicação lógica realiza a mesma ação em cada item da matriz. Para remover o loop, selecione as **reticências** ( **...** ) na barra de título do loop e, em seguida, selecione **excluir**.

   1. Na caixa **Corpo**, introduza este texto e selecione estes tokens para o corpo da mensagem de e-mail. Para adicionar linhas em branco a uma caixa de edição, prima Shift + Enter.

      ![Adicione conteúdo para o corpo do e-mail](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Propriedade | Descrição |
      |----------|-------------|
      | **Título do feed** | O título do item |
      | **Feed publicado em** | A data e hora de publicação do item |
      | **Ligação principal do feed** | O URL do item |
      |||

1. Guarde a aplicação lógica.

Em seguida, teste a aplicação lógica.

## <a name="run-your-logic-app"></a>Executar a aplicação lógica

Para iniciar manualmente seu aplicativo lógico, na barra da barra de ferramentas do designer, selecione **executar**. Em alternativa, aguarde que a aplicação lógica verifique o feed RSS de acordo com o agendamento especificado (a cada minuto). Se o feed RSS tiver itens novos, a sua aplicação lógica envia um e-mail para cada item novo. Caso contrário, a aplicação lógica aguarda até o intervalo seguinte antes de realizar uma nova consulta. Se não receber nenhum e-mail, veja a pasta de e-mail de lixo.

Por exemplo, eis um exemplo de uma mensagem de e-mail enviada por esta aplicação lógica.

![E-mail enviado para novo item de feed RSS](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Em termos técnicos, quando o acionador verifica o feed RSS e encontra itens novos, este é acionado e o motor do Logic Apps cria uma instância do fluxo de trabalho da aplicação lógica que executa as ações no fluxo de trabalho. Se o acionador não encontrar itens novos, este não é acionado e "ignora" a criação de instâncias do fluxo de trabalho.

Parabéns, você agora criou e executou com êxito seu primeiro aplicativo lógico com o portal do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar deste exemplo, elimine o grupo de recursos que contém a aplicação lógica e os recursos relacionados.

1. No menu principal do Azure, selecione **grupos de recursos**e, em seguida, selecione o grupo de recursos do aplicativo lógico. No painel **visão geral** , selecione **excluir grupo de recursos**.

   !["Grupos de recursos" > "Descrição geral" > "Eliminar grupo de recursos"](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Insira o nome do grupo de recursos como confirmação e selecione **excluir**.

   ![Confirmar eliminação](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Quando elimina uma aplicação lógica, não são instanciadas novas execuções. Todas as execuções em curso e pendentes são canceladas. Se tiver milhares de execuções, o cancelamento pode demorar muito tempo a concluir.

## <a name="get-support"></a>Obter suporte

Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/home?forum=azurelogicapps).

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou a sua primeira aplicação lógica que verifica a existência de atualizações RSS com base no seu agendamento especificado (a cada minuto) e realiza uma ação (envia e-mail) quando existem atualizações. Para saber mais, continue com este tutorial que cria fluxos de trabalho mais avançados com base num agendamento:

> [!div class="nextstepaction"]
> [Verificar o tráfego com uma aplicação lógica baseada num agendamento](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

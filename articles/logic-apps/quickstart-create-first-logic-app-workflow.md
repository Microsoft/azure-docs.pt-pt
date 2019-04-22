---
title: Início rápido - criar e automatizar o seu primeiro fluxo de trabalho - Azure Logic Apps | Documentos da Microsoft
description: Crie a sua primeira aplicação lógica que automatiza tarefas, processos e fluxos de trabalho com o Azure Logic Apps. Criar aplicações lógicas para soluções de integração de sistemas e integração de aplicações empresariais (EAI) para os seus sistemas e serviços na cloud
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 577a047fb88208d7e14e8ba06a1b9e7e87c5ef91
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58884244"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>Início rápido: Criar seu primeiro fluxo de trabalho automatizado com o Azure Logic Apps - portal do Azure

Este início rápido mostra como criar o primeiro fluxo de trabalho automatizado com o [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Neste artigo, vai criar uma aplicação lógica que verifica regularmente o feed RSS de um site relativamente à existência de novos itens. Se existirem novos itens, a aplicação lógica envia uma mensagem de e-mail para cada item. Quando terminar, a aplicação lógica é semelhante a este fluxo de trabalho a alto nível:

![Descrição geral - exemplo de aplicação lógica](./media/quickstart-create-first-logic-app-workflow/overview.png)

Para seguir este início rápido, precisa de uma conta de e-mail de um fornecedor suportado pelo Logic Apps, como o Outlook do Office 365, Outlook.com ou Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](https://docs.microsoft.com/connectors/). Esta aplicação lógica utiliza uma conta do Outlook do Office 365. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente. 

Além disso, se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se numa conta do Azure gratuita</a>.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as credenciais da sua conta do Azure.

## <a name="create-your-logic-app"></a>Criar uma aplicação lógica 

1. No menu principal do Azure, selecione **Criar um recurso** > **Integração** > **Aplicação Lógica**.

   ![Criar uma aplicação lógica](./media/quickstart-create-first-logic-app-workflow/create-logic-app.png)

3. Em **Criar aplicação lógica**, forneça detalhes sobre a sua aplicação lógica, conforme aqui apresentado. Depois de terminar, escolha **criar**.

   ![Indicar os detalhes da aplicação lógica](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Propriedade | Valor | Descrição | 
   |----------|-------|-------------| 
   | **Nome** | MyFirstLogicApp | O nome para a aplicação lógica | 
   | **Subscrição** | <*your-Azure-subscription-name*> | O nome para a subscrição do Azure | 
   | **Grupo de recursos** | My-First-LA-RG | O nome para o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) utilizado para organizar os recursos relacionados | 
   | **Localização** | EUA Oeste | A região onde pretende armazenar as informações da aplicação lógica | 
   | **Log Analytics** | Desativado | Mantenha a definição **Desativado** para o registo de diagnósticos. | 
   |||| 

3. Depois de o Azure implementar a aplicação, o Estruturador de Aplicações Lógicas abre e mostra uma página com um vídeo de introdução e os acionadores habitualmente utilizados. Em **Modelos**, escolha **Aplicação Lógica em Branco**.

   ![Escolher o modelo de aplicação lógica em branco](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Em seguida, adicione um [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts) que é desencadeado quando um novo item do feed RSS é apresentado. Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que inicia e executa o fluxo de trabalho.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>Verificar o feed RSS com um acionador

1. No estruturador, introduza "rss" na caixa de pesquisa. Selecione este acionador: **RSS - quando um item de feed é publicado**

   ![Selecionar acionador: "RSS - quando for publicado um item de feed"](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss.png)

2. Forneça estas informações para o acionador, tal como apresentadas e descritas: 

   ![Configurar o acionador com o feed RSS, a frequência e o intervalo](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss-settings.png)

   | Propriedade | Valor | Descrição | 
   |----------|-------|-------------| 
   | **O URL do feed RSS** | ```http://feeds.reuters.com/reuters/topNews``` | A ligação para o feed RSS que quer monitorizar | 
   | **Intervalo** | 1 | O número de intervalos de espera entre verificações | 
   | **Frequência** | Minuto | A unidade de tempo para cada intervalo entre verificações  | 
   |||| 

   Em conjunto, o intervalo e a frequência definem o agendamento para o acionador da aplicação lógica. 
   Esta aplicação lógica verifica o feed a cada minuto.

3. Para ocultar os detalhes do acionador por agora, clique no interior da barra de título do acionador.

   ![Fechar forma para ocultar os detalhes](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

4. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**. 

A sua aplicação lógica está agora ativa, mas não faz mais nada que não verificar o feed RSS. Por isso, adicione uma ação que responde quando o acionador é acionado.

## <a name="send-email-with-an-action"></a>Enviar mensagens de e-mail com uma ação

Agora, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que envia uma mensagem de e-mail quando um novo item aparece no feed RSS. 

1. Sob o **quando é publicado um item de feed** acionador, escolha **novo passo**.

   ![Adicionar uma ação](./media/quickstart-create-first-logic-app-workflow/add-new-action.png)

2. Sob **escolher uma ação**, introduza "enviar e-mail" na caixa de pesquisa. Na caixa de pesquisa, escolha **todos os**. Na lista de ações, selecione a ação "enviar um e-mail" para o fornecedor de e-mail que pretende. 

   ![Selecione a ação: "Outlook do office 365 - enviar um e-mail"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Para filtrar a lista de ações para um serviço ou aplicação específico, pode selecionar primeiro essa aplicação ou serviço:

   * Relativamente a contas escolares ou profissionais do Azure, selecione Office 365 Outlook (Outlook do Office 365). 
   * Quanto a contas Microsoft pessoais, selecione Outlook.com.

3. Se lhe forem pedidas credenciais, inicie sessão na sua conta de e-mail para que o Logic Apps crie uma ligação para a sua conta de e-mail.

4. Na ação **Enviar uma mensagem de e-mail**, especifique os dados que pretende que a mensagem inclua. 

   1. Na caixa **Para**, introduza o endereço de e-mail do destinatário. 
   Para fins de teste, pode utilizar o seu próprio endereço de e-mail.

      Por agora, ignore a lista **Adicionar conteúdo dinâmico** que é apresentada. 
      Quando clica no interior de algumas caixas de edição, esta lista aparece e mostra os parâmetros disponíveis do passo anterior que pode incluir como entradas no fluxo de trabalho. 

   2. Na caixa **Assunto**, introduza este texto com um espaço em branco à direita: ```New RSS item:```

      ![Introduza o assunto do e-mail](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)
 
   3. Na lista **Adicionar conteúdo dinâmico**, selecione **Título de feed** para incluir o título do item RSS.

      ![Lista de conteúdo dinâmico - "Título do feed"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Quando estiver pronto, o assunto do e-mail terá o aspeto deste exemplo:

      ![Título do feed adicionado](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Se um ciclo "For each" aparecer no estruturador, significa que selecionou token para uma matriz, como o token **categories-item**. 
      Para estes tipos de tokens, o estruturador adiciona automaticamente este ciclo à volta da ação que faz referência a esse token. 
      Desta forma, a aplicação lógica realiza a mesma ação em cada item da matriz. 
      Para remover o ciclo, escolha as **reticências** (**...**) na barra de título do ciclo e escolha **Eliminar**.

   4. Na caixa **Corpo**, introduza este texto e selecione estes tokens para o corpo da mensagem de e-mail. 
   Para adicionar linhas em branco a uma caixa de edição, prima Shift + Enter. 

      ![Adicione conteúdo para o corpo do e-mail](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Propriedade | Descrição | 
      |----------|-------------| 
      | **Título do feed** | O título do item | 
      | **Feed publicado em** | A data e hora de publicação do item | 
      | **Ligação principal do feed** | O URL do item | 
      ||| 
   
5. Guarde a aplicação lógica.

Em seguida, teste a aplicação lógica.

## <a name="run-your-logic-app"></a>Executar a aplicação lógica

Para iniciar manualmente a sua aplicação lógica, na barra de ferramentas do estruturador, escolha **Executar**. Em alternativa, aguarde que a aplicação lógica verifique o feed RSS de acordo com o agendamento especificado (a cada minuto). Se o feed RSS tiver itens novos, a sua aplicação lógica envia um e-mail para cada item novo. Caso contrário, a aplicação lógica aguarda até o intervalo seguinte antes de realizar uma nova consulta. 

Por exemplo, eis um exemplo de uma mensagem de e-mail enviada por esta aplicação lógica. Se não receber nenhum e-mail, veja a pasta de e-mail de lixo.

![E-mail enviado para novo item de feed RSS](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Em termos técnicos, quando o acionador verifica o feed RSS e encontra itens novos, este é acionado e o motor do Logic Apps cria uma instância do fluxo de trabalho da aplicação lógica que executa as ações no fluxo de trabalho.
Se o acionador não encontrar itens novos, este não é acionado e "ignora" a criação de instâncias do fluxo de trabalho.

Parabéns, criou com êxito e executou a sua primeira aplicação lógica com o portal do Azure!

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar deste exemplo, elimine o grupo de recursos que contém a aplicação lógica e os recursos relacionados. 

1. No menu principal do Azure, aceda a **Grupos de recursos** e selecione o grupo de recursos da sua aplicação lógica. Na página **Descrição geral**, selecione **Eliminar grupo de recursos**. 

   !["Grupos de recursos" > "Descrição geral" > "Eliminar grupo de recursos"](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

2. Introduza o nome do grupo de recursos como confirmação e escolha **Eliminar**.

   ![Confirmar eliminação](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Quando elimina uma aplicação lógica, não são instanciadas novas execuções. Todas as execuções em curso e pendentes são canceladas. Se tiver milhares de execuções, o cancelamento pode demorar muito tempo a concluir.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou a sua primeira aplicação lógica que verifica a existência de atualizações RSS com base no seu agendamento especificado (a cada minuto) e realiza uma ação (envia e-mail) quando existem atualizações. Para saber mais, continue com este tutorial que cria fluxos de trabalho mais avançados com base num agendamento:

> [!div class="nextstepaction"]
> [Verificar o tráfego com uma aplicação lógica baseada num agendamento](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

---
title: Construir fluxos de trabalho automatizados baseados em aprovação
description: Tutorial - Criar um fluxo de trabalho automatizado baseado em aprovação que processa subscrições de listas de correio utilizando aplicações da Lógica Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: 7d7f573e5b18e6e0e63d3275aecefe408a9143fb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75456601"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Tutorial: Criar fluxos de trabalho automatizados baseados em aprovação utilizando aplicações da Lógica Azure

Este tutorial mostra como construir uma [aplicação lógica](../logic-apps/logic-apps-overview.md) que automatiza um fluxo de trabalho baseado em aprovação. Especificamente, esta aplicação lógica processa pedidos de subscrição para uma lista de correio que é gerida pelo serviço [MailChimp.](https://mailchimp.com/) Esta aplicação lógica monitoriza uma conta de e-mail quanto a esses pedidos, envia-os para aprovação e adiciona os membros aprovados à mesma.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma aplicação lógica em branco.
> * Adicionar um acionador que monitoriza os e-mails quanto a pedidos de subscrição.
> * Adicionar uma ação que envia e-mails para aprovar ou rejeitar esses pedidos.
> * Adicionar uma condição que verifica a resposta de aprovação.
> * Adicionar uma ação que adiciona os membros aprovados à lista de correio.
> * Adicionar uma condição que verifica se esses membros foram associados com êxito à lista.
> * Adicionar uma ação que envia e-mails a confirmar se esses membros foram associados com êxito à lista.

Quando terminar, a aplicação lógica é semelhante a este fluxo de trabalho a alto nível:

![Visão geral da aplicação lógica acabada de alto nível](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

* Uma conta MailChimp que contém uma lista chamada "test-members-ML" onde a sua aplicação lógica pode adicionar endereços de e-mail para membros aprovados. Se não tiver uma conta, [inscreva-se para uma conta gratuita](https://login.mailchimp.com/signup/), e depois aprenda a criar uma lista [mailChimp](https://us17.admin.mailchimp.com/lists/#).

* Uma conta de e-mail no Office 365 Outlook ou Outlook.com, que suporta fluxos de trabalho de aprovação. Este artigo utiliza o Outlook do Office 365. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

## <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. A partir do menu principal do Azure, selecione Criar uma**Aplicação Lógica**de**Integração** > de **Recursos.** > 

   ![Crie o seu novo recurso de aplicação lógica](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. Em **Criar aplicação lógica**, indique estas informações sobre a sua aplicação lógica, conforme mostrado e descrito. Quando terminar, selecione **Criar**.

   ![Forneça informações sobre a sua aplicação lógica](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | LA-MailingList | O nome da sua aplicação lógica, que pode conter`-`apenas`_`letras, números,`(` `)`hífenes`.`(), parênteses ( ), e períodos ( ). Este exemplo utiliza "LA-MailingList". |
   | **Subscrição** | <*seu nome de assinatura Azure*> | O seu nome de subscrição Azure |
   | **Grupo de recursos** | LA-MailingList-RG | O nome do [grupo de recursos Azure,](../azure-resource-manager/management/overview.md)que é usado para organizar recursos relacionados. Este exemplo utiliza "LA-MailingList-RG". |
   | **Localização** | E.U.A. Oeste | TA região onde armazenar a sua informação lógica de aplicações. Este exemplo usa "West US". |
   | **Log Analytics** | Desativado | Mantenha a definição **Desativado** para o registo de diagnósticos. |
   ||||

1. Depois de o Azure implementar a sua aplicação, na barra de ferramentas Azure, selecione **Notificações** > **Para recorrer para** a sua aplicação lógica implementada.

   ![Vá ao seu novo recurso de aplicação lógica](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   Ou, pode encontrar e selecionar a sua aplicação lógica digitando o nome na caixa de pesquisa.

   O Logic Apps Designer abre e mostra uma página com um vídeo de introdução e gatilhos comumente usados e padrões de aplicações lógicas. Em **Modelos**, selecione **Aplicação Lógica em Branco**.

   ![Selecione modelo de aplicativo de lógica em branco](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Em seguida, adicione um [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts) que escuta os e-mails recebidos que têm pedidos de subscrição. Cada aplicação lógica tem de começar com um gatilho, que dispara quando um evento específico acontece ou quando novos dados cumprem uma condição específica. Para obter mais informações, veja [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Adicionar acionador para monitorizar e-mails

1. No Logic App Designer, na caixa `when email arrives` de pesquisa, introduza como filtro. A partir da lista **de Gatilhos,** selecione o **gatilho quando um novo e-mail chega** para o seu fornecedor de e-mail.

   Este exemplo utiliza o gatilho do Office 365 Outlook:

   ![Selecione "Quando um novo e-mail chegar" para o fornecedor de e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Relativamente a contas escolares ou profissionais do Azure, selecione Office 365 Outlook (Outlook do Office 365).
   * Quanto a contas Microsoft pessoais, selecione Outlook.com.

1. Se solicitado, inscreva-se na sua conta de e-mail com as suas credenciais para que as Aplicações Lógicas possam criar uma ligação à sua conta de e-mail.

1. No gatilho, forneça os critérios para verificar todos os novos e-mails.

   1. Especifique a pasta, o intervalo e a frequência para a verificação dos e-mails.

      ![Especifique a pasta, o intervalo e a frequência para a verificação dos e-mails](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Propriedade | Valor | Descrição |
      |----------|-------|-------------|
      | **Pasta** | `Inbox` | A pasta de e-mail a monitorizar |
      | **Intervalo** | `1` | O número de intervalos de espera entre verificações |
      | **Frequência** | `Hour` | A unidade de tempo a utilizar para a periodicidade |
      ||||

   1. Adicione agora outra propriedade ao gatilho para que possa filtrar na linha de assunto de e-mail. Abra a **lista de novos parâmetros,** e selecione a propriedade **Filtro de Assuntos.**

      ![Adicione a propriedade "Filtro de Assunto" para desencadear](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Para obter mais informações sobre as propriedades deste gatilho, consulte a referência do [conector Do Office 365 Outlook](https://docs.microsoft.com/connectors/office365/) ou a [referência do conector Outlook.com](https://docs.microsoft.com/connectors/outlook/).

   1. Depois de a propriedade aparecer no gatilho, introduza este texto:`subscribe-test-members-ML`

      ![Insira texto para a propriedade "Filtro de Assunto"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Para ocultar os detalhes do acionador por agora, clique na barra de título do mesmo.

   ![Fechar forma para ocultar os detalhes](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Guarde a aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

A sua aplicação lógica está agora ativa, mas não faz mais nada que não verificar os e-mails recebidos. Por isso, adicione uma ação que responde quando o acionador é acionado.

## <a name="send-approval-email"></a>Enviar o e-mail de aprovação

Agora que tem um acionador, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que envia um e-mail para aprovar ou rejeitar o pedido.

1. Sob o gatilho, selecione **Novo passo**. 

1. Em **'Escolha uma ação**' `approval` na caixa de pesquisa, introduza como filtro. Na lista de ações, selecione a ação de **e-mail de aprovação enviar** para o seu fornecedor de e-mail. 

   Este exemplo utiliza a ação Do Office 365 Outlook:

   ![Selecione ação "Enviar e-mail de aprovação"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Forneça as informações sobre esta ação conforme descrito: 

   ![Enviar propriedades de e-mail de aprovação](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Para** | <*seu endereço de e-mail*> | O endereço de e-mail do aprovador. Para fins de teste, pode utilizar o seu próprio endereço. Este exemplo usasophia.owen@fabrikam.como endereço de e-mail ficcional " " " |
   | **Assunto** | `Approve member request for test-members-ML` | Um assunto de e-mail descritivo |
   | **Opções do Utilizador** | `Approve, Reject` | As opções de resposta que o aprovador pode selecionar. Por predefinição, o aprovador pode selecionar "Approve" ou "Rejeitar" como resposta. |
   ||||

   Por enquanto, ignore a lista de conteúdos dinâmicos que aparece quando clica em caixas de edição específicas. Esta lista permite selecionar a saída disponível de ações anteriores que pode utilizar como inputs no seu fluxo de trabalho.

   Para obter mais informações sobre as propriedades desta ação, consulte a referência do [conector Do Office 365 Outlook](https://docs.microsoft.com/connectors/office365/) ou a [referência do conector Outlook.com](https://docs.microsoft.com/connectors/outlook/).
 
1. Guarde a aplicação lógica.

Em seguida, adicione uma condição para verificar a resposta selecionada do aprovador.

## <a name="check-approval-response"></a>Verificar a resposta de aprovação

1. Sob a ação **de e-mail de aprovação Enviar,** selecione **Novo passo**".

1. Em **'Escolha uma ação**', selecione **Incorporado**' . Na caixa de `condition` pesquisa, introduza como filtro. Na lista de ações, selecione a ação **Condição.**

   ![Localizar e selecionar a ação "Condição"](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Mude o nome da condição com uma descrição melhor.

   1. Na barra de título da condição, selecione o botão **elipses** (**...**) > **Rename**.

      ![Descrição da condição de renomear](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Mude o nome da condição com a descrição `If request approved`

1. Construa uma condição que verifique se o aprovador selecionado **Aprovar**.

   1. Na condição, clique no interior da caixa **de valor Escolha uma** caixa de valor no lado esquerdo da circunstância.

   1. A partir da lista de conteúdos dinâmicos que aparece, sob **o email de aprovação Enviar,** selecione a propriedade **SelectedOption.**

      ![A partir da lista de conteúdos dinâmicos, selecione "SelectedOption"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Na caixa de comparação média, selecione o **é igual ao** operador.

   1. Na **caixa** de valor Escolha uma caixa de valor no lado direito da condição, introduza este texto:`Approve`

      Quando terminar, a condição parece com este exemplo:

      ![Condição final para exemplo aprovado](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Guarde a aplicação lógica.

Em seguida, especifique a ação que a sua aplicação lógica executa quando o revisor aprova o pedido. 

## <a name="add-member-to-mailchimp-list"></a>Adicionar membro à lista do MailChimp

Adicione agora uma ação que adiciona o membro aprovado à sua lista de correio.

1. Na condição Se o **verdadeiro** ramo, selecione **Adicionar uma ação**.

1. Em **'Escolha uma ação**', introduza `mailchimp` como filtro' e selecione o membro Adicionar para **enumerar a** ação.

   ![Selecione ação "Adicionar membro à lista"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Se for solicitado acesso à sua conta MailChimp, inscreva-se nas suas credenciais MailChimp.

1. Forneça informações sobre esta ação como mostrado e descrito aqui:

   ![Indicar informações para "Adicionar membro à lista"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **ID da Lista** | Sim | `test-members-ML` | O nome da sua lista de correio mailChimp. Este exemplo utiliza "test-members-ML". |
   | **Estado** | Sim | `subscribed` | Selecione o estado de subscrição do novo membro. Este exemplo utiliza "subscrito". <p>Para obter mais informações, veja [Manage subscribers with the MailChimp API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/) (Grir subscritores com a API do MailChimp). |
   | **Endereço de e-mail** | Sim | <*novo membro-endereço de e-mail*> | A partir da lista de conteúdos dinâmicos, selecione **A partir de** baixo Quando chega um novo **correio,** que passa no endereço de e-mail para o novo membro. |
   ||||

   Para mais informações sobre as propriedades desta ação, consulte a referência do [conector MailChimp](https://docs.microsoft.com/connectors/mailchimp/).

1. Guarde a aplicação lógica.

Em seguida, adicione uma condição para verificar se o membro novo foi associado com êxito à sua lista de correio. Dessa forma, a aplicação lógica notifica-o se esta operação foi concluída com êxito ou se falhou.

## <a name="check-for-success-or-failure"></a>Verificar se a operação foi concluída com êxito ou se falhou

1. No ramo **If true,** sob o **membro Adicionar para listar** a ação, selecione **Adicionar uma ação**.

1. Em **'Escolha uma ação**', selecione **Incorporado**' . Na caixa de `condition` pesquisa, introduza como filtro. Da lista de ações, selecione **Condição**.

1. Mude o nome da condição com a descrição `If add member succeeded`

1. Crie uma condição que verifica se o membro aprovado foi adicionado com êxito ou não à lista de correio:

   1. Na condição, clique no interior da caixa de valor Escolha uma caixa de **valor,** que está no lado esquerdo da circunstância. A partir da lista de conteúdos dinâmicos, em **adicionar membro à lista,** selecione a propriedade **Status.**

      Por exemplo, a sua condição parece-se com este exemplo:

      ![Em "Add member to list", selecione "Estado"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Na caixa de comparação média, selecione o **é igual ao** operador.

   1. Na **caixa** de valor Escolha uma caixa de valor no lado direito da condição, introduza este texto:`subscribed`

      Quando terminar, a condição parece com este exemplo:

      ![Condição final para exemplo subscrito](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Depois, configure os e-mails que vão ser enviados quando a associação do membro aprovado à lista de correio for concluída com êxito ou quando falhar.

## <a name="send-email-if-member-added"></a>Enviar e-mail se o membro for adicionado

1. Sob a condição de adição se adicionar **membro bem sucedido,** no ramo **If true,** selecione **Adicionar uma ação**.

   ![No ramo "If true", selecione "Add a action"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Em **'Escolha uma ação**' `outlook send email` na caixa de pesquisa, introduza como filtro e selecione a ação de e-mail Enviar uma ação de **e-mail.**

   ![Adicione ação "Enviar um e-mail"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Mude o nome da ação com a descrição `Send email on success`

1. Indique informações para esta ação, conforme apresentado e descrito:

   ![Indique informações para o e-mail de êxito](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Para** | Sim | <*seu endereço de e-mail*> | O endereço de e-mail para onde enviar o e-mail de êxito. Para fins de teste, pode utilizar o seu próprio endereço de e-mail. |
   | **Assunto** | Sim | <*assunto-para-sucesso-e-email*> | O assunto do e-mail de êxito. Neste tutorial, introduza este texto: <p>`Success! Member added to "test-members-ML": ` <p>A partir da lista de conteúdos dinâmicos, em **adicionar membro à lista,** selecione a propriedade **Email Address.** |
   | **Corpo** | Sim | <*corpo-para-sucesso-e-e-mail*> | O conteúdo do corpo do e-mail de êxito. Neste tutorial, introduza este texto: <p>`New member has joined "test-members-ML":` <p>A partir da lista de conteúdos dinâmicos, selecione a propriedade **Email Address.** <p>Na próxima fila, introduza este texto:`Member opt-in status: ` <p> A partir da lista de conteúdos dinâmicos, em **adicionar membro à lista,** selecione a propriedade **Status.** |
   |||||

1. Guarde a aplicação lógica.

## <a name="send-email-if-member-not-added"></a>Enviar e-mail se o membro não for adicionado

1. Sob a condição de adição de **membro se tiver sucesso,** no ramo **If false,** selecione **Adicionar uma ação**.

   ![No ramo "Se for falso", selecione "Adicionar uma ação"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Em **'Escolha uma ação**' `outlook send email` na caixa de pesquisa, introduza como filtro e selecione a ação de e-mail Enviar uma ação de **e-mail.**

   ![Adicione a ação para "Send an email"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Mude o nome da ação com a descrição `Send email on failure`

1. Forneça informações sobre esta ação como mostrado e descrito aqui:

   ![Indique as informações do e-mail de falha](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Para** | Sim | <*seu endereço de e-mail*> | O endereço de e-mail para onde enviar o e-mail de falha. Para fins de teste, pode utilizar o seu próprio endereço de e-mail. |
   | **Assunto** | Sim | <*assunto-para-falha-e-e-mail*> | O assunto do e-mail de falha. Neste tutorial, introduza este texto: <p>`Failed, member not added to "test-members-ML": ` <p>A partir da lista de conteúdos dinâmicos, em **adicionar membro à lista,** selecione a propriedade **Email Address.** |
   | **Corpo** | Sim | <*corpo-para-falha-e-e-e-mail*> | O conteúdo do corpo do e-mail de falha. Neste tutorial, introduza este texto: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Guarde a aplicação lógica. 

Em seguida, teste a sua aplicação lógica, que é agora semelhante a este exemplo:

![Fluxo de trabalho de aplicação lógica terminada](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Executar a aplicação lógica

1. Envie para si próprio um pedido de e-mail para aderir à sua lista de correio. Aguarde que o pedido apareça na sua caixa de entrada.

1. Para iniciar manualmente a sua aplicação lógica, na barra de ferramentas de design, selecione **Run**. 

   Se o assunto do seu e-mail corresponder ao filtro de assunto do acionador, a aplicação lógica envia-lhe o e-mail para aprovar o pedido de subscrição.

1. No e-mail de aprovação, selecione **Aprovar**.

1. Se o endereço de e-mail do subscritor não existir na sua lista de correio, a sua aplicação lógica adiciona o endereço dessa pessoa e envia-lhe a si um e-mail igual ao do exemplo abaixo:

   ![Email exemplo - subscrição bem sucedida](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Se a sua aplicação lógica não conseguir adicionar o subscritor, receberá um e-mail igual ao do exemplo abaixo:

   ![Email exemplo - subscrição falhada](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Se não receber nenhuma mensagem de e-mail, verifique a pasta de lixo do e-mail. O filtro de lixo de e-mail poderá redirecionar estes tipos de mensagem de e-mail. Caso contrário, se não tiver a certeza de que a aplicação lógica foi executada corretamente, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Resolver problemas da sua aplicação lógica).

Parabéns, acabou de criar e executar uma aplicação lógica que integra informações do Azure, dos serviços Microsoft e de outras aplicações SaaS.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa da aplicação lógica da amostra, elimine o grupo de recursos que contém a sua aplicação lógica e recursos relacionados. 

1. No menu principal do Azure, aceda a **Grupos de recursos** e selecione o grupo de recursos para a sua aplicação lógica.

1. No menu do grupo de recursos, selecione **Visão Geral** > **Eliminar grupo de recursos**. 

   !["Descrição geral" > "Eliminar grupo de recursos"](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Introduza o nome do grupo de recursos como confirmação e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma aplicação lógica que gere aprovações para pedidos de listas de correio. Agora, aprenda a criar uma aplicação lógica que processa e armazena anexos de e-mail através da integração de serviços do Azure, como o Armazenamento do Azure e as Funções do Azure.

> [!div class="nextstepaction"]
> [Processar anexos de e-mail](../logic-apps/tutorial-process-email-attachments-workflow.md)

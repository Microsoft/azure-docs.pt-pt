---
title: Criar fluxos de trabalho de aprovação para processar pedidos de listas de correio | Azure Logic Apps | Microsoft Docs
description: Tutorial - Como criar fluxos de trabalho de aprovação automáticos para processar subscrições de listas de correio com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: 016d004a538a1313ca31f36b46e961098051785c
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051723"
---
# <a name="manage-mailing-list-requests-with-azure-logic-apps"></a>Gerir pedidos de listas de correio com o Azure Logic Apps

O Azure Logic Apps ajuda-o a automatizar fluxos de trabalho e a integrar dados nos serviços do Azure, nos serviços Microsoft, noutras aplicações de software como serviço (Saas) e em sistemas no local. Este tutorial mostra como pode criar uma [aplicação lógica](../logic-apps/logic-apps-overview.md) que processa pedidos de subscrição para uma lista de correio gerida pelo serviço [MailChimp](https://mailchimp.com/).
Esta aplicação lógica monitoriza uma conta de e-mail quanto a esses pedidos, envia-os para aprovação e adiciona os membros aprovados à mesma.

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

![Aplicação lógica concluída de alto nível](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-overview.png)

Se não tiver uma subscrição do Azure, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do MailChimp. Crie uma lista com o nome "test-members-ML", em que a aplicação lógica pode adicionar endereços de e-mail dos membros aprovados. Se não tiver uma conta, [inscreva-se numa conta gratuita](https://login.mailchimp.com/signup/) e [saiba como criar listas](https://us17.admin.mailchimp.com/lists/#). 

* Uma conta de email com o Office 365 Outlook ou Outlook.com, que dá suporte a fluxos de trabalho de aprovação. Este artigo utiliza o Outlook do Office 365. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

## <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. No menu principal do Azure, escolha **Criar um recurso** > **Enterprise Integration** > **Aplicação Lógica**.

   ![Criar uma aplicação lógica](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app.png)

2. Em **Criar aplicação lógica**, indique estas informações sobre a sua aplicação lógica, conforme mostrado e descrito. Quando tiver terminado, escolha **Afixar ao dashboard** > **Criar**.

   ![Indicar as informações da aplicação lógica](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **Name** | LA-MailingList | O nome para a aplicação lógica | 
   | **Subscrição** | <*your-Azure-subscription-name*> | O nome para a subscrição do Azure | 
   | **Grupo de recursos** | LA-MailingList-RG | O nome para o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) utilizado para organizar os recursos relacionados | 
   | **Location** | EUA Leste 2 | A região onde pretende armazenar as informações da aplicação lógica | 
   | **Log Analytics** | Desativado | Mantenha a definição **Desativado** para o registo de diagnósticos. | 
   |||| 

3. Depois de o Azure implementar a aplicação, o Estruturador de Aplicações Lógicas abre-se e mostra uma página com um vídeo de introdução e modelos de padrões de aplicações lógicas comuns. Em **Modelos**, escolha **Aplicação Lógica em Branco**.

   ![Escolher o modelo de aplicação lógica em branco](./media/tutorial-process-mailing-list-subscriptions-workflow/choose-logic-app-template.png)

Em seguida, adicione um [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts) que escuta os e-mails recebidos que têm pedidos de subscrição.
Todas as aplicações lógicas têm de ser iniciadas com um acionador, que é acionado quando um evento específico acontece ou quando dados novos satisfazem uma condição específica. Para obter mais informações, veja [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Adicionar acionador para monitorizar e-mails

1. No estruturador, introduza "when email arrives" (quando chegarem e-mails) na caixa de pesquisa. Selecione o acionador para o seu fornecedor de e-mail: **<*fornecedor_de_e-mail*> - When a new email arrives** (Quando é recebido um e-mail novo)
   
   ![Selecione este gatilho para o provedor de email: "Quando um novo email chegar"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Relativamente a contas escolares ou profissionais do Azure, selecione Office 365 Outlook (Outlook do Office 365).
   * Quanto a contas Microsoft pessoais, selecione Outlook.com.

2. Se lhe forem pedidas credenciais, inicie sessão na sua conta de e-mail para que o Logic Apps possa criar uma ligação para a sua conta de e-mail.

3. Agora, especifique os critérios que o acionador verifica em todos os e-mails novos.

   1. Especifique a pasta, o intervalo e a frequência para a verificação dos e-mails.

      ![Especifique a pasta, o intervalo e a frequência para a verificação dos e-mails](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Definição | Valor | Descrição | 
      | ------- | ----- | ----------- | 
      | **Pasta** | A Receber | A pasta de e-mail a monitorizar | 
      | **Intervalo** | 1 | O número de intervalos de espera entre verificações | 
      | **Frequência** | Hora | A unidade de tempo para cada intervalo entre verificações  | 
      |  |  |  | 

   2. Escolha **Mostrar opções avançadas**. Na caixa **Filtro do Assunto**, introduza este texto para o acionador para encontrar o assunto do e-mail: ```subscribe-test-members-ML```

      ![Definir as opções avançadas](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-advanced-options.png)

4. Para ocultar os detalhes do acionador por agora, clique na barra de título do mesmo.

   ![Fechar forma para ocultar os detalhes](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

5. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

   A sua aplicação lógica está agora ativa, mas não faz mais nada que não verificar os e-mails recebidos. 
   Por isso, adicione uma ação que responde quando o acionador é acionado.

## <a name="send-approval-email"></a>Enviar mensagem de e-mail de aprovação

Agora que tem um acionador, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que envia um e-mail para aprovar ou rejeitar o pedido. 

1. No acionador, escolha **+ Novo passo** > **Add an action**. Procure "aprovação" e selecione a ação **<*fornecedor_de_e-mail*>-Send approval email**

   ![Escolha "fornecedor_de_e-mail> - Send approval email"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

2. Indique informações para esta ação, conforme apresentado e descrito: 

   ![Configurar as definições do e-mail de aprovação](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **To** | <*approver-email-address*> | O endereço de e-mail do aprovador. Para fins de teste, pode utilizar o seu próprio endereço. | 
   | **Opções do Utilizador** | Aprovar, Rejeitar | As opções de resposta que o aprovador pode escolher. Por predefinição, o aprovador pode escolher "Aprovar" ou "Rejeitar" como resposta. | 
   | **Subject** | Aprovar pedido de membro para test-members-ML | Um assunto de e-mail descritivo | 
   |  |  |  | 

   Por agora, ignore a lista de conteúdo dinâmico ou a lista de parâmetros inline que aparece quando clica dentro das caixas de edição específicas. 
   Esta lista permite-lhe selecionar parâmetros de ações anteriores que pode utilizar como entradas no seu fluxo de trabalho. 
   A largura do browser determina a lista que é apresentada. 
 
4. Guarde a aplicação lógica.

Em seguida, adicione uma condição para verificar a resposta que o aprovador escolheu.

## <a name="check-approval-response"></a>Verificar a resposta de aprovação

1. Na ação **Send approval email**, escolha **+ Novo passo** > **Adicionar uma condição**.

   É apresentada a forma de condição, juntamente com todos os parâmetros disponíveis que pode incluir como entradas no seu fluxo de trabalho. 

2. Mude o nome da condição com uma descrição melhor.

   1. Na barra de título da condição, escolha o botão **reticências** ( **...** ) > **Rename** (Mudar o nome).

      Por exemplo, se o browser estiver na vista estreita:

      ![Mudar o nome da condição](./media/tutorial-process-mailing-list-subscriptions-workflow/condition-rename.png)

      Se o browser estiver na vista alargada e a lista de conteúdo dinâmico bloquear o acesso ao botão de reticências, escolha **Add dynamic content** (Adicionar conteúdo dinâmico), dentro da condição, para fechar a lista.

   2. Mude o nome da condição com a descrição ```If request approved```

3. Crie uma condição que verifica se o aprovador selecionou **Aprovar**:

   1. Dentro da condição, clique dentro da caixa **Escolher um valor**, que está no lado esquerdo (vista de browser alargada) ou na parte superior (vista de browser estreita).
   Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione o campo **SelectedOption** em **Send approval email**.

      Por exemplo, se estiver a trabalhar na vista alargada, a condição terá este aspeto:

      ![Em "Send approval email", selecione "SelectedOption"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   2. Na caixa do operador de comparação, selecione o operador **é igual a**

   3. Na caixa da direita (vista alargada) ou da parte inferior (vista estreita) **Escolher valor**, introduza o valor ```Approve```

      Quando estiver pronto, a condição terá o aspeto deste exemplo:

      ![Concluir condição](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

4. Guarde a aplicação lógica.

Em seguida, especifique a ação que a sua aplicação lógica executa quando o revisor aprova o pedido. 

## <a name="add-member-to-mailchimp-list"></a>Adicionar membro à lista do MailChimp

Agora, adicione uma ação que adiciona o membro aprovado à sua lista de correio.

1. Dentro do ramo **Se verdadeiro** da condição, escolha **Adicionar uma ação**.
Pesquise por "MailChimp" e selecione esta ação: **MailChimp-Adicionar membro à lista**

   ![Selecione "MailChimp - Add member to list"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

3. Se lhe for pedido que inicie sessão na sua conta do MailChimp, inicie-a com as suas credenciais do MailChimp.

4. Indique informações para esta ação, conforme apresentado e descrito aqui:

   ![Indicar informações para "Adicionar membro à lista"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **ID da Lista** | test-members-ML | O nome da sua lista de correio do MailChimp | 
   | **Estado** | subscrito | O estado da subscrição do membro novo. Para obter mais informações, veja [Manage subscribers with the MailChimp API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/) (Grir subscritores com a API do MailChimp). | 
   | **Endereço de E-mail** | <*new-member-email-address*> | Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione **De** em **When a new email arrives**, o que transmite o endereço de e-mail do membro novo. 
   |  |  |  | 

5. Guarde a aplicação lógica.

Em seguida, adicione uma condição para verificar se o membro novo foi associado com êxito à sua lista de correio. Dessa forma, a aplicação lógica notifica-o se esta operação foi concluída com êxito ou se falhou.

## <a name="check-for-success-or-failure"></a>Verificar se a operação foi concluída com êxito ou se falhou

1. No ramo **Se verdadeiro**, na ação **Add member to list** , escolha **Mais...**  > **Adicionar uma condição**.

2. Mude o nome da condição com a descrição ```If add member succeeded```

3. Crie uma condição que verifica se o membro aprovado foi adicionado com êxito ou não à lista de correio:

   1. Dentro da condição, clique dentro da caixa **Escolher um valor**, que está no lado esquerdo (vista de browser alargada) ou na parte superior (vista de browser estreita).
   Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione o campo **Status** em **Add member to list**.

      Por exemplo, se estiver a trabalhar na vista alargada, a condição terá este aspeto:

      ![Em "Add member to list", selecione "Estado"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   2. Na caixa do operador de comparação, selecione o operador **é igual a**

   3. Na caixa da direita (vista alargada) ou da parte inferior (vista estreita) **Escolher valor**, introduza o valor ```subscribed```

   Quando estiver pronto, a condição terá o aspeto deste exemplo:

   ![Condição concluída](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Depois, configure os e-mails que vão ser enviados quando a associação do membro aprovado à lista de correio for concluída com êxito ou quando falhar.

## <a name="send-email-if-member-added"></a>Enviar e-mail se o membro for adicionado

1. No ramo **Se verdadeiro** da condição **If add member succeeded**, escolha **Adicionar uma ação**.

   ![No ramo “Se verdadeiro” da condição, escolha “Adicionar uma ação”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

2. Procure "outlook send email" e selecione a ação **<*fornecedor_de_e-mail*>-Send an email**

   ![Adicione a ação para "Send an email"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

3. Mude o nome da ação com a descrição ```Send email on success```

4. Indique informações para esta ação, conforme apresentado e descrito:

   ![Indique informações para o e-mail de êxito](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **To** | <*your-email-address*> | O endereço de e-mail para onde enviar o e-mail de êxito. Para fins de teste, pode utilizar o seu próprio endereço de e-mail. | 
   | **Subject** | <*subject-for-success-email*> | O assunto do e-mail de êxito. Neste tutorial, introduza este texto e selecione o campo especificado em **Add member to list** na lista de parâmetros ou na lista de conteúdo dinâmico: <p>"Êxito! Membro adicionado a ' test-Members-ML ': **Endereço de email**" | 
   | **Corpo** | <*body-for-success-email*> | O conteúdo do corpo do e-mail de êxito. Neste tutorial, introduza este texto e selecione os campos especificados em **Add member to list** na lista de parâmetros ou na lista de conteúdo dinâmico:  <p>"Novo membro ingressou em ' test-Members-ML ': **Endereço de email**"</br>"Status de aceitação do membro: **Status**" | 
   | | | | 

5. Guarde a aplicação lógica.

## <a name="send-email-if-member-not-added"></a>Enviar e-mail se o membro não for adicionado

1. No ramo **Se falso** da condição **If add member succeeded**, escolha **Adicionar uma ação**.

   ![No ramo “Se falso” da condição, escolha “Adicionar uma ação”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

2. Procure "outlook send email" e selecione a ação **<*fornecedor_de_e-mail*>-Send an email**

   ![Adicione a ação para "Send an email"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

3. Mude o nome da ação com a descrição ```Send email on failure```

4. Indique informações para esta ação, conforme apresentado e descrito aqui:

   ![Indique as informações do e-mail de falha](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **To** | <*your-email-address*> | O endereço de e-mail para onde enviar o e-mail de falha. Para fins de teste, pode utilizar o seu próprio endereço de e-mail. | 
   | **Subject** | <*subject-for-failure-email*> | O assunto do e-mail de falha. Neste tutorial, introduza este texto e selecione o campo especificado em **Add member to list** na lista de parâmetros ou na lista de conteúdo dinâmico: <p>"Com falha, membro não adicionado a ' test-Members-ML ': **Endereço de email**" | 
   | **Corpo** | <*body-for-failure-email*> | O conteúdo do corpo do e-mail de falha. Neste tutorial, introduza este texto: <p>"É possível que o membro já exista. Verifique a sua conta do MailChimp." | 
   | | | | 

5. Guarde a aplicação lógica. 

Em seguida, teste a sua aplicação lógica, que é agora semelhante a este exemplo:

 ![Aplicação lógica concluída](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-complete.png)

## <a name="run-your-logic-app"></a>Executar a aplicação lógica

1. Envie para si próprio um pedido de e-mail para aderir à sua lista de correio.
Aguarde que o pedido apareça na sua caixa de entrada.

3. Para iniciar manualmente a sua aplicação lógica, na barra de ferramentas do estruturador, escolha **Executar**. 

   Se o assunto do seu e-mail corresponder ao filtro de assunto do acionador, a aplicação lógica envia-lhe o e-mail para aprovar o pedido de subscrição.

4. No e-mail de aprovação, escolha **Aprovar**.

5. Se o endereço de e-mail do subscritor não existir na sua lista de correio, a sua aplicação lógica adiciona o endereço dessa pessoa e envia-lhe a si um e-mail igual ao do exemplo abaixo:

   ![E-mail de êxito](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-success.png)

   Se a sua aplicação lógica não conseguir adicionar o subscritor, receberá um e-mail igual ao do exemplo abaixo:

   ![E-mail de falha](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-failed.png)

   Se não receber nenhuma mensagem de e-mail, verifique a pasta de lixo do e-mail. 
   O filtro de lixo de e-mail poderá redirecionar estes tipos de mensagem de e-mail. 
   Caso contrário, se não tiver a certeza de que a aplicação lógica foi executada corretamente, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Resolver problemas da sua aplicação lógica).

Parabéns, acabou de criar e executar uma aplicação lógica que integra informações do Azure, dos serviços Microsoft e de outras aplicações SaaS.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos que contém a aplicação lógica e os recursos relacionados. No menu principal do Azure, aceda a **Grupos de recursos** e selecione o grupo de recursos para a sua aplicação lógica. Escolha **Eliminar grupo de recursos**. Introduza o nome do grupo de recursos como confirmação e escolha **Eliminar**.

!["Descrição geral" > "Eliminar grupo de recursos"](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma aplicação lógica que gere aprovações para pedidos de listas de correio. Agora, aprenda a criar uma aplicação lógica que processa e armazena anexos de e-mail através da integração de serviços do Azure, como o Armazenamento do Azure e as Funções do Azure.

> [!div class="nextstepaction"]
> [Processar anexos de e-mail](../logic-apps/tutorial-process-email-attachments-workflow.md)

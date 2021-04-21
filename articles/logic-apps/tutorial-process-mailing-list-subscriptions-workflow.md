---
title: Construir fluxos de trabalho automatizados baseados em aprovação
description: Tutorial - Criar um fluxo de trabalho automatizado baseado em aprovação que processa subscrições de listas de correio através da utilização de Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: c6186e6e9f60e852f77943834bcd1ae3d526491d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777280"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Tutorial: Criar fluxos de trabalho automatizados baseados em aprovação utilizando apps Azure Logic

Este tutorial mostra como construir uma [aplicação lógica](../logic-apps/logic-apps-overview.md) de exemplo que automatiza um fluxo de trabalho baseado em aprovação. Especificamente, este exemplo de aplicação lógica processa pedidos de subscrição para uma lista de correio que é gerida pelo serviço [MailChimp.](https://mailchimp.com/) Esta aplicação lógica inclui várias etapas, que começam por monitorizar uma conta de e-mail para pedidos, envia estes pedidos de aprovação, verifica se o pedido obtém ou não aprovação, adiciona membros aprovados à lista de correio eletrónico e confirma se novos membros são ou não adicionados à lista.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
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

* Uma conta e subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Uma conta MailChimp onde criou previamente uma lista chamada "test-members-ML" onde a sua aplicação lógica pode adicionar endereços de e-mail para membros aprovados. Se não tiver uma conta, [inscreva-se numa conta gratuita](https://login.mailchimp.com/signup/)e, em seguida, aprenda [a criar uma lista mailChimp](https://us17.admin.mailchimp.com/lists/#).

* Uma conta de e-mail de um fornecedor de e-mail que é suportado por Aplicações Lógicas, como Office 365 Outlook, Outlook.com ou Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](/connectors/). Este quickstart usa o Office 365 Outlook com uma conta de trabalho ou escola. Se utilizar uma conta de e-mail diferente, os passos gerais permanecem os mesmos, mas a sua UI pode diferir ligeiramente.

* Uma conta de e-mail no Office 365 Outlook ou Outlook.com, que suporta fluxos de trabalho de aprovação. Este tutorial utiliza o Outlook do Office 365. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

* Se a sua aplicação lógica necessitar de comunicar através de uma firewall que limite o tráfego a endereços IP específicos, essa firewall precisa de permitir o acesso tanto aos endereços IP [de entrada](logic-apps-limits-and-config.md#inbound) *como* [de saída](logic-apps-limits-and-config.md#outbound) utilizados pelo serviço De aplicações lógicas ou ao tempo de funcionamento na região de Azure, onde existe a sua aplicação lógica. Se a sua aplicação lógica também utilizar [conectores geridos](../connectors/managed.md)– como o conector Office 365 Outlook ou o conector SQL, ou utilizar [conectores personalizados](/connectors/custom-connectors/)– a firewall também precisa de permitir o acesso de *todos os* [endereços IP de saída geridos](logic-apps-limits-and-config.md#outbound) do conector na região Azure da sua aplicação lógica.

## <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure. Na página inicial do Azure, selecione **Criar um recurso**.

1. No menu Azure Marketplace, selecione **Integration**  >  **Logic App**.

   ![Screenshot que mostra o menu Azure Marketplace com "Integração" e "Logic App" selecionados.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. No painel **da Aplicação Lógica,** forneça as informações descritas aqui sobre a aplicação lógica que pretende criar.

   ![Screenshot que mostra o painel de criação da Logic App e a informação para fornecer a nova aplicação lógica.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Subscrição** | <*Nome de subscrição Azure*> | O nome da subscrição do Azure. Este exemplo utiliza `Pay-As-You-Go`. |
   | **Grupo de recursos** | LA-MailingList-RG | O nome para o [grupo de recursos Azure,](../azure-resource-manager/management/overview.md)que é usado para organizar recursos relacionados. Este exemplo cria um novo grupo de recursos chamado `LA-MailingList-RG` . |
   | **Nome** | LA-MailingList | O nome da sua aplicação lógica, que contém apenas letras, números, hífenes `-` (), sublinha `_` (), parênteses ( `(` , ) e `)` períodos `.` (). Este exemplo utiliza `LA-MailingList`. |
   | **Localização** | E.U.A. Oeste | A região onde armazenar a sua informação lógica de aplicações. Este exemplo utiliza `West US`. |
   | **Log Analytics** | Desligado | Mantenha a definição **Desativado** para o registo de diagnósticos. |
   ||||

1. Quando terminar, selecione **Review + create**. Depois de o Azure validar as informações sobre a sua aplicação lógica, selecione **Criar**.

1. Depois de o Azure implementar a sua aplicação, selecione **Ir para o recurso**.

   O Azure abre o painel de seleção do modelo de Apps Lógicas, que mostra um vídeo de introdução, gatilhos geralmente usados e padrões de modelo de aplicações lógicas.

1. Percorra as secções de gatilhos para baixo para a secção **Modelos** e selecione **A App lógica em branco**.

   ![Screenshot que mostra o painel de seleção do modelo de Apps Lógicas com "Blank Logic App" selecionado.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Em seguida, adicione um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) do Outlook que ouve os e-mails recebidos com pedidos de subscrição. Cada aplicação lógica deve começar com um gatilho, que dispara quando um evento específico acontece ou quando novos dados encontram uma condição específica. Para obter mais informações, veja [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Adicionar acionador para monitorizar e-mails

1. Na caixa de pesquisa do Logic Apps Designer, `when email arrives` insira e selecione o gatilho chamado **Quando um novo e-mail chegar**.

   * Para contas escolares ou profissionais do Azure, selecione **Office 365 Outlook** (Outlook do Office 365).
   * Par contas Microsoft pessoais, selecione **Outlook.com**.

   Este exemplo continua selecionando o Office 365 Outlook.

   ![Screenshot que mostra a caixa de pesquisa do Logic Apps Designer que contém o termo de pesquisa "quando o e-mail chega" e o gatilho "Quando um novo e-mail chega" aparece selecionado.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Se ainda não tiver uma ligação, faça o seu sômis e autente o acesso à sua conta de e-mail quando solicitado.

   A Azure Logic Apps cria uma ligação à sua conta de e-mail.

1. No gatilho, forneça os critérios para verificar novos emails.

   1. Especifique a pasta para verificar e-mails e mantenha as outras propriedades definidas para os seus valores predefinidos.

      ![Screenshot que mostra o designer com a ação "Quando um novo e-mail chega" e "Pasta" definida para "Inbox".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. Adicione a propriedade 'Filtro de **Objecto'** do gatilho para que possa filtrar e-mails com base na linha de assunto. Abra a nova lista **de parâmetros** e selecione **Filtro de Assunto**.

      ![Screenshot que mostra a lista aberta "Adicionar novo parâmetro" com "Filtro de Assunto" selecionado.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Para obter mais informações sobre as propriedades deste gatilho, consulte a [referência do conector Do Office 365 Outlook](/connectors/office365/) ou a referência do [conector Outlook.com](/connectors/outlook/).

   1. Depois de a propriedade aparecer no gatilho, introduza este texto: `subscribe-test-members-ML`

      ![Screenshot que mostra a propriedade "Filtro de Sujeito" com o texto "subscrever-test-members-ML" introduzido.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Para ocultar os detalhes do gatilho por enquanto, desabar a forma clicando dentro da barra de título da forma.

   ![Screenshot que mostra a forma do gatilho colapsado.](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

A sua aplicação lógica está agora ativa, mas não faz mais nada que não verificar os e-mails recebidos. Por isso, adicione uma ação que responde quando o acionador é acionado.

## <a name="send-approval-email"></a>Enviar o e-mail de aprovação

Agora que tem um acionador, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que envia um e-mail para aprovar ou rejeitar o pedido.

1. No Logic Apps Designer, no âmbito do **"Quando um novo e-mail" chega,** selecione **Novo passo**.

1. Em **'Escolha uma operação'** na caixa de pesquisa, insira `send approval` e selecione a ação denominada **Enviar por email de aprovação**.

   ![Screenshot que mostra a lista "Escolha uma operação" filtrada por ações de "aprovação" e a ação "Enviar e-mails de aprovação" selecionadas.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Agora introduza os valores das propriedades especificadas mostradas e descritas aqui. deixando todos os outros nos seus valores padrão. Para obter mais informações sobre estas propriedades, consulte a [referência do conector Do Office 365 Outlook](/connectors/office365/) ou a referência do [conector Outlook.com](/connectors/outlook/).

   ![Screenshot que mostra as propriedades de "Enviar e-mail de aprovação"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Para** | <*endereço de e-mail de aprovação*> | O endereço de e-mail do aprovador. Para fins de teste, pode utilizar o seu próprio endereço. Este exemplo utiliza o `sophiaowen@fabrikam.com` endereço de e-mail fictício. |
   | **Assunto** | `Approve member request for test-members-ML` | Um assunto de e-mail descritivo |
   | **Opções do Utilizador** | `Approve, Reject` | Certifique-se de que esta propriedade especifica as opções de resposta que o aprovador pode selecionar, que são **Aprovar** ou **Rejeitar** por padrão. |
   ||||

   > [!NOTE]
   > Quando clica dentro de algumas caixas de edição, aparece a lista de conteúdos dinâmicos, que pode ignorar por enquanto. Esta lista mostra as saídas de ações anteriores que estão disponíveis para selecionar como entradas para as ações subsequentes no seu fluxo de trabalho.
 
1. Guarde a sua aplicação lógica.

Em seguida, adicione uma condição que verifique a resposta selecionada do aprovador.

## <a name="check-approval-response"></a>Verificar a resposta de aprovação

1. No âmbito da ação **de e-mail de aprovação** enviar, selecione **Novo passo**.

1. Em **Selecione uma operação,** selecione **Built-in**. Na caixa de pesquisa, insira `condition` e selecione a ação **denominada Condição**.

   ![Screenshot que mostra a caixa de pesquisa "Escolha uma operação" com "Incorporado" selecionado e "condição" como termo de pesquisa, enquanto a ação "Condição" aparece selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Na barra de título **condition,** selecione o botão **elipses** **(...**) e, em seguida, selecione **Rename**. Mude o nome da condição com a descrição `If request approved`

   ![Screenshot que mostra o botão de elipses selecionado com a lista "Definições" aberta e selecionado o comando "Renomear".](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Construa uma condição que verifique se o aprovador selecionado **aprova .**

   1. No lado esquerdo da condição, clique no interior da caixa **de valor Escolha.**

   1. A partir da lista de conteúdos dinâmicos que aparece, no **e-mail de aprovação enviar,** selecione a propriedade **EscolhaOption.**

      ![Screenshot que mostra a lista de conteúdos dinâmicos onde na secção "Enviar e-mail de aprovação", a saída "SelecioneOption" aparece selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Na caixa de comparação média, selecione o **é igual ao** operador.

   1. No lado direito da condição, na caixa **de valor escolher,** insira o texto, `Approve` .

      Quando terminar, a condição parece este exemplo:

      ![Screenshot que mostra a condição final para o exemplo de pedido aprovado](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Guarde a sua aplicação lógica.

Em seguida, especifique a ação que a sua aplicação lógica executa quando o revisor aprova o pedido. 

## <a name="add-member-to-mailchimp-list"></a>Adicionar membro à lista do MailChimp

Adicione agora uma ação que adicione o membro aprovado à sua lista de correio.

1. No ramo **True** da condição, selecione **Adicione uma ação**.

1. No âmbito da caixa de pesquisa de **operação,** selecione **All**. Na caixa de pesquisa, insira `mailchimp` e selecione a ação **denominada Adicionar membro à lista**.

   ![Screenshot que mostra a caixa "Escolha uma operação" com o termo de pesquisa "mailchimp" e a ação "Adicionar membro à lista" selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Se ainda não tiver uma ligação com a sua conta MailChimp, é solicitado que faça o seu sedível.

1. No **membro Adicionar para listar a** ação, forneça as informações como mostrado e descrito aqui:

   ![Screenshot que mostra a informação de ação "Adicionar membro à lista".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **ID da Lista** | Yes | <*nome da lista de correio*> | Selecione o nome da sua lista de correio mailChimp. Este exemplo utiliza `test-members-ML`. |
   | **Endereço de e-mail** | Yes | <*novo membro-endereço de e-mail*> | Na lista de conteúdos dinâmicos que abre, a partir da secção **Quando chega um novo email,** selecione **From**, que é saída do gatilho e especifica o endereço de e-mail para o novo membro. |
   | **Estado** | Yes | <*estatuto de subscrição de membros*> | Selecione o estado de subscrição a definir para o novo membro. Este exemplo `subscribed` seleciona. <p>Para obter mais informações, veja [Manage subscribers with the MailChimp API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/) (Grir subscritores com a API do MailChimp). |
   |||||

   Para obter mais informações sobre o **membro Adicionar para listar** propriedades de ação, consulte a referência do [conector MailChimp](/connectors/mailchimp/).

1. Guarde a sua aplicação lógica.

Em seguida, adicione uma condição para verificar se o membro novo foi associado com êxito à sua lista de correio. Desta forma, a sua aplicação lógica pode notificá-lo se esta operação foi bem sucedida ou falhou.

## <a name="check-for-success-or-failure"></a>Verificar se a operação foi concluída com êxito ou se falhou

1. No ramo **True,** sob o **membro Adicionar para listar ação,** selecione Adicionar uma **ação**.

1. Em **Selecione uma operação,** selecione **Built-in**. Na caixa de pesquisa, insira `condition` e selecione a ação **denominada Condição**.

1. Mude o nome da condição com a descrição `If add member succeeded`

1. Crie uma condição que verifica se o membro aprovado foi adicionado com êxito ou não à lista de correio:

   1. No lado esquerdo da condição, clique no interior da caixa **de valor Escolha.** A partir da lista de conteúdos dinâmicos que aparece, na secção Adicionar à secção **de lista,** selecione a propriedade **Status.**

      Por exemplo, a sua condição parece este exemplo:

      ![Screenshot que mostra a caixa do lado esquerdo da condição "Escolha um valor" com "Status" introduzido.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Na caixa de comparação média, selecione o **é igual ao** operador.

   1. No lado direito da condição, na caixa **de valor Escolha,** insira este texto: `subscribed`

      Quando terminar, a condição parece este exemplo:

      ![Screenshot que mostra a condição final para verificar subscrição bem sucedida ou falhada.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Em seguida, crie os e-mails para enviar quando o membro aprovado tiver sucesso ou não aderir à sua lista de correio.

## <a name="send-email-if-member-added"></a>Enviar e-mail se o membro for adicionado

1. Sob a condição **de adicionar membro bem sucedido,** no ramo **True,** selecione **Adicione uma ação**.

   ![Screenshot que mostra o ramo "True" da condição "Se adicionar membro" com "Adicionar uma ação" selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Na caixa de pesquisa de **operação Escolha,** insira `outlook send email` e selecione a ação denominada **Enviar um e-mail**.

   ![Screenshot que mostra a caixa de pesquisa "Escolha uma operação" com "outlook send email" inserida e a ação "Enviar um e-mail" selecionada para notificação.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Mude o nome da ação com a descrição `Send email on success`

1. No **email Enviar e-mail sobre** ação de sucesso, forneça as informações como mostrado e descrito aqui:

   ![Screenshot que mostra a ação "Enviar e-mail sobre o sucesso" e as informações fornecidas para o e-mail de sucesso.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Corpo** | Yes | <*sucesso-e-mail-corpo*> | O conteúdo do corpo do e-mail de êxito. Para este tutorial, siga estes passos: <p>1. Introduza este texto com um espaço de fuga: `New member has joined "test-members-ML":` <p>2. Da lista de conteúdos dinâmicos que aparece, selecione a propriedade **'Email Address'.** <p>**Nota:** Se esta propriedade não aparecer, junto ao membro adicionar ao cabeçalho da secção **de lista,** selecione **Ver mais**. <p>3. Na linha seguinte, insira este texto com um espaço de fuga: `Member opt-in status: ` <p>4. Da lista de conteúdos dinâmicos, em **lista de adição a lista,** selecione a propriedade **Status.** |
   | **Assunto** | Yes | <*sucesso-e-mail-assunto*> | O assunto do e-mail de êxito. Para este tutorial, siga estes passos: <p>1. Introduza este texto com um espaço de fuga: `Success! Member added to "test-members-ML": ` <p>2. Da lista de conteúdos dinâmicos, em **lista de adicionar ao membro,** selecione a propriedade **'Endereço de e-mail'.** |
   | **Para** | Yes | <*seu endereço de e-mail*> | O endereço de e-mail para onde enviar o e-mail de êxito. Para fins de teste, pode utilizar o seu próprio endereço de e-mail. |
   |||||

1. Guarde a sua aplicação lógica.

## <a name="send-email-if-member-not-added"></a>Enviar e-mail se o membro não for adicionado

1. Sob a condição **de adicionar membro bem sucedido,** no ramo **Falso,** selecione **Adicione uma ação**.

   ![Screenshot que mostra o ramo "Se adicionar membro com sucesso" da condição "Falso" com "Adicionar uma ação" selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Na caixa de pesquisa de **operação Escolha,** insira `outlook send email` e selecione a ação denominada **Enviar um e-mail**.

   ![Screenshot que mostra a caixa de pesquisa "Escolha uma operação" com "outlook send email" inserida e a ação "Enviar um e-mail" selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Mude o nome da ação com a descrição `Send email on failure`

1. Forneça informações sobre esta ação como mostrado e descrito aqui:

   ![Screenshot que mostra a ação "Enviar e-mail sobre falha" e as informações fornecidas para o e-mail de falha.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Corpo** | Yes | <*corpo-para-falha-e-mail*> | O conteúdo do corpo do e-mail de falha. Neste tutorial, introduza este texto: <p>`Member might already exist. Check your MailChimp account.` |
   | **Assunto** | Yes | <*assunto-para-falha-e-mail*> | O assunto do e-mail de falha. Para este tutorial, siga estes passos: <p>1. Introduza este texto com um espaço de fuga: `Failed, member not added to "test-members-ML": ` <p>2. Da lista de conteúdos dinâmicos, em **lista de adicionar ao membro,** selecione a propriedade **'Endereço de e-mail'.** |
   | **Para** | Yes | <*seu endereço de e-mail*> | O endereço de e-mail para onde enviar o e-mail de falha. Para fins de teste, pode utilizar o seu próprio endereço de e-mail. |
   |||||

1. Guarde a sua aplicação lógica. 

Em seguida, teste a sua aplicação lógica, que é agora semelhante a este exemplo:

![Screenshot que mostra o exemplo terminado fluxo de trabalho de aplicações lógicas.](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Executar a aplicação lógica

1. Envie para si próprio um pedido de e-mail para aderir à sua lista de correio. Aguarde que o pedido apareça na sua caixa de entrada.

1. Para iniciar manualmente a sua aplicação lógica, na barra de ferramentas do designer, selecione **Run**. 

   Se o assunto do seu e-mail corresponder ao filtro de assunto do acionador, a aplicação lógica envia-lhe o e-mail para aprovar o pedido de subscrição.

1. No e-mail de aprovação que recebe, **selecione Aprovar.**

1. Se o endereço de e-mail do subscritor não existir na sua lista de correio, a sua aplicação lógica adiciona o endereço dessa pessoa e envia-lhe a si um e-mail igual ao do exemplo abaixo:

   ![Screenshot que mostra o e-mail de exemplo para uma subscrição bem sucedida.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Se a sua aplicação lógica não conseguir adicionar o subscritor, receberá um e-mail igual ao do exemplo abaixo:

   ![Screenshot que mostra o e-mail de exemplo para uma subscrição falhada.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > Se não receber nenhuma mensagem de e-mail, verifique a pasta de lixo do e-mail. O filtro de lixo de e-mail poderá redirecionar estes tipos de mensagem de e-mail. Caso contrário, se não tiver a certeza de que a aplicação lógica foi executada corretamente, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Resolver problemas da sua aplicação lógica).

Parabéns, acabou de criar e executar uma aplicação lógica que integra informações do Azure, dos serviços Microsoft e de outras aplicações SaaS.

## <a name="clean-up-resources"></a>Limpar os recursos

A sua aplicação lógica continua a funcionar até desativar ou eliminar a aplicação. Quando já não precisar da aplicação lógica da amostra, elimine o grupo de recursos que contém a sua aplicação lógica e recursos relacionados.

1. Na caixa de pesquisa do portal Azure, insira o nome para o grupo de recursos que criou. A partir dos resultados, em **Grupos de Recursos,** selecione o grupo de recursos.

   Este exemplo criou o grupo de recursos denominado `LA-MailingList-RG` .

   ![Screenshot que mostra a caixa de pesquisa Azure com "la-mailinglist-rg" inserida e **LA-MailingList-RG** selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Se a página inicial do Azure mostrar o grupo de recursos sob **recursos recentes,** pode selecionar o grupo na página inicial.

1. No menu do grupo de recursos, verifique **se a visão geral** está selecionada. Na barra de **ferramentas** do painel de ferramentas, selecione **Delete resource group**.

   ![Screenshot que mostra o painel "Visão Geral" do grupo de recursos e na barra de ferramentas do painel, "Delete resource group" é selecionado.](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. No painel de confirmação que aparece, introduza o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma aplicação lógica que trata de aprovações para pedidos de lista de correio. Agora, aprenda a criar uma aplicação lógica que processa e armazena anexos de e-mail através da integração de serviços do Azure, como o Armazenamento do Azure e as Funções do Azure.

> [!div class="nextstepaction"]
> [Processar anexos de e-mail](../logic-apps/tutorial-process-email-attachments-workflow.md)

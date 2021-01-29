---
title: Integrar-se com o Office 365 Outlook
description: Automatizar tarefas e fluxos de trabalho que gerem e-mails, contactos e calendários no Office 365 Outlook utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 790879894c3b268fcd55aafc96507319b29fe1e5
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055081"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Gerir e-mails, contactos e calendários no Outlook do Office 365 com o Azure Logic Apps

Com [as Apps Azure Logic](../logic-apps/logic-apps-overview.md) e o [conector Office 365 Outlook,](/connectors/office365connector/)pode criar tarefas e fluxos de trabalho automatizados que gerem o seu trabalho ou conta escolar através da construção de aplicações lógicas. Por exemplo, pode automatizar as seguintes tarefas:

* Receber, enviar e responder ao e-mail.
* Marque reuniões no seu calendário.
* Adicione e edite os contactos.

Pode utilizar qualquer gatilho para iniciar o seu fluxo de trabalho, por exemplo, quando um novo e-mail chega, quando um item de calendário é atualizado, ou quando um evento acontece num serviço de diferença, como o Salesforce. Pode utilizar ações que respondam ao evento de gatilho, por exemplo, enviar um e-mail ou criar um novo evento de calendário.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Outlook onde se inscreve com uma [conta de trabalho ou escola.](https://www.office.com/) Se tiver uma @outlook.com conta ou @hotmail.com conta, utilize o [conector Outlook.com.](../connectors/connectors-create-api-outlook.md) Para ligar ao Outlook com uma conta de utilizador diferente, como uma conta de serviço, consulte [Connect utilizando outras contas.](#connect-using-other-accounts)

* Uma conta e subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* A aplicação lógica onde pretende aceder à sua conta Outlook. Para iniciar o seu fluxo de trabalho com um gatilho do Office 365 Outlook, precisa de ter uma [aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para adicionar uma ação do Office 365 Outlook ao seu fluxo de trabalho, a sua aplicação lógica já precisa de ter um gatilho.

## <a name="add-a-trigger"></a>Adicionar um acionador

Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um evento que inicia o fluxo de trabalho na sua aplicação lógica. Esta aplicação lógica de exemplo utiliza um gatilho de "sondagens" que verifica qualquer evento de calendário atualizado na sua conta de e-mail, com base no intervalo e frequência especificados.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica em branco no Logic App Designer.

1. Na caixa de pesquisa, introduza `office 365 outlook` como filtro. Este exemplo seleciona **Quando um próximo evento começa em breve**.
   
   ![Selecione o gatilho para iniciar a sua aplicação lógica](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Se não tiver uma ligação ativa à sua conta Outlook, é solicitado que faça o seu sessão e crie essa ligação. Para ligar ao Outlook com uma conta de utilizador diferente, como uma conta de serviço, consulte [Connect utilizando outras contas.](#connect-using-other-accounts) Caso contrário, forneça a informação sobre as propriedades do gatilho.

   > [!NOTE]
   > A sua ligação não expira até ser revogada, mesmo que altere as suas credenciais de inscrição. Para obter mais informações, consulte [as vidas de símbolos configurantes no Diretório Ativo Azure](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Este exemplo seleciona o calendário que o gatilho verifica, por exemplo:

   ![Configure as propriedades do gatilho](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. No gatilho, desaccione os valores **de Frequência** e **Intervalo.** Para adicionar outras propriedades disponíveis do gatilho, como **o Fuso Horário,** selecione essas propriedades da nova lista de **parâmetros Add.**

   Por exemplo, se quiser que o gatilho verifique o calendário a cada 15 minutos, desaccione **a frequência** ao **minuto** e desemocione **o intervalo** para `15` . 

   ![Definir frequência e intervalo para o gatilho](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Na barra de ferramentas do designer, **selecione Save**.

Agora adicione uma ação que corre depois do gatilho disparar. Por exemplo, pode adicionar a ação de mensagem Twilio **Enviar,** que envia um texto quando um evento de calendário começa em 15 minutos.

## <a name="add-an-action"></a>Adicionar uma ação

Uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma operação que é gerida pelo fluxo de trabalho na sua aplicação lógica. Esta aplicação lógica de exemplo cria um novo contacto no Office 365 Outlook. Pode utilizar a saída a partir de outro gatilho ou ação para criar o contacto. Por exemplo, suponha que a sua aplicação lógica utilize o gatilho Dynamics 365, **quando um disco é criado**. Pode adicionar o Office 365 Outlook **Criar ação de contacto** e utilizar as saídas do gatilho SalesForce para criar o novo contacto.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Para adicionar uma ação como último passo no seu fluxo de trabalho, selecione **Novo passo**. 

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre esses degraus. Selecione o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Na caixa de pesquisa, introduza `office 365 outlook` como filtro. Este exemplo seleciona **Criar contacto.**

   ![Selecione a ação para executar na sua aplicação lógica](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Se não tiver uma ligação ativa à sua conta Outlook, é solicitado que faça o seu sessão e crie essa ligação. Para ligar ao Outlook com uma conta de utilizador diferente, como uma conta de serviço, consulte [Connect utilizando outras contas.](#connect-using-other-accounts) Caso contrário, forneça a informação sobre os imóveis da ação.

   > [!NOTE]
   > A sua ligação não expira até ser revogada, mesmo que altere as suas credenciais de inscrição. Para obter mais informações, consulte [as vidas de símbolos configurantes no Diretório Ativo Azure](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Este exemplo seleciona a pasta de contactos onde a ação cria o novo contacto, por exemplo:

   ![Configure os imóveis da ação](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Para adicionar outras propriedades de ação disponíveis, selecione essas propriedades da nova lista de **parâmetros Adicionar.**

1. Na barra de ferramentas do designer, **selecione Save**.

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>Conecte-se usando outras contas

Se tentar ligar o Outlook utilizando uma conta diferente da que está atualmente assinada no Azure, poderá obter [erros de sso únicos.sso.](../active-directory/manage-apps/what-is-single-sign-on.md) Este problema acontece quando se inscreve no portal Azure com uma conta, mas utiliza uma conta diferente para criar a ligação. O designer espera que utilize a conta que está assinada no portal Azure. Para resolver este problema, tem estas opções:

* Crie a outra conta com o papel **do Contribuinte** no grupo de recursos da sua aplicação lógica.

  1. No menu do grupo de recursos da sua aplicação lógica, selecione **Access control (IAM)**. Criar a outra conta com a **função Contribuinte.** 
  
     Para obter mais informações, veja [Utilizar o portal do Azure para adicionar ou remover atribuições de funções do Azure](../role-based-access-control/role-assignments-portal.md).

  1. Depois de configurar esta função, inscreva-se no portal Azure com a conta que agora tem permissões de Contribuinte. Agora pode utilizar esta conta para criar a ligação ao Outlook.

* Crie a outra conta para que o seu trabalho ou conta escolar tenha permissões de "enviar as".

   Se tiver permissões de administração, na caixa de correio da conta de serviço, crie a sua conta de trabalho ou escola com **o Envio** ou Envio em **nome das** permissões. Para obter mais informações, consulte [Dar permissões na caixa de correio a outro utilizador - Ajuda de administração](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user). Em seguida, pode criar a ligação utilizando o seu trabalho ou conta escolar. Agora, em gatilhos ou ações onde pode especificar o remetente, pode utilizar o endereço de e-mail da conta de serviço.

   Por exemplo, a ação **Enviar um email** tem um parâmetro opcional, a partir de **(Enviar como)** que pode adicionar à ação e utilizar o endereço de e-mail da sua conta de serviço como remetente. Para adicionar este parâmetro, siga estes passos:

   1. Na ação **Enviar um e-mail,** abra a lista **de parâmetros Adicionar** e selecione o parâmetro De **(Enviar as)** parâmetro.

   1. Depois de o parâmetro aparecer na ação, insira o endereço de e-mail da conta de serviço.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre este conector, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/office365/). 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)

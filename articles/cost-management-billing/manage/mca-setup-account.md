---
title: Configurar a faturação do Contrato de Cliente Microsoft – Azure
description: Saiba como configurar a conta de faturação para um Contrato de Cliente da Microsoft. Veja os pré-requisitos da configuração e veja recursos disponíveis adicionais.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/20/2020
ms.author: banders
ms.openlocfilehash: 7e930ca938b2531fb001c327132f79f703b2ea74
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316418"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Configurar a conta de faturação para um Contrato de Cliente da Microsoft

Se a sua inscrição direta do Contrato Enterprise tiver expirado ou estiver prestes a expirar, poderá assinar um Contrato de Cliente Microsoft para renovar a inscrição. Este artigo descreve as alterações à sua faturação atual após a configuração e orienta-o ao longo da configuração da nova conta de faturação. Atualmente, os Contratos Enterprise indiretos a expirar podem ser renovados com um Contrato de Cliente Microsoft.

A renovação inclui os seguintes passos:

1. Aceitar o novo Contrato de Cliente da Microsoft. Trabalhe com o seu representante no terreno da Microsoft para compreender os detalhes e aceitar o novo contrato.
2. Configurar a nova conta de faturação criada para o novo Contrato de Cliente da Microsoft.

Para configurar a conta de faturação, deve realizar a transição da faturação das subscrições do Azure, da sua inscrição de Contrato Enterprise para a nova conta. A configuração não afeta os serviços do Azure em execução nas suas subscrições. No entanto, altera a forma como vai gerir a faturação das suas subscrições.

- Em vez do [EA Portal](https://ea.azure.com), vai gerir os serviços e a faturação do Azure no [portal do Azure](https://portal.azure.com).
- Receberá mensalmente uma fatura digital dos seus custos. Pode ver e analisar a fatura na página Azure Cost Management + Faturação.
- Em vez de departamentos e conta na sua inscrição do Contrato Enterprise, utilizará a estrutura e os âmbitos de faturação da nova conta para gerir e organizar a faturação.

Antes de iniciar a configuração, recomendamos que realize as ações seguintes:

- **Compreender a nova conta de faturação**
  - A nova conta simplifica a faturação da sua organização. [Obter uma descrição geral rápida da nova conta de faturação](../understand/mca-overview.md)
- **Verificar o acesso para concluir a configuração**
  - Apenas os utilizadores com determinadas permissões administrativas podem concluir a configuração. Verifique se tem o [acesso necessário para concluir a configuração](#access-required-to-complete-the-setup).
- **Compreender as alterações à hierarquia de faturação**
  - A nova conta de faturação é organizada de uma forma diferente da inscrição do Contrato Enterprise. [Compreender as alterações à hierarquia de faturação na nova conta](#understand-changes-to-your-billing-hierarchy).
- **Compreender as alterações ao acesso dos seus administradores de faturação**
  - Os administradores da inscrição do Contrato Enterprise obtêm acesso aos âmbitos de faturação na nova conta. [Compreender as alterações do acesso](#changes-to-billing-administrator-access).
- **Ver as funcionalidades do Contrato Enterprise que são substituídas pela nova conta**
  - Veja as funcionalidades da inscrição do Contrato Enterprise que são substituídas pelas funcionalidades na nova conta.
- **Ver respostas às perguntas mais comuns**
  - Veja as [informações adicionais](#additional-information) para saber mais sobre a configuração.

## <a name="access-required-to-complete-the-setup"></a>Acesso necessário para concluir a configuração

Para concluir a configuração, precisa do seguinte acesso:

- O proprietário do perfil de faturação que foi criado quando o Contrato de Cliente da Microsoft foi assinado. Para saber mais sobre perfis de faturação, veja [Compreender os perfis de faturação](../understand/mca-overview.md#billing-profiles).

- O administrador do Enterprise na inscrição que é renovada.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Se não for um administrador do Enterprise na inscrição

Pode pedir aos administradores do Enterprise da inscrição para concluírem a configuração da sua conta de faturação.

1. Inicie sessão no portal do Azure com a ligação no e-mail que recebeu quando assinou o Contrato de Cliente da Microsoft.

2. Se não tiver o e-mail, inicie sessão com a ligação a seguir. Substitua `<enrollmentNumber>` pelo número de inscrição do contrato Enterprise que foi renovado.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. Selecione os administradores do Enterprise para os quais quer enviar o pedido.

   ![Captura de ecrã que mostra o convite aos administradores do Enterprise](./media/mca-setup-account/ea-mca-invite-admins.png)

4. Selecione **Enviar pedido**.

   Os administradores receberão um e-mail com instruções para concluírem a configuração.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Se não for um proprietário do perfil de faturação

O utilizador na sua organização, que assinou o Contrato de Cliente da Microsoft, é adicionado como o proprietário no perfil de faturação. Peça ao utilizador que o adicione como um proprietário para que possa concluir a configuração.

## <a name="understand-changes-to-your-billing-hierarchy"></a>Compreender as alterações à hierarquia de faturação

A nova conta de faturação simplifica a faturação da sua organização, ao mesmo tempo que fornece capacidades avançadas de gestão de custos e faturação. O seguinte diagrama explica como a faturação é organizada na nova conta de faturação.

![Imagem de ea-mca-post-transition-hierarchy](./media/mca-setup-account/mca-post-transition-hierarchy.png)

1. Pode utilizar a conta de faturação para gerir a faturação do seu contrato de cliente da Microsoft. Os administradores do Enterprise tornam-se proprietários da conta de faturação. Para saber mais sobre as contas de faturação, veja [Compreender a conta de faturação](../understand/mca-overview.md#your-billing-account).
2. Pode utilizar o perfil de faturação para gerir a faturação da organização, que é semelhante à sua inscrição no Contrato Enterprise. Os administradores de empresas tornam-se proprietários do perfil de faturação. Para saber mais sobre perfis de faturação, veja [Compreender os perfis de faturação](../understand/mca-overview.md#billing-profiles).
3. Pode utilizar uma secção da fatura para organizar os custos com base nas suas necessidades, semelhante aos departamentos na inscrição do Contrato Enterprise. O departamento passa a ser secções da fatura e os administradores de departamentos passam a ser proprietários das respetivas secções da fatura. Para obter mais informações sobre as secções da fatura, veja [Compreender as secções de fature](../understand/mca-overview.md#invoice-sections).
4. As contas que foram criadas no Contrato Enterprise não são suportadas na nova conta de faturação. As subscrições da conta pertencem à respetiva secção da fatura do departamento. Os proprietários das contas podem criar e gerir subscrições para as secções da fatura.

## <a name="changes-to-billing-administrator-access"></a>Alterações ao acesso do administrador de faturação

Dependendo do acesso que possuem, os administradores de faturação na inscrição do Contrato Enterprise obtêm acesso aos âmbitos de faturação na nova conta. A tabela seguinte explica a alteração ao acesso durante a configuração:

| Função existente | Função pós-transição |
| --- | --- |
| **Administrador do Enterprise (Só de Leitura = Não)** | **– Proprietário da conta de faturação** </br> Gere tudo na conta de faturação </br> **- Proprietário do perfil de faturação** </br> Gere tudo no perfil de faturação </br> **– Proprietário da secção da fatura em todas as secções da fatura** </br> Gere tudo nas secções da fatura |
| **Administrador do Enterprise (Só de Leitura = Não)** | **– Leitor da conta de faturação** </br> Vista só de leitura de tudo na conta de faturação</br> **- Leitor do perfil de faturação** </br> Vista só de leitura de tudo no perfil de faturação</br>**- Leitor da secção da fatura em todas as secções da fatura**</br> Vista só de leitura de tudo nas secções da fatura|
| **Administrador de departamento (Só de Leitura = Não)** |**- Proprietário da secção da fatura na secção da fatura criada para o respetivo departamento** </br>Gere tudo na secção da fatura|
| **Administrador de departamento (Só de Leitura = Sim)**|**- Leitor da secção da fatura na secção da fatura criada para o respetivo departamento**</br> Vista só de leitura de tudo na secção da fatura|
| **Proprietário da conta** | **- Criador da subscrição do Azure na secção da fatura criada para o respetivo departamento** </br>  Criar subscrições do Azure para a secção da fatura|

Um inquilino do Azure Active Directory (AD) é selecionado para a nova conta de faturação durante a aceitação do Contrato de Cliente da Microsoft. Se não existir nenhum inquilino na sua organização, será criado um. O inquilino representa a sua organização no Azure Active Directory. Os administradores de inquilinos globais na sua organização utilizam o inquilino para gerir o acesso às aplicações e aos dados na sua organização.

A nova conta apenas suporta utilizadores do inquilino que foi selecionado durante a assinatura do Contrato de Cliente da Microsoft. Se os utilizadores com permissão administrativa no Contrato Enterprise fizerem parte do inquilino, terão acesso à nova conta de faturação durante a configuração. Se não fizerem parte do inquilino, não podem aceder à conta de faturação nova, a menos que os convide.

Quando convida os utilizadores, estes são adicionados ao inquilino como utilizadores convidados e obtêm acesso à conta de faturação. Para convidar os utilizadores, o acesso de convidados deve ser ativado para o inquilino. Para obter mais informações, veja [Control guest access in Azure Active Directory](/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory) (Controlar o acesso de convidados no Azure Active Directory). Se o acesso de convidados estiver desativado, contacte os administradores globais do seu inquilino para o ativar. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>Ver as funcionalidades substituídas

As seguintes funcionalidades do Contrato Enterprise são substituídas pelas novas funcionalidades na conta de faturação de um Contrato de Cliente da Microsoft.

### <a name="enterprise-agreement-accounts"></a>Contas do Contrato Enterprise

As contas que foram criadas na inscrição do Contrato Enterprise não são suportadas na nova conta de faturação. As subscrições da conta pertencem à secção da fatura criada para o respetivo departamento. Os proprietários tornam-se criadores de subscrições do Azure e podem criar e gerir subscrições para as respetivas secções da fatura.

### <a name="notification-contacts"></a>Contactos de notificação

Os contactos de notificação recebem comunicações por e-mail sobre o Contrato Enterprise do Azure. Não são suportados na conta de faturação nova. Os e-mails sobre créditos e faturas do Azure são enviados aos utilizadores que têm acesso aos perfis de faturação na conta de faturação.

### <a name="spending-quotas"></a>Quotas de despesas

As quotas de despesas que foram definidas para os departamentos na sua inscrição do Contrato Enterprise são substituídas por orçamentos na nova conta de faturação. É criado um orçamento para cada conjunto de quotas de despesas nos departamentos na sua inscrição. Para obter mais informações sobre os orçamentos, veja [Tutorial: Create and manage Azure budgets](../costs/tutorial-acm-create-budgets.md) (Tutorial: criar e gerir orçamentos do Azure).

### <a name="cost-centers"></a>Centros de custos

O centro de custos que foi definido nas subscrições do Azure na sua inscrição do Contrato Enterprise é transportado para a nova conta de faturação. No entanto, os centros de custos de departamentos e contas do Contrato Enterprise não são suportados.

## <a name="additional-information"></a>Informações adicionais

As secções a seguir fornecem informações adicionais sobre como configurar a conta de faturação.

### <a name="no-service-downtime"></a>Nenhum tempo de inatividade do serviço

Os serviços do Azure na sua subscrição continuam em execução sem quaisquer interrupções. Apenas fazemos a transição da relação de faturação das suas subscrições do Azure. Não tem impacto nos recursos, grupos de recursos ou grupos de gestão existentes.

### <a name="user-access-to-azure-resources"></a>Acesso de utilizador aos recursos do Azure

O acesso aos recursos do Azure que foi definido com o controlo de acesso baseado em funções do Azure (RBAC do Azure) não é afetado durante a transição.

### <a name="azure-reservations"></a>Reservas do Azure

Todas as Reservas do Azure na sua inscrição do Contrato Enterprise são movidas para a nova conta de faturação. Durante a transição, não são feitas quaisquer alterações aos descontos de reservas que estão a ser aplicados às suas subscrições.

### <a name="azure-marketplace-products"></a>Produtos do Azure Marketplace

Todos os produtos do Azure Marketplace na sua inscrição do contrato Enterprise são movidos juntamente com as subscrições. Não haverá nenhuma alteração ao acesso do serviço dos produtos do Marketplace durante a transição.

### <a name="support-plan"></a>Plano de suporte

Os benefícios de suporte não são transferidos como parte da transição. Adquira um novo plano de suporte para obter os benefícios das subscrições do Azure na nova conta de faturação.

### <a name="past-charges-and-balance"></a>Custos e saldo anteriores

Os custos e o saldo dos créditos antes da transição podem ser visualizados na sua inscrição do Contrato Enterprise através do portal do Azure. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Quando é que a configuração deve ser concluída?

Conclua a configuração da conta de faturação antes que a inscrição do Contrato Enterprise expire. Se a sua inscrição expirar, os serviços nas subscrições do Azure continuarão em execução sem interrupções. No entanto, será faturado pelas tarifas pay as you go dos serviços.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Alterações à inscrição do Contrato Enterprise após a configuração

As subscrições do Azure que são criadas para a inscrição do Contrato Enterprise após a transição podem ser movidas manualmente para a nova conta de faturação. Para obter mais informações, veja [Obter a propriedade da faturação das subscrições do Azure de outros utilizadores](mca-request-billing-ownership.md). Para mover as Reservas do Azure adquiridas após a transição, [contacte o Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Também pode fornecer aos utilizadores acesso à conta de faturação após a transição. Para obter mais informações, veja [Gerir as funções de faturação no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Reverter a transição

A transição não pode ser revertida. Assim que a faturação das subscrições do Azure for transitada para a nova conta de faturação, não poderá revertê-la para a inscrição do Contrato Enterprise.

### <a name="closing-your-browser-during-setup"></a>Fechar o browser durante a configuração

Antes de selecionar **Iniciar transição**, pode fechar o browser. Pode voltar à configuração com a ligação que recebeu no e-mail e iniciar a transição. Se fechar o browser depois de iniciada a transição, a transição continuará em execução. Volte à página do estado da transição para monitorizar o estado mais recente da transição. Receberá um e-mail quando a transição for concluída.

## <a name="complete-the-setup-in-the-azure-portal"></a>Concluir a configuração no portal do Azure

Para concluir a instalação, precisa de aceder à nova conta de faturação e à inscrição do Contrato Enterprise. Para obter mais informações, veja [Acesso necessário para concluir a configuração da conta de faturação](#access-required-to-complete-the-setup).

1. Inicie sessão no portal do Azure com a ligação no e-mail que recebeu quando assinou o Contrato de Cliente da Microsoft.

2. Se não tiver o e-mail, inicie sessão com a ligação a seguir. Substitua `<enrollmentNumber>` pelo número de inscrição do contrato Enterprise que foi renovado.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. Selecione **Iniciar transição** no último passo da configuração. Depois de selecionar iniciar transição:

    ![Captura de ecrã que mostra o assistente da configuração](./media/mca-setup-account/ea-mca-set-up-wizard.png)

    - É criada uma hierarquia de faturação correspondente à sua hierarquia do Contrato Enterprise na nova conta de faturação. Para obter mais informações, veja [Compreender as alterações à hierarquia de faturação](#understand-changes-to-your-billing-hierarchy).
    - Os administradores da sua inscrição do Contrato Enterprise obtêm acesso à nova conta de faturação para que possam continuar a gerir a faturação da sua organização.
    - A faturação das suas subscrições do Azure é transferida para a nova conta. **Durante a transição, os seus serviços do Azure não sofrerão qualquer impacto. Continuarão em execução sem qualquer interrupção**.
    - Se tiver Reservas do Azure, estas são movidas para a nova conta de faturação sem alterações nos benefícios ou no período.

4. Pode monitorizar o estado da transição na página **Estado da transição**.

   ![Captura de ecrã que mostra o estado da transição](./media/mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-setup"></a>Validar a configuração da conta de faturação

 Valide o seguinte para garantir que a nova conta de faturação é configurada corretamente:

### <a name="azure-subscriptions"></a>Subscrições do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/mca-setup-account/search-cmb.png)

3. Selecione a conta de faturação. A conta de faturação será do tipo **Contrato de Cliente da Microsoft**.

4. Selecione **Subscrições do Azure** no lado esquerdo.

   ![Captura de ecrã que mostra a lista de subscrições](./media/mca-setup-account/mca-subscriptions-post-transition.png)

As subscrições do Azure que transitam da inscrição do Contrato Enterprise para a nova conta de faturação são apresentadas na página Subscrições do Azure. Se acreditar que alguma subscrição está em falta, realize manualmente a transição da faturação da subscrição no portal do Azure. Para obter mais informações, veja [Obter a propriedade da faturação das subscrições do Azure de outros utilizadores](mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Reservas do Azure

A reservas do Azure na inscrição do Contrato Enterprise serão movidas para a nova conta de faturação sem alterações nos benefícios ou no período. As transações concluídas antes da transição não aparecerão na nova conta de faturação. No entanto, pode verificar se os benefícios das reservas são aplicados às suas subscrições. Para tal, aceda à [página de reservas do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

### <a name="access-of-enterprise-administrators-on-the-billing-account"></a>Acesso dos administradores do Enterprise na conta de faturação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/mca-setup-account/search-cmb.png)

3. Selecione a conta de faturação do **Contrato de Cliente da Microsoft**.

4. Selecione **Controlo de acesso (IAM)** no lado esquerdo.

   ![Captura de ecrã que mostra o acesso dos administradores empresariais listados como os proprietários da conta de faturação após a transição.](./media/mca-setup-account/mca-ea-admins-ba-access-post-transition.png)

Os administradores do Enterprise são listados como proprietários da conta de faturação, enquanto os administradores do Enterprise com permissões só de leitura são listados como leitores da conta de faturação. Se acreditar que o acesso de algum administrador do Enterprise está em falta, poderá conceder-lhe acesso no portal do Azure. Para obter mais informações, veja [Gerir funções de faturação no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Acesso dos administradores de empresas no perfil de faturação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/mca-setup-account/search-cmb.png)

3. Selecione o perfil de faturação criado na inscrição. Dependendo do seu acesso, pode ser preciso selecionar uma conta de faturação. Na conta de faturação, selecione Perfis de faturação e, em seguida, o perfil de faturação.

4. Selecione **Controlo de acesso (IAM)** no lado esquerdo.

   ![Captura de ecrã que mostra o acesso dos administradores empresariais listados como os proprietários do perfil de faturação após a transição.](./media/mca-setup-account/mca-ea-admins-bp-access-post-transition.png)

Os administradores do Enterprise são listados como proprietários do perfil de faturação, enquanto os administradores do Enterprise com permissões só de leitura são listados como leitores do perfil de faturação. Se acreditar que o acesso de algum administrador do Enterprise está em falta, poderá conceder-lhe acesso no portal do Azure. Para obter mais informações, veja [Gerir funções de faturação no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Acesso dos administradores do Enterprise, administradores de departamentos e proprietários de contas nas secções da fatura

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/mca-setup-account/search-cmb.png).

3. Selecione uma secção da fatura. As secções da fatura têm o mesmo nome que os respetivos departamentos nas inscrições do Contrato Enterprise. Dependendo do seu acesso, pode ser preciso selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione **Secções da fatura**. Na lista de secções da fatura, selecione uma secção da fatura.

   ![Captura de ecrã que mostra a lista das secções da fatura após a transição](./media/mca-setup-account/mca-invoice-sections-post-transition.png)

4. Selecione **Controlo de acesso (IAM)** no lado esquerdo.

    ![Captura de ecrã que mostra o acesso dos administradores de departamento e de conta após a transição](./media/mca-setup-account/mca-department-account-admins-access-post-transition.png)

Os administradores do Enterprise e os administradores de departamento são listados como proprietários das secções da fatura ou leitores das secções da fatura, enquanto os proprietários de conta no departamento são listados como criadores de subscrições do Azure. Repita o passo para todas as secções da fatura para verificar o acesso a todos os departamentos na sua inscrição do Contrato Enterprise. Os proprietários de conta que não faziam parte de nenhum departamento obterão permissão numa secção da fatura chamada **Secção da fatura predefinida**. Se acreditar que o acesso de algum administrador está em falta, poderá conceder-lhe acesso no portal do Azure. Para obter mais informações, veja [Gerir funções de faturação no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

- [Introdução à nova conta de faturação](../understand/mca-overview.md)

- [Concluir as tarefas do Contrato Enterprise na conta de faturação de um Contrato de Cliente da Microsoft](mca-enterprise-operations.md)

- [Gerir o acesso à conta de faturação](understand-mca-roles.md)
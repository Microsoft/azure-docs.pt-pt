---
title: Configurar a sua conta de cobrança para um contrato de cliente da Microsoft - Azure | Documentos da Microsoft
description: Saiba como configurar a sua conta de cobrança para um contrato de cliente da Microsoft.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 0bb5b118b3d1bc7b89354bb23305eed15a96985d
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249240"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Configurar a sua conta de cobrança para um contrato de cliente da Microsoft

Se sua organização que assinou um contrato de cliente da Microsoft para renovar a sua inscrição de Enterprise Agreement, tem de definir a sua conta de faturação. Sua nova conta de cobrança fornece capacidades de gestão de custos por meio de uma nova experiência de gerenciamento simplificado e unificada e de faturação melhorada:  

- Gerir serviços do Azure e a faturação, incluindo notas fiscais, no portal do Azure da sua organização

- Organizar os custos na sua fatura com base nas suas necessidades para facilitar o controlo e a alocação de custos

- Rever e analisar a sua fatura mensal, digital na página de faturação + Azure Cost Management

Quando configurou a conta de cobrança, faça a transição a faturação das suas subscrições do Azure existentes para a nova conta. A forma como vai gerir a faturação das suas subscrições na nova conta é diferente de como geridos na sua inscrição de Enterprise Agreement. Recomendamos que faça o seguinte, antes de iniciar a configuração:

- **Compreender as alterações para a hierarquia de faturação**
    - Nova conta de faturação é organizados de forma diferente do que a sua inscrição de Enterprise Agreement. Para obter mais informações, consulte [compreender as alterações para a hierarquia de faturação](#understand-changes-to-your-billing-hierarchy).
- **Compreender as alterações para o acesso dos administradores de faturação**
    - Os administradores a sua inscrição de Enterprise Agreement obtém acesso para os âmbitos de faturas na nova conta. Para obter mais informações, consulte [compreender as alterações para o acesso dos administradores de faturação](#understand-changes-to-your-billing-administrators-access).
- **Reveja as funcionalidades de contrato Enterprise que são substituídas pela nova conta**
    - Recursos de exibição do Registro do Enterprise Agreement que são substituídos por recursos atualizados na nova conta. [Revisar recursos substituídos pela nova conta de faturação](#review-features-replaced-by-the-new-billing-account).
- **Ver respostas para perguntas mais comuns**
    - Ver informações adicionais para saber mais sobre a configuração. Aceda a [informações adicionais](#additional-information).

## <a name="set-up-your-billing-account-in-the-azure-portal"></a>Configurar a sua conta de cobrança no portal do Azure

Para concluir a configuração, precisa de acesso para a nova conta de faturação e a inscrição de Enterprise Agreement. Para obter mais informações, consulte [acesso necessário para concluir o conjunto de cópia de segurança da sua conta de cobrança](#access-required-to-complete-the-setup).

1. Inicie sessão no portal do Azure através da ligação do e-mail que foi enviado para si quando se inscreveu o contrato de cliente da Microsoft.

2. Se alguém na sua organização assinado o contrato ou não tem o e-mail, inicie sessão com a seguinte hiperligação. Substitua **enrollmentNumber** com o número de inscrição do seu contrato Enterprise que foi renovado.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Selecione **iniciar transição** no último passo da configuração. Depois de selecionar a transição de início:

    ![Captura de ecrã que mostra o Assistente de configuração](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - Uma hierarquia de faturação correspondente para a hierarquia de contrato Enterprise é criada na nova conta de faturação. Para obter mais informações, consulte [compreender as alterações para a hierarquia de faturação](#understand-changes-to-your-billing-hierarchy).
    - Os administradores a sua inscrição de Enterprise Agreement recebem acesso para a nova conta de faturação para que eles continuam a gerenciar a cobrança para sua organização.
    - A faturação das suas subscrições do Azure é transferida para a nova conta. **Não haverá qualquer impacto nos seus serviços do Azure durante essa transição. Irá continuar a executar sem qualquer interrupção**.
    - Se tiver as reservas do Azure, são movidas para a sua nova conta de cobrança com o mesmo desconto e termo. O desconto de reserva irá continuar a ser aplicadas durante a transição.

4. Pode monitorizar o estado da transição na **fazer a transição de estado** página.

   ![Captura de ecrã que mostra o estado de transição](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="access-required-to-complete-the-setup"></a>Necessário para concluir a configuração de acesso

Para concluir a configuração, terá o seguinte acesso:

- Proprietário do perfil de faturação que foi criado quando o contrato de cliente da Microsoft foi assinado. Para saber mais sobre os perfis de faturas, veja [compreender os perfis de faturas](billing-mca-overview.md#understand-billing-profiles).

- Administrador de empresa da inscrição, que é renovado.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Se não for um administrador empresarial da inscrição

Pode solicitar os administradores da empresa do Registro para concluir a configuração da sua conta de faturação.

1. Inicie sessão no portal do Azure através da ligação do e-mail que foi enviado para si quando se inscreveu o contrato de cliente da Microsoft.

2. Se alguém na sua organização assinado o contrato ou não tem o e-mail, inicie sessão com a seguinte hiperligação. Substitua **enrollmentNumber** com o número de inscrição do seu contrato enterprise que foi renovado.

   (https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber)

3. Selecione os administradores da empresa que pretende enviar o pedido.

   ![Captura de ecrã que mostra convidando os administradores da empresa](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. Selecione **pedido de envio**.

   Os administradores irão receber um e-mail com instruções para concluir a configuração.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Se não for um proprietário do perfil de faturação

O utilizador na sua organização, quem assinou o contrato de cliente da Microsoft é adicionado como o proprietário no perfil de faturação. Pedir ao utilizador para o adicionar como um proprietário para que pode concluir a configuração.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>Compreender as alterações à sua conta de faturação

Sua nova conta de cobrança simplifica a faturação para a sua organização ao fornecer-lhe avançada de faturação e capacidades de gestão de custos. O diagrama seguinte explica como a faturação é organizada na nova conta de faturação.

![Imagem dos ea-mca-post-transição-hierarquia](./media/billing-mca-setup-account/mca-post-transition-hierarchy.svg)

1. Utilize a conta de cobrança para gerenciar a cobrança para o contrato do cliente Microsoft. Para saber mais sobre a conta de cobrança, veja [entender a conta de cobrança](billing-mca-overview.md#understand-billing-account).
2. Utilize o perfil de faturação para gerenciar a cobrança para sua organização, semelhante à sua inscrição de Enterprise Agreement. Administradores da empresa tornam-se os proprietários do perfil de faturação. Para saber mais sobre os perfis de faturas, veja [compreender os perfis de faturas](billing-mca-overview.md#understand-billing-profiles).
3. Utilize uma seção de nota fiscal para organizar os custos com base nas suas necessidades, semelhantes a departamentos em sua inscrição de Enterprise Agreement. Departamento torna-se em secções de nota fiscal e os administradores de departamento tornam-se proprietários das seções de nota fiscal respectivos. Para saber mais sobre as secções de nota fiscal, veja [secções de nota fiscal de compreender](billing-mca-overview.md#understand-invoice-sections).
4. As contas que foram criadas no seu contrato Enterprise não são suportadas na nova conta de faturação. Subscrições da conta pertencem a seção de nota fiscal respectivos para os seus departamentos. Proprietários da conta podem criar e gerir subscrições para suas seções de nota fiscal.

## <a name="understand-changes-to-your-billing-administrators-access"></a>Compreender as alterações para o acesso dos administradores de faturação

Consoante o acesso, os administradores de faturação na sua inscrição de Enterprise Agreement obtém acesso aos âmbitos de faturas na nova conta. O diagrama seguinte explica a alteração no acesso durante a transição:

![Imagem dos ea mca-post-transição-acesso](./media/billing-mca-setup-account/mca-post-transition-access.png)

| Função existente | Função de transição de postagem  |
|---------|---------|
|Administrador de empresa (Só de leitura = Não)     |-Proprietário no perfil de faturação para a inscrição </br> -Proprietário em todas as seções de nota fiscal |
|Administrador da empresa | -Leitor no perfil de faturação para a inscrição </br> -Leitor em todas as seções de nota fiscal |
|Administrador do departamento (leitura apenas = não)    |-Proprietário na seção de nota fiscal criado para o respetivo departamento      |
|Administrador de departamento     | -Leitor na secção de nota fiscal criada para o respetivo departamento      |
|Proprietário do Azure     | -Criador de subscrição as do azure na seção de nota fiscal criada para o respetivo departamento |

Um inquilino do Azure Active Directory está selecionado para a nova conta de faturação durante a assinatura do contrato de cliente da Microsoft. Se um inquilino não existir na sua organização, é criado um novo inquilino. O inquilino representa a sua organização no Azure Active Directory. Os administradores de inquilinos global na sua organização utilizam o inquilino para gerir o acesso das aplicações e dados na sua organização.

Sua nova conta suporta apenas os utilizadores ao inquilino tiver sido selecionada durante a assinatura do contrato de cliente da Microsoft. Se os utilizadores com permissões administrativas no seu contrato Enterprise fazem parte do inquilino, elas receberão acesso para a nova conta de faturação durante a transição. Se eles não são parte do inquilino, não será capazes de aceder a nova conta de cobrança, a menos que convidá-los.

Quando convida os usuários, eles são adicionados ao inquilino como os utilizadores convidados e obtém acesso à conta de faturação. Para convidar os utilizadores, o acesso de convidado tem de estar ativado para o inquilino. Para obter mais informações, consulte [controlar o acesso de convidado no Azure Active Directory](https://docs.microsoft.com/en-us/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Se o acesso de convidado é desativado, contacte os administradores globais do seu inquilino para ativá-la. <!-- Todo - How can they find their global administrator -->

## <a name="review-features-replaced-by-the-new-billing-account"></a>Revisar recursos substituídos pela nova conta de faturação

Funcionalidades do contrato Enterprise seguintes são substituídas por novos recursos na conta de faturação para um contrato de cliente da Microsoft.

### <a name="enterprise-agreement-accounts"></a>Contas de contrato Enterprise

As contas que foram criadas na sua inscrição de Enterprise Agreement não são suportadas na nova conta de faturação. Subscrições da conta pertencem a seção de nota fiscal criado para o respetivo departamento. Proprietários da conta se tornar os criadores de subscrição do Azure e podem criar e gerir subscrições para suas seções de nota fiscal.

### <a name="notification-contacts"></a>Contactos de notificação

Contactos de notificação são enviados comunicações por e-mail sobre o Azure Enterprise Agreement. Não são suportados na nova conta de faturação. E-mails sobre os créditos do Azure e notas fiscais são enviados para os utilizadores que têm acesso aos perfis de faturas na sua conta de cobrança.

### <a name="spending-quotas"></a>Quotas de gastos

As quotas de gastos que tiverem sido definidas para os departamentos na sua inscrição de Enterprise Agreement são substituídas por orçamentos na nova conta de faturação. Um orçamento é criado para cada quota de gastos definido nos departamentos em sua inscrição. Para obter mais informações sobre os orçamentos, consulte [criar e gerir o Azure orçamentos](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Centros de custos

Centro de custos que estavam definidas as subscrições do Azure no seu registro de contrato Enterprise são passadas a nova conta de faturação. No entanto, não são suportados os centros de custos de departamentos e contas de contrato Enterprise.

## <a name="additional-information"></a>Informações adicionais

As secções seguintes fornecem informações adicionais sobre como configurar a sua conta de cobrança.

### <a name="no-service-downtime"></a>Sem períodos de indisponibilidade de serviço

Continuar a executar serviços do Azure na sua subscrição sem qualquer interrupção. Só que fazemos a transição da relação de faturação para subscrições do Azure. Não haverá impacto para os recursos existentes, grupos de recursos ou grupos de gestão.

### <a name="user-access-to-azure-resources"></a>Acesso de utilizador aos recursos do Azure

Acesso aos recursos do Azure que foi definido com o Azure RBAC (controlo de acesso baseado em funções) não é afetado durante a transição.

### <a name="azure-reservations"></a>Azure Reservations

Nenhumas reservas de Azure no seu registro de contrato Enterprise é movida para a sua nova conta de cobrança. Durante a transição, não haverá quaisquer alterações para os descontos de reserva que estão a ser aplicadas às suas subscrições.

### <a name="azure-marketplace-products"></a>Produtos do Azure Marketplace

Produtos do Azure Marketplace no seu registro de contrato Enterprise são movidos juntamente com as subscrições. Não haverá quaisquer alterações ao serviço de acesso dos produtos do Marketplace durante a transição.

### <a name="support-plan"></a>Plano de suporte

Benefícios de suporte não se transferem como parte da transição. Compre um novo plano de suporte para obter os benefícios das subscrições do Azure na sua nova conta de faturação.

### <a name="past-charges-and-balance"></a>Passado custos e saldo

Saldo de custos e os créditos antes da transição pode ser visualizado na sua inscrição de Enterprise Agreement através do portal do Azure. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Quando deve ser concluída a configuração?

Conclua a configuração da sua conta de faturação antes de expira a inscrição de Enterprise Agreement. Se a sua inscrição expirar, serviços nas suas subscrições do Azure ainda continuará na ser executada sem interrupção. No entanto, será cobrado tarifas de revenda para os serviços.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Alterações para a inscrição de Enterprise Agreement após a configuração

Subscrições do Azure que são criadas para a inscrição de Enterprise Agreement, após a transição pode ser movida manualmente para a nova conta de faturação. Para obter mais informações, consulte [obter a propriedade das subscrições do Azure com outros usuários de faturação](billing-mca-request-billing-ownership.md). Para mover as reservas do Azure que podem ser compradas após a transição [contacte o suporte Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Também pode fornecer aos usuários acesso à conta de faturação após a transição. Para obter mais informações, consulte [gerir funções de faturas no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Reverter a transição

Não é possível reverter a transição. Depois da faturação das suas subscrições do Azure é transferida para a nova conta de faturação, não é possível revertê-lo novamente para a inscrição de Enterprise Agreement.

### <a name="closing-your-browser-during-setup"></a>Fechar seu navegador durante a configuração

Antes de clicar no **iniciar transição**, pode fechar o navegador. Pode voltar para a configuração através da ligação que obteve no e-mail e iniciar a transição. Se fechar o navegador, após a transição é iniciada, sua transição para continuarão na ser executadas. Volte para a página de estado de transição para monitorizar o estado mais recente da sua transição. Irá receber um e-mail quando é concluída a transição.

## <a name="validate-the-billing-account-is-set-up-properly"></a>Validar a faturação conta está corretamente configurada

 Valide o seguinte para garantir a que sua nova conta de faturação está corretamente configurada:

### <a name="azure-subscriptions"></a>Subscrições do Azure

1. Inicie sessão no [portal do Azure](http://portal.azure.com).

2. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Selecione o perfil de faturação. Consoante o acesso, terá de selecionar uma conta de cobrança. A partir da conta de cobrança, selecione **perfis de faturação** e, em seguida, o perfil de faturação.

4. Selecione **subscrições do Azure** do lado esquerdo.

   ![Captura de ecrã que mostra a lista de subscrições](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

Todas as subscrições do Azure que são transferidas da sua inscrição do contrato Enterprise para a nova conta de faturação são apresentadas na página de subscrições do Azure. Se acreditar que qualquer subscrição está em falta, fazer a transição a faturação da subscrição manualmente no portal do Azure. Para obter mais informações, consulte [obter a propriedade das subscrições do Azure com outros usuários de faturação](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Azure Reservations

1. Inicie sessão no [portal do Azure](http://portal.azure.com).

2. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Selecione uma secção de nota fiscal. Consoante o acesso, terá de selecionar uma conta de cobrança ou um perfil de faturação.  O perfil de faturação ou conta de cobrança, selecione **secções de nota fiscal** e, em seguida, uma seção de nota fiscal.

    ![Captura de ecrã que mostra a lista de transição de postagem de seção de nota fiscal](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Selecione **todos os produtos** do lado esquerdo.

5. Pesquisar nos **reservado**.

    ![Captura de ecrã que mostra a lista de transição de postagem de subscrições](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Todas as reservas do Azure que são movidos do seu registro do Enterprise Agreement para a nova conta de faturação são apresentadas na página de todos os produtos. Repita os passos para todas as seções de nota fiscal verificar que todas as reservas do Azure são movidas do seu registro do Enterprise Agreement. Se acreditar que qualquer Azure Reservation está em falta, [contacte o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para mover a reserva para a nova conta de faturação.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Acesso aos administradores da empresa no perfil de faturação

1. Inicie sessão no [portal do Azure](http://portal.azure.com).

2. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Selecione o perfil de faturação criado para a sua inscrição. Consoante o acesso, terá de selecionar uma conta de cobrança.  A partir da conta de cobrança, selecione **perfis de faturação** e, em seguida, o perfil de faturação.

4. Selecione **controlo de acesso (IAM)** do lado esquerdo.

   ![Captura de ecrã que mostra o acesso de transição de postagem de administradores de empresa](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

Os administradores empresariais são listados como faturação proprietários de perfil enquanto a empresa que os administradores com permissões só de leitura são listados como leitores de perfil de faturação. Se o acesso a quaisquer administradores da empresa está em falta, pode lhes dar acesso no portal do Azure. Para obter mais informações, consulte [gerir funções de faturas no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Acesso de administradores da empresa, os administradores do departamento e proprietários da conta em secções de nota fiscal

1. Inicie sessão no [portal do Azure](http://portal.azure.com).

2. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. Selecione uma secção de nota fiscal. Secções de nota fiscal têm o mesmo nome que seus respetivos departamentos em inscrições de contrato Enterprise. Consoante o acesso, terá de selecionar um perfil de faturação ou de uma conta de cobrança. A partir do perfil de faturação ou a conta de cobrança, selecione **secções de nota fiscal** e, em seguida, selecione uma seção de nota fiscal.

   ![Captura de ecrã que mostra a lista de transição de postagem de seção de nota fiscal](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Selecione **controlo de acesso (IAM)** do lado esquerdo.

    ![Captura de ecrã que mostra o acesso de departamento e transição de publicação de acesso de administradores de conta](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

Administradores da empresa e administradores de departamento são listados como proprietários da secção de nota fiscal ou leitores de seção de nota fiscal enquanto proprietários da conta do departamento de estão listados como criadores de subscrição do Azure. Repita o passo para todas as seções de nota fiscal verificar o acesso de todos os departamentos na sua inscrição de Enterprise Agreement. Proprietários da conta que não estavam parte de qualquer departamento irão obter permissão numa seção de nota fiscal com o nome **seção de nota fiscal Default**. Se o acesso a quaisquer administradores estiver ausente, pode lhes dar acesso no portal do Azure. Para obter mais informações, consulte [gerir funções de faturas no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.

## <a name="next-steps"></a>Passos Seguintes

- [Introdução à sua nova conta de cobrança](billing-mca-overview.md)

- [Saiba como executar tarefas comuns de EA na sua nova conta de faturação](billing-mca-enterprise-operations.md)

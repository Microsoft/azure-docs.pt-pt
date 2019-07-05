---
title: Transferir a propriedade de faturação de subscrição do Azure para outra conta | Documentos da Microsoft
description: Descreve como transferir a propriedade de faturação de uma subscrição do Azure para outra conta e algumas perguntas mais frequentes (FAQ) sobre o processo
keywords: Transferir a subscrição de transferência de subscrição do azure, azure, mova a subscrição do azure para outro proprietário de subscrição de alteração de conta, do azure, transferir a subscrição do azure para outra transferência de conta, do azure de faturação
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff3c57c31526119ab81225a1c70b163173be937
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514432"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Transferir a propriedade de faturação de uma subscrição do Azure para outra conta

Pode querer transferir a propriedade de faturação da sua subscrição do Azure se estão a deixar sua organização ou pretende que a sua subscrição a ser cobradas para outra conta. Transferir a propriedade da faturação para outra conta fornece os administradores na nova permissão de conta para executar tarefas de faturas, como alterar o método de pagamento, ver custos e cancelar a subscrição.

Se pretender manter a propriedade de faturação, mas alterar o tipo da sua subscrição, veja [mudar a sua subscrição do Azure para outra oferta](billing-how-to-switch-azure-offer.md). Se pretender controlar quem pode gerir os recursos na subscrição, veja [funções incorporadas para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Se for um cliente Enterprise Agreement(EA), os administradores de empresa podem transferir a propriedade de faturação das suas subscrições entre contas. Para obter mais informações, consulte [transferir a propriedade da faturação das subscrições de contrato Enterprise (EA)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Transferir a propriedade de faturação de uma subscrição do Azure

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como administrador da conta de faturação que tem a subscrição que pretende transferir. Para saber se for um administrador, consulte [perguntas mais frequentes sobre](#faq).

1. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Selecione **subscrições** no painel esquerdo. Consoante o acesso, poderá ter de selecionar um âmbito de faturação e, em seguida, selecione **subscrições** ou **subscrições do Azure**.

1. Selecione **transferência para outra conta** para a subscrição que pretende transferir. 

   ![Selecione a subscrição para transferir](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Introduza o endereço de e-mail de um utilizador que seja um administrador de faturação da conta que será o novo proprietário da subscrição e, em seguida, selecione **pedido de transferência de envio**.

    > [!IMPORTANT]
    >
    > Se transferir a propriedade de faturação da sua subscrição para a conta de um utilizador noutro Azure AD de inquilino, todos os [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) atribuições para gerir os recursos na subscrição são removidas permanentemente. O novo proprietário terá acesso para gerir os recursos na subscrição. Para obter mais informações, consulte [transferência de subscrição para um utilizador noutro inquilino do Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md).
  
    ![Envie a página de transferência](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. O utilizador obtém uma mensagem de e-mail com instruções para rever o pedido de transferência.

   ![E-mail de transferência de subscrição para o destinatário](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Para aprovar o pedido de transferência, o usuário seleciona a ligação no e-mail e segue as instruções. O utilizador terá de selecionar um método de pagamento que será utilizado para pagar a subscrição. Além disso, se o utilizador não tiver uma conta do Azure, que é preciso para se inscrever numa conta nova. 

   ![Primeira página de web de transferência de subscrição](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Segunda página de web de transferência de subscrição](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Segunda página de web de transferência de subscrição](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Êxito! A subscrição é transferida agora.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>A transferência de subscrição para uma conta no outro inquilino do Azure AD

Um inquilino do Azure Active Directory (AD) é criado para quando se inscreve para o Azure. O inquilino representa a sua conta. Utilizar o inquilino para gerir o acesso às suas subscrições e recursos.

Quando cria uma nova subscrição, está alojado no inquilino do Azure AD da sua conta. Se quiser fornecer a que outros utilizadores a aceder à sua subscrição ou recursos, é preciso convidá-los ao seu inquilino. Isto ajuda a controlar o acesso aos seus recursos e subscrições.

Quando transferir a propriedade de faturação da sua subscrição para uma conta no outro inquilino do Azure AD, a subscrição é transferida para o inquilino da nova conta. Todos os utilizadores, grupos ou principais de serviço que tiveram [(RBAC) de acesso baseado em funções](../role-based-access-control/overview.md) perderão o acesso para gerir recursos na subscrição. Apenas o utilizador na conta do novo que aceita o pedido de transferência têm acesso para gerir os recursos. Para fornecer acesso aos utilizadores que tinham originalmente acesso, o novo proprietário precisaria [adicionar manualmente estes utilizadores à subscrição](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Transferir o Visual Studio, Microsoft Partner Network (MPN) e subscrições Pay as you go programador/teste

As subscrições do Visual Studio e o Microsoft Partner Network tem crédito do Azure mensal recorrente associado a eles. Quando a transferência estas subscrições, o seu crédito não está disponível na conta de faturação de destino. A subscrição utiliza o crédito na conta de faturação de destino. Por exemplo, se Bob transfere uma subscrição do Visual Studio Enterprise para a conta em 9 de Setembro e a Jane de Joana aceita a transferência. Depois de concluída a transferência, a subscrição começa a utilizar o crédito na conta de Joana. O crédito irá repor todos os meses no 9th. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Transferir a propriedade da faturação das subscrições de contrato Enterprise (EA)

O administrador de empresa pode transferir a propriedade de subscrições entre contas dentro de uma inscrição. Para obter mais informações, consulte [transferir a propriedade da conta](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) no portal do EA.

<a id="CSP"></a>

## <a name="next-steps-after-accepting-billing-ownership"></a>Passos seguintes depois de aceitar a propriedade de faturação

Se aceitar a propriedade de faturação de uma subscrição do Azure, recomendamos que reveja os passos seguintes:

1. Rever e atualizar o administrador de serviço e outras funções do RBAC Coadministradores. Para obter mais informações, consulte [adicionar ou alterar os administradores de subscrição do Azure](billing-add-change-azure-subscription-administrator.md) e [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).
1. Atualize as credenciais associadas com serviços nesta subscrição, incluindo:
   1. Certificados de gestão que concedem ao utilizador direitos de administrador aos recursos de subscrição. Para obter mais informações, consulte [criar e carregar um gerenciamento de certificados para o Azure](../cloud-services/cloud-services-certs-create.md)
   1. Chaves de acesso para serviços como o armazenamento. Para obter mais informações, consulte [sobre as contas de armazenamento](../storage/common/storage-create-storage-account.md)
   1. Credenciais de acesso remoto para os serviços, como máquinas virtuais do Azure.
1. Se estiver trabalhando com um parceiro, considere atualizar o ID de parceiro nesta subscrição. Pode atualizar o ID de parceiro no [portal do Azure](https://portal.azure.com). Para obter mais informações, consulte [ligar um ID de parceiro a suas contas do Azure](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Tipos de subscrição de suporte

Transferência de subscrição no portal do Azure está disponível para os tipos de subscrição listados abaixo. Transferência não é atualmente suportada para [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) ou [do Azure no Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) subscrições. Para obter uma solução, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md). Para transferir outras subscrições, como [patrocínio](https://azure.microsoft.com/offers/ms-azr-0036p/) ou planos de suporte, [contacte o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Rede de parceiros da Microsoft](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Subscritores do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [O MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Pay as you go programador/teste](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [Através do portal EA](#EA).

\*\* Só é suportada para as contas que são criadas durante o início de sessão no site do Azure backup. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Perguntas mais frequentes (FAQ) sobre remetentes

Estas FAQ aplicam-se aos utilizadores, o que estiver a transferir a propriedade de faturação de uma subscrição do Azure para outra conta.

### <a name="whoisaa"></a> Quem é um administrador de faturação de uma conta?

Um administrador de faturação é uma pessoa que tem permissão para gerir a faturação de uma conta. Estão autorizados a aceder aos faturação no [portal do Azure](https://portal.azure.com) e realizar várias tarefas de faturas como criar subscrições, notas fiscais de exibição e a pagar ou atualização métodos de pagamento.

Para identificar pelos quais é um administrador de faturação de subscrições, utilize os seguintes passos:

1. Visite o [gestão de custos + faturação página no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Selecione **subscrições** no painel esquerdo. Consoante o acesso, poderá ter de selecionar um âmbito de faturação e, em seguida, selecione **subscrições** ou **subscrições do Azure**
1. A página de subscrições apresenta uma lista de todas as subscrições para o qual é um administrador de faturação.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Tudo o que transferir? Incluindo grupos de recursos, as VMs, discos e outros serviços em execução?

Todos os seus recursos, como VMs, discos e transferência de Web sites para a nova conta. No entanto, se transferir a subscrição a uma conta no outro Azure AD de inquilino, qualquer [funções de administrador](billing-add-change-azure-subscription-administrator.md) e [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/role-assignments-portal.md) atribuições na subscrição [não transferência](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Além disso, [registos das aplicações](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) e outros serviços de inquilino específico não se transferem juntamente com a subscrição.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Posso transferir a propriedade para uma conta em outro país?
Infelizmente, cruzada país não não possível efetuar transferências no portal do Azure. Para transferir a sua subscrição em países, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Sou um administrador em duas contas. Posso transferir uma subscrição de uma das minhas contas para outro?
Sim, pode transferir a subscrição entre as suas contas. As suas contas conceitualmente são consideradas contas de dois utilizadores diferentes, pelo que pode utilizar os passos acima para transferir subscrições entre as suas contas.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Uma transferência de subscrição resulta em qualquer período de indisponibilidade de serviço?

Se a transferência de subscrição para um utilizador no mesmo inquilino do Azure AD, não há nenhum impacto para os recursos em execução na subscrição.  No entanto, se transferir a subscrição a um utilizador em outro inquilino, todos os utilizadores, grupos e os principais de serviço que tiveram [(RBAC) de acesso baseado em funções](../role-based-access-control/overview.md) perderão o acesso para gerir recursos na subscrição. 

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>O destinatário tem acesso ao histórico de faturação e utilização?

A única informação disponível para o destinatário é o custo do mês passado para a sua subscrição. O restante do histórico de faturação e utilização não transfere com a subscrição

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Como migrar dados e serviços para a minha subscrição do Azure para a nova subscrição?

Se não é possível transferir a propriedade de subscrição, pode migrar manualmente os recursos. Ver [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Se eu transferir uma subscrição Visual Studio ou Microsoft Partner Network, meu crédito há para a frente com a subscrição na conta de novo?

Não, o seu crédito não está disponível na nova conta. O utilizador que aceita o pedido de transferência tem de ter uma licença do Visual Studio para aceitar o pedido de transferência. A subscrição utiliza o crédito do Visual Studio que está disponível na conta do utilizador. Para obter mais informações, consulte [subscrições de transferir o Visual Studio, Microsoft Partner Network (MPN) e Pay as you go programador/teste](#transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions)


## <a name="frequently-asked-questions-faq-for-recipients"></a>Perguntas mais frequentes (FAQ) sobre os destinatários

Estas FAQ aplicam-se aos utilizadores, que estão a demorar mais a propriedade de faturação de uma subscrição do Azure a partir de outra conta.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Se eu assumir a propriedade de faturação de uma subscrição a partir de outra conta, fazer os utilizadores nessa conta continuam a ter acesso a meus recursos?

Se a subscrição é transferida para uma conta no mesmo inquilino do Azure AD, todos os utilizadores, grupos e os principais de serviço que tiveram [(RBAC) de acesso baseado em funções](../role-based-access-control/overview.md) para gerir recursos na subscrição mantém o acesso. Para ver os utilizadores que têm acesso RBAC para a subscrição, utilize os seguintes passos:

1. Visite o [página de subscrição no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que pretende verificar e, em seguida, selecione **controlo de acesso (IAM)** no painel esquerdo.
1. Selecione **atribuições de funções** da parte superior da página. A página de atribuições de função apresenta uma lista de todos os utilizadores com acesso RBAC na subscrição.

Se a subscrição é transferida para uma conta no outro inquilino do Azure AD, todos os utilizadores, grupos e os principais de serviço que tiveram [(RBAC) de acesso baseado em funções](../role-based-access-control/overview.md) perderão o acesso para gerir recursos na subscrição. No entanto, mesmo que eles não têm acesso RBAC deixa de poder, poderá continua a ter acesso à subscrição através de alguns mecanismos de segurança, incluindo:

* Certificados de gestão que concedem ao utilizador direitos de administrador aos recursos de subscrição. Para obter mais informações, consulte [criar e carregar um certificado de gestão do Azure](../cloud-services/cloud-services-certs-create.md).
* Chaves de acesso para serviços como o armazenamento. Para obter mais informações, veja [Acerca das contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).
* Credenciais de acesso remoto para os serviços, como máquinas virtuais do Azure.

Se o destinatário tem de restringir, aceder aos respetivos recursos, deve considerar atualizar quaisquer segredos associados ao serviço. A maioria dos recursos podem ser atualizados utilizando os seguintes passos:

  1. Inicie sessão no [portal do Azure](https://portal.azure.com).
  2. No Hub menu, selecione **todos os recursos**.
  3. Selecione o recurso.
  4. Na página de recursos, clique em **definições**. Aqui pode ver e atualizar segredos existentes.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Se eu pegar a propriedade de faturação de uma subscrição no meio do ciclo de faturação, tenho de pagar por todo o ciclo de faturação?

Sua conta é responsável pelo pagamento de qualquer utilização que é comunicada a partir da hora de transferência e posteriores. Pode haver alguns utilização que ocorria antes da transferência, mas foi reportada posteriormente. A utilização está incluída na fatura da sua conta.

### <a name="can-i-use-a-different-payment-method"></a>Pode utilizar um método de pagamento diferente?

Sim. Ao aceitar o pedido de transferência, pode selecionar um método de pagamento existente que está ligado à sua conta ou adicionar um novo método de pagamento.

## <a name="troubleshooting"></a>Resolução de problemas

### <a id="no-button"></a> Por que motivo não vejo no botão "Transferir a subscrição"?

Infelizmente, a transferência de subscrição self-service não está disponível para a sua conta de cobrança. Atualmente, não suportamos a transferência de propriedade de faturação da conta de Enterprise Agreement (EA) no portal do Azure. Além disso, as contas de contrato de cliente da Microsoft que são criadas durante o trabalho por meio de vendas da Microsoft não suportam a transferir a propriedade de faturação das subscrições. 

### <a id="no-button"></a> Por que minha assinatura do tipo de transferência de suporte? 

Infelizmente, nem todos os tipos de subscrições suportam transferências de propriedade de faturação. Para ver a lista de tipos de subscrição que suportam transferências, consulte [suportados tipos de subscrição](#supported-subscription-types)

### <a id="no-button"></a> Por que estou recebendo um erro de acesso negado ao tentar transferir a propriedade de faturação de uma subscrição? 

Verá este erro se estiver a tentar transferir uma subscrição do plano do Microsoft Azure e não tem a permissão necessária. Para transferir uma subscrição do plano de Microsoft Azure, terá de ser um proprietário ou contribuinte na seção de nota fiscal para o qual a subscrição é faturada. Para obter mais informações, consulte [gerir subscrições para a secção de nota fiscal](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

- Rever e atualizar o administrador de serviço e outras funções do RBAC Coadministradores. Para obter mais informações, consulte [adicionar ou alterar os administradores de subscrição do Azure](billing-add-change-azure-subscription-administrator.md) e [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

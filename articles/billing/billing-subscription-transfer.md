---
title: Transferir propriedade da subscrição do Azure para outra conta | Microsoft Docs
description: Descreve como transferir a propriedade de faturação de uma subscrição do Azure para outra conta e algumas das perguntas mais frequentes (FAQ) sobre o processo
keywords: transferir subscrição do azure, subscrição de transferência do azure, mover subscrição do azure para outra conta, alterar proprietário da subscrição do azure, transferir subscrição do azure para outra conta, faturação de transferência do azure
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a858ef9918135bfb660c059d80f4c7c49e1e3364
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223570"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Transferir a propriedade de faturação de uma subscrição do Azure para outra conta

Pode querer transferir a propriedade de faturação da sua subscrição do Azure se estiver de saída da organização ou se quiser que a sua subscrição seja faturada noutra conta. A transferência da propriedade de faturação para outra conta fornece permissão aos administradores na nova conta para executar tarefas de faturação, como alterar o método de pagamento, ver custos e cancelar a subscrição.

Se quiser manter a propriedade de faturação, mas alterar o tipo da subscrição, veja [Mudar a sua subscrição do Azure para outra oferta](billing-how-to-switch-azure-offer.md). Se quiser controlar quem pode gerir recursos na subscrição,veja [Funções internas para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Se for um cliente do Contrato Enterprise (EA), os administradores de empresa poderão transferir a propriedade de faturação das suas subscrições entre contas. Para mais informações, veja [Transferir a propriedade da faturação das subscrições do Contrato Enterprise (EA)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Transferir a propriedade de faturação de uma subscrição do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como o administrador da conta de faturação que possui a subscrição que quer transferir. Para saber se é um administrador, veja [Perguntas mais frequentes](#faq).

1. Faça uma pesquisa em **Gestão de Custos + Faturação**.

   ![Captura de ecrã que mostra a pesquisa no portal do Azure](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Selecione **Subscrições** no painel à esquerda. Consoante o seu acesso, pode ter de selecionar um âmbito da faturação e, em seguida, selecionar **Subscrições** ou **Subscrições do Azure**.

1. Selecione **Transferir propriedade da faturação**  da subscrição que quer transferir.

   ![Selecionar a subscrição a transferir](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Insira o endereço de e-mail de um utilizador que seja um administrador de faturação da conta e que será o novo proprietário da subscrição.

1. Se estiver a transferir a sua subscrição para uma conta noutro inquilino do Azure AD, selecione se pretende mover a subscrição para o inquilino da nova conta. Para obter mais informações, veja [Transferir a subscrição para uma conta noutro inquilino do Microsoft Azure AD](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)

    > [!IMPORTANT]
    >
    > Se optar por mover a subscrição para o inquilino do Microsoft Azure AD da nova conta, todas as atribuições de [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) para gerir os recursos na subscrição serão permanentemente removidos. Apenas o utilizador na nova conta que aceite o seu pedido de transferência terá acesso para gerir recursos na subscrição. Para obter mais informações, veja [Transferir a subscrição para um utilizador noutro inquilino do Microsoft Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md). Como alternativa, pode desmarcar a caixa para o inquilino da Subscrição do Microsoft Azure AD para transferir a propriedade de faturação sem mover a subscrição para o inquilino da nova conta. Se o fizer, as permissões RBAC existentes para gerir os recursos do Azure serão mantidas.

    ![Enviar página de transferência](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Selecione **Enviar pedido de transferência**.

1. O utilizador recebe um e-mail com instruções para rever o pedido de transferência.

   ![E-mail de transferência de subscrição enviado para o destinatário](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Para aprovar o pedido de transferência, o utilizador seleciona a ligação no e-mail e segue as instruções. O utilizador precisaria de selecionar um método de pagamento que será utilizado para pagar a subscrição. Além disso, se o utilizador não tiver uma conta do Azure, precisará de se inscrever para uma nova conta.

   ![Página Web de transferência da primeira subscrição](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Página Web de transferência da segunda subscrição](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Página Web de transferência da segunda subscrição](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Êxito! A subscrição foi transferida.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Transferir a subscrição para uma conta noutro inquilino do Microsoft Azure AD

Quando se inscreve no Azure, é criado um inquilino do Azure Active Directory (AD). O inquilino representa a sua conta. O inquilino é utilizado para gerir o acesso às respetivas subscrições e recursos.

Quando cria uma nova subscrição, ela é alojada no inquilino do Azure AD da sua conta. Se quiser conceder acesso aos outros à sua subscrição ou aos seus recursos, precisa de convidá-los para aderir ao seu inquilino. Isto ajuda-o a controlar o acesso às suas subscrições e recursos.

Quando transfere a propriedade de faturação da sua subscrição para uma conta noutro inquilino do Azure AD, pode mover a subscrição para o inquilino da nova conta. Se o fizer, deixarão de ter acesso todos os utilizadores, grupos ou principais de serviço que tiverem [acesso baseado em funções (RBAC)](../role-based-access-control/role-assignments-portal.md) para gerir subscrições e respetivos recursos. Apenas o utilizador na nova conta que aceite o seu pedido de transferência terá acesso para gerir os recursos. Para conceder acesso aos utilizadores que originalmente tinham acesso, o novo proprietário teria que [adicionar manualmente estes utilizadores à subscrição](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Transferir subscrições Dev/Test do Visual Studio, MPN e Pay as you go

As subscrições do Visual Studio e do Microsoft Partner Network têm associadoum crédito do Azure mensal recorrente. Quando transfere estas subscrições, o seu crédito não está disponível na conta de faturação de destino. A subscrição utiliza o crédito na conta de faturação de destino. Por exemplo, o Bernardo transfere uma subscrição do Visual Studio Enterprise para a conta da Júlia em 9 de setembro e a Júlia aceita a transferência. Após a transferência estar concluída, a subscrição começará a utilizar o crédito na conta de Júlia. O crédito será reposto todos os meses no dia 9.


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Transferir a propriedade da faturação das subscrições do Contrato Enterprise (EA)

O Administrador da Empresa pode transferir a propriedade das subscrições entre contas que pertençam a uma inscrição. Para obter mais informações, veja [Transferir Propriedade da Conta](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) no portal do EA.

## <a name="next-steps-after-accepting-billing-ownership"></a>Próximos passos após aceitar a propriedade da faturação

Se aceitou a propriedade de faturação de uma subscrição do Azure, recomendamos que analise os próximos passos:

1. Reveja e atualize o Administrador do Serviço, os Coadministradores e outras funções de RBAC. Para saber mais, veja [Adicionar ou alterar os administradores de subscrição](billing-add-change-azure-subscription-administrator.md) e [Gerir acesso através do RBAC e do portal do Azure](../role-based-access-control/role-assignments-portal.md).
1. Atualize as credenciais associadas aos serviços desta subscrição, incluindo:
   1. Os certificados de gestão que concedem ao utilizador direitos de administrador aos recursos da subscrição. Para obter mais informações, veja [Criar e carregar um certificado de gestão para o Azure](../cloud-services/cloud-services-certs-create.md)
   1. As chaves de acesso para serviços, como o Armazenamento. Para obter mais informações, veja [Acerca das contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md)
   1. As credenciais de Acesso Remoto para serviços, como as Máquinas Virtuais do Azure.
1. Se estiver a trabalhar com um parceiro, considere atualizar a ID do parceiro nesta subscrição. Pode atualizar a ID do parceiro no [portal do Azure](https://portal.azure.com). Para obter mais informações, veja [Ligar uma ID de parceiro às suas contas do Azure](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Tipos de subscrições suportadas

A transferência de subscrição no portal do Azure está disponível para os tipos de subscrição listados abaixo. Atualmente, a transferência não é suportada nas subscrições de [Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) nem nas do [Azure no Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/). Para uma solução, veja [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md). Para transferir outras subscrições, como [Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) ou planos de suporte, [entre em contacto com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Subscritores do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pay As You Go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [Através do portal do EA](#EA).

\*\*Só é suportado para contas criadas durante a inscrição no site do Azure.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Perguntas mais frequentes (FAQ) para remetentes

Estas FAQs aplicam-se a utilizadores que estão a transferir a propriedade da faturação de uma subscrição do Azure para outra conta.

### <a name="whoisaa"></a> Quem é administrador de faturação de uma conta?

Administrador de faturação é uma pessoa que tem permissão para gerir a faturação de uma conta. Está autorizado a aceder à faturação no [portal do Azure](https://portal.azure.com) e executar várias tarefas de faturação, como criar subscrições, ver e pagar faturas ou atualizar métodos de pagamento.

Para identificar as contas para as quais é  administrador de faturação, utilize os seguintes passos:

1. Aceda à [página de Gestão de Custos + Faturação](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) no portal do Azure.
1. Selecione **Todos os âmbitos de faturação** no painel à esquerda.
1. A página de subscrições lista todas as subscrições para as quais é administrador de faturação.

Se não tiver a certeza de quem é o administrador de conta de uma subscrição, utilize os seguintes passos para descobrir.

1. Visite a [página de Subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que pretende verificar e, em seguida, procure em **Definições**.
1. Selecione **Propriedades**. O administrador de conta da subscrição é apresentado na caixa **Administrador de Conta**.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>É transferido tudo? Incluindo grupos de recursos, VMs, discos e outros serviços em execução?

Todos os recursos, como VMs, discos e sites, são transferidos para a nova conta. No entanto, se transferir a subscrição para uma conta noutro inquilino do Azure AD, nenhuma [função de administrador](billing-add-change-azure-subscription-administrator.md) nem as atribuições [ de ](../role-based-access-control/role-assignments-portal.md)Controlo de Acesso Baseado em Funções (RBAC) na subscrição [são transferidas](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Além disso, os [registos de aplicações](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) e outros serviços específicos do inquilino não são transferidos juntamente com a subscrição.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Posso transferir a propriedade para uma conta noutro país?
Infelizmente, as transferências entre países não podem ser executadas no portal do Azure. Para transferir a subscrição de um país para outro, [entre em contacto com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Sou administrador em duas contas. Posso transferir uma subscrição de uma das minhas contas para outra?
Sim, pode transferir a subscrição entre as suas contas. As suas contas são consideradas conceitualmente como contas de dois utilizadores diferentes, por isso, pode utilizar os passos acima para transferir subscrições entre as contas.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Uma transferência de subscrição resulta num eventual tempo de inatividade de serviço?

Se transferir uma subscrição para uma conta no mesmo inquilino do Azure AD, não tem impacto nos recursos em execução na subscrição. Contudo, se transferir a subscrição para uma conta noutro inquilino e decidir mover a subscrição para o inquilino, todos os utilizadores, grupos e principais de serviço que tinham um [acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) para gerir recursos na subscrição deixarão de ter acesso. Esta ação pode resultar em tempo de inatividade do serviço.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>Os utilizadores na nova conta têm acesso ao histórico de utilização e faturação?

A única informação disponível para os utilizadores na nova conta é o custo do último mês da subscrição. O resto do histórico de utilização e faturação não é transferido com a subscrição

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Como migrar dados e serviços da minha subscrição do Azure para uma nova subscrição?

Se não puder transferir a propriedade da subscrição, poderá migrar manualmente os seus recursos. Veja como [Mover recursos para um grupo de recursos ou subscrição nova](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Se eu transferir uma subscrição do Visual Studio ou do Microsoft Partner Network, o meu crédito será transportado com a subscrição para a nova conta?

Não, o crédito não está disponível na nova conta. O utilizador que aceita o pedido de transferência precisa de ter uma licença do Visual Studio para aceitar o pedido de transferência. A subscrição utiliza o crédito do Visual Studio que está disponível na conta do utilizador. Para obter mais informações, veja [Transferir subscrições Dev/Test do Visual Studio, Microsoft Partner Network (MPN) e Pay as you go](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Perguntas mais frequentes (FAQ) para destinatários

Estas FAQs aplicam-se a utilizadores que estão a aceitar a propriedade da faturação de uma subscrição do Azure de outra conta.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Se eu assumir a propriedade de faturação de uma subscrição de outra conta, os utilizadores nessa conta continuarão a ter acesso aos meus recursos?

Sim. Contudo, se a sua conta estiver num inquilino do Azure AD diferente do inquilino da subscrição e o utilizador que enviou o pedido de transferência mover a subscrição para o inquilino da sua conta, são removidas todas as [funções de administrador](billing-add-change-azure-subscription-administrator.md) e atribuições de [Controlo de Acesso Baseado em Funções (RBAC)](../role-based-access-control/role-assignments-portal.md). Para ver os utilizadores que têm [Controlo de Acesso Baseado em Funções (RBAC)](../role-based-access-control/overview.md) para gerir recursos na subscrição, utilize os seguintes passos:

1. Visite a [página de Subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que pretende verificar e, em seguida, selecione **Controlo de acesso (IAM)** no painel à esquerda.
1. Selecione **Atribuições de função** na parte superior da página. A página de atribuições de função lista todos os utilizadores que têm acesso RBAC na subscrição.

Mesmo que as atribuições de [Controlo de Acesso Baseado em Funções (RBAC)](../role-based-access-control/role-assignments-portal.md) sejam removidas durante a transferência, os utilizadores na conta do proprietário original ainda poderão ter acesso à subscrição por meio de alguns mecanismos de segurança, incluindo:

* Os certificados de gestão que concedem ao utilizador direitos de administrador aos recursos da subscrição. Para obter mais informações, veja [Criar e carregar um certificado de gestão do Azure](../cloud-services/cloud-services-certs-create.md).
* As chaves de acesso para serviços, como o Armazenamento. Para obter mais informações, veja [Acerca das contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).
* As credenciais de Acesso Remoto para serviços, como as Máquinas Virtuais do Azure.

Se o destinatário precisar de restringir o acesso aos seus recursos, deverá considerar a atualização de todos os segredos associados ao serviço. A maioria dos recursos pode ser atualizada com os seguintes passos:

  1. Inicie sessão no [portal do Azure](https://portal.azure.com).
  2. No menu Hub, selecione **Todos os recursos**.
  3. Selecione o recurso.
  4. Na página de recursos, clique em **Configurações**. Aqui pode ver e atualizar os segredos existentes.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Se eu assumir a propriedade de faturação de uma subscrição no meio do ciclo de faturação, preciso de pagar o ciclo de faturação na totalidade?

A sua conta é responsável pelo pagamento de uma eventual utilização relatada a partir do momento da transferência em diante. Pode haver utilização que tenha ocorrido antes da transferência, mas que seja relatada depois. A utilização está incluída na fatura da sua conta.

### <a name="can-i-use-a-different-payment-method"></a>Posso utilizar um método de pagamento diferente?

Sim. Ao aceitar o pedido de transferência, pode selecionar um método de pagamento existente que esteja ligado à sua conta ou adicionar um novo método de pagamento.

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>Como posso transferir a propriedade da minha conta de subscrição Contrato Enterprise (EA) se o proprietário da conta original já não fizer parte da organização?

O Administrador de Empresa pode atualizar a propriedade da conta para qualquer conta, mesmo depois de o proprietário da conta original deixar de fazer parte da organização. Pode fazê-lo seguindo as instruções para [Transferir a Propriedade da Conta para Todas as Subscrições](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) no EA Portal.

## <a name="troubleshooting"></a>Resolução de problemas

### <a id="no-button"></a> Por que não vejo o botão "Transferir subscrição"?

A transferência de subscrição personalizada não está disponível para a sua conta de faturação. Atualmente, não há suporte para transferir a propriedade de faturação de subscrições em contas Contrato Enterprise (EA) no portal do Azure. Além disso, as contas do Contrato de Cliente Microsoft criadas enquanto trabalham com um representante da Microsoft não suportam a transferência de propriedade de faturação.

### <a id="no-button"></a> Por que o meu tipo de subscrição não suporta a transferência?

Nem todos os tipos de subscrição suportam a transferência de propriedade de faturação. Para ver a lista de tipos de subscrição que suportam a transferência, veja [Tipos de subscrição suportados](#supported-subscription-types)

### <a id="no-button"></a> Por que estou a obter um erro de acesso negado quanto tento transferir a propriedade de faturação de uma subscrição?

Pode ver este erro se estiver a tentar transferir uma subscrição do Plano de Microsoft Azure e não tiver a permissão necessária. Para transferir uma subscrição do plano do Microsoft Azure, precisa de ser um proprietário ou colaborador na secção Fatura para a qual a subscrição é faturada. Para obter mais informações, veja [Gerir subscrições para a secção de fatura](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- Reveja e atualize o Administrador do Serviço, os Coadministradores e outras funções de RBAC. Para saber mais, veja [Adicionar ou alterar os administradores de subscrição](billing-add-change-azure-subscription-administrator.md) e [Gerir acesso através do RBAC e do portal do Azure](../role-based-access-control/role-assignments-portal.md).

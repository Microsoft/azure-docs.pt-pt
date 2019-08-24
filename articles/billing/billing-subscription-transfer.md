---
title: Transferir a propriedade de cobrança da assinatura do Azure para outra conta | Microsoft Docs
description: Descreve como transferir a propriedade de cobrança de uma assinatura do Azure para outra conta e algumas perguntas frequentes sobre o processo
keywords: transferir assinatura do Azure, assinatura de transferência do Azure, mover assinatura do Azure para outra conta, Azure alterar proprietário da assinatura, transferir assinatura do Azure para outra conta, cobrança de transferência do Azure
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
ms.openlocfilehash: 1e4496e2b5d2b21fd878ef68665b8e5b06fa6cc5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012519"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Transferir a propriedade de cobrança de uma assinatura do Azure para outra conta

Talvez você queira transferir a propriedade de cobrança da sua assinatura do Azure se estiver saindo de sua organização ou quiser que sua assinatura seja cobrada em outra conta. A transferência da propriedade de cobrança para outra conta fornece aos administradores a permissão nova conta para executar tarefas de cobrança, como alterar o método de pagamento, exibir encargos e cancelar a assinatura.

Se você quiser manter a propriedade de cobrança, mas alterar o tipo de sua assinatura, consulte [mudar sua assinatura do Azure para outra oferta](billing-how-to-switch-azure-offer.md). Se você quiser controlar quem pode gerenciar recursos na assinatura, consulte [funções internas para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Se você for um cliente Enterprise Agreement (EA), seus administradores corporativos poderão transferir a propriedade de cobrança de suas assinaturas entre contas. Para obter mais informações, consulte [transferir a propriedade de cobrança das assinaturas do ea (Enterprise Agreement)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Transferir a propriedade de cobrança de uma assinatura do Azure

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador da conta de cobrança que tem a assinatura que você deseja transferir. Para descobrir se você é um administrador, confira [perguntas](#faq)frequentes.

1. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Selecione **assinaturas** no painel à esquerda. Dependendo do seu acesso, talvez seja necessário selecionar um escopo de cobrança e, em seguida, selecionar **assinaturas** ou **assinaturas do Azure**.

1. Selecione **transferir propriedade de cobrança** para a assinatura que você deseja transferir. 

   ![Selecione a assinatura a ser transferida](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Insira o endereço de email de um usuário que seja um administrador de cobrança da conta que será o novo proprietário da assinatura.

1. Se você estiver transferindo sua assinatura para uma conta em outro locatário do Azure AD, selecione se deseja mover a assinatura para o locatário da nova conta. Para obter mais informações, consulte [transferindo a assinatura para uma conta em outro locatário do Azure ad](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)

    > [!IMPORTANT]
    >
    > Se você optar por mover a assinatura para o locatário do Azure AD da nova conta, todas as atribuições de [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md) para gerenciar recursos na assinatura serão removidas permanentemente. Somente o usuário na nova conta que aceita sua solicitação de transferência terá acesso para gerenciar recursos na assinatura. Para obter mais informações, consulte [transferindo a assinatura para um usuário em outro locatário do Azure ad](../active-directory/managed-identities-azure-resources/known-issues.md). Como alternativa, você pode desmarcar a caixa para assinatura do locatário do Azure AD para transferir a propriedade de cobrança sem mover a assinatura para o locatário da nova conta. Se você fizer isso, as permissões RBAC existentes para gerenciar os recursos do Azure serão mantidas.
  
    ![Enviar página de transferência](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Selecione **Enviar solicitação de transferência**.

1. O usuário recebe um email com instruções para revisar sua solicitação de transferência.

   ![Email de transferência de assinatura enviado ao destinatário](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Para aprovar a solicitação de transferência, o usuário seleciona o link no email e segue as instruções. O usuário precisaria selecionar um método de pagamento que será usado para pagar pela assinatura. Além disso, se o usuário não tiver uma conta do Azure, precisará se inscrever para uma nova conta. 

   ![Primeira página da Web de transferência de assinatura](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Segunda página da Web de transferência de assinatura](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Segunda página da Web de transferência de assinatura](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Êxito! A assinatura agora é transferida.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Transferindo a assinatura para uma conta em outro locatário do Azure AD

Um locatário Azure Active Directory (AD) é criado quando você se inscreve no Azure. O locatário representa sua conta. Você usa o locatário para gerenciar o acesso às suas assinaturas e recursos.

Quando você cria uma nova assinatura, ela é hospedada no locatário do Azure AD da sua conta. Se você quiser fornecer aos outros acesso à sua assinatura ou aos seus recursos, você precisa convidá-los para ingressar no seu locatário. Isso ajuda a controlar o acesso às suas assinaturas e recursos.

Quando você transfere a propriedade de cobrança de sua assinatura para uma conta em outro locatário do Azure AD, você pode mover a assinatura para o locatário da nova conta. Se você fizer isso, todos os usuários, grupos ou entidades de serviço que tiverem o [acesso baseado em função (RBAC)](../role-based-access-control/role-assignments-portal.md) para gerenciar assinaturas e seus recursos perderão seu acesso. Somente o usuário na nova conta que aceita sua solicitação de transferência terá acesso para gerenciar os recursos. Para fornecer acesso aos usuários que originalmente tinham acesso, o novo proprietário teria que [adicionar manualmente esses usuários à assinatura](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Transferindo assinaturas de desenvolvimento/teste do Visual Studio, MPN e pré-pago

As assinaturas do Visual Studio e do Microsoft Partner Network têm o crédito do Azure recorrente mensal associado a elas. Quando você transfere essas assinaturas, seu crédito não está disponível na conta de cobrança de destino. A assinatura usa o crédito na conta de cobrança de destino. Por exemplo, se Bob transferir uma assinatura Visual Studio Enterprise para a conta de Jane no nono-set e Jane aceitar a transferência. Depois que a transferência for concluída, a assinatura começará a usar o crédito na conta de Jane. O crédito será redefinido todos os meses na 9ª. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Transferir a propriedade de cobrança de assinaturas Enterprise Agreement (EA)

O administrador corporativo pode transferir a propriedade de assinaturas entre contas em um registro. Para obter mais informações, consulte [transferir a propriedade da conta](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) no portal de ea.

## <a name="next-steps-after-accepting-billing-ownership"></a>Próximas etapas depois de aceitar a propriedade de cobrança

Se você aceitou a propriedade de cobrança de uma assinatura do Azure, recomendamos que você examine estas próximas etapas:

1. Examine e atualize o administrador do serviço, os coadministradores e outras funções de RBAC. Para saber mais, confira [Adicionar ou alterar os administradores de assinatura do Azure](billing-add-change-azure-subscription-administrator.md) e [gerenciar o acesso usando o RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).
1. Atualize as credenciais associadas aos serviços desta assinatura, incluindo:
   1. Certificados de gerenciamento que concedem direitos de administrador de usuário aos recursos de assinatura. Para obter mais informações, consulte [criar e carregar um certificado de gerenciamento para o Azure](../cloud-services/cloud-services-certs-create.md)
   1. Chaves de acesso para serviços como armazenamento. Para obter mais informações, consulte [sobre contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md)
   1. Credenciais de acesso remoto para serviços como máquinas virtuais do Azure.
1. Se você estiver trabalhando com um parceiro, considere atualizar a ID do parceiro nesta assinatura. Você pode atualizar a ID do parceiro no [portal do Azure](https://portal.azure.com). Para obter mais informações, consulte [vincular uma ID de parceiro às suas contas do Azure](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

A transferência de assinatura no portal do Azure está disponível para os tipos de assinatura listados abaixo. Atualmente, a transferência não tem suporte para assinaturas de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) ou [de Azure via Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) . Para obter uma solução alternativa, consulte [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md). Para transferir outras assinaturas, como [patrocínio](https://azure.microsoft.com/offers/ms-azr-0036p/) ou planos de suporte, [entre em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Rede de parceiros da Microsoft](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Assinantes do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Desenvolvimento/Teste Pago Conforme o Uso](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plano de Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\*[Por meio do portal de ea](#EA).

\*\*Somente com suporte para contas criadas durante a inscrição no site do Azure. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Perguntas frequentes (FAQ) para remetentes

Essas perguntas frequentes se aplicam aos usuários, que estão transferindo a propriedade de cobrança de uma assinatura do Azure para outra conta.

### <a name="whoisaa"></a>Quem é um administrador de cobrança de uma conta?

Um administrador de cobrança é uma pessoa que tem permissão para gerenciar a cobrança de uma conta. Eles estão autorizados a acessar a cobrança no [portal do Azure](https://portal.azure.com) e executar várias tarefas de cobrança, como criar assinaturas, exibir e pagar faturas ou atualizar métodos de pagamento.

Para identificar as contas para as quais você é um administrador de cobrança, use as seguintes etapas:

1. Visite a [página Gerenciamento de custos + cobrança em portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Selecione **todos os** escopos de cobrança no painel esquerdo. 
1. A página assinaturas lista todas as assinaturas para as quais você é um administrador de cobrança.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Tudo é transferido? Incluindo grupos de recursos, VMs, discos e outros serviços em execução?

Todos os seus recursos, como VMs, discos e sites, são transferidos para a nova conta. No entanto, se você transferir a assinatura para uma conta em outro locatário do Azure AD, as [funções de administrador](billing-add-change-azure-subscription-administrator.md) e as atribuições de [RBAC (controle de acesso baseado em função)](../role-based-access-control/role-assignments-portal.md) na assinatura [não são](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)transferidas. Além disso, os [registros de aplicativo](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) e outros serviços específicos de locatário não são transferidos junto com a assinatura.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Posso transferir a propriedade para uma conta em outro país?
Infelizmente, as transferências entre países não podem ser executadas no portal do Azure. Para transferir sua assinatura entre os países, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Sou um administrador em duas contas. Posso transferir uma assinatura de uma das minhas contas para outra?
Sim, você pode transferir a assinatura entre suas contas. Suas contas são consideradas conceitualmente em contas de dois usuários diferentes, para que você possa usar as etapas acima para transferir assinaturas entre suas contas.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Uma transferência de assinatura resulta em qualquer tempo de inatividade do serviço?

Se você transferir uma assinatura para uma conta no mesmo locatário do Azure AD, não haverá nenhum impacto para os recursos em execução na assinatura. No entanto, se você transferir a assinatura para uma conta em outro locatário e decidir mover a assinatura para o locatário, todos os usuários, grupos e entidades de serviço que tinham o [acesso baseado em função (RBAC)](../role-based-access-control/overview.md) para gerenciar os recursos na assinatura perderão seu acesso . Isso pode resultar em tempo de inatividade do serviço.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>Os usuários na nova conta têm acesso ao histórico de uso e cobrança?

As únicas informações disponíveis para os usuários em nova conta são o custo do mês passado para sua assinatura. O restante do histórico de uso e cobrança não é transferido com a assinatura

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Como fazer migrar dados e serviços para minha assinatura do Azure para uma nova assinatura?

Se você não puder transferir a propriedade da assinatura, poderá migrar manualmente seus recursos. Consulte [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Se eu transferir uma assinatura do Visual Studio ou Microsoft Partner Network, meu crédito continuará com a assinatura na nova conta?

Não, seu crédito não está disponível na nova conta. O usuário que aceita a solicitação de transferência precisa ter uma licença do Visual Studio para aceitar a solicitação de transferência. A assinatura usa o crédito do Visual Studio que está disponível na conta do usuário. Para obter mais informações, consulte [transferindo as assinaturas de desenvolvimento/teste do Visual Studio, Microsoft Partner Network (MPN) e pago pelo uso](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Perguntas frequentes (FAQ) para destinatários

Essas perguntas frequentes se aplicam aos usuários, que estão aceitando a propriedade de cobrança de uma assinatura do Azure de outra conta.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Se eu assumir a propriedade de cobrança de uma assinatura de outra conta, os usuários nessa conta continuarão tendo acesso aos meus recursos?

Sim. No entanto, se sua conta estiver em um locatário do Azure AD diferente do locatário da assinatura e o usuário que enviou a solicitação de transferência mover a assinatura para o locatário da sua conta, quaisquer [funções de administrador](billing-add-change-azure-subscription-administrator.md) e [RBAC (controle de acesso baseado em função) ](../role-based-access-control/role-assignments-portal.md)as atribuições são removidas. Para exibir os usuários que têm acesso de [RBAC (acesso baseado em função)](../role-based-access-control/overview.md) para gerenciar recursos na assinatura, use as seguintes etapas:

1. Visite a [página de assinatura no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a assinatura que você deseja verificar e, em seguida, selecione **controle de acesso (iam)** no painel esquerdo.
1. Selecione **atribuições de função** na parte superior da página. A página atribuições de função lista todos os usuários que têm acesso de RBAC na assinatura.

Mesmo que as atribuições de [RBAC (controle de acesso baseado em função)](../role-based-access-control/role-assignments-portal.md) sejam removidas durante a transferência, os usuários na conta do proprietário original ainda poderão ter acesso à assinatura por meio de alguns mecanismos de segurança, incluindo:

* Certificados de gerenciamento que concedem direitos de administrador de usuário aos recursos de assinatura. Para obter mais informações, consulte [criar e carregar um certificado de gerenciamento para o Azure](../cloud-services/cloud-services-certs-create.md).
* Chaves de acesso para serviços como armazenamento. Para obter mais informações, veja [Acerca das contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).
* Credenciais de acesso remoto para serviços como máquinas virtuais do Azure.

Se o destinatário precisar restringir, o acesso aos seus recursos, eles deverão considerar a atualização de todos os segredos associados ao serviço. A maioria dos recursos pode ser atualizada usando as seguintes etapas:

  1. Inicie sessão no [portal do Azure](https://portal.azure.com).
  2. No menu Hub, selecione **todos os recursos**.
  3. Selecione o recurso.
  4. Na página de recursos, clique em **configurações**. Aqui você pode exibir e atualizar os segredos existentes.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Se eu assumir a propriedade de cobrança de uma assinatura no meio do ciclo de cobrança, preciso pagar pelo ciclo de cobrança inteiro?

Sua conta é responsável pelo pagamento por qualquer uso relatado a partir do momento da transferência em diante. Pode haver algum uso que tenha ocorrido antes da transferência, mas que foi relatado depois. O uso está incluído na fatura da sua conta.

### <a name="can-i-use-a-different-payment-method"></a>Posso usar um método de pagamento diferente?

Sim. Ao aceitar a solicitação de transferência, você pode selecionar um método de pagamento existente que esteja vinculado à sua conta ou adicionar um novo método de pagamento.

## <a name="troubleshooting"></a>Resolução de problemas

### <a id="no-button"></a>Por que não vejo o botão "transferir assinatura"?

A transferência de assinatura de autoatendimento não está disponível para sua conta de cobrança. Atualmente, não há suporte para transferir a propriedade de cobrança de assinaturas em contas Enterprise Agreement (EA) no portal do Azure. Além disso, as contas de contrato do cliente da Microsoft criadas enquanto trabalham com um representante da Microsoft não dão suporte à transferência de propriedade de cobrança. 

### <a id="no-button"></a>Por que meu tipo de assinatura não dá suporte à transferência? 

Nem todos os tipos de assinaturas dão suporte à transferência de propriedade de cobrança. Para exibir a lista de tipos de assinatura que dão suporte a transferências, consulte [tipos de assinatura com suporte](#supported-subscription-types)

### <a id="no-button"></a>Por que estou recebendo um erro de acesso negado ao tentar transferir a propriedade de cobrança de uma assinatura? 

Você verá esse erro se estiver tentando transferir uma assinatura do plano de Microsoft Azure e não tiver a permissão necessária. Para transferir uma assinatura do plano de Microsoft Azure, você precisa ser um proprietário ou colaborador na seção fatura para a qual a assinatura é cobrada. Para obter mais informações, consulte [gerenciar assinaturas para a seção fatura](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

- Examine e atualize o administrador do serviço, os coadministradores e outras funções de RBAC. Para saber mais, confira [Adicionar ou alterar os administradores de assinatura do Azure](billing-add-change-azure-subscription-administrator.md) e [gerenciar o acesso usando o RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

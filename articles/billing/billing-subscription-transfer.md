---
title: Transferir a propriedade de subscrição do Azure para outra conta | Documentos da Microsoft
description: Descreve como transferir uma subscrição do Azure para outro utilizador e algumas perguntas mais frequentes (FAQ) sobre o processo
keywords: Transferir a subscrição de transferência de subscrição do azure, azure, mova a subscrição do azure para outro proprietário de subscrição de alteração de conta, do azure, transferir a subscrição do azure para outra conta
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d8baa5f10aeba179ef141cfea86fded8cf44dfc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878215"
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Transferir a propriedade de uma subscrição do Azure para outra conta

Transferi a sua subscrição para outro utilizador no Centro de contas para alterar o administrador de conta e passar a propriedade de faturação de subscrição. Para alterar a sua subscrição para outra oferta, consulte [mudar a sua subscrição do Azure para outra oferta](billing-how-to-switch-azure-offer.md).

> [!IMPORTANT]
> 
> Se transferir uma subscrição para um novo do Azure AD de inquilino, todas as atribuições de função na [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) são eliminados permanentemente do inquilino de origem e não são migradas para o inquilino de destino.

## <a name="transfer-ownership-of-an-azure-subscription"></a>Transferir a propriedade de uma subscrição do Azure

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Inicie sessão em [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions) como administrador de conta. Para saber quem é o administrador de conta da subscrição, consulte [perguntas mais frequentes sobre](#faq).

1. Selecione a subscrição para transferir.

1. Certifique-se de que a sua subscrição é elegível para transferência Self-Service, verificando a **oferecem** e **ID da oferta** com o [lista de ofertas de suporte](#supported).

   ![Verifique se o ID de oferta de subscrição no Centro de contas](./media/billing-subscription-transfer/image0.png)
1. Clique em **Transferir subscrição**.

   ![Separador de subscrições de conta do Azure](./media/billing-subscription-transfer/image1.png)
1. Especifique o destinatário.

   > [!IMPORTANT]
   > 
   > Se transferir uma subscrição para um novo do Azure AD de inquilino, todas as atribuições de função na [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) são eliminados permanentemente do inquilino de origem e não são migradas para o inquilino de destino.

   ![Caixa de diálogo transferência de subscrição](./media/billing-subscription-transfer/image2.PNG)

1. O destinatário recebe automaticamente um e-mail com uma ligação de aceitação.

   ![E-mail de transferência de subscrição para o destinatário](./media/billing-subscription-transfer/image3.png)
1. O destinatário clica na ligação e segue as instruções, o que inclui a introdução das respetivas informações de pagamento.

   ![Primeira página de web de transferência de subscrição](./media/billing-subscription-transfer/image4.png)

   ![Segunda página de web de transferência de subscrição](./media/billing-subscription-transfer/image5.png)
1. Êxito! A subscrição é transferida agora.

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Transferir a propriedade de subscrição para clientes Enterprise Agreement (EA)

O Administrador da Empresa pode transferir a propriedade das subscrições que pertençam a uma inscrição. Para começar a utilizar, veja [transferir a propriedade da conta](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) no portal do EA.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Passos seguintes depois de aceitar a propriedade de uma subscrição

1. Agora, está o administrador de conta. Rever e atualizar o administrador de serviço e outras funções do RBAC Coadministradores. Para obter mais informações, consulte [adicionar ou alterar os administradores de subscrição do Azure](billing-add-change-azure-subscription-administrator.md) e [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).
1. Atualize as credenciais associadas com serviços nesta subscrição, incluindo:
   1. Certificados de gestão que concedem ao utilizador direitos de administrador aos recursos de subscrição. Para obter mais informações, consulte [criar e carregar um gerenciamento de certificados para o Azure](../cloud-services/cloud-services-certs-create.md)
   1. Chaves de acesso para serviços como o armazenamento. Para obter mais informações, consulte [sobre as contas de armazenamento](../storage/common/storage-create-storage-account.md)
   1. Credenciais de acesso remoto para os serviços, como máquinas virtuais do Azure. 
1. Se estiver trabalhando com um parceiro, considere atualizar o ID de parceiro nesta subscrição. Pode atualizar o ID de parceiro no [portal do Azure](https://portal.azure.com).

<a id="supported"></a>

## <a name="whats-supported"></a>O que é suportado:

Transferência de subscrição Self-Service está disponível para ofertas ou tipos de subscrição listados na tabela seguinte. Atualmente não é possível transferir uma versão de avaliação gratuita ou [do Azure no Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) subscrições. Para obter uma solução, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md). Para transferir outras subscrições, como [patrocínio](https://azure.microsoft.com/offers/ms-azr-0036p/) ou planos de suporte, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

| Nome da Oferta                                                                             | Número da oferta |
|----------------------------------------------------------------------------------------|--------------|
| [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR-0017P        |
| [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR-0025P        |
| [O MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR-0062P        |
| [Pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR-0003P        |
| [Pay as you go programador/teste](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR-0023P        |
| [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR-0063P        |
| [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P        |
| [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR-0059P        |
| [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR-0060P        |

\* [Através do portal EA](#EA)

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

### <a name="whoisaa"></a> Quem é o administrador de conta da subscrição?

O administrador de conta é a pessoa que inscreveu ou comprou a subscrição do Azure. Eles estão autorizados a aceder a [Centro de contas](https://account.azure.com/Subscriptions) e realizar várias tarefas de gestão, como criar subscrições, cancelar subscrições, alterar a faturação de uma subscrição ou alterar o administrador de serviço. Se não tiver a certeza de quem é o administrador de conta de uma subscrição, utilize os seguintes passos para descobrir.

1. Visite a [página de Subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que pretende verificar e, em seguida, procure em **definições**.
1. Selecione **propriedades**. O administrador de conta da subscrição é apresentado na **administrador de conta** caixa.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Tudo o que transferir? Incluindo grupos de recursos, as VMs, discos e outros serviços em execução?

Todos os seus recursos, como VMs, discos e transferência de Web sites para o novo proprietário. No entanto, qualquer [funções de administrador](billing-add-change-azure-subscription-administrator.md) e [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/role-assignments-portal.md) não transferir políticas que configurou em diretórios diferentes. Além disso, [registos das aplicações](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) e outros serviços de inquilino específico não se transferem ao longo.

### <a id="no-button"></a> Por que motivo não vejo no botão "Transferir a subscrição"?

Infelizmente, a transferência de subscrição Self-Service não está disponível para a sua oferta ou país. Para transferir a sua subscrição [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Uma transferência de subscrição resulta em qualquer período de indisponibilidade de serviço?

Não há nenhum impacto sobre o serviço. Transferir a subscrição cancela a subscrição sob a conta administrador do atual e cria uma subscrição na conta do destinatário. A nova subscrição está associada aos serviços do Azure subjacentes. O ID de subscrição permanece igual.

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>Como posso utilizar este processo para alterar o diretório da subscrição?

Uma subscrição do Azure é criada no diretório que o administrador de conta pertence. Para alterar o diretório, transferi a subscrição para uma conta de utilizador no diretório de destino. Quando esse utilizador tiver concluído os passos para aceitar a transferência, a subscrição é automaticamente movida para o diretório de destino.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>Se eu assumir a propriedade de faturação de uma subscrição de outra organização, continua a ter acesso aos meus recursos?

Se a subscrição é transferida para outro inquilino, os utilizadores associados ao inquilino anterior perdem o acesso à subscrição. Mesmo que um utilizador não for um administrador de serviço ou coadministrador, poderá continua a ter acesso à subscrição através de outros mecanismos de segurança, incluindo:

* Certificados de gestão que concedem ao utilizador direitos de administrador aos recursos de subscrição. Para obter mais informações, consulte [criar e carregar um certificado de gestão do Azure](../cloud-services/cloud-services-certs-create.md).
* Chaves de acesso para serviços como o armazenamento. Para obter mais informações, veja [Acerca das contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).
* Credenciais de acesso remoto para os serviços, como máquinas virtuais do Azure.

Se o destinatário tem de restringir o acesso aos respetivos recursos, deve considerar atualizar quaisquer segredos associados ao serviço. A maioria dos recursos podem ser atualizados utilizando os seguintes passos:

  1. Aceda ao [Portal do Azure](https://portal.azure.com).
  2. No Hub menu, selecione **todos os recursos**.
  3. Selecione o recurso.
  4. No painel de recursos, clique em **definições**. Aqui pode ver e atualizar segredos existentes.

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>Se eu transferir a subscrição no meio do ciclo de faturação, o pagamento de destinatário para a faturação todo ciclo?

O remetente é responsável pelo pagamento de qualquer utilização que foi relatado até ao ponto que a transferência foi concluída. O destinatário é responsável por utilização comunicada desde o momento de transferência e posteriores. Pode haver alguns utilização que ocorria antes da transferência, mas foi reportada posteriormente. A utilização está incluída na fatura do destinatário.

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>O destinatário tem acesso ao histórico de faturação e utilização?

  A única informação disponível para o destinatário é a quantidade de última fatura ou se a subscrição foi transferida antes da primeiro fatura foi gerada, o saldo atual. O restante do histórico de faturação e utilização não transfere com a subscrição.

### <a name="can-the-offer-be-changed-during-a-transfer"></a>A oferta pode ser alterada durante uma transferência?

A oferta tem de permanecer o mesmo. Para alterar a sua oferta, consulte [mudar a sua subscrição do Azure para outra oferta](billing-how-to-switch-azure-offer.md).

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-country"></a>Pode transferir uma subscrição para uma conta de utilizador em outro país?

Não, a transferência de uma subscrição para uma conta de utilizador em outro país não é suportada. Conta de utilizador do destinatário tem de estar no mesmo país.

### <a name="can-the-recipient-use-a-different-payment-method"></a>O destinatário utilizar um método de pagamento diferente?

Sim. Mas o histórico de faturação de subscrição é dividido em duas contas.  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>O método de pagamento é afetado depois de transferida, uma subscrição do Azure?

Para aceitar uma transferência de subscrição, deve ser fornecido um cartão de crédito ou semelhante de pagamento para pagar a subscrição. Por exemplo, se Bob transfere uma subscrição para a Joana e a Joana aceita a transferência, a Joana tem de fornecer um método de pagamento para pagar a subscrição. Após a transferência estiver concluída, a Joana é cobrada para a subscrição não Bob.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Como migrar dados e serviços para a minha subscrição do Azure para a nova subscrição?

Se não é possível transferir a propriedade de subscrição, pode migrar manualmente os recursos. Ver [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
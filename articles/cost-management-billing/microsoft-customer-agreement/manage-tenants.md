---
title: Gerir inquilinos na sua conta de faturação do Microsoft Customer Agreement - Azure
description: O artigo ajuda-o a compreender e a gerir os inquilinos associados à sua conta de faturação do Microsoft Customer Agreement.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: dc34d0f12430838be29897ccc5cbeee382ecaa2b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107493125"
---
# <a name="manage-tenants-in-your-microsoft-customer-agreement-billing-account"></a>Gerir inquilinos na sua conta de faturação do Microsoft Customer Agreement

O artigo ajuda-o a compreender e a gerir os inquilinos associados à sua conta de faturação do Microsoft Customer Agreement. Use a informação para gerir inquilinos, transferir subscrições e administrar a propriedade de faturação enquanto garante acesso seguro ao seu ambiente de faturação.

## <a name="whats-a-tenant"></a>O que é um inquilino?

Um inquilino é uma representação digital da sua organização e está principalmente associado a um domínio, como Microsoft.com. É um ambiente gerido através do Azure Ative Directory que lhe permite atribuir permissões aos utilizadores para gerir os recursos e faturação do Azure.

Cada inquilino é distinto e separado de outros inquilinos, no entanto, você pode permitir que utilizadores convidados de outros inquilinos acedam ao seu inquilino para rastrear seus custos e gerir a faturação.

## <a name="how-tenants-and-subscriptions-relate-to-billing-account"></a>Como os inquilinos e assinaturas se relacionam com a conta de faturação

Utiliza o Seu Microsoft Customer Agreement (conta de faturação) para controlar os custos e gerir a faturação. Cada conta de faturação tem pelo menos um perfil de faturação. O perfil de faturação permite-lhe gerir a sua fatura e o seu método de pagamento. Cada perfil de faturação inclui uma secção de fatura, por padrão. Pode criar mais secções de fatura para agrupar, rastrear e gerir custos a um nível mais granular, se necessário.

- Sua conta de faturação está associada a um único inquilino. Significa que apenas os utilizadores que fazem parte do inquilino podem aceder à sua conta de faturação.
- Quando cria uma nova subscrição da Azure para a sua conta de faturação, é sempre criada no seu inquilino de conta de faturação. No entanto, pode mover subscrições para outros inquilinos. Também pode ligar as assinaturas existentes de outros inquilinos à sua conta de faturação. Permite-lhe gerir centralmente a faturação através de um inquilino, mantendo recursos e assinaturas em outros inquilinos com base nas suas necessidades.

O diagrama seguinte mostra como a conta de faturação e as assinaturas estão ligadas aos inquilinos. A conta de faturação do Contoso MCA está associada ao Inquilino 1 enquanto a conta Contoso PAYG está associada ao Inquilino 2. Vamos supor que a Contoso quer pagar a subscrição do PAYG através da sua conta de faturação MCA, eles podem usar uma transferência de propriedade de faturação para ligar a subscrição à sua conta de faturação MCA. A subscrição e os seus recursos continuarão associados ao Inquilino 2, mas são pagos pela utilização da conta de faturação da MCA.

:::image type="content" source="./media/manage-tenants/billing-hierarchy-example.png" alt-text="Diagrama mostrando um exemplo da hierarquia de faturação." border="false" lightbox="./media/manage-tenants/billing-hierarchy-example.png":::

## <a name="manage-subscriptions-under-multiple-tenants-in-a-single-microsoft-customer-agreement"></a>Gerir subscrições com vários inquilinos num único Acordo de Cliente da Microsoft

Os proprietários de faturação podem criar subscrições quando têm as [permissões apropriadas](../manage/understand-mca-roles.md#subscription-billing-roles-and-tasks) para a conta de faturação. Por padrão, quaisquer novas subscrições criadas ao abrigo do Microsoft Customer Agreement estão no inquilino do Microsoft Customer Agreement.

- Pode ligar subscrições de outros inquilinos à sua conta de faturação do Microsoft Customer Agreement. A tomada de posse de uma subscrição só altera o regime de faturação. Não afeta o inquilino de serviço ou os papéis de Azure RBAC.
- Para alterar o proprietário da subscrição no arrendatário de serviço, deve transferir a [subscrição para um diretório Azure Ative diferente.](../../role-based-access-control/transfer-subscription.md)

## <a name="add-guest-users-to-your-microsoft-customer-agreement-tenant"></a>Adicione utilizadores convidados ao seu inquilino do Microsoft Customer Agreement

Os utilizadores que são adicionados ao seu inquilino de faturação do Microsoft Customer Agreement, para gerir as responsabilidades de faturação de um inquilino diferente, devem ser convidados como convidados.

Para convidar alguém como hóspede, o utilizador deve ter um endereço de e-mail existente com um domínio diferente do seu domínio Azure Ative Directory (AD). A Azure AD envia ao utilizador convidado um e-mail com um link para autenticação.

:::image type="content" source="./media/manage-tenants/guest-invitation-email.png" alt-text="Screenshot mostrando um convite de e-mail exemplo." lightbox="./media/manage-tenants/guest-invitation-email.png" :::

Quando um utilizador é adicionado ao inquilino do Microsoft Customer Agreement, deve [aceitar o convite](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation).

Quando selecionam o link **de convite aceitar,** são solicitados a autenticar com a Azure.

:::image type="content" source="./media/manage-tenants/authenticate.png" alt-text="Screenshot mostrando um pedido para autenticar com Azure." lightbox="./media/manage-tenants/authenticate.png" :::

Em seguida, selecionam **Aceitar**.

:::image type="content" source="./media/manage-tenants/accept-invitation.png" alt-text="Screenshot mostrando um pedido para aceitar o convite." lightbox="./media/manage-tenants/accept-invitation.png" :::

Depois de aceitarem, podem [ver a conta de faturação do Microsoft Customer Agreement sob Gestão de Custos + Faturação](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

:::image type="content" source="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" alt-text="Screenshot mostrando o Microsoft Customer Agreement na lista de contas de faturação." lightbox="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" :::

A autorização para convidar utilizadores convidados é controlada pelas definições AZure AD. O valor das definições é mostrado em **Definições** na página **de relações organizacionais.** Certifique-se de que a regulação é selecionada, caso contrário o convite falha. Para obter mais informações, consulte [permissões de acesso ao utilizador do utilizador.](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)

:::image type="content" source="./media/manage-tenants/external-collaboration-settings.png" alt-text="Screenshot mostrando definições de colaboração externa." lightbox="./media/manage-tenants/external-collaboration-settings.png" :::

> [!IMPORTANT]
> Os utilizadores convidados têm acesso ao inquilino do Microsoft Customer Agreement, o que pode potencialmente constituir uma preocupação de segurança. Para obter mais informações, consulte [Saiba como restringir as permissões padrão dos utilizadores.](../../active-directory/fundamentals/users-default-permissions.md#restrict-member-users-default-permissions)

## <a name="manage-multiple-microsoft-cloud-services-under-an-azure-ad-tenant"></a>Gerir vários serviços na nuvem da Microsoft sob um inquilino AZure AD

Você pode gerir vários serviços em nuvem para a sua organização sob um único inquilino AZure AD. As contas dos utilizadores de todas as ofertas em nuvem da Microsoft estão armazenadas no inquilino AZure AD, que contém contas e grupos de utilizadores. O diagrama que se segue mostra um exemplo de uma organização com múltiplos serviços usando um inquilino comum da AD Azure contendo contas. Cada serviço tem o seu próprio portal, em texto azul, onde os utilizadores gerem os seus serviços.

:::image type="content" source="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png" alt-text="Diagrama mostrando um exemplo de uma organização com múltiplos serviços usando um inquilino comum AZure AD contendo contas." border="false" lightbox="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png":::

## <a name="next-steps"></a>Passos seguintes

Leia os seguintes artigos para aprender a administrar a propriedade de faturação flexível e garantir o acesso seguro ao seu Contrato de Cliente da Microsoft.

- [Como configurar um inquilino](../../active-directory/develop/quickstart-create-new-tenant.md)
- [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md)
- [Transfira uma subscrição do Azure para um diretório AD Azure diferente](../../role-based-access-control/transfer-subscription.md)
- [Restringir permissões de acesso a hóspedes (pré-visualização) no Azure Ative Directory](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)
- [Adicionar utilizadores convidados ao seu diretório no portal do Azure](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Quais são as permissões de utilizador predefinidas no Azure Ative Directory?](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [O que é o Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)
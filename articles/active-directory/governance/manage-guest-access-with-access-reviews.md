---
title: Gerir o acesso dos hóspedes com comentários de acesso - Azure AD
description: Gerir utilizadores convidados como membros de um grupo ou atribuídos a uma aplicação com avaliações de acesso ao Azure Ative Directory
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76aeb63feb006e6962efeb3687363f18acb59904
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746699"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gerir o acesso de convidado com as revisões de acesso do Azure AD


Com o Azure Ative Directory (Azure AD), pode facilmente permitir a colaboração através dos limites organizacionais utilizando a [funcionalidade Azure AD B2B](../external-identities/what-is-b2b.md). Os utilizadores convidados de outros inquilinos podem ser [convidados por administradores](../external-identities/add-users-administrator.md) ou por [outros utilizadores.](../external-identities/what-is-b2b.md) Esta capacidade também se aplica a identidades sociais, como as contas da Microsoft.

Também pode garantir facilmente que os utilizadores dos hóspedes têm acesso adequado. Você pode pedir aos próprios hóspedes ou um decisor para participar em uma revisão de acesso e recertificar (ou atestar) o acesso dos hóspedes. Os revisores podem dar o seu parecer relativamente à necessidade de acesso contínuo de cada utilizador, com base nas sugestões do Azure AD. Quando uma revisão de acesso estiver terminada, pode então escruexá-las e remover o acesso aos hóspedes que já não precisam.

> [!NOTE]
> Este documento centra-se na revisão do acesso dos utilizadores convidados. Se pretender rever o acesso de todos os utilizadores, e não apenas os hóspedes, consulte [gerir o acesso do utilizador com comentários de acesso.](manage-user-access-with-access-reviews.md) Se pretender rever a adesão dos utilizadores em funções administrativas, como administrador global, consulte [Iniciar uma revisão de acesso na Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2

Para mais informações, [os requisitos da licença.](access-reviews-overview.md#license-requirements)

## <a name="create-and-perform-an-access-review-for-guests"></a>Criar e realizar uma revisão de acesso para os hóspedes

Primeiro, deve ser-lhe atribuída uma das seguintes funções:
- administrador global
- Administrador de utilizadores
- (Pré-visualização) M365 ou AAD Security Group proprietário do grupo a ser revisto

Em seguida, vá à [página de Governação da Identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) para garantir que as avaliações de acesso estão prontas para a sua organização.

O Azure AD permite vários cenários para a revisão dos utilizadores convidados.

Pode rever:

 - Um grupo em Azure AD que tem um ou mais convidados como membros.
 - Uma aplicação ligada ao Azure AD que tem um ou mais utilizadores convidados atribuídos à empresa. 

Em seguida, pode decidir se pede a cada hóspede para rever o seu próprio acesso ou pedir a um ou mais utilizadores que revejam o acesso de cada hóspede.

 Estes cenários são abordados nas seguintes secções.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Peça aos hóspedes para reverem a sua própria filiação num grupo

Pode utilizar comentários de acesso para garantir que os utilizadores que foram convidados e adicionados a um grupo continuem a precisar de acesso. Você pode facilmente pedir aos hóspedes para rever a sua própria adesão nesse grupo.

1. Para criar uma revisão de acesso para o grupo, selecione a revisão apenas para incluir os membros do utilizador convidado e que os membros se revejam. Para obter mais informações, consulte [Criar uma revisão de acesso de grupos ou aplicações.](create-access-review.md)

2. Peça a cada hóspede que reveja a sua própria filiação. Por padrão, cada hóspede que aceitou um convite recebe um e-mail da Azure AD com um link para a revisão de acesso. A Azure AD tem instruções para os hóspedes sobre como [rever o acesso a grupos ou aplicações.](perform-access-review.md)

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para mais informações, consulte [Complete uma revisão de acesso de grupos ou aplicações.](complete-access-review.md)

4. Além dos utilizadores que negaram a sua própria necessidade de acesso continuado, também é possível remover utilizadores que não responderam. Os utilizadores que não respondem potencialmente já não recebem e-mail.

5. Se o grupo não for utilizado para a gestão de acessos, também pode remover utilizadores que não foram selecionados para participar na revisão porque não aceitaram o convite. Não aceitar pode indicar que o endereço de e-mail do utilizador convidado tinha um erro. Se um grupo é usado como uma lista de distribuição, talvez alguns utilizadores convidados não tenham sido selecionados para participar porque são objetos de contacto.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Peça a um patrocinador para rever a adesão de um hóspede em um grupo

Você pode pedir a um patrocinador, como o proprietário de um grupo, para rever a necessidade de um hóspede continuar a ser membro em um grupo.

1. Para criar uma revisão de acesso para o grupo, selecione o comentário apenas para incluir membros do utilizador convidado. Em seguida, especifique um ou mais revisores. Para obter mais informações, consulte [Criar uma revisão de acesso de grupos ou aplicações.](create-access-review.md)

2. Peça aos revisores para darem o seu parecer. Por padrão, cada um recebe um e-mail da Azure AD com um link para o painel de acesso, onde analisam o [acesso a grupos ou aplicações.](perform-access-review.md)

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para mais informações, consulte [Complete uma revisão de acesso de grupos ou aplicações.](complete-access-review.md)

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Peça aos hóspedes que revejam o seu próprio acesso a uma aplicação

Pode utilizar comentários de acesso para garantir que os utilizadores que foram convidados para uma determinada aplicação continuem a precisar de acesso. Você pode facilmente pedir aos próprios hóspedes para rever a sua própria necessidade de acesso.

1. Para criar uma revisão de acesso para a aplicação, selecione o comentário apenas para incluir os hóspedes e que os utilizadores revejam o seu próprio acesso. Para obter mais informações, consulte [Criar uma revisão de acesso de grupos ou aplicações.](create-access-review.md)

2. Peça a cada hóspede que reveja o seu próprio acesso à aplicação. Por padrão, cada hóspede que aceitou um convite recebe um e-mail da Azure AD. Esse e-mail tem um link para a revisão de acesso no painel de acesso da sua organização. A Azure AD tem instruções para os hóspedes sobre como [rever o acesso a grupos ou aplicações.](perform-access-review.md)

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para mais informações, consulte [Complete uma revisão de acesso de grupos ou aplicações.](complete-access-review.md)

4. Além dos utilizadores que negaram a sua própria necessidade de acesso continuado, também é possível remover utilizadores convidados que não responderam. Os utilizadores que não respondem potencialmente já não recebem e-mail. Também pode remover utilizadores convidados que não foram selecionados para participar, especialmente se não foram convidados recentemente. Estes utilizadores não aceitaram o convite e por isso não tiveram acesso à aplicação. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Peça a um patrocinador que reveja o acesso de um hóspede a uma aplicação

Pode pedir a um patrocinador, como o proprietário de uma aplicação, que reveja a necessidade de continuar o acesso do hóspede à aplicação.

1. Para criar um revisão de acesso para a aplicação, selecione o comentário apenas para incluir os hóspedes. Em seguida, especifique um ou mais utilizadores como revisores. Para obter mais informações, consulte [Criar uma revisão de acesso de grupos ou aplicações.](create-access-review.md)

2. Peça aos revisores para darem o seu parecer. Por padrão, cada um recebe um e-mail da Azure AD com um link para o painel de acesso, onde analisam o [acesso a grupos ou aplicações.](perform-access-review.md)

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para mais informações, consulte [Complete uma revisão de acesso de grupos ou aplicações.](complete-access-review.md)

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Peça aos hóspedes que revejam a sua necessidade de acesso, em geral

Em algumas organizações, os hóspedes podem não estar cientes dos seus membros do grupo.

> [!NOTE]
> Versões anteriores do portal Azure não permitiram o acesso administrativo dos utilizadores com o UserType do Guest. Em alguns casos, um administrador no seu diretório pode ter alterado o valor do UserType de um hóspede para Membro utilizando o PowerShell. Se esta alteração ocorrer anteriormente no seu diretório, a consulta anterior pode não incluir todos os utilizadores convidados que historicamente tinham direitos de acesso administrativo. Neste caso, você precisa alterar o UserType do hóspede ou incluir manualmente o hóspede na associação de grupo.

1. Crie um grupo de segurança em Azure AD com os convidados como membros, se um grupo adequado já não existir. Por exemplo, pode criar um grupo com uma adesão de hóspedes mantida manualmente. Ou, você pode criar um grupo dinâmico com um nome como "Convidados de Contoso" para utilizadores no inquilino Contoso que têm o valor de atributo UserType do Hóspede.  Para obter eficiência, certifique-se de que o grupo é predominantemente convidado - não selecione um grupo que tenha utilizadores membros, uma vez que os utilizadores membros não precisam de ser revistos.  Além disso, tenha em mente que um utilizador convidado que seja membro do grupo pode ver os outros membros do grupo.

2. Para criar uma revisão de acesso para esse grupo, selecione os revisores para serem os próprios membros. Para obter mais informações, consulte [Criar uma revisão de acesso de grupos ou aplicações.](create-access-review.md)

3. Peça a cada hóspede que reveja a sua própria filiação. Por padrão, cada hóspede que aceitou um convite recebe um e-mail da Azure AD com um link para a revisão de acesso no painel de acesso da sua organização. A Azure AD tem instruções para os hóspedes sobre como [rever o acesso a grupos ou aplicações.](perform-access-review.md)  Os hóspedes que não aceitaram o convite aparecerão nos resultados da revisão como "Notificados".

4. Depois de os revisores darem entrada, pare a revisão de acesso. Para mais informações, consulte [Complete uma revisão de acesso de grupos ou aplicações.](complete-access-review.md)

5. Remova o acesso dos hóspedes a hóspedes que foram negados, não completaram a revisão, ou não aceitaram previamente o seu convite. Se alguns dos hóspedes forem contactos selecionados para participar na revisão ou não aceitarem previamente um convite, pode desativar as suas contas utilizando o portal Azure ou o PowerShell. Se o hóspede já não precisar de acesso e não for um contacto, pode remover o seu objeto de utilizador do seu diretório utilizando o portal Azure ou o PowerShell para eliminar o objeto do utilizador convidado.

## <a name="next-steps"></a>Passos seguintes

[Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
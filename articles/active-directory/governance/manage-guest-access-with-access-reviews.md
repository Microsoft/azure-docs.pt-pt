---
title: Gerir o acesso dos hóspedes com avaliações de acesso - Azure AD
description: Gerir os utilizadores convidados como membros de um grupo ou atribuídos a uma aplicação com avaliações de acesso ao Diretório Ativo azure
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/13/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bf2f9503ae704110786a1e73aec3da18c17e4ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932433"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gerir o acesso de convidado com as revisões de acesso do Azure AD


Com o Azure Ative Directory (Azure AD), pode facilmente permitir a colaboração através dos limites organizacionais utilizando a [funcionalidade Azure AD B2B](../b2b/what-is-b2b.md). Os utilizadores convidados de outros inquilinos podem ser [convidados por administradores](../b2b/add-users-administrator.md) ou por [outros utilizadores.](../b2b/what-is-b2b.md) Esta capacidade também se aplica a identidades sociais, como contas da Microsoft.

Também pode garantir facilmente que os utilizadores de hóspedes tenham acesso adequado. Você pode pedir aos próprios hóspedes ou a um decisor para participar em uma revisão de acesso e recertificar (ou atestar) o acesso dos hóspedes. Os revisores podem dar o seu parecer relativamente à necessidade de acesso contínuo de cada utilizador, com base nas sugestões do Azure AD. Quando uma revisão de acesso estiver concluída, pode então fazer alterações e remover o acesso aos hóspedes que já não precisam.

> [!NOTE]
> Este documento centra-se na revisão do acesso dos utilizadores convidados. Se quiser rever o acesso de todos os utilizadores, e não apenas os hóspedes, consulte [Gerir o acesso ao utilizador com avaliações](manage-user-access-with-access-reviews.md)de acesso . Se pretender rever a adesão dos utilizadores em funções administrativas, como administrador global, consulte Iniciar uma revisão de [acesso na Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2

Para mais informações, [requisitos de Licença.](access-reviews-overview.md#license-requirements)

## <a name="create-and-perform-an-access-review-for-guests"></a>Criar e realizar uma revisão de acesso para os hóspedes

Em primeiro lugar, como administrador global ou administrador de utilizadores, aceda à [página de Governança de Identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) para garantir que as avaliações de acesso estão prontas para a sua organização.

A Azure AD permite vários cenários para rever os utilizadores convidados.

Pode rever qualquer um deles:

 - Um grupo em Azure AD que tem um ou mais convidados como membros.
 - Uma aplicação ligada à AD Azure que tem um ou mais utilizadores convidados atribuídos ao mesmo. 

Pode então decidir se pede a cada hóspede para rever o seu próprio acesso ou pedir a um ou mais utilizadores que revejam o acesso de cada hóspede.

 Estes cenários estão cobertos nas seguintes secções.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Peça aos hóspedes que revejam a sua própria adesão num grupo

Pode utilizar avaliações de acesso para garantir que os utilizadores que foram convidados e adicionados a um grupo continuem a precisar de acesso. Você pode facilmente pedir aos hóspedes para rever a sua própria adesão nesse grupo.

1. Para criar uma revisão de acesso para o grupo, selecione a revisão para incluir apenas os membros dos utilizadores convidados e que os membros se revejam. Para mais informações, consulte [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md).

2. Peça a cada hóspede para rever a sua própria filiação. Por defeito, cada hóspede que aceitasse um convite recebe um e-mail da Azure AD com um link para a revisão de acesso. A Azure AD tem instruções para os hóspedes sobre como [rever o acesso a grupos ou aplicações](perform-access-review.md).

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para mais informações, consulte [Complete uma revisão de acesso de grupos ou aplicações](complete-access-review.md).

4. Além dos utilizadores que negaram a sua própria necessidade de acesso continuado, também pode remover utilizadores que não responderam. Os utilizadores que não respondem potencialmente já não recebem e-mail.

5. Se o grupo não for utilizado para a gestão de acessos, também pode remover utilizadores que não foram selecionados para participar na revisão porque não aceitaram o convite. Não aceitar pode indicar que o endereço de e-mail do utilizador convidado tinha uma gralha. Se um grupo é usado como uma lista de distribuição, talvez alguns utilizadores convidados não tenham sido selecionados para participar porque são objetos de contacto.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Peça a um patrocinador para rever a adesão de um hóspede em um grupo

Você pode pedir a um patrocinador, como o proprietário de um grupo, para rever a necessidade de um hóspede continuar a aderir em um grupo.

1. Para criar uma revisão de acesso para o grupo, selecione a revisão para incluir apenas os membros dos utilizadores convidados. Em seguida, especifique um ou mais revisores. Para mais informações, consulte [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md).

2. Peça aos revisores para darem o seu parecer. Por predefinição, cada um recebe um e-mail da Azure AD com um link para o painel de acesso, onde analisam o [acesso a grupos ou aplicações.](perform-access-review.md)

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para mais informações, consulte [Complete uma revisão de acesso de grupos ou aplicações](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Peça aos hóspedes que revejam o seu próprio acesso a uma aplicação

Pode utilizar avaliações de acesso para garantir que os utilizadores convidados para uma determinada aplicação continuem a precisar de acesso. Você pode facilmente pedir aos próprios hóspedes para rever a sua própria necessidade de acesso.

1. Para criar uma revisão de acesso para a aplicação, selecione a revisão para incluir apenas os hóspedes e que os utilizadores revejam o seu próprio acesso. Para mais informações, consulte [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md).

2. Peça a cada hóspede que reveja o seu próprio acesso à aplicação. Por predefinição, cada hóspede que aceitasse um convite recebe um e-mail da Azure AD. Esse e-mail tem um link para a revisão de acesso no painel de acesso da sua organização. A Azure AD tem instruções para os hóspedes sobre como [rever o acesso a grupos ou aplicações](perform-access-review.md).

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para mais informações, consulte [Complete uma revisão de acesso de grupos ou aplicações](complete-access-review.md).

4. Além dos utilizadores que negaram a sua própria necessidade de acesso continuado, também pode remover os utilizadores convidados que não responderam. Os utilizadores que não respondem potencialmente já não recebem e-mail. Também pode remover utilizadores convidados que não foram selecionados para participar, especialmente se não foram convidados recentemente. Estes utilizadores não aceitaram o convite e por isso não tiveram acesso à aplicação. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Peça a um patrocinador para rever o acesso de um hóspede a uma aplicação

Pode pedir a um patrocinador, como o proprietário de uma aplicação, que reveja a necessidade de acesso continuado do hóspede à aplicação.

1. Para criar uma revisão de acesso para a aplicação, selecione a revisão para incluir apenas os hóspedes. Em seguida, especifique um ou mais utilizadores como revisores. Para mais informações, consulte [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md).

2. Peça aos revisores para darem o seu parecer. Por predefinição, cada um recebe um e-mail da Azure AD com um link para o painel de acesso, onde analisam o [acesso a grupos ou aplicações.](perform-access-review.md)

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para mais informações, consulte [Complete uma revisão de acesso de grupos ou aplicações](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Peça aos hóspedes que revejam a sua necessidade de acesso, em geral

Em algumas organizações, os hóspedes podem não estar cientes dos seus membros em grupo.

> [!NOTE]
> As versões anteriores do portal Azure não permitiram o acesso administrativo por utilizadores com o UserType of Guest. Em alguns casos, um administrador no seu diretório pode ter alterado o valor do UserType de um hóspede para Membro utilizando o PowerShell. Se esta alteração ocorreu anteriormente no seu diretório, a consulta anterior pode não incluir todos os utilizadores convidados que historicamente tinham direitos de acesso administrativo. Neste caso, tem de alterar o UserType do hóspede ou incluir manualmente o hóspede na adesão ao grupo.

1. Crie um grupo de segurança em Azure AD com os hóspedes como membros, se um grupo adequado já não existir. Por exemplo, você pode criar um grupo com uma adesão manualmente mantida de hóspedes. Ou, pode criar um grupo dinâmico com um nome como "Convidados de Contoso" para utilizadores do inquilino Contoso que tenham o valor de atributo userType do Guest.  Para obter eficiência, certifique-se de que o grupo é predominantemente hóspedes - não selecione um grupo que tenha utilizadores membros, uma vez que os utilizadores membros não precisam de ser revistos.  Além disso, lembre-se que um utilizador convidado que seja membro do grupo pode ver os outros membros do grupo.

2. Para criar uma revisão de acesso para esse grupo, selecione os próprios revisores para serem os próprios membros. Para mais informações, consulte [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md).

3. Peça a cada hóspede para rever a sua própria filiação. Por predefinição, cada hóspede que aceitou um convite recebe um e-mail da Azure AD com um link para a revisão de acesso no painel de acesso da sua organização. A Azure AD tem instruções para os hóspedes sobre como [rever o acesso a grupos ou aplicações](perform-access-review.md).  Os convidados que não aceitaram o convite vão aparecer nos resultados da revisão como "Não Notificados".

4. Depois de os revisores darem entrada, pare a revisão de acesso. Para mais informações, consulte [Complete uma revisão de acesso de grupos ou aplicações](complete-access-review.md).

5. Remova o acesso dos hóspedes a hóspedes que foram negados, não completaram a revisão, ou não aceitaram previamente o seu convite. Se alguns dos hóspedes forem contactos selecionados para participar na revisão ou não aceitarem previamente um convite, pode desativar as suas contas utilizando o portal Azure ou powerShell. Se o hóspede já não necessitar de acesso e não for um contacto, pode remover o seu objeto de utilizador do seu diretório utilizando o portal Azure ou powerShell para eliminar o objeto de utilizador convidado.

## <a name="next-steps"></a>Passos seguintes

[Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)








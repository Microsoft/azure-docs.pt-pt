---
title: Gerir o acesso de convidado com revisões de acesso do Azure AD | Documentos da Microsoft
description: Gerir utilizadores convidados como membros de um grupo ou atribuídos a uma aplicação com revisões de acesso do Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/13/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bed8a5d20b50fd90922730c958cb1a36933776f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881049"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gerir o acesso de convidado com revisões de acesso do Azure AD


Com o Azure Active Directory (Azure AD), pode facilmente ativar colaboração entre fronteiras organizacionais com o [funcionalidade do Azure AD B2B](../b2b/what-is-b2b.md). Os utilizadores convidados de outros inquilinos podem ser [convidados por administradores](../b2b/add-users-administrator.md) ou pelo [outros utilizadores](../b2b/what-is-b2b.md). Esta capacidade também se aplica a identidades sociais, como contas Microsoft.

Também pode facilmente garantir que os utilizadores convidados têm acesso adequado. É possível pedir os convidados próprios ou um decisor para participar de uma revisão de acesso e voltar a certificar (ou provam) para o acesso dos convidados. Os revisores podem dar o seu parecer relativamente à necessidade de acesso contínuo de cada utilizador, com base nas sugestões do Azure AD. Quando uma revisão de acesso estiver concluída, pode, em seguida, fazer alterações e remover o acesso para os convidados que já não precisam dele.

> [!NOTE]
> Este documento se concentra na revisão de acesso dos utilizadores convidados. Se pretender rever o acesso de todos os utilizadores, não apenas convidados, consulte [gerir o acesso de utilizador com as revisões de acesso](manage-user-access-with-access-reviews.md). Se quiser rever a associação do utilizador a funções administrativas, como administrador global, veja [iniciar uma revisão de acesso no Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Pré-requisitos 


As revisões de acesso estão disponíveis com a edição Premium P2 do Azure AD, que está incluída no Microsoft Enterprise Mobility + Security, E5. Para obter mais informações, consulte [Edições do Azure Active Directory](../fundamentals/active-directory-whatis.md). Cada utilizador que interage com esta funcionalidade, incluindo para criar uma revisão, preencher uma revisão ou confirmar o acesso, precisa de uma licença. 

Também pode fazer os utilizadores convidados para rever o seu próprio acesso. Para cada licença paga do Azure AD Premium P2 que são atribuídas a um dos utilizadores da sua organização, pode usar B2B convidar até cinco utilizadores convidados sob a permissão de utilizador externo. Estes utilizadores convidados também podem utilizar as funcionalidades do Azure AD Premium P2. Para obter mais informações, consulte [licenciamento de colaboração B2B do Azure AD](../b2b/licensing-guidance.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Criar e executar uma revisão de acesso para convidados

Em primeiro lugar, como um administrador global ou administrador de utilizadores, vá para o [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) para garantir que as revisões de acesso está pronto para a sua organização.

O Azure AD permite vários cenários para rever os utilizadores convidados.

Pode rever o:

 - Um grupo no Azure AD que tenha um ou mais convidados como membros.
 - Uma aplicação ligada ao Azure AD que tenha um ou mais utilizadores convidados atribuídos ao mesmo. 

Em seguida, pode decidir se perguntar a cada convidada para rever o seu próprio acesso ou fazer um ou mais utilizadores para rever o acesso de todos os convidados.

 Estes cenários são abordados nas seções a seguir.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Pedir convidados para rever a sua própria associação num grupo

Pode utilizar as revisões de acesso para garantir que os utilizadores que foram convidados e adicionados a um grupo continuam a precisar de acesso. Pode facilmente pedir convidados para rever a sua própria associação nesse grupo.

1. Para criar uma revisão de acesso para o grupo, selecione a revisão para incluir apenas membros de utilizador de convidado e que os membros da consulta propriamente ditas. Para obter mais informações, consulte [criar uma revisão de acesso de grupos ou aplicações](create-access-review.md).

2. Pedir a cada convidada para rever a sua própria associação. Por predefinição, cada convidado que aceite um convite recebe uma mensagem de e-mail do Azure AD com uma ligação para a revisão de acesso. O Azure AD tem instruções para convidados sobre como [rever o acesso a grupos ou aplicações](perform-access-review.md).

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md).

4. Além dos utilizadores que negado seus próprios precisa de acesso contínuo, também pode remover os utilizadores que não respondem. Os utilizadores a responder não potencialmente já não recebem o e-mail.

5. Se o grupo não é utilizado para gestão de acesso, também pode remover utilizadores que não foram selecionados para participar da revisão porque eles não aceitam o convite. Não abertos ao recebimento podem indicar que o endereço de e-mail do utilizador convidado tinha um erro de digitação. Se um grupo é utilizado como uma lista de distribuição, talvez alguns utilizadores convidados não foram selecionados para participar, pois são objetos de contato.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Faça um patrocinador para rever a associação de um convidado num grupo

Pode pedir um patrocinador, como o proprietário de um grupo, para rever a necessidade de um convidado de associação contínua num grupo.

1. Para criar uma revisão de acesso para o grupo, selecione a revisão para incluir apenas membros de utilizador de convidado. Em seguida, especifique um ou mais revisores. Para obter mais informações, consulte [criar uma revisão de acesso de grupos ou aplicações](create-access-review.md).

2. Peça aos revisores para darem o seu parecer. Por predefinição, cada um deles recebe um e-mail do Azure AD com uma ligação para o painel de acesso, onde eles [rever o acesso a grupos ou aplicações](perform-access-review.md).

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Pedir convidados para rever o seu próprio acesso a uma aplicação

Pode utilizar as revisões de acesso para garantir que os utilizadores que foram convidados para um determinado aplicativo continuam a precisar de acesso. Pode facilmente pedir próprios convidados para rever os seus próprios precisam de acesso.

1. Para criar uma revisão de acesso para a aplicação, selecione a revisão para incluir apenas os convidados e utilizadores rever o seu próprio acesso. Para obter mais informações, consulte [criar uma revisão de acesso de grupos ou aplicações](create-access-review.md).

2. Pedir a cada convidada para rever o seu próprio acesso à aplicação. Por predefinição, cada convidado que aceite um convite, receberá uma mensagem de e-mail do Azure AD. Que e-mail tem uma ligação para a revisão de acesso no painel de acesso da sua organização. O Azure AD tem instruções para convidados sobre como [rever o acesso a grupos ou aplicações](perform-access-review.md).

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md).

4. Além dos utilizadores que negado seus próprios precisa de acesso contínuo, também pode remover utilizadores convidados que não respondem. Os utilizadores a responder não potencialmente já não recebem o e-mail. Também pode remover utilizadores convidados que não foram selecionados para participar, especialmente se eles não foram recentemente convidados. Os utilizadores não aceitam que o convite e, portanto, não tem acesso à aplicação. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Faça um patrocinador para rever o acesso de um convidado a uma aplicação

Pode pedir um patrocinador, como o proprietário de uma aplicação, para rever a necessidade do convidado de acesso contínuo ao aplicativo.

1. Para criar uma revisão de acesso para a aplicação, selecione a revisão para incluir "somente convidados". Em seguida, especifique um ou mais utilizadores como revisores. Para obter mais informações, consulte [criar uma revisão de acesso de grupos ou aplicações](create-access-review.md).

2. Peça aos revisores para darem o seu parecer. Por predefinição, cada um deles recebe um e-mail do Azure AD com uma ligação para o painel de acesso, onde eles [rever o acesso a grupos ou aplicações](perform-access-review.md).

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Pedir convidados para rever a sua necessidade de acesso, em geral

Em algumas organizações, convidados não podem estar atento a suas associações de grupo.

> [!NOTE]
> Versões anteriores do portal do Azure não permitem o acesso administrativo por usuários com UserType do convidado. Em alguns casos, um administrador no seu diretório poderá alterou UserType valor um convidado para o membro com o PowerShell. Se esta alteração ocorreu anteriormente no seu diretório, a consulta anterior pode não incluir todos os utilizadores convidados que historicamente tiveram direitos de acesso administrativo. Neste caso, terá de alterar UserType o convidado ou incluir manualmente o convidado na associação ao grupo.

1. Crie um grupo de segurança no Azure AD com convidados como membros, se ainda não existir um grupo adequado. Por exemplo, pode criar um grupo com uma adesão manter manualmente de convidados. Em alternativa, pode criar um grupo dinâmico com um nome como "Convidados de Contoso" para os utilizadores no inquilino Contoso com o valor do atributo UserType do convidado.  Para uma eficiência, certifique-se o grupo de convidados, predominantemente, - não selecionar um grupo com utilizadores de membro, como utilizadores membros não precisam de ser revisto.  Além disso, tenha em atenção que um utilizador convidado que é um membro do grupo pode ver os outros membros do grupo.

2. Para criar uma revisão de acesso desse grupo, selecione os revisores de ser membros próprios. Para obter mais informações, consulte [criar uma revisão de acesso de grupos ou aplicações](create-access-review.md).

3. Pedir a cada convidada para rever a sua própria associação. Por predefinição, cada convidado que aceite um convite recebe uma mensagem de e-mail do Azure AD com uma ligação para a revisão de acesso no painel de acesso da sua organização. O Azure AD tem instruções para convidados sobre como [rever o acesso a grupos ou aplicações](perform-access-review.md).  Desses convidados que não aceitam o convite serão apresentada nos resultados da revisão como "Não notificado".

4. Depois dos revisores dar parecer, pare a revisão de acesso. Para obter mais informações, consulte [concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md).

5. Remova o acesso de convidado para os convidados que foram negados, não foi concluída a revisão ou anteriormente não aceitam o convite. Se algumas das convidadas são contactos que foram selecionados para participar da revisão ou anteriormente não aceitarem um convite, pode desativar as respetivas contas através do portal do Azure ou o PowerShell. Se o convidado já não precisa de acesso e não é um contato, pode remover seus objetos de utilizador do diretório ao utilizar o portal do Azure ou o PowerShell para eliminar o objeto de utilizador convidado.

## <a name="next-steps"></a>Passos Seguintes

[Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)








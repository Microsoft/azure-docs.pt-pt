---
title: Gerenciar o acesso de convidado com revisões de acesso-Azure AD
description: Gerenciar usuários convidados como membros de um grupo ou atribuídos a um aplicativo com Azure Active Directory revisões de acesso
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
ms.openlocfilehash: 562a94bbbd1999b9ca9cf0c65cdb816848648133
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422477"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gerenciar o acesso de convidado com revisões de acesso do Azure AD


Com o Azure Active Directory (AD do Azure), você pode facilmente habilitar a colaboração entre limites organizacionais usando o [recurso B2B do Azure ad](../b2b/what-is-b2b.md). Os usuários convidados de outros locatários podem ser [convidados por administradores](../b2b/add-users-administrator.md) ou por [outros usuários](../b2b/what-is-b2b.md). Esse recurso também se aplica a identidades sociais, como contas da Microsoft.

Você também pode garantir facilmente que os usuários convidados tenham acesso apropriado. Você pode pedir que os próprios convidados ou um tomador de decisão participem de uma revisão de acesso e recertificem (ou atestam) o acesso dos convidados. Os revisores podem dar o seu parecer relativamente à necessidade de acesso contínuo de cada utilizador, com base nas sugestões do Azure AD. Quando uma revisão de acesso é concluída, você pode fazer alterações e remover o acesso para convidados que não precisam mais dela.

> [!NOTE]
> Este documento se concentra na revisão do acesso de usuários convidados. Se você quiser examinar o acesso de todos os usuários, não apenas convidados, consulte [gerenciar o acesso do usuário com revisões de acesso](manage-user-access-with-access-reviews.md). Se você quiser revisar a Associação dos usuários em funções administrativas, como administrador global, consulte [iniciar uma revisão de acesso no Azure ad Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2

Para obter mais informações, consulte [quais usuários devem ter licenças?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-and-perform-an-access-review-for-guests"></a>Criar e executar uma revisão de acesso para convidados

Primeiro, como administrador global ou administrador de usuários, acesse a [página de governança de identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) para garantir que as revisões de acesso estejam prontas para sua organização.

O Azure AD permite vários cenários para revisar os usuários convidados.

Você pode revisar:

 - Um grupo no Azure AD que tem um ou mais convidados como membros.
 - Um aplicativo conectado ao Azure AD que tem um ou mais usuários convidados atribuídos a ele. 

Em seguida, você pode decidir se deseja solicitar que cada convidado revise seu próprio acesso ou peça a um ou mais usuários para examinar o acesso de todos os convidados.

 Esses cenários são abordados nas seções a seguir.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Pedir que os convidados revisem suas próprias associações em um grupo

Você pode usar as revisões de acesso para garantir que os usuários que foram convidados e adicionados a um grupo continuem a precisar de acesso. Você pode pedir que os convidados revisem suas próprias associações nesse grupo.

1. Para criar uma revisão de acesso para o grupo, selecione a revisão para incluir somente membros do usuário convidado e os membros se revisam. Para obter mais informações, consulte [criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md).

2. Peça a cada convidado que revise sua própria associação. Por padrão, cada convidado que aceitou um convite recebe um email do Azure AD com um link para a revisão de acesso. O Azure AD tem instruções para convidados sobre como [examinar o acesso a grupos ou aplicativos](perform-access-review.md).

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md).

4. Além dos usuários que negaram sua própria necessidade de acesso contínuo, você também pode remover usuários que não responderam. Usuários que não respondem potencialmente não recebem mais emails.

5. Se o grupo não for usado para o gerenciamento de acesso, você também poderá remover os usuários que não foram selecionados para participar da análise porque eles não aceitaram o convite. Não aceitar pode indicar que o endereço de email do usuário convidado tinha um retorno de digitação. Se um grupo for usado como uma lista de distribuição, talvez alguns usuários convidados não tenham sido selecionados para participar, pois eles são objetos de contato.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Peça para um patrocinador examinar a associação de um convidado em um grupo

Você pode pedir a um patrocinador, como o proprietário de um grupo, para examinar a necessidade de uma associação contínua de um convidado em um grupo.

1. Para criar uma revisão de acesso para o grupo, selecione a revisão para incluir somente membros do usuário convidado. Em seguida, especifique um ou mais revisores. Para obter mais informações, consulte [criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md).

2. Peça aos revisores para darem o seu parecer. Por padrão, cada um recebe um email do Azure AD com um link para o painel de acesso, no qual eles [revisam o acesso a grupos ou aplicativos](perform-access-review.md).

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Pedir que os convidados revisem seu próprio acesso a um aplicativo

Você pode usar as revisões de acesso para garantir que os usuários que foram convidados para um aplicativo específico continuem a precisar de acesso. Você pode facilmente pedir que os próprios convidados revisem suas próprias necessidades de acesso.

1. Para criar uma revisão de acesso para o aplicativo, selecione a revisão para incluir somente convidados e que os usuários revisam seu próprio acesso. Para obter mais informações, consulte [criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md).

2. Peça a cada convidado que revise seu próprio acesso ao aplicativo. Por padrão, cada convidado que aceitou um convite recebe um email do Azure AD. Esse email tem um link para a revisão de acesso no painel de acesso da sua organização. O Azure AD tem instruções para convidados sobre como [examinar o acesso a grupos ou aplicativos](perform-access-review.md).

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md).

4. Além dos usuários que negaram sua própria necessidade de acesso contínuo, você também pode remover usuários convidados que não responderam. Usuários que não respondem potencialmente não recebem mais emails. Você também pode remover usuários convidados que não foram selecionados para participar, especialmente se eles não foram convidados recentemente. Esses usuários não aceitaram seu convite e, portanto, não tinham acesso ao aplicativo. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Peça ao patrocinador para examinar o acesso de um convidado a um aplicativo

Você pode pedir a um patrocinador, como o proprietário de um aplicativo, a revisar a necessidade do convidado para o acesso contínuo ao aplicativo.

1. Para criar uma revisão de acesso para o aplicativo, selecione a revisão para incluir somente convidados. Em seguida, especifique um ou mais usuários como revisores. Para obter mais informações, consulte [criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md).

2. Peça aos revisores para darem o seu parecer. Por padrão, cada um recebe um email do Azure AD com um link para o painel de acesso, no qual eles [revisam o acesso a grupos ou aplicativos](perform-access-review.md).

3. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Peça que os convidados revisem sua necessidade de acesso, em geral

Em algumas organizações, os convidados podem não estar cientes de suas associações de grupo.

> [!NOTE]
> As versões anteriores do portal do Azure não permitiam acesso administrativo por usuários com o UserType de Guest. Em alguns casos, um administrador em seu diretório pode ter alterado o valor UserType de um convidado para membro usando o PowerShell. Se essa alteração ocorreu anteriormente no seu diretório, a consulta anterior pode não incluir todos os usuários convidados que historicamente tinham direitos de acesso administrativo. Nesse caso, você precisa alterar o UserType do convidado ou incluir manualmente o convidado na associação do grupo.

1. Crie um grupo de segurança no Azure AD com os convidados como membros, se um grupo adequado ainda não existir. Por exemplo, você pode criar um grupo com uma associação de convidados mantida manualmente. Ou, você pode criar um grupo dinâmico com um nome como "convidados da Contoso" para usuários no locatário da Contoso que têm o valor de atributo UserType de convidado.  Para obter eficiência, verifique se o grupo é convidados predominantemente – não selecione um grupo que tenha usuários Membros, pois os usuários Membros não precisam ser revisados.  Além disso, tenha em mente que um usuário convidado que é membro do grupo pode ver os outros membros do grupo.

2. Para criar uma revisão de acesso para esse grupo, selecione os revisores como os próprios membros. Para obter mais informações, consulte [criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md).

3. Peça a cada convidado que revise sua própria associação. Por padrão, cada convidado que aceitou um convite recebe um email do Azure AD com um link para a revisão de acesso no painel de acesso da sua organização. O Azure AD tem instruções para convidados sobre como [examinar o acesso a grupos ou aplicativos](perform-access-review.md).  Os convidados que não aceitaram seu convite aparecerão nos resultados da revisão como "não notificado".

4. Depois que os revisores fornecerem a entrada, pare a revisão de acesso. Para obter mais informações, consulte [concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md).

5. Remova o acesso de convidado para convidados que foram negados, não concluiu a revisão ou não aceitaram anteriormente o convite. Se alguns dos convidados forem contatos que foram selecionados para participar da revisão ou não aceitaram um convite anteriormente, você poderá desabilitar suas contas usando o portal do Azure ou o PowerShell. Se o convidado não precisar mais de acesso e não for um contato, você poderá remover seu objeto de usuário do diretório usando o portal do Azure ou o PowerShell para excluir o objeto de usuário convidado.

## <a name="next-steps"></a>Passos seguintes

[Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)








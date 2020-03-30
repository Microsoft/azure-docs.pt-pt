---
title: Gerir app & acesso a recursos utilizando grupos - Azure AD
description: Saiba como gerir o acesso às aplicações baseadas na nuvem da sua organização, aplicações no local e recursos utilizando grupos de Diretórios Ativos Do Azure.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 961444e15ae1c45db1fc7423a6ac3cc96cc7b3fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768015"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Gerir o acesso a aplicações e recursos com grupos do Azure Active Directory
O Azure Ative Directory (Azure AD) permite-lhe utilizar grupos para gerir o acesso às suas aplicações baseadas na nuvem, aplicações no local e aos seus recursos. Os seus recursos podem fazer parte da organização Azure AD, tais como permissões para gerir objetos através de papéis em Azure AD, ou externos à organização, tais como para aplicações de Software como serviço (SaaS), serviços Azure, sites SharePoint e recursos no local.

>[!NOTE]
>Para utilizar o Azure Active Directory, precisa de uma conta do Azure. Se não tiver uma conta, pode [inscrever-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).
>
> No portal Azure, pode ver alguns grupos cujaa adesão e detalhes do grupo não consegue gerir no portal:
>
> - Os grupos sincronizados a partir do local O Diretório Ativo só pode ser gerido no local do Diretório Ativo.
> - Outros tipos de grupo, como listas de distribuição e grupos de segurança ativados por correio, são geridos apenas no centro de administração do Exchange ou no centro de administração da Microsoft 365. Deve inscrever-se no Exchange admin center ou no Microsoft 365 admin center para gerir estes grupos.

## <a name="how-access-management-in-azure-ad-works"></a>Como funciona a gestão de acesso saquea da AD Azure

A Azure AD ajuda-o a dar acesso aos recursos da sua organização, fornecendo direitos de acesso a um único utilizador ou a todo um grupo de Anúncios Azure. A utilização de grupos permite que o proprietário do recurso (ou o proprietário do diretório do Azure AD) atribua um conjunto de permissões de acesso a todos os membros do grupo, em vez de ter de fornecer os direitos um a um. O proprietário de recursos ou diretórios também pode dar direitos de gestão para a lista de membros a outra pessoa, como um gestor de departamento ou um administrador de Helpdesk, permitindo que essa pessoa adicione e remova os membros, conforme necessário. Para obter mais informações sobre como gerir os proprietários do grupo, consulte [Gerir os proprietários do grupo](active-directory-accessmanagement-managing-group-owners.md)

![Diagrama da gestão de acesso no Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Formas de atribuir direitos de acesso

Existem quatro formas de atribuir direitos de acesso aos recursos aos seus utilizadores:

- **Atribuição direta.** O proprietário do recurso atribui diretamente o utilizador ao recurso.

- **Atribuição de grupo.** O proprietário do recurso atribui um grupo De AD Azure ao recurso, o que automaticamente dá a todos os membros do grupo acesso ao recurso. A adesão ao grupo é gerida tanto pelo proprietário do grupo como pelo proprietário do recurso, permitindo que o proprietário adicione ou remova membros do grupo. Para obter mais informações sobre a adição ou remoção de membros do grupo, consulte [Como: Adicionar ou remover um grupo de outro grupo utilizando o portal de Diretório Ativo Azure](active-directory-groups-membership-azure-portal.md). 

- **Atribuição baseada em regras.** O proprietário do recurso cria um grupo e usa uma regra para definir quais os utilizadores que são atribuídos a um recurso específico. A regra baseia-se em atributos atribuídos a utilizadores individuais. O proprietário do recurso gere a regra, determinando quais os atributos e valores necessários para permitir o acesso ao recurso. Para mais informações, consulte [Criar um grupo dinâmico e verificar o estado](../users-groups-roles/groups-create-rule.md).

    Também pode ver este pequeno vídeo para uma explicação rápida sobre a criação e utilização de grupos dinâmicos:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Atribuição de autoridade externa.** O acesso provém de uma fonte externa, como um diretório no local ou uma app SaaS. Nesta situação, o proprietário do recurso atribui a um grupo o acesso ao recurso e, em seguida, a fonte externa gere os membros do grupo.

   ![Descrição geral do diagrama de gestão de acesso](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Os utilizadores podem juntar-se a grupos sem serem atribuídos?
O proprietário do grupo pode permitir que os utilizadores encontrem os seus próprios grupos para se juntarem, em vez de os atribuirem. O proprietário também pode configurar o grupo para aceitar automaticamente todos os utilizadores que aderirem ou exigir aprovação.

Depois de um utilizador pedir para se juntar a um grupo, o pedido é encaminhado para o proprietário do grupo. Se for necessário, o proprietário pode aprovar o pedido e o utilizador é notificado da adesão ao grupo. No entanto, se tiver vários proprietários e um deles não aprovar, o utilizador é notificado, mas não é adicionado ao grupo. Para mais informações e instruções sobre como permitir que os seus utilizadores peçam para se juntarem a grupos, consulte [AD Azure para que os utilizadores possam solicitar a adesão a grupos](../users-groups-roles/groups-self-service-management.md)

## <a name="next-steps"></a>Passos seguintes
Agora que tem um pouco de introdução à gestão de acesso usando grupos, começa a gerir os seus recursos e aplicações.

- [Crie um novo grupo utilizando o Azure Ative Directory](active-directory-groups-create-azure-portal.md) ou [Crie e gereça um novo grupo utilizando cmdlets PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Utilizar grupos para atribuir acesso a uma aplicação SaaS integrada](../users-groups-roles/groups-saasapps.md)

- [Sync um grupo no local para Azure usando Azure AD Connect](../hybrid/whatis-hybrid-identity.md)

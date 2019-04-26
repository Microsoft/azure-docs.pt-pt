---
title: Adicionar ou remover os proprietários do grupo - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como adicionar ou remover do grupo proprietários com o Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2541a1d76b56f92b250fb422951769db7877213e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60249852"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Adicionar ou remover os proprietários do grupo no Azure Active Directory
Os grupos do Active Directory (Azure AD) do Azure são detidos e geridos pelo proprietários do grupo. Os proprietários do grupo são atribuídos para gerir um grupo e seus membros por um proprietário do recurso (administrador). Os proprietários do grupo não têm de ser membros do grupo. Depois que foi atribuído a um proprietário do grupo, apenas um proprietário do recurso pode adicionar ou remover proprietários.

Em alguns casos,, como o administrador pode decidir não atribuir um proprietário do grupo. Neste caso, se torna o proprietário do grupo. Além disso, os proprietários podem atribuir outros proprietários ao respetivo grupo, a menos que já restrito isso nas definições de grupo.

## <a name="add-an-owner-to-a-group"></a>Adicionar proprietário a um grupo
Adicione proprietários do grupo adicionais a um grupo com o Azure AD.

### <a name="to-add-a-group-owner"></a>Para adicionar um proprietário do grupo
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**, selecione **grupos**e, em seguida, selecione o grupo para o qual pretende adicionar um proprietário (neste exemplo, *política da MDM - oeste*).

3. Sobre o **política MDM - descrição geral do Oeste** página, selecione **proprietários**.

    ![Política MDM - página de descrição geral do oeste com a opção de proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na **MDM política - oeste - proprietários** página, selecione **adicionar proprietários**e, em seguida, procure e selecione o utilizador que será o novo proprietário de grupo e, em seguida, escolha **selecionar**.

    ![Página de proprietários de - oeste - política MDM com a opção de proprietários de adicionar realçada](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Depois de selecionar o novo proprietário, pode atualizar o **proprietários** página e ver o nome adicionado à lista de proprietários.

## <a name="remove-an-owner-from-a-group"></a>Remover um proprietário de um grupo
Remova um proprietário de um grupo com o Azure AD.

### <a name="to-remove-an-owner"></a>Para remover um proprietário
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**, selecione **grupos**e, em seguida, selecione o grupo para o qual pretende remover um proprietário (neste exemplo, *política da MDM - oeste*).

3. Sobre o **política MDM - descrição geral do Oeste** página, selecione **proprietários**.

    ![Política MDM - página de descrição geral do oeste com a opção de proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na **MDM política - oeste - proprietários** página, selecione o utilizador que pretende remover como proprietário de um grupo, escolha **remover** na página de informações do usuário e selecione **Sim** para confirmar sua decisão.

    ![Página de informações do utilizador com a opção de remover realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Depois de remover o proprietário, pode retornar para o **proprietários** página e ver o nome foi removido da lista de proprietários.

## <a name="next-steps"></a>Passos Seguintes
- [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)

- [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-cmdlets.md)

- [Utilizar grupos para atribuir acesso a uma aplicação SaaS integrada](../users-groups-roles/groups-saasapps.md)

- [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-v2-cmdlets.md)

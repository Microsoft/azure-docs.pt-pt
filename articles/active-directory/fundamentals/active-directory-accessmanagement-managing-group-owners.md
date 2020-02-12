---
title: Adicionar ou remover os proprietários do grupo - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como adicionar ou remover do grupo proprietários com o Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83a48ad13fe4ae217319b2aa85adf976aa6a7ae
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149852"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Adicionar ou remover os proprietários do grupo no Azure Active Directory
Os grupos do Active Directory (Azure AD) do Azure são detidos e geridos pelo proprietários do grupo. Os proprietários de grupos podem ser utilizadores ou diretores de serviço, e são capazes de gerir o grupo, incluindo a adesão. Apenas os proprietários de grupos existentes ou administradores de gestão de grupos podem atribuir aos proprietários do grupo. Os proprietários do grupo não têm de ser membros do grupo.

Quando um grupo não tem proprietário, os administradores de gestão de grupos ainda são capazes de gerir o grupo. É aconselhável que cada grupo tenha pelo menos um proprietário. Uma vez que os proprietários são assgined para um grupo, o último proprietário do grupo não pode ser removido. Por favor, certifique-se de selecionar outro proprietário antes de retirar o último proprietário do grupo.

## <a name="add-an-owner-to-a-group"></a>Adicionar proprietário a um grupo
Abaixo estão as instruções para adicionar um utilizador como proprietário a um grupo que utiliza o portal Azure AD. Para adicionar um diretor de serviço como proprietário de um grupo, siga as instruções para o fazer utilizando o [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Para adicionar um proprietário do grupo
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**, selecione **Groups,** e, em seguida, selecione o grupo para o qual pretende adicionar um proprietário (por exemplo, *política de MDM - West*).

3. Na política do MDM - página **West Overview,** selecione **Owners**.

    ![Política MDM - página de descrição geral do oeste com a opção de proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na **política do MDM - página West - Proprietários,** selecione **Adicionar proprietários,** e depois procurar e selecionar o utilizador que será o novo proprietário do grupo, e depois escolher **Select**.

    ![Página de proprietários de - oeste - política MDM com a opção de proprietários de adicionar realçada](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Depois de selecionar o novo proprietário, pode refrescar a página **Owners** e ver o nome adicionado à lista de proprietários.

## <a name="remove-an-owner-from-a-group"></a>Remover um proprietário de um grupo
Remova um proprietário de um grupo com o Azure AD.

### <a name="to-remove-an-owner"></a>Para remover um proprietário
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**, selecione **Groups,** e, em seguida, selecione o grupo para o qual pretende remover um proprietário (por exemplo, *política de MDM - West*).

3. Na política do MDM - página **West Overview,** selecione **Owners**.

    ![Política MDM - página de descrição geral do oeste com a opção de proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na **política do MDM - Página West - Proprietários,** selecione o utilizador que pretende remover como proprietário de grupo, escolha **Remover** da página de informação do utilizador e selecione **Sim** para confirmar a sua decisão.

    ![Página de informações do utilizador com a opção de remover realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Depois de remover o proprietário, pode voltar à página **Proprietários** e ver se o nome foi removido da lista de proprietários.

## <a name="next-steps"></a>Passos seguintes
- [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)

- [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-cmdlets.md)

- [Utilizar grupos para atribuir acesso a uma aplicação SaaS integrada](../users-groups-roles/groups-saasapps.md)

- [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-v2-cmdlets.md)

---
title: Adicione ou remova os proprietários do grupo - Azure Ative Directory | Microsoft Docs
description: Instruções sobre como adicionar ou remover os proprietários do grupo utilizando o Diretório Azure Ative.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b255f64547c3bae56d31415dc94a751989ca1f45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97504904"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Adicionar ou remover os proprietários do grupo no Azure Ative Directory
Os grupos Azure Ative Directory (Azure AD) são propriedade e geridos pelos proprietários do grupo. Os proprietários do grupo podem ser utilizadores ou diretores de serviço, e são capazes de gerir o grupo, incluindo a adesão. Apenas os proprietários de grupos existentes ou administradores de gestão de grupos podem atribuir aos proprietários do grupo. Os donos do grupo não são obrigados a ser membros do grupo.

Quando um grupo não tem proprietário, os administradores de gestão de grupos ainda são capazes de gerir o grupo. É recomendado para cada grupo ter pelo menos um proprietário. Uma vez que os proprietários são atribuídos a um grupo, o último proprietário do grupo não pode ser removido. Por favor, certifique-se de selecionar outro proprietário antes de retirar o último proprietário do grupo.

## <a name="add-an-owner-to-a-group"></a>Adicione um proprietário a um grupo
Abaixo estão as instruções para adicionar um utilizador como proprietário a um grupo que utiliza o portal AD AZure. Para adicionar um titular de serviço como proprietário de um grupo, siga as instruções para o fazer utilizando [o PowerShell](/powershell/module/Azuread/Add-AzureADGroupOwner).

### <a name="to-add-a-group-owner"></a>Para adicionar um dono do grupo
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**, selecione **Groups**, e, em seguida, selecione o grupo para o qual pretende adicionar um proprietário (para este exemplo, *política de MDM - Oeste*).

3. Na política do **MDM - Página geral do Oeste,** selecione **Proprietários**.

    ![Política do MDM - Página de visão geral do Oeste com opção de proprietários em destaque](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na política do **MDM - Página De Proprietários,** selecione Adicionar os **proprietários,** e, em seguida, procure e selecione o utilizador que será o novo proprietário do grupo e, em seguida, escolha **Select**.

    ![Política do MDM - West - Página de proprietários com opção adicionar proprietários em destaque](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Depois de selecionar o novo proprietário, pode atualizar a página **Proprietários** e ver o nome adicionado à lista de proprietários.

## <a name="remove-an-owner-from-a-group"></a>Remova um dono de um grupo
Remova um proprietário de um grupo que utilize a Azure AD.

### <a name="to-remove-an-owner"></a>Para remover um proprietário
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**, selecione **Groups**, e, em seguida, selecione o grupo para o qual pretende remover um proprietário (para este exemplo, *política de MDM - Oeste*).

3. Na política do **MDM - Página geral do Oeste,** selecione **Proprietários**.

    ![Política do MDM - Página geral do Oeste com a opção Remove Owners em destaque](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na política do **MDM - Página De Proprietários,** selecione o utilizador que pretende remover como proprietário de um grupo, escolha **Remover** na página de informações do utilizador e selecione **Sim** para confirmar a sua decisão.

    ![Página de informação do utilizador com opção Remover realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Depois de remover o proprietário, pode voltar à página **dos Proprietários** e ver se o nome foi removido da lista de proprietários.

## <a name="next-steps"></a>Passos seguintes
- [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)

- [Cmdlets do Azure Active Directory para configurar definições de grupo](../enterprise-users/groups-settings-cmdlets.md)

- [Utilize grupos para atribuir acesso a uma aplicação Integrada SaaS](../enterprise-users/groups-saasapps.md)

- [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Cmdlets do Azure Active Directory para configurar definições de grupo](../enterprise-users/groups-settings-v2-cmdlets.md)

---
title: Atribuir ou remover licenças - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como atribuir ou remover licenças do Azure Active Directory, os usuários ou grupos.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e7a3f80067adb3093bd27e34a45b3afd72b4993
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286861"
---
# <a name="assign-or-remove-licenses-using-the-azure-active-directory-portal"></a>Atribuir ou remover licenças através do portal do Azure Active Directory
Muitos serviços do Azure Active Directory (Azure AD) requerem para ativar um produto do Azure AD e para cada um dos seus utilizadores ou grupos (e os membros associados) de licença do produto em questão. Apenas os utilizadores com licenças ativas poderão aceder e utilizar o licenciada serviços do Azure AD.

## <a name="available-product-editions"></a>Edições do produto disponíveis
Há várias edições disponíveis para o produto do Azure AD.

- Azure AD Gratuito

- Azure AD Básico

- O Azure AD Premium 1 (Azure AD P1)

- O Azure AD Premium 2 (Azure AD P2)

Para obter informações específicas sobre cada edição do produto e os detalhes de licenciamento associados, consulte [de que licença é necessário?](../authentication/concept-sspr-licensing.md).

## <a name="view-your-product-edition-and-license-details"></a>Ver os detalhes de edição e a licença de produto
Pode ver os seus produtos disponíveis, incluindo as licenças individuais, verificar a existência de quaisquer datas de expiração pendente e o número de atribuições disponíveis.

### <a name="to-find-your-product-and-license-details"></a>Para encontrar os detalhes da sua licença e de produto
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**e, em seguida, selecione **licenças**.

    O **licenças** é apresentada a página.

    ![Página de licenças, com o número de produtos adquiridos e licenças atribuídas](media/license-users-groups/license-details-blade.png)
    
3. Selecione o **comprado produtos** link para exibir a **produtos** página e para ver o **atribuído**, **disponível**, e  **Expira em breve** detalhes de cada edição do produto específico.

    ![Página de produtos, com as edições do produto e informações de licenciamento associados](media/license-users-groups/license-products-blade-with-products.png)

4. Selecione um nome de edição de produto para ver os seus utilizadores licenciados e grupos.

## <a name="assign-licenses-to-users-or-groups"></a>Atribuir licenças a utilizadores ou grupos
Certifique-se de que alguém precisar usar um licenciada serviço do Azure AD tem a licença adequada. Cabe a se pretende adicionar os direitos de licenciamento a utilizadores individuais ou a um grupo inteiro.

>[!Note]
>Licenciamento baseado em grupo é uma funcionalidade de pré-visualização pública do Azure AD e disponível com qualquer é pago plano de licenciamento do Azure AD. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).<br><br>Para obter informações detalhadas sobre como adicionar utilizadores, consulte [como adicionar ou eliminar utilizadores no Azure Active Directory](add-users-azure-active-directory.md). Para obter informações detalhadas sobre como criar grupos e adicionar membros, consulte [criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md).

### <a name="to-assign-a-license-to-a-specific-user"></a>Para atribuir uma licença a um utilizador específico
1. Sobre o **produtos** página, selecione o nome da edição que pretende atribuir ao utilizador. Por exemplo, _do Azure Active Directory Premium plano 2_.

    ![Página de produtos, com a edição de produto realçado](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Sobre o **do Azure Active Directory Premium plano 2** página, selecione **atribuir**.

    ![Página de produtos, com a opção de atribuir realçada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Sobre o **atribuir** página, selecione **utilizadores e grupos**e, em seguida, procure e selecione o utilizador está a atribuir a licença. Por exemplo, _Mary Parker_.

    ![Atribuir a página de licença, com pesquisa destacada e selecione as opções](media/license-users-groups/assign-license-blade-with-highlight.png)

4. Selecione **opções de atribuição**, certifique-se de que tem o adequado licenciar opções está ativada e, em seguida, selecione **OK**.

    ![Página de opção de licença, com todas as opções disponíveis na edição](media/license-users-groups/license-option-blade-assignments.png)

    O **atribuir licenças** página atualizações para mostrar que um usuário está selecionado e que as atribuições estão configuradas.

    >[!NOTE]
    >Nem todos os serviços da Microsoft estão disponíveis em todas as localizações. Antes de uma licença pode ser atribuída a um utilizador, tem de especificar o **localização de utilização**. Pode definir este valor **do Azure Active Directory &gt; usuários &gt; perfil &gt; definições** área no Azure AD.

5. Selecione **Atribuir**.

    O utilizador é adicionado à lista de utilizadores licenciados e tem acesso a incluído para serviços do Azure AD.

### <a name="to-assign-a-license-to-an-entire-group"></a>Para atribuir uma licença a um grupo inteiro
1. Sobre o **produtos** página, selecione o nome da edição que pretende atribuir ao utilizador. Por exemplo, _do Azure Active Directory Premium plano 2_.

    ![Painel de produtos, com a edição de produto realçado](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Sobre o **do Azure Active Directory Premium plano 2** página, selecione **atribuir**.

    ![Página de produtos, com a opção de atribuir realçada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Sobre o **atribuir** página, selecione **utilizadores e grupos**e, em seguida, procure e selecione o grupo está a atribuir a licença. Por exemplo, _política MDM - oeste_.

    ![Atribuir a página de licença, com pesquisa destacada e selecione as opções](media/license-users-groups/assign-group-license-blade-with-highlight.png)

4. Selecione **opções de atribuição**, certifique-se de que tem o adequado licenciar opções está ativada e, em seguida, selecione **OK**.

    ![Página de opção de licença, com todas as opções disponíveis na edição](media/license-users-groups/license-option-blade-group-assignments.png)

    O **atribuir licenças** página atualizações para mostrar que um usuário está selecionado e que as atribuições estão configuradas.

    >[!NOTE]
    >Nem todos os serviços da Microsoft estão disponíveis em todas as localizações. Antes de uma licença pode ser atribuída a um grupo, tem de especificar o **localização de utilização** para todos os membros. Pode definir este valor **do Azure Active Directory &gt; usuários &gt; perfil &gt; definições** área no Azure AD. Qualquer utilizador cuja localização de utilização não está especificada herda a localização do inquilino.

5. Selecione **Atribuir**.

    O grupo é adicionado à lista de grupos licenciados e todos os membros têm acesso ao incluído serviços do Azure AD.

## <a name="remove-a-license"></a>Remover uma licença
Pode remover uma licença de um utilizador ou um grupo a partir da **licenças** página.

### <a name="to-remove-a-license-from-a-specific-user"></a>Para remover uma licença de um utilizador específico
1. Sobre o **utilizadores com licença** para a edição de produto, selecione o utilizador que já não deve ter a licença. Por exemplo, _Alain Charon_.

2. Selecione **remover licença**.

    ![Página dos usuários licenciados com a opção de licença de remover realçada](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

### <a name="to-remove-a-license-from-a-group"></a>Para remover uma licença a partir de um grupo
1. Sobre o **licenciado grupos** página para a edição de produto, selecione o grupo que já não deve ter a licença. Por exemplo, _política MDM - oeste_.

2. Selecione **remover licença**.

    ![Página de grupos licenciados com a opção de licença de remover realçada](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

>[!Important]
>Não não possível remover o licenças herdadas por um utilizador de um grupo diretamente. Em vez disso, terá de remover o utilizador do grupo do qual eles estão a herdar a licença.

## <a name="next-steps"></a>Passos Seguintes
Depois de atribuir as licenças, pode efetuar os seguintes processos:

- [Identificar e resolver problemas de atribuição de licença](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Adicionar os utilizadores licenciados a um grupo para licenciamento](../users-groups-roles/licensing-groups-migrate-users.md)

- [Cenários, limitações e problemas conhecidos, utilizar grupos para gerir o licenciamento no Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Adicionar ou alterar as informações de perfil](active-directory-users-profile-azure-portal.md)

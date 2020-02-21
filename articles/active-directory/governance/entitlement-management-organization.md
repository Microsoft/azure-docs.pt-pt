---
title: Adicione uma organização conectada na gestão de direitos da Azure AD - Azure Ative Directory
description: Saiba como permitir que pessoas fora da sua organização solicitem pacotes de acesso para que possa colaborar em projetos.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/22/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ecf3a8819518c674a3d8bd7af55d1a3c6393c42
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483861"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Adicione uma organização conectada na gestão de direitos da Azure AD

A gestão de direitos da Azure AD permite-lhe colaborar com pessoas fora da sua organização. Se colaborar frequentemente com os utilizadores num diretório ou domínio externo do Azure AD, pode adicioná-los como uma organização conectada. Este artigo descreve como adicionar uma organização conectada para que possa permitir que utilizadores fora da sua organização solicitem recursos no seu diretório.

## <a name="what-is-a-connected-organization"></a>O que é uma organização conectada?

Uma organização conectada é um diretório ou domínio externo da AD Azure com o que tem uma relação.

Por exemplo, suponha que você trabalhe no Banco Woodgrove e queira colaborar com duas organizações externas. Essas duas organizações têm configurações diferentes:

- O Graphic Design Institute utiliza o Azure AD e os seus utilizadores têm um nome principal de utilizador que termina com `graphicdesigninstitute.com`
- A contoso ainda não usa o Azure AD. Os utilizadores da Contoso têm um nome principal do utilizador que termina com `contoso.com`.

Nesse caso, você pode configurar duas organizações conectadas. Você deve criar uma organização conectada para o design gráfico Institute e outra para a contoso. Se você adicionar essas duas organizações conectadas a uma política, os usuários de cada organização com um nome principal de usuário correspondente à política poderão solicitar pacotes de acesso. Os usuários com um nome principal de usuário que tem um domínio de graphicdesigninstitute.com corresponderão à organização conectada do design gráfico Institute e terão permissão para enviar solicitações, enquanto os usuários com um nome UPN que tem um domínio de contoso.com seriam correspondentes a organização conectada da Contoso e também teria permissão para solicitar pacotes. Além disso, como o Graphic Design Institute utiliza o Azure AD, quaisquer utilizadores com um nome principal que corresponda a um [domínio verificado](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) adicionado ao seu inquilino, como o graphicdesigninstitute.example também poderão solicitar pacotes de acesso usando a mesma política.

![Exemplo de organização conectada](./media/entitlement-management-organization/connected-organization-example.png)

A forma como os utilizadores do diretório ou domínio azure autenticarão depende do tipo de autenticação. Os tipos de autenticação para organizações ligadas são os seguintes:

- Azure AD
- [Federação direta](../b2b/direct-federation.md)
- [Senha única](../b2b/one-time-passcode.md) (domínio)

Para uma demonstração de como adicionar uma organização conectada, veja o seguinte vídeo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Adicionar uma organização associada

Siga estes passos para adicionar um diretório ou domínio externo do Azure AD como uma organização conectada.

**Papel pré-requisito:** Administrador global, administrador de utilizador ou convidado

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **organizações conectadas** e, em seguida, clique em **Adicionar organização conectada**.

    ![Governança de Identidade - Organizações conectadas - Adicionar organização conectada](./media/entitlement-management-organization/connected-organization.png)

1. No separador **Basics,** introduza um nome de exibição e descrição para a organização.

    ![Adicionar organização conectada - Separador Basics](./media/entitlement-management-organization/organization-basics.png)

1. No separador **De domínio Diretório +** , clique em Adicionar **diretório + domínio** para abrir o painel DeDirecties + domínios Select.

1. Digite um nome de domínio para procurar o diretório ou domínio Azure AD. Deve escrever todo o nome de domínio.

1. Verifique se é a organização correta pelo nome e tipo de autenticação fornecidos. A forma como os utilizadores irão fazer o login depende do tipo de autenticação.

    ![Adicionar organização conectada - Selecionar diretórios + domínios](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Clique em **Adicionar** para adicionar o diretório ou domínio Azure AD. Atualmente, só pode adicionar um diretório de AD Azure ou domínio por organização conectada.

    > [!NOTE]
    > Todos os utilizadores do diretório ou domínio da AD Azure poderão solicitar este pacote de acesso. Isto inclui utilizadores em Azure AD de todos os subdomínios associados ao diretório, a menos que esses domínios sejam bloqueados pela lista de permitir ou negar do Azure B2B. Para mais informações, consulte [Permitir ou bloquear convites a utilizadores B2B de organizações específicas](../b2b/allow-deny-list.md).

1. Depois de ter adicionado o diretório ou domínio Azure AD, clique em **Select**.

    A organização aparece na lista.

    ![Adicionar organização conectada - Separador de diretórios](./media/entitlement-management-organization/organization-directory-domain.png)

1. No separador **Patrocinadores,** adicione patrocinadores opcionais para esta organização conectada.

    Os patrocinadores são utilizadores internos ou externos já no seu diretório que são o ponto de contacto para a relação com esta organização conectada. Patrocinadores internos são utilizadores membros no seu diretório. Patrocinadores externos são utilizadores convidados da organização conectada que foram previamente convidados e já estão no seu diretório. Os patrocinadores podem ser utilizados como aprovadores quando os utilizadores desta organização conectada solicitarem o acesso a este pacote de acesso. Para obter informações sobre como convidar um utilizador convidado para o seu diretório, consulte os utilizadores de colaboração Add [Azure Ative Directory B2B no portal Azure](../b2b/add-users-administrator.md).

    Quando clica em **Adicionar/Remover,** aparece um painel para selecionar os patrocinadores internos ou externos. O painel apresenta uma lista não filtrada de utilizadores e grupos no seu diretório.

    ![Pacote de acesso - Política - Adicionar organização conectada - Separador patrocinadores](./media/entitlement-management-organization/organization-sponsors.png)

1. No **separador Review + criar,** rever as definições da sua organização e, em seguida, clicar em **Criar**.

    ![Pacote de acesso - Política - Adicionar organização conectada - Rever + criar separador](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Atualizar uma organização conectada 

Se a organização conectada mudar para um domínio diferente, se tiver um novo nome para essa organização, ou se quiser alterar os patrocinadores, pode atualizar a organização conectada.

**Papel pré-requisito:** Administrador global, administrador de utilizador ou convidado

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **organizações conectadas** e clique em abrir a organização conectada.

1. Na página 'Overview', clique em **Editar** para alterar o nome ou descrição da organização.  

1. Na página de domínio Do Diretório +, clique em **Atualizar o diretório + domínio** para alterar para um diretório ou domínio diferente.

1. Na página De Patrocinadores, clique em **Adicionar patrocinadores internos** ou **adicionar patrocinadores externos** para adicionar um utilizador como patrocinador.  Para remover um patrocinador, clique no patrocinador e no menu à direita, clique em **Eliminar**.


## <a name="delete-a-connected-organization"></a>Eliminar uma organização conectada

Se já não tiver uma relação com um diretório ou domínio externo da AD, pode eliminar a organização conectada.

**Papel pré-requisito:** Administrador global, administrador de utilizador ou convidado

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **organizações conectadas** e clique em abrir a organização conectada.

1. Na página 'Visão Geral', clique em **Eliminar** para eliminar a organização conectada.

    Atualmente, só é possível eliminar uma organização conectada se não existirem utilizadores conectados.

    ![Governança de Identidade - Organizações Conectadas - Eliminar organização conectada](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Passos Seguintes

- [Governar o acesso a utilizadores externos](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Para utilizadores que não estão no seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)

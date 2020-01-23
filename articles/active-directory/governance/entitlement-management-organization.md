---
title: Adicionar uma organização conectada no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como permitir que as pessoas de fora da sua organização solicitem pacotes de acesso para que você possa colaborar em projetos.
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
ms.openlocfilehash: 0c1b6f5ebffa39d3b735e85df794e37329e3aa2e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548905"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Adicionar uma organização conectada no gerenciamento de direitos do Azure AD

O gerenciamento de direitos do Azure AD permite que você colabore com pessoas de fora da sua organização. Se você colabora com frequência com usuários em um diretório ou domínio externo do Azure AD, você pode adicioná-los como uma organização conectada. Este artigo descreve como adicionar uma organização conectada para que você possa permitir que usuários fora da sua organização solicitem recursos em seu diretório.

## <a name="what-is-a-connected-organization"></a>O que é uma organização conectada?

Uma organização conectada é um diretório ou domínio externo do Azure AD com o qual você tem uma relação.

Por exemplo, suponha que você trabalhe no Banco Woodgrove e queira colaborar com duas organizações externas. Essas duas organizações têm configurações diferentes:

- O design gráfico Institute usa o Azure AD e seus usuários têm um nome principal de usuário que termina com `graphicdesigninstitute.com`
- A contoso ainda não usa o Azure AD. Os usuários da Contoso têm um nome principal de usuário que termina com `contoso.com`.

Nesse caso, você pode configurar duas organizações conectadas. Você deve criar uma organização conectada para o design gráfico Institute e outra para a contoso. Se você adicionar essas duas organizações conectadas a uma política, os usuários de cada organização com um nome principal de usuário correspondente à política poderão solicitar pacotes de acesso. Os usuários com um nome principal de usuário que tem um domínio de graphicdesigninstitute.com corresponderão à organização conectada do design gráfico Institute e terão permissão para enviar solicitações, enquanto os usuários com um nome UPN que tem um domínio de contoso.com seriam correspondentes a organização conectada da Contoso e também teria permissão para solicitar pacotes. Além disso, como o design gráfico Institute usa o Azure AD, qualquer usuário com um nome principal que corresponda a um [domínio verificado](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) adicionado ao seu locatário, como graphicdesigninstitute. example também será capaz de solicitar pacotes de acesso usando a mesma política.

![Exemplo de organização conectada](./media/entitlement-management-organization/connected-organization-example.png)

Como os usuários do diretório ou domínio do Azure AD serão autenticados depende do tipo de autenticação. Os tipos de autenticação para organizações conectadas são os seguintes:

- Azure AD
- [Federação direta](../b2b/direct-federation.md)
- [Senha de uso único](../b2b/one-time-passcode.md) (domínio)

Para ver uma demonstração de como adicionar uma organização conectada, Assista ao vídeo a seguir:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Adicionar uma organização associada

Siga estas etapas para adicionar um diretório ou domínio externo do Azure AD como uma organização conectada.

**Função de pré-requisito:** Administrador global, administrador de usuário ou convite de convidado

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **organizações conectadas** e, em seguida, clique em **Adicionar organização conectada**.

    ![Governança de identidade – organizações conectadas-adicionar organização conectada](./media/entitlement-management-organization/connected-organization.png)

1. Na guia **noções básicas** , insira um nome de exibição e uma descrição para a organização.

    ![Adicionar organização conectada-guia noções básicas](./media/entitlement-management-organization/organization-basics.png)

1. Na guia **diretório + domínio** , clique em **Adicionar diretório + domínio** para abrir o painel Selecionar diretórios + domínios.

1. Digite um nome de domínio para pesquisar o diretório ou domínio do Azure AD. Você deve digitar o nome de domínio inteiro.

1. Verifique se é a organização correta pelo nome e tipo de autenticação fornecidos. Como os usuários entrarão depende do tipo de autenticação.

    ![Adicionar organização conectada-selecionar diretórios + domínios](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Clique em **Adicionar** para adicionar o diretório ou domínio do Azure AD. No momento, você só pode adicionar um diretório ou domínio do Azure AD por organização conectada.

    > [!NOTE]
    > Todos os usuários do diretório ou domínio do Azure AD poderão solicitar esse pacote de acesso. Isso inclui os usuários no Azure AD de todos os subdomínios associados ao diretório, a menos que esses domínios sejam bloqueados pela lista de permissão ou negação B2B do Azure. Para obter mais informações, consulte [permitir ou bloquear convites aos utilizadores B2B de organizações específicos](../b2b/allow-deny-list.md).

1. Depois de adicionar o diretório ou domínio do Azure AD, clique em **selecionar**.

    A organização aparece na lista.

    ![Adicionar organização conectada-guia diretórios](./media/entitlement-management-organization/organization-directory-domain.png)

1. Na guia **patrocinadores** , adicione patrocinadores opcionais para esta organização conectada.

    Os patrocinadores são usuários internos ou externos que já estão em seu diretório que são o ponto de contato para a relação com essa organização conectada. Os patrocinadores internos são usuários Membros em seu diretório. Os patrocinadores externos são usuários convidados da organização conectada que foram convidados anteriormente e já estão em seu diretório. Os patrocinadores podem ser utilizados como aprovadores quando os usuários nesta organização conectada solicitam acesso a esse pacote de acesso. Para obter informações sobre como convidar um usuário convidado para seu diretório, consulte [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](../b2b/add-users-administrator.md).

    Quando você clica em **Adicionar/remover**, um painel é exibido para selecionar os patrocinadores internos ou externos. O painel exibe uma lista não filtrada de usuários e grupos em seu diretório.

    ![Pacote de acesso-política-adicionar organização conectada-guia patrocinadores](./media/entitlement-management-organization/organization-sponsors.png)

1. Na guia **revisar + criar** , examine as configurações da organização e clique em **criar**.

    ![Pacote de acesso-política-adicionar organização conectada-examinar + guia criar](./media/entitlement-management-organization/organization-review-create.png)

## <a name="delete-a-connected-organization"></a>Excluir uma organização conectada

Se você não tiver mais uma relação com um diretório ou domínio do Azure AD externo, poderá excluir a organização conectada.

**Função de pré-requisito:** Administrador global, administrador de usuário ou convite de convidado

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **organizações conectadas** e, em seguida, clique para abrir a organização conectada.

1. Na página Visão geral, clique em **excluir** para excluir a organização conectada.

    No momento, você só poderá excluir uma organização conectada se não houver usuários conectados.

    ![Governança de identidade – organizações conectadas – excluir organização conectada](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Passos seguintes

- [Controlar o acesso para usuários externos](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Para usuários que não estão em seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)

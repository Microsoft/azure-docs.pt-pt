---
title: Adicionar uma organização conectada no gerenciamento de direitos do Azure AD (versão prévia)-Azure Active Directory
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
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23d45db1f62b370a2692ed932572e16c247b8903
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952488"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management-preview"></a>Adicionar uma organização conectada no gerenciamento de direitos do Azure AD (versão prévia)

O gerenciamento de direitos do Azure AD permite que você colabore com pessoas de fora da sua organização. Se você colabora com frequência com usuários em um diretório ou domínio externo do Azure AD, você pode adicioná-los como uma organização conectada. Este artigo descreve como adicionar uma organização conectada para que você possa permitir que usuários fora da sua organização solicitem recursos em seu diretório.

## <a name="what-is-a-connected-organization"></a>O que é uma organização conectada?

Uma organização conectada é um diretório ou domínio externo do Azure AD com o qual você tem uma relação.

Por exemplo, suponha que você trabalhe no Banco Woodgrove e queira colaborar com duas organizações externas: Instituto de design gráfico e contoso. Você foi informado por seu contato no design gráfico Institute que eles usam o Azure AD e que os usuários do Instituto de design gráfico têm um nome principal de usuário que termina com `graphicdesigninstitute.com`. E você foi informado por seu contato na contoso que eles ainda não usam o Azure AD, mas que os usuários da Contoso têm um nome principal de usuário que termina com `contoso.com`.

Você pode configurar duas organizações conectadas, uma para o Instituto de design gráfico com o domínio `graphicdesigninstitute.com`e outra para a contoso com o domínio `contoso.com`. Se você adicionar essas duas organizações conectadas a uma política, os usuários de cada organização que têm um nome principal de usuário que corresponde à política poderão solicitar pacotes de acesso. Além disso, como o Instituto de design gráfico foi identificado como usando o Azure AD, se o Instituto de design gráfico, em seguida, tiver subdomínios, como `graphicdesigninstitute.example`, os usuários com esse nome de entidade de usuário também poderão solicitar pacotes de acesso usando o mesma política.

![Exemplo de organização conectada](./media/entitlement-management-organization/connected-organization-example.png)

Como os usuários do diretório ou domínio do Azure AD serão autenticados depende do tipo de autenticação. Os tipos de autenticação para organizações conectadas são os seguintes:

| Tipo de autenticação | Estado |
| --- | --- |
| Azure AD | Suportadas |
| [Federação direta](../b2b/direct-federation.md) | Pré-visualização |
| [Senha de uso único](../b2b/one-time-passcode.md) (domínio) | Pré-visualização |

## <a name="add-a-connected-organization"></a>Adicionar uma organização conectada

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
    > Todos os usuários do diretório ou domínio do Azure AD poderão solicitar esse pacote de acesso. Isso inclui os usuários no Azure AD de todos os subdomínios associados ao diretório, a menos que esses domínios sejam bloqueados pela lista de permissão ou negação B2B do Azure. Para obter mais informações, consulte [permitir ou bloquear convites para usuários B2B de organizações específicas](../b2b/allow-deny-list.md).

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

- [Controlar o acesso para usuários externos](entitlement-management-organization.md)
- [Para usuários que não estão em seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)

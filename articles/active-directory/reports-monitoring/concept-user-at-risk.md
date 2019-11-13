---
title: Utilizadores sinalizados no relatório de segurança de risco no portal do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre os utilizadores sinalizados no relatório de segurança de risco no portal do Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 894d8dfb7f870ec4a2a11f1d75ee0376b25d8c7f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014455"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Usuários sinalizados para o relatório de risco no portal do Azure

O Azure Active Directory (AD do Azure) detecta ações suspeitas relacionadas às suas contas de usuário. Para cada ação detectada, um registro chamado [detecção de risco](concept-risk-events.md) é criado.

Você pode acessar os relatórios de segurança do [portal do Azure](https://portal.azure.com) selecionando a folha **Azure Active Directory** e, em seguida, navegando até a seção **segurança** . 

As detecções de risco detectadas são usadas para calcular:

- **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. 

- **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. 

Para saber como configurar as políticas que disparam essas detecções de risco, consulte [como configurar a política de risco do usuário](../identity-protection/howto-user-risk-policy.md). 

![Inícios de Sessão de Risco](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Qual licença do Azure AD você precisa para acessar o relatório de risco dos usuários?  

Todas as edições do Azure Active Directory disponibilizam os relatórios de utilizadores sinalizados para risco. No entanto, o nível de granularidade dos relatórios varia entre as edições: 

- Nas **edições Azure Active Directory gratuito e Basic**, você obtém uma lista de usuários sinalizados para risco. 

- Além disso, a edição **Azure Active Directory Premium 1** permite que você examine algumas das detecções de risco subjacentes que foram detectadas para cada relatório. 

- A edição **Azure Active Directory Premium 2** fornece as informações mais detalhadas sobre todas as detecções de riscos subjacentes e também permite que você configure políticas de segurança que respondem automaticamente aos níveis de risco configurados.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Relatório de usuários em risco para as edições gratuita e básica do Azure AD

Os usuários sinalizados para o relatório de risco nas edições gratuita e básica do Azure AD fornecem uma lista de contas de usuário que podem ter sido comprometidas. 

![Inícios de Sessão de Risco](./media/concept-user-at-risk/03.png)

A seleção de um usuário fornece informações de entrada. Para os utilizadores que estão em risco, pode rever o histórico de início de sessão do utilizador e repor a palavra-passe, se necessário.

Esta caixa de diálogo disponibiliza uma opção para:

- Transferir o relatório
- Procurar utilizadores

    ![Inícios de Sessão de Risco](./media/concept-user-at-risk/16.png)

Para obter informações mais detalhadas, você precisa de uma licença Premium.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Relatório de usuários em risco para as edições do Azure AD Premium

Os usuários sinalizados para o relatório de risco nas edições do Azure AD Premium fornecem:

- Uma lista de contas de utilizador que poderão ter sido comprometidas 

- Informações agregadas sobre os [tipos de detecção de risco](concept-risk-events.md) que foram detectados

- Uma opção para transferir o relatório

- Uma opção para configurar uma [política de remediação de risco do utilizador](../identity-protection/howto-user-risk-policy.md)  

![Inícios de Sessão de Risco](./media/concept-user-at-risk/71.png)

Ao selecionar um utilizador, obtém uma vista de relatório detalhado para este utilizador que lhe permite:

- Abrir a vista Todos os inícios de sessão

- Repor a palavra-passe do utilizador

- Dispensar todos os eventos

- Investigue as detecções de risco relatadas para o usuário. 

![Inícios de Sessão de Risco](./media/concept-user-at-risk/324.png)

Para investigar uma detecção de risco, selecione uma na lista para abrir a folha de **detalhes** para essa detecção de risco. Na folha **detalhes** , você tem a opção de fechar manualmente uma detecção de risco ou reativar uma detecção de risco fechada manualmente. 

![Inícios de Sessão de Risco](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Passos seguintes

- [Como configurar a política de risco do usuário](../identity-protection/howto-user-risk-policy.md)
- [Como configurar a política de correção de risco](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)


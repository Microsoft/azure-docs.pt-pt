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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74014455"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Utilizadores sinalizados para relatório de risco no portal Azure

O Azure Ative Directory (Azure AD) deteta ações suspeitas relacionadas com as suas contas de utilizador. Para cada ação detetada, é criado um registo chamado deteção de [risco.](concept-risk-events.md)

Pode aceder aos relatórios de segurança do [portal Azure](https://portal.azure.com) selecionando a lâmina do **Diretório Ativo Azure** e, em seguida, navegando até à secção **de Segurança.** 

As deteções de risco detetadas são utilizadas para calcular:

- **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. 

- **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. 

Para aprender a configurar as políticas que desencadeiam estas deteções de risco, consulte [como configurar a política](../identity-protection/howto-user-risk-policy.md)de risco do utilizador . 

![Inícios de Sessão de Risco](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Que licença da Azure AD precisa para aceder aos utilizadores em relatório de risco?  

Todas as edições do Azure Active Directory disponibilizam os relatórios de utilizadores sinalizados para risco. No entanto, o nível de granularidade dos relatórios varia entre as edições: 

- Nas **edições Azure Ative Directory Free and Basic,** obtém-se uma lista de utilizadores sinalizados para o risco. 

- Além disso, a edição **Azure Ative Directory Premium 1** permite examinar algumas das deteções de risco subjacentes que foram detetadas para cada relatório. 

- A edição **Azure Ative Directory Premium 2** fornece-lhe as informações mais detalhadas sobre todas as deteções de risco subjacentes e também lhe permite configurar políticas de segurança que respondem automaticamente aos níveis de risco configurados.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Utilizadores em relatório de risco para edições gratuitas e básicas do Azure AD

Os utilizadores sinalizados para relatório de risco nas edições gratuitas e básicas do Azure AD fornecem-lhe uma lista de contas de utilizador que podem ter sido comprometidas. 

![Inícios de Sessão de Risco](./media/concept-user-at-risk/03.png)

A seleção de um utilizador fornece informações de sessão. Para os utilizadores que estão em risco, pode rever o histórico de início de sessão do utilizador e repor a palavra-passe, se necessário.

Esta caixa de diálogo disponibiliza uma opção para:

- Transferir o relatório
- Procurar utilizadores

    ![Inícios de Sessão de Risco](./media/concept-user-at-risk/16.png)

Para obter informações mais detalhadas, precisa de uma licença premium.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Utilizadores em risco reportam para edições premium Azure AD

Os utilizadores sinalizados para relatório de risco nas edições premium Azure AD fornecem-lhe:

- Uma lista de contas de utilizador que poderão ter sido comprometidas 

- Informação agregada sobre os tipos de [deteção](concept-risk-events.md) de risco que foram detetados

- Uma opção para transferir o relatório

- Uma opção para configurar uma [política de remediação de risco do utilizador](../identity-protection/howto-user-risk-policy.md)  

![Inícios de Sessão de Risco](./media/concept-user-at-risk/71.png)

Ao selecionar um utilizador, obtém uma vista de relatório detalhado para este utilizador que lhe permite:

- Abrir a vista Todos os inícios de sessão

- Repor a palavra-passe do utilizador

- Dispensar todos os eventos

- Investigue as deteções de risco reportadas para o utilizador. 

![Inícios de Sessão de Risco](./media/concept-user-at-risk/324.png)

Para investigar uma deteção de risco, selecione um da lista para abrir a lâmina **Detalhes** para esta deteção de risco. Na lâmina **Details,** tem a opção de fechar manualmente uma deteção de risco ou reativar uma deteção de risco fechada manualmente. 

![Inícios de Sessão de Risco](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Passos seguintes

- [Como configurar a política de risco do utilizador](../identity-protection/howto-user-risk-policy.md)
- [Como configurar a política de reparação de riscos](../identity-protection/howto-user-risk-policy.md)
- [Proteção de Identidade do Diretório Ativo Azure](../active-directory-identityprotection.md)


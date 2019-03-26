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
ms.openlocfilehash: 463f5c2d03cd96089342aa9b22ef85ebc05aa909
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436732"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Utilizadores sinalizados no relatório de risco no portal do Azure

Azure Active Directory (Azure AD) Deteta as ações suspeitas relacionadas às contas de utilizador. Para cada ação detetada, um registo denominado um [evento de risco](concept-risk-events.md) é criado.

Pode acessar os relatórios de segurança do [portal do Azure](https://portal.azure.com) ao selecionar o **Azure Active Directory** painel e, em seguida, ao navegar para o **segurança** secção. 

Os eventos de risco detetados são utilizados para calcular:

- **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. 

- **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. 

Para saber como configurar as políticas que acionam esses eventos de risco, veja [como configurar a política de risco do utilizador](../identity-protection/howto-user-risk-policy.md). 

![Inícios de Sessão de Risco](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Que licença do Azure AD precisa acessar os utilizadores no relatório de risco?  

Todas as edições do Azure Active Directory disponibilizam os relatórios de utilizadores sinalizados para risco. No entanto, o nível de granularidade dos relatórios varia entre as edições: 

- Na **edições do Azure Active Directory gratuito e Basic**, obtém uma lista de utilizadores sinalizados para risco. 

- Além disso, o **do Azure Active Directory Premium 1** edition permite que examine alguns dos eventos de risco subjacentes que foram detetados para cada relatório. 

- A edição **do Azure Active Directory Premium 2** proporciona-lhe as informações mais detalhadas sobre todos os eventos de risco subjacentes e também lhe permite configurar políticas de segurança que respondam automaticamente aos níveis de risco configurados.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Utilizadores no relatório de risco para as edições gratuitas e básicas do Azure AD

Os utilizadores sinalizados no relatório de risco no Azure AD gratuito e as edições basic fornece-lhe uma lista de contas de utilizador que poderá ter sido comprometida. 

![Inícios de Sessão de Risco](./media/concept-user-at-risk/03.png)

Seleção de um utilizador fornece informações de início de sessão. Para os utilizadores que estão em risco, pode rever o histórico de início de sessão do utilizador e repor a palavra-passe, se necessário.

Esta caixa de diálogo disponibiliza uma opção para:

- Transferir o relatório
- Procurar utilizadores

    ![Inícios de Sessão de Risco](./media/concept-user-at-risk/16.png)

Para obter informações mais detalhadas, precisa de uma licença premium.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Utilizadores no relatório de risco para as edições premium do Azure AD

Os utilizadores sinalizados no relatório de risco nas edições premium do Azure AD fornecem-lhe:

- Uma lista de contas de utilizador que poderão ter sido comprometidas 

- Informações adicionais sobre os [tipos de eventos de risco](concept-risk-events.md) que foram detetados

- Uma opção para transferir o relatório

- Uma opção para configurar uma [política de remediação de risco do utilizador](../identity-protection/howto-user-risk-policy.md)  

![Inícios de Sessão de Risco](./media/concept-user-at-risk/71.png)

Ao selecionar um utilizador, obtém uma vista de relatório detalhado para este utilizador que lhe permite:

- Abrir a vista Todos os inícios de sessão

- Repor a palavra-passe do utilizador

- Dispensar todos os eventos

- Investigar os eventos de risco comunicados para o utilizador. 

![Inícios de Sessão de Risco](./media/concept-user-at-risk/324.png)

Para investigar um evento de risco, selecione um na lista para abrir o painel **Detalhes** para este evento de risco. No painel **Detalhes**, tem a opção de fechar manualmente um evento de risco ou reativar um evento de risco fechado manualmente. 

![Inícios de Sessão de Risco](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Passos Seguintes

- [Como configurar a política de risco do utilizador](../identity-protection/howto-user-risk-policy.md)
- [Como configurar a política de remediação do risco](../identity-protection/howto-user-risk-policy.md)
- [O Azure Active Directory Identity Protection](../active-directory-identityprotection.md)


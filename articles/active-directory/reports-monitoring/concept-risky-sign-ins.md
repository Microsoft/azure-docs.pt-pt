---
title: Relatório de inícios de sessão de risco no portal do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre o relatório de inícios de sessão de risco no portal do Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e125f8e1e7909c5866a03c0571f49ec42d690a
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437939"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Relatório de inícios de sessão de risco no portal do Azure Active Directory

Azure Active Directory (Azure AD) Deteta as ações suspeitas relacionadas com as contas de utilizador. Para cada ação detetada, um registo denominado um **evento de risco** é criado. Para obter mais detalhes, consulte [eventos de risco do Azure AD](concept-risk-events.md). 

Pode acessar os relatórios de segurança do [portal do Azure](https://portal.azure.com) ao selecionar o **Azure Active Directory** painel e, em seguida, ao navegar para o **segurança** secção. 

Existem dois relatórios de segurança diferentes que são calculados com base nos eventos de risco:

- **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

- **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. 

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/10.png)

Para saber como configurar as políticas que acionam esses eventos de risco, veja [como configurar a política de risco do utilizador](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Quem pode aceder o relatório de inícios de sessão de risco?

Os relatórios de inícios de sessão arriscados estão disponíveis para utilizadores as seguintes funções:

- Administrador de Segurança
- Administrador Global
- Leitor de Segurança

Para saber como atribuir funções administrativas a um utilizador no Azure Active Directory, veja [modo de exibição e atribuir funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>De que licença do Azure AD precisa para aceder a um relatório de segurança?  

Todas as edições do Azure AD fornecem relatórios de inícios de sessão de risco. No entanto, o nível de granularidade dos relatórios varia entre as edições: 

- Na **edições do Azure Active Directory gratuito e Basic**, obtém uma lista de inícios de sessão de risco. 

- Além disso, o **do Azure Active Directory Premium 1** edition permite que examine alguns dos eventos de risco subjacentes que foram detetados para cada relatório. 

- A edição **do Azure Active Directory Premium 2** proporciona-lhe as informações mais detalhadas sobre todos os eventos de risco subjacentes e também lhe permite configurar políticas de segurança que respondam automaticamente aos níveis de risco configurados.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Relatório de inícios de sessão de risco para o Azure AD gratuito e a edição básica

As edições gratuita e básica do Azure AD fornecem uma lista de risco inícios de sessão que foram detetados para os seus utilizadores. Cada registro contém os seguintes atributos:

- **Utilizador** -o nome do utilizador que foi utilizado durante a operação de início de sessão.
- **IP** -o endereço IP do dispositivo que foi utilizado para ligar ao Azure Active Directory.
- **Localização** -a localização utilizada para ligar ao Azure Active Directory. Esta é uma aproximação de esforço melhor, com base em rastreamentos, os dados de registo, pesquisas inversa e outras informações.
- **Hora de início de sessão** – A hora quando o início de sessão foi efetuado
- **Estado** – O estado do início de sessão

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/01.png)

Com base na sua investigação do início de sessão-in risco, pode fornecer seus comentários para o Azure AD, efetuando as seguintes ações:

- Resolver
- Marcar como falso positivo
- Ignorar
- Reativar

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/21.png)

Este relatório também fornece uma opção para:

- Procurar recursos
- Transferir os dados do relatório

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Relatório de inícios de sessão de risco para as edições premium do Azure AD

O relatório de inícios de sessão de risco nas edições premium do Azure AD fornece-lhe:

- Informações adicionais sobre o [tipos de eventos de risco](concept-risk-events.md) que foram detetados. Com o **do Azure AD Premium P1 edition**, deteções de Trojans não são cobertas pela sua licença aparecem como o evento de risco **início de sessão com risco adicional detetado**. Com o **edição Azure AD Premium P2**, obtém as informações mais detalhadas sobre todas as detecções subjacentes.

- Uma opção para transferir o relatório

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/456.png)

Ao selecionar um evento de risco, obtém uma vista de relatório detalhado para este evento de risco que lhe permite:

- Uma opção para configurar uma [política de remediação de risco do utilizador](../identity-protection/howto-user-risk-policy.md)  

- Reveja a linha cronológica de deteção para o evento de risco  

- Reveja uma lista de utilizadores para os quais foi detetado este evento de risco

- Feche manualmente eventos de risco. 

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Às vezes, pode encontrar um evento de risco sem uma entrada correspondente início de sessão no [relatório de inícios de sessão](concept-sign-ins.md). Isto acontece porque o Identity Protection avalia o risco para ambos **interativo** e **não interativa** inícios de sessão, ao passo que o relatório de inícios de sessão mostra apenas os interativos inícios de sessão.

Ao selecionar um utilizador, obtém uma vista de relatório detalhado para este utilizador que lhe permite:

- Abrir a vista Todos os inícios de sessão

- Repor a palavra-passe do utilizador

- Dispensar todos os eventos

- Investigar os eventos de risco comunicados para o utilizador. 

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/324.png)

Para investigar um evento de risco, selecione um na lista.  
Esta ação abre o painel **Detalhes** para este evento de risco. No painel **Detalhes**, tem a opção de fechar manualmente um evento de risco ou reativar um evento de risco fechado manualmente. 

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Passos Seguintes

- [Como configurar a política de risco do utilizador](../identity-protection/howto-user-risk-policy.md)
- [Como configurar a política de remediação do risco](../identity-protection/howto-user-risk-policy.md)
- [Tipos de eventos de risco](concept-risk-events.md)

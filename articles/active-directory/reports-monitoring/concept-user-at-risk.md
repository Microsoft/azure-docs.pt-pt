---
title: Utilizadores sinalizados no relatório de segurança de risco no portal do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre os utilizadores sinalizados no relatório de segurança de risco no portal do Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/14/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fde063cb593ca1f610dc35cd044fe41e34ab9202
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578376"
---
# <a name="users-flagged-for-risk-security-report-in-the-azure-active-directory-portal"></a>Utilizadores sinalizados no relatório de segurança de risco no portal do Azure Active Directory

Com os relatórios de segurança no Azure Active Directory (Azure AD), pode obter informações sobre a probabilidade de contas de utilizador comprometidas no seu ambiente. 

O Azure Active Directory deteta as ações suspeitas relacionadas com as suas contas de utilizador. Para cada ação detetada, é criado um registo denominado *evento de risco*. Para obter mais informações, consulte [Eventos de risco do Azure Active Directory](concept-risk-events.md). 

Os eventos de risco detetados são utilizados para calcular:

- **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. Para obter mais informações, veja [Como configurar a política de risco de início de sessão](../identity-protection/howto-sign-in-risk-policy.md). 

- **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. Para obter mais informações, veja [Como configurar a política de risco de utilizador](../identity-protection/howto-user-risk-policy.md).  

No portal do Azure, pode encontrar os relatórios de segurança no painel do **Azure Active Directory**, na secção **Segurança**.  

![Inícios de Sessão de Risco](./media/concept-user-at-risk/10.png)



## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>De que licença do Azure AD precisa para aceder a um relatório de segurança?  

Todas as edições do Azure Active Directory disponibilizam os relatórios de utilizadores sinalizados para risco.  
No entanto, o nível de granularidade dos relatórios varia entre as edições: 

- Nas **edições Gratuita e Básica do Azure Active Directory**, já tem disponível uma lista dos utilizadores sinalizados para risco. 

- A edição **Azure Active Directory Premium 1** expande este modelo, permitindo-lhe também examinar alguns dos eventos de risco subjacentes que foram detetados em cada relatório. 

- A edição **do Azure Active Directory Premium 2** proporciona-lhe as informações mais detalhadas sobre todos os eventos de risco subjacentes e permite-lhe configurar políticas de segurança que respondam automaticamente aos níveis de risco configurados.



## <a name="azure-active-directory-free-and-basic-edition"></a>Edição gratuita e básica do Azure Active Directory

Os utilizadores sinalizados no relatório de risco nas edições gratuita e básica do Azure Active Directory fornecem uma lista de contas de utilizador que podem ter sido comprometidas. 


![Inícios de Sessão de Risco](./media/concept-user-at-risk/03.png)

Selecionar um utilizador abre o painel de dados do utilizador relacionado.
Para os utilizadores que estão em risco, pode rever o histórico de início de sessão do utilizador e repor a palavra-passe, se necessário.

![Inícios de Sessão de Risco](./media/concept-user-at-risk/46.png)


Esta caixa de diálogo disponibiliza uma opção para:

- Transferir o relatório

- Procurar utilizadores

![Inícios de Sessão de Risco](./media/concept-user-at-risk/16.png)


## <a name="azure-active-directory-premium-editions"></a>Edições premium do Azure Active Directory

Os utilizadores sinalizados no relatório de risco nas edições premium do Azure Active Directory fornecem-lhe:

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



## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o Azure Active Directory Identity Protection, veja [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).


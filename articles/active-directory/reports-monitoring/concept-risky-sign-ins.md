---
title: Relatório de inícios de sessão de risco no portal do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre o relatório de inícios de sessão de risco no portal do Azure Active Directory
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 629e7bf8ee7100b5927483ecfd4efa1d9223c151
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989926"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Relatório de inícios de sessão de risco no portal do Azure Active Directory

O Azure Active Directory (AD do Azure) detecta ações suspeitas relacionadas às suas contas de usuário. Para cada ação detectada, um registro chamado **evento de risco** é criado. Para obter mais detalhes, consulte [eventos de risco do Azure ad](concept-risk-events.md). 

Você pode acessar os relatórios de segurança do [portal do Azure](https://portal.azure.com) selecionando a folha **Azure Active Directory** e, em seguida, navegando até a seção **segurança** . 

Há dois relatórios de segurança diferentes que são calculados com base nos eventos de risco:

- **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

- **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. 

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/10.png)

Para saber como configurar as políticas que disparam esses eventos de risco, consulte [como configurar a política de risco do usuário](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Quem pode acessar o relatório de entradas arriscadas?

Os relatórios de entradas arriscadas estão disponíveis para os usuários nas seguintes funções:

- Administrador de Segurança
- Administrador Global
- Leitor de Segurança

Para saber como atribuir funções administrativas a um usuário no Azure Active Directory, consulte [Exibir e atribuir funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>De que licença do Azure AD precisa para aceder a um relatório de segurança?  

Todas as edições do Azure AD fornecem relatórios de entradas arriscadas. No entanto, o nível de granularidade dos relatórios varia entre as edições: 

- Nas **edições Azure Active Directory gratuito e Basic**, você obtém uma lista de entradas arriscadas. 

- Além disso, a edição **Azure Active Directory Premium 1** permite que você examine alguns dos eventos de risco subjacentes que foram detectados para cada relatório. 

- A edição **do Azure Active Directory Premium 2** proporciona-lhe as informações mais detalhadas sobre todos os eventos de risco subjacentes e também lhe permite configurar políticas de segurança que respondam automaticamente aos níveis de risco configurados.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Relatório de entradas arriscadas para o Azure AD Free e Basic Edition

As edições gratuita e básica do Azure AD fornecem uma lista de entradas arriscadas que foram detectadas para seus usuários. Cada registro contém os seguintes atributos:

- **User** -o nome do usuário que foi usado durante a operação de entrada.
- **IP** -o endereço IP do dispositivo que foi usado para se conectar ao Azure Active Directory.
- **Local** -o local usado para se conectar ao Azure Active Directory. Essa é uma aproximação de melhor esforço com base em rastreamentos, dados de registro, pesquisas inversas e outras informações.
- **Hora de início de sessão** – A hora quando o início de sessão foi efetuado
- **Estado** – O estado do início de sessão

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/01.png)

Com base na investigação da entrada arriscada, você pode fornecer comentários ao Azure AD executando as seguintes ações:

- Resolver
- Marcar como falso positivo
- Ignorar
- Reativar

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/21.png)

Esse relatório também fornece uma opção para:

- Procurar recursos
- Transferir os dados do relatório

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Relatório de entradas arriscadas para edições do Azure AD Premium

O relatório de entradas arriscadas nas edições do Azure AD Premium fornece:

- Informações agregadas sobre os [tipos de evento de risco](concept-risk-events.md) que foram detectados. Com a **edição P1 do Azure ad Premium**, as detecções que não são cobertas pela sua licença são exibidas como a entrada do evento de risco **com risco adicional detectado**. Com a **edição do Azure ad Premium P2**, você obtém as informações mais detalhadas sobre todas as detecções subjacentes.

- Uma opção para transferir o relatório

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/456.png)

Ao selecionar um evento de risco, obtém uma vista de relatório detalhado para este evento de risco que lhe permite:

- Uma opção para configurar uma [política de remediação de risco do utilizador](../identity-protection/howto-user-risk-policy.md)  

- Reveja a linha cronológica de deteção para o evento de risco  

- Reveja uma lista de utilizadores para os quais foi detetado este evento de risco

- Fechar manualmente os eventos de risco. 

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Às vezes, você pode encontrar um evento de risco sem uma entrada de logon correspondente no [relatório](concept-sign-ins.md)de entradas. Isso ocorre porque a proteção de identidade avalia o risco de entradas interativas e **não** interativas, enquanto o relatório de entradas mostra apenas as entradas interativas.

Ao selecionar um utilizador, obtém uma vista de relatório detalhado para este utilizador que lhe permite:

- Abrir a vista Todos os inícios de sessão

- Repor a palavra-passe do utilizador

- Dispensar todos os eventos

- Investigar os eventos de risco comunicados para o utilizador. 

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/324.png)

Para investigar um evento de risco, selecione um na lista.  
Esta ação abre o painel **Detalhes** para este evento de risco. No painel **Detalhes**, tem a opção de fechar manualmente um evento de risco ou reativar um evento de risco fechado manualmente. 

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Passos seguintes

- [Como configurar a política de risco do usuário](../identity-protection/howto-user-risk-policy.md)
- [Como configurar a política de correção de risco](../identity-protection/howto-user-risk-policy.md)
- [Tipos de eventos de risco](concept-risk-events.md)

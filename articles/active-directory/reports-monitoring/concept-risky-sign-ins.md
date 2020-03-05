---
title: Relatório de inscrições de risco no portal Microsoft Docs
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
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77486064139895799ac5a48327377154f75da6d
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273830"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Relatório de inícios de sessão de risco no portal do Azure Active Directory

O Azure Ative Directory (Azure AD) deteta ações suspeitas relacionadas com as suas contas de utilizador. Para cada ação detetada, é criado um registo chamado deteção de **risco.** Para mais detalhes, consulte deteções de [risco da AD Azure](concept-risk-events.md). 

Pode aceder aos relatórios de segurança do [portal Azure](https://portal.azure.com) selecionando a lâmina do **Diretório Ativo Azure** e, em seguida, navegando até à secção **de Segurança.** 

Existem dois relatórios de segurança diferentes que são calculados com base nas deteções de risco:

- **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

- **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. 

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/10.png)

Para aprender a configurar as políticas que desencadeiam estas deteções de risco, consulte [como configurar a política](../identity-protection/howto-user-risk-policy.md)de risco do utilizador .  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Quem pode aceder ao relatório de inscrição arriscado?

Os relatórios de inscrição de risco estão disponíveis para os utilizadores nas seguintes funções:

- Administrador de Segurança
- Administrador Global
- Leitor de Segurança

Para aprender a atribuir funções administrativas a um utilizador no Azure Ative Directory, consulte [o View e atribua funções de administrador no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>De que licença do Azure AD precisa para aceder a um relatório de segurança?  

Todas as edições da Azure AD fornecem-lhe relatórios de inscrição arriscados. No entanto, o nível de granularidade dos relatórios varia entre as edições: 

- Na **edição Azure Ative Directory Free,** obtém-se uma lista de inscrições arriscadas. 

- Além disso, a edição **Azure Ative Directory Premium 1** permite examinar algumas das deteções de risco subjacentes que foram detetadas para cada relatório. 

- A edição **Azure Ative Directory Premium 2** fornece-lhe as informações mais detalhadas sobre todas as deteções de risco subjacentes e também lhe permite configurar políticas de segurança que respondem automaticamente aos níveis de risco configurados.

## <a name="risky-sign-ins-report-for-azure-ad-free-edition"></a>Relatório de inscrições de risco para edição gratuita da Azure AD

A edição gratuita da Azure AD fornece-lhe uma lista de inscrições arriscadas que foram detetadas para os seus utilizadores. Cada registo contém os seguintes atributos:

- **Utilizador** - O nome do utilizador utilizado durante a operação de entrada.
- **IP** - O endereço IP do dispositivo que foi utilizado para ligar ao Diretório Ativo Azure.
- **Localização** - A localização utilizada para ligar ao Diretório Ativo Azure. Esta é uma melhor aproximação de esforço com base em vestígios, dados de registo, olhares invertidos e outras informações.
- **Hora de início de sessão** – A hora quando o início de sessão foi efetuado
- **Estado** – O estado do início de sessão

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/01.png)

Com base na sua investigação sobre o inserido arriscado, pode fornecer feedback ao Azure AD tomando as seguintes ações:

- Resolver
- Marcar como falso positivo
- Ignorar
- Reativar

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/21.png)

Este relatório também lhe fornece uma opção para:

- Procurar recursos
- Transferir os dados do relatório

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Relatório de inscrições de risco para edições premium Azure AD

O relatório de inscrições de risco nas edições premium Azure AD fornece-lhe:

- Informação agregada sobre os tipos de [deteção](concept-risk-events.md) de risco que foram detetados. Com a **edição Azure AD Premium P1,** as deteções que não estão cobertas pela sua licença aparecem como o **Sign-in de**deteção de risco com risco adicional detetado . Com a **edição Azure AD Premium P2,** obtém-se a informação mais detalhada sobre todas as deteções subjacentes.

- Uma opção para transferir o relatório

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/456.png)

Ao selecionar uma deteção de risco, obtém uma visão detalhada do relatório para esta deteção de risco que lhe permite:

- Uma opção para configurar uma [política de remediação de risco do utilizador](../identity-protection/howto-user-risk-policy.md)  

- Reveja a linha temporal de deteção para a deteção de riscos  

- Reveja uma lista de utilizadores para os quais esta deteção de risco foi detetada

- Feche manualmente as deteções de risco. 

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Por [vezes,](concept-sign-ins.md)pode encontrar uma deteção de risco sem uma entrada correspondente no relatório de inscrição . Isto porque a Proteção de Identidade avalia o risco tanto para os signos **interativos** como para os não **interativos,** enquanto o relatório de inscrição mostra apenas os sign-ins interativos.

Ao selecionar um utilizador, obtém uma vista de relatório detalhado para este utilizador que lhe permite:

- Abrir a vista Todos os inícios de sessão

- Repor a palavra-passe do utilizador

- Dispensar todos os eventos

- Investigue as deteções de risco reportadas para o utilizador. 

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/324.png)

Para investigar uma deteção de risco, selecione um da lista.  
Isto abre a lâmina **Detalhes** para esta deteção de risco. Na lâmina **Details,** tem a opção de fechar manualmente uma deteção de risco ou reativar uma deteção de risco fechada manualmente. 

![Inícios de Sessão de Risco](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Passos seguintes

- [Como configurar a política de risco do utilizador](../identity-protection/howto-user-risk-policy.md)
- [Como configurar a política de reparação de riscos](../identity-protection/howto-user-risk-policy.md)
- [Tipos de deteção de riscos](concept-risk-events.md)

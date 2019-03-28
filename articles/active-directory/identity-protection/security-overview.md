---
title: Descrição geral de segurança do Azure Active Directory (Azure AD) Identity Protection | Documentos da Microsoft
description: Saiba como o "Descrição geral da segurança" dá-lhe uma visão geral de postura de segurança da sua organização.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 610668768c7baca13cb60caf1d810cced31ebec3
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58516438"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>O Azure Active Directory Identity Protection - descrição geral da segurança

O [descrição geral da segurança](https://aka.ms/IdentityProtectionRefresh) dá-lhe uma visão geral de postura de segurança da sua organização. Ele ajuda a identificar potenciais ataques e compreender a eficácia das suas políticas.

O "Descrição geral da segurança" amplamente está dividido em duas seções:

- Tendências, à esquerda, fornecem uma linha cronológica de risco em sua organização.

- Mosaicos, à direita, realçam os principais problemas em curso na sua organização e sugerem como agir rapidamente.


![Descrição geral da segurança](./media/security-overview/01.png)
  
## <a name="trends"></a>Tendências

### <a name="new-risky-users-detected"></a>Novos utilizadores arriscados detetados

Este gráfico mostra o número de novos utilizadores de risco que foram detetados ao longo do período de tempo escolhido. Pode filtrar a vista de neste gráfico de nível de risco de utilizador (baixa, média, alta). Coloque o cursor sobre os incrementos de data UTC para ver o número de utilizadores de risco detetados para esse dia. Um clique neste gráfico irão direcioná-lo para o relatório de utilizadores de risco. Para remediar utilizadores que estão em risco, considere alterar a palavra-passe.

### <a name="new-risky-sign-ins-detected"></a>Novos inícios de sessão arriscados detetados

Este gráfico mostra o número de risco inícios de sessão detetados ao longo do período de tempo escolhido. Pode filtrar a vista deste gráfico, o tipo de risco de início de sessão (em tempo real ou agregação) e o nível de início de sessão de risco (baixa, média, alta). Inícios de sessão não protegidos são de risco em tempo real com êxito inícios de sessão que não eram desafiado de MFA. (Nota: Sign-ins que são de risco devido a detecções offline não podem ser protegidos em tempo real por políticas de risco de início de sessão). Coloque o cursor sobre os incrementos de data UTC para ver o número de inícios de sessão detetado em risco para esse dia. Um clique neste gráfico irão direcioná-lo para o relatório de "Risco inícios de sessão".

## <a name="tiles"></a>Mosaicos
 
### <a name="high-risk-users"></a>Utilizadores de alto risco

O mosaico "utilizadores de alto risco" mostra a contagem de utilizadores mais recentes com alta probabilidade de fugas de identidade. Devem ser uma prioridade para investigação. Um clique no mosaico "utilizadores de risco elevado" irá redirecionar para uma vista filtrada do relatório de utilizadores de risco que mostra apenas os utilizadores com um nível de risco alta. Usando esse relatório, pode saber mais e remediar esses utilizadores com uma reposição de palavra-passe.

![Descrição geral da segurança](./media/security-overview/02.png)


### <a name="medium-risk-users"></a>Utilizadores de risco médio
O mosaico "utilizadores de médio risco" mostra a contagem de mais recente de utilizadores com probabilidade média de comprometimento de identidade. Um clique no mosaico "utilizadores de médio risco" irá redirecionar para uma vista filtrada do relatório de utilizadores de risco que mostra apenas os utilizadores com um nível de risco média. Usando esse relatório, pode investigar e remediar esses utilizadores.

### <a name="unprotected-risky-sign-ins"></a>Inícios de sessão de risco desprotegidos

O mosaico "Desprotegidos risco inícios de sessão" apresenta o número de na última semana de em tempo real e bem-sucedida risco inícios de sessão que não foram bloqueados nem MFA contestado por uma política de acesso condicional, política de proteção de identidade de risco ou MFA por utilizador. Estes são potencialmente comprometidos inícios de sessão que foram bem-sucedidas e MFA não desafiados. Para proteger tais inícios de sessão no futuro, aplica uma política de risco de início de sessão. Um clique no mosaico 'Desprotegidos risco inícios de sessão' irá redirecionar para o painel de início de sessão de risco de configuração de política, onde pode configurar a política de risco de início de sessão para exigir a MFA num início de sessão com um nível de risco especificado.


### <a name="legacy-authentication"></a>Autenticação legada

O mosaico "Autenticação de legado" mostra a contagem da última semana de autenticações legadas na sua organização. Protocolos de autenticação legados não suportam os métodos de segurança moderno, como uma MFA. Para impedir que os antigos de autenticação, pode aplicar uma política de acesso condicional. Um clique no mosaico "Autenticação de legado" irá redirecioná-lo para o 'identidade Secure Score".


### <a name="identity-secure-score"></a>Pontuação de seguro de identidade

A pontuação de seguro de identidade mede e compara a sua postura de segurança aos padrões do setor. Se clicar no mosaico "Identity Secure pontuação (pré-visualização)", ele será redirecionado para o painel "Identidade Secure pontuação (pré-visualização)", onde pode aprender mais sobre como melhorar a sua postura de segurança.


## <a name="next-steps"></a>Passos Seguintes

- [Channel 9: O Azure AD e mostrar de identidade: Pré-visualização de proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

- [Ativar o Azure Active Directory Identity Protection](enable.md)


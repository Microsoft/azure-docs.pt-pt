---
title: Visão geral da segurança da proteção de identidade do diretório ativo azure
description: Saiba como a visão geral da Segurança lhe dá uma visão da postura de segurança da sua organização.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9550684ad154f28a02ee347fd0a79c1ec286beb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74382186"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Ative Directory Identity Protection - Visão geral de segurança

A [visão geral](https://aka.ms/IdentityProtectionRefresh) da Segurança no portal Azure dá-lhe uma visão da postura de segurança da sua organização. Ajuda a identificar potenciais ataques e a compreender a eficácia das suas políticas.

A "visão geral da segurança" é amplamente dividida em duas secções:

- As tendências, à esquerda, fornecem uma linha temporal de risco na sua organização.
- Os azulejos, à direita, destacam as principais questões em curso na sua organização e sugerem como agir rapidamente.

![Descrição geral da segurança](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>Tendências

### <a name="new-risky-users-detected"></a>Novos utilizadores de risco detetados

Este gráfico mostra o número de novos utilizadores de risco que foram detetados durante o período de tempo escolhido. Pode filtrar a vista deste gráfico pelo nível de risco do utilizador (baixo, médio, alto). Paire sobre os incrementos da data UTC para ver o número de utilizadores de risco detetados para esse dia. Um clique neste gráfico irá levá-lo ao relatório 'Utilizadores Arriscados'. Para remediar os utilizadores que estão em risco, considere alterar a sua palavra-passe.

### <a name="new-risky-sign-ins-detected"></a>Novos sign-ins de risco detetados

Este gráfico mostra o número de inscrições arriscadas detetadas durante o período de tempo escolhido. Pode filtrar a vista deste gráfico pelo tipo de risco de inscrição (em tempo real ou agregado) e pelo nível de risco de inscrição (baixo, médio, alto). Os inscrições desprotegidos são inscrições de risco em tempo real bem sucedidas que não foram desafiadas pela MFA. (Nota: Os insinsques que são arriscados devido a deteções offline não podem ser protegidos em tempo real por políticas de risco de entrada). Pairar sobre os incrementos da data UTC para ver o número de inscrições detetados em risco para esse dia. Um clique neste gráfico irá levá-lo ao relatório 'Sign-ins' de risco.

## <a name="tiles"></a>Mosaicos
 
### <a name="high-risk-users"></a>Utilizadores de alto risco

O azulejo de "utilizadores de alto risco" mostra a mais recente contagem de utilizadores com elevada probabilidade de compromisso de identidade. Estes devem ser uma prioridade para a investigação. Um clique no azulejo dos "utilizadores de alto risco" redirecionará para uma visão filtrada do relatório "Utilizadores arriscados", mostrando apenas utilizadores com um nível de risco elevado. Utilizando este relatório, pode aprender mais e remediar estes utilizadores com um reset de palavra-passe.

![Descrição geral da segurança](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>Utilizadores de risco médio
O azulejo "utilizadores de risco médio" mostra a mais recente contagem de utilizadores com probabilidade média de compromisso de identidade. Um clique no azulejo "Utilizadores de risco médio" redirecionará para uma visão filtrada do relatório "Utilizadores de Risco", mostrando apenas utilizadores com um nível de risco de médio. Utilizando este relatório, pode investigar e remediar estes utilizadores.

### <a name="unprotected-risky-sign-ins"></a>Inscrições arriscadas desprotegidas

O azulejo "insins de risco desprotegido" mostra a contagem da última semana de inscrições arriscadas bem sucedidas e em tempo real que não foram bloqueadas ou mFA contestadas por uma política de acesso condicional, política de risco de proteção de identidade ou MFA por utilizador. Estes são logins potencialmente comprometidos que foram bem sucedidos e não MFA desafiados. Para proteger tais inscrições no futuro, aplique uma política de risco de inscrição. Um clique no azulejo 'Desprotegido de risco' redirecionará para a lâmina de configuração da política de risco de entrada, onde pode configurar a política de risco de entrada para exigir mFA num início de sessão com um nível de risco especificado.

### <a name="legacy-authentication"></a>Autenticação legado

O azulejo 'Legacy authentication' mostra a contagem da última semana de autenticações antigas na sua organização. Os protocolos de autenticação do legado não suportam métodos de segurança modernos, como um MFA. Para evitar a autenticação do legado, pode aplicar uma política de Acesso Condicional. Um clique no azulejo 'Legacy authentication' irá redirecioná-lo para a 'Identity Secure Score'.

### <a name="identity-secure-score"></a>Pontuação segura de identidade

O Identity Secure Score mede e compara a sua postura de segurança com os padrões da indústria. Se clicar em azulejo 'Identity Secure Score (Preview)', redirecionará para a lâmina 'Identity Secure Score' onde poderá aprender mais sobre melhorar a sua postura de segurança.

## <a name="next-steps"></a>Passos seguintes

- [O que é o risco](concept-identity-protection-risks.md)

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)

---
title: Visão geral da segurança da proteção de identidade do Azure Ative Directory
description: Saiba como a visão geral da Segurança lhe dá uma visão da postura de segurança da sua organização.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95c589289d77597be2550673944c8fa21902e0fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93098472"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Descrição geral de segurança do Azure Active Directory Identity Protection

A [visão geral](https://aka.ms/IdentityProtectionRefresh) da Segurança no portal Azure dá-lhe uma visão da postura de segurança da sua organização. Ajuda a identificar potenciais ataques e a compreender a eficácia das suas políticas.

A "visão geral da segurança" está amplamente dividida em duas secções:

- As tendências, à esquerda, fornecem uma linha temporal de risco na sua organização.
- Os azulejos, à direita, destacam as principais questões em curso na sua organização e sugerem como tomar medidas rapidamente.

:::image type="content" source="./media/concept-identity-protection-security-overview/01.png" alt-text="Screenshot do portal Azure Visão geral da segurança. Os gráficos dos bares mostram a contagem de riscos ao longo do tempo. Os azulejos resumem a informação sobre os utilizadores e as entradas." border="false":::
  
## <a name="trends"></a>Tendências

### <a name="new-risky-users-detected"></a>Detetados novos utilizadores de risco

Este gráfico mostra o número de novos utilizadores de risco que foram detetados durante o período de tempo escolhido. Pode filtrar a vista deste gráfico pelo nível de risco do utilizador (baixo, médio, alto). Pairar sobre os incrementos da data UTC para ver o número de utilizadores de risco detetados para esse dia. Um clique neste gráfico irá levá-lo ao relatório "utilizadores arriscados". Para remediar os utilizadores que estão em risco, considere alterar a sua palavra-passe.

### <a name="new-risky-sign-ins-detected"></a>Detetados novos insusitados de risco

Este gráfico mostra o número de inserções de risco detetadas durante o período de tempo escolhido. Pode filtrar a vista deste gráfico pelo tipo de risco de inscrição (em tempo real ou agregado) e pelo nível de risco de inscrição (baixo, médio, alto). Os sign-ins desprotegidos são insuportuos de risco em tempo real que não foram desafiados pela MFA. (Nota: As entradas que são arriscadas devido a deteções offline não podem ser protegidas em tempo real por políticas de risco de inscrição). Pairar sobre os incrementos da data UTC para ver o número de inserções detetadas em risco para esse dia. Um clique neste gráfico irá levá-lo ao relatório 'Risky sign-ins'.

## <a name="tiles"></a>Mosaicos
 
### <a name="high-risk-users"></a>Utilizadores de alto risco

O azulejo "utilizadores de alto risco" mostra a mais recente contagem de utilizadores com elevada probabilidade de compromisso de identidade. Estas devem ser uma prioridade para a investigação. Um clique no azulejo "utilizadores de alto risco" irá redirecionar para uma visão filtrada do relatório "Utilizadores arriscados" que mostra apenas utilizadores com um nível de risco elevado. Utilizando este relatório, pode aprender mais e remediar estes utilizadores com uma palavra-passe reposta.

:::image type="content" source="./media/concept-identity-protection-security-overview/02.png" alt-text="Screenshot do portal Azure Visão geral de segurança, com azulejos visíveis para utilizadores de alto risco e de risco médio e outros fatores de risco." border="false":::

### <a name="medium-risk-users"></a>Utilizadores de risco médio
O azulejo "utilizadores de risco médio" mostra a contagem mais recente de utilizadores com probabilidade média de compromisso de identidade. Um clique no azulejo 'utilizadores de risco médio' irá redirecionar para uma visão filtrada do relatório "Utilizadores arriscados" que mostra apenas utilizadores com um nível de risco de médio nível. Utilizando este relatório, pode investigar e remediar mais estes utilizadores.

### <a name="unprotected-risky-sign-ins"></a>Inscrições arriscadas desprotegidas

O azulejo 'Ins desprotegido de risco' mostra a contagem de entradas de risco bem sucedidas e em tempo real que não foram bloqueadas ou a MFA contestada por uma política de acesso condicional, política de risco de proteção de identidade ou MFA por utilizador. Estes são logins potencialmente comprometidos que foram bem sucedidos e não MFA desafiados. Para proteger esses ins-ins no futuro, aplique uma política de risco de inscrição. Um clique no azulejo 'Ins de risco não protegido' irá redirecionar para a lâmina de configuração da política de risco de inscrição, onde pode configurar a política de risco de inscrição para exigir MFA numa sação com um nível de risco especificado.

### <a name="legacy-authentication"></a>Autenticação do legado

O azulejo 'Legacy Authentication' mostra a contagem de autenticações antigas da semana passada com risco presente na sua organização. Os protocolos de autenticação legado não suportam métodos de segurança modernos, como um MFA. Para evitar a autenticação de legados, pode aplicar uma política de Acesso Condicional. Um clique no azulejo 'Legacy authentication' irá redirecioná-lo para a 'Pontuação Segura de Identidade'.

### <a name="identity-secure-score"></a>Pontuação segura de identidade

O Identity Secure Score mede e compara a sua postura de segurança com os padrões da indústria. Se clicar em azulejo 'Identity Secure Score (Preview)', irá redirecionar para a lâmina 'Identity Secure Score', onde poderá aprender mais sobre como melhorar a sua postura de segurança.

## <a name="next-steps"></a>Passos seguintes

- [O que é o risco](concept-identity-protection-risks.md)

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)

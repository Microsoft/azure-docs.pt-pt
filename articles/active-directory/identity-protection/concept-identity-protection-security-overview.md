---
title: Visão geral da segurança do Azure Active Directory Identity Protection
description: Saiba como a visão geral de segurança fornece informações sobre a postura de segurança de sua organização.
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
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382186"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Visão geral de Azure Active Directory Identity Protection segurança

A [visão geral de segurança](https://aka.ms/IdentityProtectionRefresh) na portal do Azure fornece uma percepção da postura de segurança de sua organização. Ele ajuda a identificar possíveis ataques e a entender a eficácia de suas políticas.

A ' visão geral de segurança ' está dividida em duas seções:

- As tendências, à esquerda, fornecem uma linha do tempo de risco em sua organização.
- Os blocos, à direita, destacam as principais questões em andamento na sua organização e sugerem como agir rapidamente.

![Descrição geral da segurança](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>Tendências

### <a name="new-risky-users-detected"></a>Novos usuários arriscados detectados

Este gráfico mostra o número de novos usuários arriscados que foram detectados no período de tempo escolhido. Você pode filtrar a exibição deste gráfico por nível de risco do usuário (baixo, médio, alto). Passe o mouse sobre os incrementos de data UTC para ver o número de usuários arriscados detectados naquele dia. Um clique neste gráfico irá levá-lo para o relatório ' usuários arriscados '. Para corrigir os usuários que estão em risco, considere alterar sua senha.

### <a name="new-risky-sign-ins-detected"></a>Novas entradas arriscadas detectadas

Este gráfico mostra o número de entradas arriscadas detectadas no período de tempo escolhido. Você pode filtrar a exibição desse gráfico pelo tipo de risco de entrada (em tempo real ou agregação) e o nível de risco de entrada (baixo, médio, alto). As entradas desprotegidas são entradas de risco em tempo real com êxito que não foram desafiadas pela MFA. (Observação: as entradas que são arriscadas devido a detecções offline não podem ser protegidas em tempo real por políticas de risco de entrada). Passe o mouse sobre os incrementos de data UTC para ver o número de entradas detectadas em risco para esse dia. Um clique neste gráfico irá levá-lo para o relatório ' entradas arriscadas '.

## <a name="tiles"></a>Blocos
 
### <a name="high-risk-users"></a>Usuários de alto risco

O bloco ' usuários de alto risco ' mostra a contagem mais recente de usuários com alta probabilidade de comprometimento de identidade. Elas devem ser uma das principais prioridades para investigação. Um clique no bloco ' usuários de alto risco ' redirecionará para uma exibição filtrada do relatório ' usuários arriscados ', mostrando apenas os usuários com um nível de risco alto. Usando esse relatório, você pode saber mais e corrigir esses usuários com uma redefinição de senha.

![Descrição geral da segurança](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>Usuários de risco médio
O bloco ' usuários de risco médio ' mostra a contagem mais recente de usuários com probabilidade média de comprometimento de identidade. Um clique no bloco "usuários de risco médio" redirecionará para uma exibição filtrada do relatório "usuários arriscados", mostrando apenas os usuários com um nível médio de risco. Usando esse relatório, você pode investigar e corrigir esses usuários.

### <a name="unprotected-risky-sign-ins"></a>Entradas arriscadas desprotegidas

O bloco ' entradas arriscadas não protegidas ' mostra a contagem da semana passada de entradas de risco em tempo real bem-sucedidas, que não foram bloqueadas ou que MFA desafiada por uma política de acesso condicional, uma política de risco de proteção de identidade ou uma MFA por usuário. Esses logons são potencialmente comprometidos que foram bem-sucedidos e não são desafiados pela MFA. Para proteger essas entradas no futuro, aplique uma política de risco de entrada. Um clique no bloco "entradas arriscadas desprotegidas" redirecionará para a folha configuração de política de risco de entrada, na qual você pode configurar a política de risco de entrada para exigir MFA em uma entrada com um nível de risco especificado.

### <a name="legacy-authentication"></a>Autenticação herdada

O bloco ' autenticação herdada ' mostra a contagem da última semana de autenticações herdadas na sua organização. Os protocolos de autenticação herdados não dão suporte a métodos de segurança modernos, como uma MFA. Para impedir a autenticação herdada, você pode aplicar uma política de acesso condicional. Um clique no bloco "autenticação herdada" o redirecionará para a "Pontuação segura de identidade".

### <a name="identity-secure-score"></a>Pontuação segura de identidade

A pontuação de segurança de identidade mede e compara sua postura de segurança com os padrões do setor. Se você clicar no bloco ' identidade segura (visualização) ', ele será redirecionado para a folha ' identidade segura ', em que você pode aprender mais sobre como melhorar sua postura de segurança.

## <a name="next-steps"></a>Passos Seguintes

- [O que é risco](concept-identity-protection-risks.md)

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)

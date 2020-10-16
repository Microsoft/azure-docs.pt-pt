---
title: Proteção de Identidade e utilizadores B2B - Diretório Ativo Azure
description: Utilização da Proteção de Identidade com utilizadores B2B como funciona e limitações conhecidas
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
ms.openlocfilehash: c9ec9e110c3a476c9096ae3e216c9780da0e0f49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88949201"
---
# <a name="identity-protection-and-b2b-users"></a>Utilizadores de proteção de identidade e B2B

Com a colaboração Azure AD B2B, as organizações podem impor políticas baseadas no risco para utilizadores B2B que usam a Proteção de Identidade. Estas políticas são configuradas de duas formas:

- Os administradores podem configurar as políticas baseadas no risco de Proteção de Identidade incorporadas, que se aplicam a todas as aplicações, que incluem utilizadores convidados.
- Os administradores podem configurar as suas políticas de Acesso Condicional, utilizando o risco de inscrição como condição, que inclui os utilizadores convidados.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Como é avaliado o risco para os utilizadores de colaboração B2B

O risco de utilização para utilizadores de colaboração B2B é avaliado no seu diretório doméstico. O risco de inscrição em tempo real para estes utilizadores é avaliado no diretório de recursos quando tentam aceder ao recurso.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Limitações da Proteção de Identidade para utilizadores de colaboração B2B

Existem limitações na implementação da Proteção de Identidade para utilizadores de colaboração B2B num diretório de recursos devido à sua identidade existente no seu diretório doméstico. As principais limitações são as seguintes:

- Se um utilizador convidado ativar a política de risco do utilizador da Proteção de Identidade para forçar o reset da palavra-passe, **esta será bloqueada**. Este bloco deve-se à incapacidade de redefinir palavras-passe no diretório de recursos.
- **Os utilizadores convidados não aparecem no relatório de utilizadores arriscados**. Esta perda de visibilidade deve-se à avaliação de risco que ocorre no diretório de utilizadores B2B.
- Os administradores **não podem dispensar ou remediar um utilizador de colaboração B2B arriscado** no seu diretório de recursos. Esta perda de funcionalidade deve-se a administradores do diretório de recursos que não têm acesso ao diretório de utilizadores B2B.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Por que não posso remediar utilizadores de colaboração B2B arriscados no meu diretório?

A avaliação e reparação de riscos para os utilizadores de B2B ocorre no seu diretório doméstico. Devido a este facto, os utilizadores convidados não aparecem no relatório de utilizadores de risco no diretório de recursos e administradores no diretório de recursos não podem forçar uma redefinição de senha segura para estes utilizadores.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>O que faço se um utilizador de colaboração B2B foi bloqueado devido a uma política baseada no risco na minha organização?

Se um utilizador B2B arriscado no seu diretório for bloqueado pela sua política baseada no risco, o utilizador terá de corrigir esse risco no seu diretório doméstico. Os utilizadores podem remediar o seu risco através da reposição de uma palavra-passe segura no seu diretório de origem. Se não tiverem a palavra-passe de autosserviço ativada no seu diretório de origem, terão de contactar o Pessoal de TI da sua própria organização para que um administrador rejeite manualmente o seu risco ou reponha a sua palavra-passe.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Como posso evitar que os utilizadores de colaboração B2B sejam afetados por políticas baseadas no risco?

Excluir os utilizadores B2B das políticas de acesso condicional baseadas em riscos da sua organização impedirá que os utilizadores de B2B sejam impactados ou bloqueados pela sua avaliação de risco. Para excluir estes utilizadores B2B, crie um grupo em Azure AD que contenha todos os utilizadores convidados da sua organização. Em seguida, adicione este grupo como uma exclusão para o risco de utilizador de proteção de identidade incorporada e políticas de risco de inscrição, bem como quaisquer políticas de acesso condicional que utilizem o risco de inscrição como condição.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração B2B do Azure AD?](../external-identities/what-is-b2b.md)
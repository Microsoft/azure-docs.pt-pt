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
ms.openlocfilehash: 95ea7eb470a5880bc88b3df903d33854f363e974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72881316"
---
# <a name="identity-protection-and-b2b-users"></a>Utilizadores de proteção de identidade e B2B

Com a colaboração azure AD B2B, as organizações podem impor políticas baseadas no risco para utilizadores B2B usando proteção de identidade. Estas políticas são configuradas de duas formas:

- Os administradores podem configurar as políticas baseadas no risco de proteção de identidade incorporadas, que se aplicam a todas as aplicações, que incluem utilizadores convidados.
- Os administradores podem configurar as suas políticas de Acesso Condicional, utilizando o risco de entrada como condição, que inclui utilizadores convidados.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Como é avaliado o risco para utilizadores de colaboração B2B

O risco do utilizador para os utilizadores de colaboração B2B é avaliado no seu diretório doméstico. O risco de inscrição em tempo real para estes utilizadores é avaliado no diretório de recursos quando tentam aceder ao recurso.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Limitações da Proteção de Identidade para utilizadores de colaboração B2B

Existem limitações na implementação da Proteção de Identidade para os utilizadores de colaboração B2B num diretório de recursos devido à sua identidade existente no seu diretório de origem. As principais limitações são as seguintes:

- Se um utilizador convidado acionar a política de risco do utilizador de Proteção de Identidade para forçar o reset da palavra-passe, **elas serão bloqueadas**. Este bloco deve-se à incapacidade de repor senhas no diretório de recursos.
- **Os utilizadores convidados não aparecem no relatório de utilizadores de risco**. Esta perda de visibilidade deve-se à avaliação de risco que ocorre no diretório inicial do utilizador B2B.
- Os administradores **não podem descartar ou remediar um utilizador de colaboração B2B arriscado** no seu diretório de recursos. Esta perda de funcionalidade deve-se a administradores do diretório de recursos que não têm acesso ao diretório inicial do utilizador B2B.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Por que não posso remediar utilizadores de colaboração B2B arriscados no meu diretório?

A avaliação e reparação de riscos para os utilizadores de B2B ocorre no seu diretório de origem. Devido a este facto, os utilizadores convidados não aparecem no relatório de utilizadores de risco no diretório de recursos e os administradores no diretório de recursos não podem forçar um reset de senha segura para estes utilizadores.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>O que faço se um utilizador de colaboração B2B foi bloqueado devido a uma política baseada no risco na minha organização?

Se um utilizador B2B arriscado no seu diretório for bloqueado pela sua política baseada no risco, o utilizador terá de remediar esse risco no seu diretório doméstico. Os utilizadores podem remediar o seu risco executando uma redefinição de senha segura no seu diretório inicial. Se não tiverem a reposição da palavra-passe de self-service ativada no seu diretório inicial, terão de contactar o Pessoal de TI da sua própria organização para que um administrador rejeite manualmente o seu risco ou repor a sua palavra-passe.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Como posso impedir que os utilizadores de colaboração B2B sejam afetados por políticas baseadas no risco?

Excluir os utilizadores b2B das políticas de acesso condicional baseadas no risco da sua organização impedirá que os utilizadores b2B sejam impactados ou bloqueados pela sua avaliação de risco. Para excluir estes utilizadores B2B, crie um grupo em Azure AD que contenha todos os utilizadores convidados da sua organização. Em seguida, adicione este grupo como uma exclusão para o risco de utilizador de proteção de identidade incorporado e políticas de risco de inscrição, bem como quaisquer políticas de Acesso Condicional que utilizem o risco de inscrição como condição.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração B2B do Azure AD?](../b2b/what-is-b2b.md)

---
title: Políticas de base de acesso condicional - Diretório Ativo Azure
description: Políticas de acesso condicional de base para proteger as organizações de ataques comuns
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de5a5c604273225a85e49ca682980f83a951d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75767573"
---
# <a name="what-are-baseline-policies"></a>O que são as políticas de base?

As políticas de base são um conjunto de políticas predefinidas que ajudam a proteger as organizações contra muitos ataques comuns. Estes ataques comuns podem incluir spray de senha, repetição e phishing. As políticas de base estão disponíveis em todas as edições da Azure AD. A Microsoft está a disponibilizar estas políticas de proteção de base a todos, porque os ataques baseados na identidade têm vindo a aumentar nos últimos anos. O objetivo destas quatro políticas é garantir que todas as organizações tenham um nível de segurança de base, sem custos adicionais.

Gerir políticas de acesso condicional personalizadas requer uma licença Azure AD Premium.

> [!IMPORTANT]
> As políticas de base estão a ser depreciadas. Vê [o que há de novo no Diretório Ativo Azure](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults) para mais informações.

## <a name="baseline-policies"></a>Políticas de linha de base

![Políticas de base de acesso condicional no portal Azure](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Há quatro políticas de base:

* Exigir MFA para administradores (pré-visualização)
* Proteção do utilizador final (pré-visualização)
* Autenticação do legado do bloco (pré-visualização)
* Exigir MFA para gestão de serviços (pré-visualização)

Todas estas quatro políticas terão impacto em fluxos de autenticação legado, como pop, IMAP e clientes de desktop mais antigos do Office.

### <a name="exclusions"></a>Exclusões

Quando as políticas de base entraram na sua pré-visualização pública inicial, havia uma opção de excluir os utilizadores das políticas. Esta capacidade evoluiu através da pré-visualização e foi removida em julho de 2019. As organizações que já tinham criado exclusões puderam continuar a mantê-los novos utilizadores incapazes de acrescentar exclusões às políticas.

### <a name="require-mfa-for-admins-preview"></a>Exigir MFA para administradores (pré-visualização)

Devido ao poder e acesso que as contas do administrador têm, deve tratá-las com especial cuidado. Um método comum para melhorar a proteção das contas privilegiadas é exigir uma forma mais forte de verificação de contas quando são utilizadas para iniciar sessão. No Diretório Ativo Azure, pode obter uma verificação de conta mais forte, exigindo que os administradores se registem e utilizem a Autenticação Multi-Factor Azure.

Exigir MFA para administradores (pré-visualização) é uma política de base que requer a autenticação de vários fatores (MFA) para as seguintes funções de diretório, consideradas as funções de AD Azure mais privilegiadas:

* Administrador global
* Administrador do SharePoint
* Administrador do Exchange
* Administrador de Acesso Condicional
* Administrador de segurança
* Administrador de helpdesk / Administrador de palavra-passe
* Administrador de faturação
* Administrador de utilizadores

Se a sua organização tiver estas contas em uso em scripts ou código, considere substituí-las por [identidades geridas](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Proteção do utilizador final (pré-visualização)

Administradores privilegiados não são os únicos visados em ataques. Os maus atores tendem a visar utilizadores normais. Depois de terem acesso, estes maus atores podem solicitar acesso a informações privilegiadas em nome do titular da conta original ou descarregar todo o diretório e realizar um ataque de phishing a toda a sua organização. Um método comum para melhorar a proteção de todos os utilizadores é exigir uma forma mais forte de verificação da conta quando for detetado um sinal de inscrição de risco.

**A proteção do utilizador final (pré-visualização)** é uma política de base que protege todos os utilizadores num diretório. Ativar esta política requer que todos os utilizadores se registem para autenticação multi-factor Azure no prazo de 14 dias. Uma vez registados, os utilizadores serão solicitados apenas para MFA durante tentativas de entrada arriscadas. As contas de utilizador comprometidas são bloqueadas até que a palavra-passe seja reposta e o despedimento de risco. 

> [!NOTE]
> Quaisquer utilizadores previamente sinalizados para o risco são bloqueados até que a palavra-passe reset e o risco de despedimento após a ativação da política.

### <a name="block-legacy-authentication-preview"></a>Autenticação do legado do bloco (pré-visualização)

Os protocolos de autenticação legado (ex: IMAP, SMTP, POP3) são protocolos normalmente utilizados por clientes de correio mais antigos para autenticar. Os protocolos legados não suportam a autenticação de vários fatores. Mesmo que tenha uma política que exija a autenticação de vários fatores para o seu diretório, um mau ator pode autenticar usando um destes protocolos legados e contornar a autenticação de vários fatores.

A melhor forma de proteger a sua conta de pedidos de autenticação maliciosos feitos por protocolos legados é bloqueá-los.

A **autenticação do legado bloco (pré-visualização)** bloqueia pedidos de autenticação que são feitos com protocolos legados. A autenticação moderna deve ser utilizada para iniciar sessão com sucesso para todos os utilizadores. Utilizados em conjunto com as outras políticas de base, os pedidos provenientes de protocolos antigos serão bloqueados. Além disso, todos os utilizadores serão obrigados a MFA sempre que necessário. Esta política não bloqueia o Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Exigir MFA para gestão de serviços (pré-visualização)

As organizações utilizam uma variedade de serviços Azure e gerem-nos a partir de ferramentas baseadas em Gestor de Recursos Azure, como:

* Portal do Azure
* Azure PowerShell
* CLI do Azure

Usar qualquer uma destas ferramentas para realizar a gestão de recursos é uma ação altamente privilegiada. Estas ferramentas podem alterar configurações em toda a subscrição, tais como configurações de serviço e faturação de subscrição.

Para proteger as ações privilegiadas, esta política **requer MFA para gestão de serviços (pré-visualização)** exigirá a autenticação de vários fatores para qualquer utilizador que aceda ao portal Azure, Azure PowerShell ou Azure CLI.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte:

* [Ativar incumprimentos de segurança](../fundamentals/concept-fundamentals-security-defaults.md)
* [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
* [Cinco passos para garantir a sua infraestrutura de identidade](../../security/fundamentals/steps-secure-identity.md)

---
title: Políticas de proteção de linha de base do acesso condicional - Azure Active Directory
description: Políticas de acesso condicional de linha de base para proteger as organizações de ataques comuns
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca062f4024bb5b0946812e00c8ccc1254b56d333
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003300"
---
# <a name="what-are-baseline-policies"></a>Quais são as políticas de linha de base?

As políticas de linha de base são um conjunto de políticas predefinidas que o ajudam a proteger as organizações contra muitos ataques comuns. Esses ataques comuns podem incluir spray de palavra-passe, reprodução e phishing. Políticas de linha de base estão disponíveis em todas as edições do Azure AD. Microsoft está a disponibilizar estas políticas de proteção de linha de base para todos os utilizadores porque ataques baseados na identidade tem sido a aumentar ao longo dos últimos anos. O objetivo dessas diretivas quatro é garantir que todas as organizações têm um nível de linha de base de segurança ativada em nenhum custos adicionais.  

A gestão de políticas de acesso condicional personalizada requer uma licença do Azure AD Premium.

## <a name="baseline-policies"></a>Políticas de linha de base

![Políticas de linha de base de acesso condicional no portal do Azure](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Existem quatro políticas de linha de base que podem permitir que as organizações:

* [Exigir a MFA para os administradores](howto-baseline-protect-administrators.md)
* [Proteção de utilizador final (pré-visualização)](howto-baseline-protect-end-users.md)
* [Bloco antigos de autenticação (pré-visualização)](howto-baseline-protect-legacy-auth.md)
* [Exigir a MFA para gestão de serviço (pré-visualização)](howto-baseline-protect-azure.md)

Todos os quatro destas políticas terá impacto sobre fluxos de autenticação legada como POP, IMAP e clientes de ambiente de trabalho do Office mais antigos.

### <a name="require-mfa-for-admins"></a>Exigir MFA aos administradores

Devido ao poder e o acesso que as contas de administrador, deve tratá-los com cuidado especial. Métodos comuns para melhorar a proteção de contas com privilégios é exigir um formulário mais forte de verificação de conta quando são utilizadas para iniciar sessão. No Azure Active Directory, pode obter uma verificação de conta mais forte ao exigir que os administradores de registro e utilizar o Azure multi-factor Authentication.

[Exigir a MFA para os administradores](howto-baseline-protect-administrators.md) é uma política de linha de base que requer autenticação multifator (MFA) para as seguintes funções de diretório, consideradas as mais privilegiadas funções do Azure AD:

* Administrador Global
* Administrator do SharePoint
* Administrador do Exchange
* Administrador de acesso condicional
* Administrador de segurança
* Administrador de suporte técnico / administrador da palavra-passe
* Administrador de Faturação
* Administrador de utilizadores

Se sua organização tiver estas contas em uso em scripts ou código, considere substituí-los com [geridos identidades](../managed-identities-azure-resources/overview.md). Como solução temporária, pode excluir contas de usuários específicos da política de linha de base.

### <a name="end-user-protection-preview"></a>Proteção de utilizador final (pré-visualização)

Os administradores com privilégios elevados não são os únicos visados ataques. Mal-intencionadas tendem a usuários normais de destino. Após obter acesso, essas pessoas mal-intencionadas pode solicitar acesso a informações privilegiadas em nome do titular da conta original ou transferir o diretório e realizar um ataque de phishing em toda a organização. É um método comum para melhorar a proteção para todos os utilizadores exigir um formulário mais forte de verificação de conta quando é detetado um risco início de sessão.

**Proteção de utilizador final (pré-visualização)** é uma política de linha de base que protege todos os utilizadores num diretório. Ativar esta política requer que todos os utilizadores para se registrar para o Azure multi-factor Authentication dentro de 14 dias. Depois de registado, serão solicitados para a MFA aos utilizadores apenas durante a tentativas de início de sessão arriscadas. Contas de utilizador comprometidas são bloqueadas até que a reposição de palavra-passe e o risco de exoneração.

### <a name="block-legacy-authentication-preview"></a>Bloco antigos de autenticação (pré-visualização)

Protocolos de autenticação (ex: IMAP, SMTP, POP3) são protocolos normalmente utilizados pelos clientes de email mais antigos para autenticar. Protocolos legados não suportam autenticação multifator. Mesmo que tenha uma política que exija a autenticação multifator para o seu diretório, um ator indevido pode autenticar com um desses protocolos herdados e ignorar a multi-factor authentication.

A melhor forma de proteger a sua conta de pedidos de autenticação malicioso efetuados por protocolos legados é bloqueá-los.

O **bloco antigos de autenticação (pré-visualização)** política de linha de base bloqueia os pedidos de autenticação que são efetuados utilizando protocolos legados. Autenticação moderna deve ser utilizada para se inscrever com êxito todos os utilizadores. Utilizado em conjunto com as políticas de linha de base, serão bloqueados pedidos provenientes de protocolos legados. Além disso, todos os utilizadores será necessários para a MFA sempre que for necessário. Esta política não bloqueia o Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Exigir a MFA para gestão de serviço (pré-visualização)

As organizações a utilizam uma variedade de serviços do Azure e geri-los a partir de ferramentas do Azure Resource Manager com base em como:

* Portal do Azure
* Azure PowerShell
* CLI do Azure

Utilizar qualquer uma destas ferramentas para executar o gerenciamento de recursos é uma ação com privilégios elevados. Essas ferramentas podem alterar as configurações de toda a subscrição, como definições de serviço e de faturação de subscrição.

Para proteger ações privilegiadas, isso **exigir a MFA para a gestão de serviço (pré-visualização)** política irá exigir a autenticação multifator para qualquer utilizador aceder ao portal do Azure, Azure PowerShell ou da CLI do Azure.

## <a name="enable-a-baseline-policy"></a>Ativar uma política de linha de base

Para ativar uma política de linha de base:

1. Inicie sessão para o **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **do Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione uma política de linha de base que pretende ativar.
1. Definir **ativar política** ao **no**.
1. Clique em Guardar.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte:

* [Cinco etapas para proteger a sua infraestrutura de identidade](../../security/azure-ad-secure-steps.md)
* [O que é o acesso condicional no Azure Active Directory?](overview.md)
* [Exigir a MFA para os administradores](howto-baseline-protect-administrators.md)
* [Proteção de utilizador final (pré-visualização)](howto-baseline-protect-end-users.md)
* [Bloco antigos de autenticação (pré-visualização)](howto-baseline-protect-legacy-auth.md)
* [Exigir a MFA para gestão de serviço (pré-visualização)](howto-baseline-protect-azure.md)
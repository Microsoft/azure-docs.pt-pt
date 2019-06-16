---
title: Política de linha de base exigir a MFA para os administradores - Azure Active Directory
description: Política de acesso condicional para exigir autenticação multifator para administradores
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
ms.openlocfilehash: 4f8b7f281ad5ed8424110696544ffdb49e50ce59
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112428"
---
# <a name="baseline-policy-require-mfa-for-admins"></a>Política de linha de base: Exigir MFA aos administradores

Os utilizadores com acesso a contas com privilégios têm acesso irrestrito ao seu ambiente. Devido ao poder que essas contas têm, deve tratá-los com cuidado especial. Métodos comuns para melhorar a proteção de contas com privilégios é exigir um formulário mais forte de verificação de conta quando são utilizadas para início de sessão. No Azure Active Directory, pode obter uma verificação de conta mais forte ao exigir autenticação multifator (MFA).

**Exigir a MFA para os administradores** é um [política de linha de base](concept-baseline-protection.md) que requer a MFA sempre que uma das seguintes funções com privilégios de administrador inicia sessão:

* Administrador global
* Administrador do SharePoint
* Administrador do Exchange
* Administrador de acesso condicional
* Administrador de segurança
* Administrador de suporte técnico / administrador da palavra-passe
* Administrador de faturação
* Administrador de utilizadores

Após ativar o MFA necessita para os administradores política, as funções de nove administrador acima serão necessário para se registar para MFA com a aplicação de autenticador. Após a conclusão do registo na MFA, os administradores terão de executar a MFA sempre único eles início de sessão.

![Exigir a MFA para a política de linha de base de administradores](./media/howto-baseline-protect-administrators/baseline-policy-require-mfa-for-admins.png)

## <a name="deployment-considerations"></a>Considerações sobre implementação

Uma vez que o **exigir a MFA para os administradores** política se aplica a todos os administradores críticos, várias considerações precisam ser feitas para assegurar uma implantação tranqüila. Essas considerações incluem a identificar os utilizadores e os princípios de serviço no Azure AD que não é possível ou não deve realizar MFA, bem como aplicações e clientes utilizados pela sua organização que não suportam a autenticação moderna.

### <a name="legacy-protocols"></a>Protocolos legados

Protocolos de autenticação (IMAP, SMTP, POP3, etc.) são utilizados pelos clientes de correio para fazer pedidos de autenticação. Esses protocolos não suportam MFA. A maioria dos comprometimentos conta vistos pela Microsoft é causada por pessoas mal-intencionadas realizar ataques contra protocolos legados tentando ignorar o MFA. Para garantir que a MFA é necessária quando iniciar sessão numa conta administrativa e mal-intencionadas não podem ignorar o MFA, esta política bloqueia todos os pedidos de autenticação feitos para as contas de administrador a partir do protocolos legados.

> [!WARNING]
> Antes de ativar esta política, certifique-se de que os administradores não estiverem a utilizar protocolos de autenticação. Consulte o artigo [como: Autenticação de legado de bloco para o Azure AD com o acesso condicional](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) para obter mais informações.

### <a name="user-exclusions"></a>Exclusões de utilizador

Esta política de linha de base fornece-lhe a opção para excluir usuários. Antes de ativar a política para o seu inquilino, recomendamos excluir as seguintes contas:

* **Acesso de emergência** ou **break glass** contas para evitar o bloqueio de conta ao nível do inquilino. No cenário improvável que todos os administradores são impedidos de aceder ao seu inquilino, sua conta de administrador de acesso de emergência pode ser utilizada para iniciar sessão para os passos de take de inquilino para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, [gerir contas de acesso de emergência no Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Contas de serviço** e **princípios de serviço**, tais como a conta do Azure AD Connect Sync. Contas de serviço são contas não interativas que não estão associadas a qualquer utilizador específico. Eles são normalmente utilizados pelos serviços de back-end e permitem acesso programático aos aplicativos. Contas de serviço devem ser excluídas, uma vez que o MFA não pode ser concluída por meio de programação.
   * Se sua organização tiver estas contas em uso em scripts ou código, considere substituí-los com [geridos identidades](../managed-identities-azure-resources/overview.md). Como solução temporária, pode excluir essas contas específicas da política de linha de base.
* Utilizadores que não têm ou não será possível usar um Smartphone.
   * Esta política requer que os administradores para se registar para MFA com a aplicação Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Ativar a política de linha de base

A política **política de linha de base: Exigir a MFA para os administradores** vem pré-configurada e será apresentada na parte superior ao navegar para o painel de acesso condicional no portal do Azure.

Para ativar esta política e proteger os seus administradores:

1. Inicie sessão para o **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **do Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione **política de linha de base: Exigir a MFA para os administradores**.
1. Definir **ativar política** ao **utilizar a política imediatamente**.
1. Adicionar exclusões utilizador ao clicar em **usuários** > **selecionar utilizadores excluídos** e escolher os utilizadores que têm de ser excluídos. Clique em **selecionar** , em seguida, **feito**.
1. Clique em **guardar**.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte:

* [Políticas de proteção de linha de base de acesso condicional](concept-baseline-protection.md)
* [Cinco etapas para proteger a sua infraestrutura de identidade](../../security/azure-ad-secure-steps.md)
* [O que é o acesso condicional no Azure Active Directory?](overview.md)

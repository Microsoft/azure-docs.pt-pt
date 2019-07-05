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
ms.openlocfilehash: 4474283b9a233e39497cd05f0f04ea0984f02401
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560943"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Política de linha de base: Exigir a MFA para os administradores (pré-visualização)

Os utilizadores com acesso a contas com privilégios têm acesso irrestrito ao seu ambiente. Devido ao poder que essas contas têm, deve tratá-los com cuidado especial. Métodos comuns para melhorar a proteção de contas com privilégios é exigir um formulário mais forte de verificação de conta quando são utilizadas para início de sessão. No Azure Active Directory, pode obter uma verificação de conta mais forte ao exigir autenticação multifator (MFA).

**Exigir a MFA para os administradores (pré-visualização)**  é um [política de linha de base](concept-baseline-protection.md) que requer a MFA sempre que uma das seguintes funções com privilégios de administrador inicia sessão:

* Administrador global
* Administrador do SharePoint
* Administrador do Exchange
* Administrador de acesso condicional
* Administrador de segurança
* Administrador de suporte técnico / administrador da palavra-passe
* Administrador de faturação
* Administrador de utilizadores

Após ativar o MFA necessita para os administradores política, as funções de nove administrador acima serão necessário para se registar para MFA com a aplicação de autenticador. Após a conclusão do registo na MFA, os administradores terão de executar a MFA sempre único eles início de sessão.

## <a name="deployment-considerations"></a>Considerações sobre implementação

Uma vez que o **exigir a MFA para os administradores (pré-visualização)** política se aplica a todos os administradores críticos, várias considerações precisam ser feitas para assegurar uma implantação tranqüila. Essas considerações incluem a identificar os utilizadores e os princípios de serviço no Azure AD que não é possível ou não deve realizar MFA, bem como aplicações e clientes utilizados pela sua organização que não suportam a autenticação moderna.

### <a name="legacy-protocols"></a>Protocolos legados

Protocolos de autenticação (IMAP, SMTP, POP3, etc.) são utilizados pelos clientes de correio para fazer pedidos de autenticação. Esses protocolos não suportam MFA. A maioria dos comprometimentos conta vistos pela Microsoft é causada por pessoas mal-intencionadas realizar ataques contra protocolos legados tentando ignorar o MFA. Para garantir que a MFA é necessária quando iniciar sessão numa conta administrativa e mal-intencionadas não podem ignorar o MFA, esta política bloqueia todos os pedidos de autenticação feitos para as contas de administrador a partir do protocolos legados.

> [!WARNING]
> Antes de ativar esta política, certifique-se de que os administradores não estiverem a utilizar protocolos de autenticação. Consulte o artigo [como: Autenticação de legado de bloco para o Azure AD com o acesso condicional](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) para obter mais informações.

## <a name="enable-the-baseline-policy"></a>Ativar a política de linha de base

A política **política de linha de base: Exigir a MFA para os administradores (pré-visualização)** vem pré-configurada e será apresentada na parte superior ao navegar para o painel de acesso condicional no portal do Azure.

Para ativar esta política e proteger os seus administradores:

1. Inicie sessão para o **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **do Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione **política de linha de base: Exigir a MFA para os administradores (pré-visualização)** .
1. Definir **ativar política** ao **utilizar a política imediatamente**.
1. Clique em **guardar**.

> [!WARNING]
> Ocorreu uma opção **ativar política automaticamente no futuro** quando esta política foi em pré-visualização. Removemos esta opção para minimizar o impacto no utilizador repentino. Se tiver selecionado esta opção quando estava disponível, **não utilizar a política** é automaticamente selecionada agora. Se pretende utilizar esta política de linha de base, veja os passos acima para ativá-la.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte:

* [Políticas de proteção de linha de base de acesso condicional](concept-baseline-protection.md)
* [Cinco etapas para proteger a sua infraestrutura de identidade](../../security/azure-ad-secure-steps.md)
* [O que é o acesso condicional no Azure Active Directory?](overview.md)

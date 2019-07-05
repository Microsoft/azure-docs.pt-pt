---
title: Política de linha de base a proteção de utilizador final (pré-visualização) - Azure Active Directory
description: Política de acesso condicional para exigir autenticação multifator para utilizadores
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
ms.openlocfilehash: f2644e0e35139ac470b89f6af1b95cf510f60a0a
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561004"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Política de linha de base: Proteção de utilizador final (pré-visualização)

Podemos tendem a pensar que as contas de administrador são as únicas contas que necessitam de proteção com autenticação multifator (MFA). Os administradores têm acesso abrangente às informações confidenciais e podem efetuar alterações às definições globais de subscrição. No entanto, as pessoas mal-intencionadas tendem para os utilizadores finais de destino. Após obter acesso, essas pessoas mal-intencionadas pode solicitar acesso a informações privilegiadas em nome do titular da conta original ou transferir o diretório para realizar um ataque de phishing em toda a organização. Um dos métodos comuns para melhorar a proteção para todos os utilizadores é adquirir uma forma mais forte de verificação de conta, por exemplo, a autenticação multifator (MFA).

Para obter um equilíbrio razoável entre segurança e usabilidade, não devem ser pedido aos utilizadores sempre único eles início de sessão. Pedidos de autenticação que refletem o comportamento de usuário normal, como iniciar sessão a partir do mesmo dispositivo, da mesma localização, tem uma chance de baixa de comprometimento. Apenas inícios de sessão que são considerados arriscado e mostram as características de um ator indevido deverá receber um pedido com desafios MFA.

Proteção de utilizador final é um MFA com base no risco [política de linha de base](concept-baseline-protection.md) que protege todos os utilizadores num diretório, incluindo todas as funções de administrador. Ativar esta política requer que todos os utilizadores para se registar para MFA com a aplicação de autenticador. Os utilizadores podem ignorar o pedido de registo MFA durante 14 dias, após o qual serão impedidos de iniciar sessão até que eles se registram para a MFA. Depois de registado para MFA, serão pedido para a MFA aos utilizadores apenas durante a tentativas de início de sessão arriscadas. Contas de utilizador comprometidas são bloqueadas até que a palavra-passe é reposta e eventos de risco têm de ser fechados.

> [!NOTE]
> Esta política aplica-se a todos os utilizadores, incluindo contas de convidado e será avaliada quando o início de sessão em todas as aplicações.

## <a name="recovering-compromised-accounts"></a>Recuperar comprometido contas

Para ajudar a proteger os nossos clientes, o serviço de fuga de credenciais da Microsoft localiza pares de nome de utilizador/palavra-passe publicamente disponíveis. Se corresponderem um de nossos usuários, utilizamos para proteger essa conta imediatamente. Usuários identificados como tendo uma fuga de credenciais sejam confirmados comprometido. Estes utilizadores serão impedidos de iniciar sessão até que a palavra-passe é reposta.

Os utilizadores atribuídos uma licença do Azure AD Premium podem restaurar o acesso através de reposição de palavra-passe self-service (SSPR) se o recurso estiver ativado no seu diretório. Os utilizadores sem uma licença premium que tornam-se bloqueado tem de contactar o administrador para efetuar uma reposição de palavra-passe manual e descartar o evento de risco do utilizador sinalizado.

### <a name="steps-to-unblock-a-user"></a>Passos para desbloquear um utilizador

Confirme que o utilizador foi bloqueado pela política, examinando os registos de início de sessão do utilizador.

1. Um administrador tem de iniciar sessão para o **portal do Azure** e navegue para **Azure Active Directory** > **utilizadores** > clique no nome do utilizador e navegue até para inícios de sessão.
1. Para iniciar a um utilizador bloqueado de reposição de palavra-passe, um administrador tem de navegar até **do Azure Active Directory** > **utilizadores sinalizados para risco**
1. Clique no utilizador cuja conta está bloqueada para ver informações sobre a atividade do utilizador recente início de sessão.
1. Clique em Repor palavra-passe para atribuir uma palavra-passe temporária que terá de ser alterada após o início de sessão seguinte.
1. Clique em dispensar todos os eventos para repor a classificação de risco do utilizador.

O utilizador pode agora iniciar sessão, repor a palavra-passe e aceder à aplicação.

## <a name="deployment-considerations"></a>Considerações sobre implementação

Uma vez que o **proteção do utilizador final** política aplica-se a todos os utilizadores no seu diretório, várias considerações precisam ser feitas para assegurar uma implantação tranqüila. Essas considerações incluem a identificar os utilizadores e os princípios de serviço no Azure AD que não é possível ou não deve realizar MFA, bem como aplicações e clientes utilizados pela sua organização que não suportam a autenticação moderna.

### <a name="legacy-protocols"></a>Protocolos legados

Protocolos de autenticação (IMAP, SMTP, POP3, etc.) são utilizados pelos clientes de correio para fazer pedidos de autenticação. Esses protocolos não suportam MFA.  A maioria dos comprometimentos conta vistos pela Microsoft é causada por pessoas mal-intencionadas realizar ataques contra protocolos legados tentando ignorar o MFA. Para garantir que a MFA é necessária quando iniciar sessão numa conta e mal-intencionadas não podem ignorar o MFA, esta política bloqueia todos os pedidos de autenticação feitos para as contas de administrador a partir do protocolos legados.

> [!WARNING]
> Antes de ativar esta política, certifique-se de que os utilizadores não estiverem a utilizar protocolos de autenticação. Consulte o artigo [como: Autenticação de legado de bloco para o Azure AD com o acesso condicional](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) para obter mais informações.

## <a name="enable-the-baseline-policy"></a>Ativar a política de linha de base

A política **política de linha de base: Proteção de utilizador final (pré-visualização)** vem pré-configurada e será apresentada na parte superior ao navegar para o painel de acesso condicional no portal do Azure.

Para ativar esta política e Proteja os seus utilizadores:

1. Inicie sessão para o **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **do Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione **política de linha de base: Proteção de utilizador final (pré-visualização)** .
1. Definir **ativar política** ao **utilizar a política imediatamente**.
1. Clique em **guardar**.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte:

* [Políticas de proteção de linha de base de acesso condicional](concept-baseline-protection.md)
* [Cinco etapas para proteger a sua infraestrutura de identidade](../../security/azure-ad-secure-steps.md)
* [O que é o acesso condicional no Azure Active Directory?](overview.md)

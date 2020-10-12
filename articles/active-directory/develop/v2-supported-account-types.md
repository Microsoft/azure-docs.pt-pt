---
title: Tipos de conta suportados - plataforma de identidade da Microsoft Rio Azure
description: Documentação conceptual sobre audiências e tipos de conta suportados em aplicações
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 50a0859b58e2db0d3f644db01cfcde8c533b6871
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86518135"
---
# <a name="supported-account-types"></a>Tipos de conta suportados

Este artigo explica quais os tipos de conta (por vezes *chamados de audiências)* que são suportados nas aplicações da plataforma de identidade da Microsoft.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>Tipos de conta na nuvem pública

Na nuvem pública do Microsoft Azure, a maioria dos tipos de aplicações pode assinar nos utilizadores com qualquer público:

- Se estiver a escrever uma aplicação de linha de negócios (LOB), pode inscrever-se nos utilizadores na sua própria organização. Tal aplicação é por vezes chamada *de inquilino único.*
- Se é um ISV, pode escrever uma aplicação que assina nos utilizadores:

  - Em qualquer organização. Tal aplicação é chamada de aplicação web *multitenant.* Por vezes, lê-se que assina nos utilizadores as suas contas de trabalho ou de escola.
  - Com o seu trabalho, escola ou contas pessoais da Microsoft.
  - Com apenas contas pessoais da Microsoft.
    
- Se estiver a escrever uma aplicação entre empresas e consumidores, também pode assinar nos utilizadores com as suas identidades sociais, utilizando o Azure Ative Directory B2C (Azure AD B2C).

## <a name="account-type-support-in-authentication-flows"></a>Suporte do tipo de conta nos fluxos de autenticação

Alguns tipos de conta não podem ser usados com certos fluxos de autenticação. Por exemplo, em aplicações de desktop, UWP ou Daemon:

- As aplicações Daemon só podem ser usadas com organizações AZure AD. Não faz sentido tentar usar aplicações daEmon para manipular contas pessoais da Microsoft. O consentimento da administração nunca será concedido.
- Pode utilizar o fluxo integrado de autenticação do Windows apenas com contas de trabalho ou escola (na sua organização ou em qualquer organização). A autenticação integrada do Windows funciona com contas de domínio, e requer que as máquinas sejam unidas ao domínio ou a AD AZure. Este fluxo não faz sentido para contas pessoais da Microsoft.
- A [concessão de credenciais de palavra-passe do proprietário de recursos](./v2-oauth-ropc.md) (nome de utilizador/palavra-passe) não pode ser utilizada com contas pessoais da Microsoft. As contas pessoais da Microsoft exigem que o utilizador consinta em aceder a recursos pessoais em cada sessão de início de sessão. É por isso que este comportamento não é compatível com fluxos não interativos.

## <a name="account-types-in-national-clouds"></a>Tipos de conta em nuvens nacionais

As aplicações também podem assinar nos utilizadores em [nuvens nacionais.](authentication-national-cloud.md) No entanto, as contas pessoais da Microsoft não são suportadas nestas nuvens. É por isso que os tipos de conta suportados são reduzidos, para estas nuvens, para a sua organização (inquilino único) ou qualquer organização (aplicações multitenantes).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o arrendamento no Azure Ative Directory](./single-and-multi-tenant-apps.md).
- Saiba mais sobre [as nuvens nacionais.](./authentication-national-cloud.md)

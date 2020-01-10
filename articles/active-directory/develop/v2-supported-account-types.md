---
title: Tipos de contas com suporte-plataforma Microsoft Identity | Azure
description: Documentação conceitual sobre audiências e tipos de conta com suporte em aplicativos
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9303c8477bfdac3cb0b9c4bdaf28e5f2dea5e101
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423206"
---
# <a name="supported-account-types"></a>Tipos de conta suportados

Este artigo explica quais tipos de contas (às vezes chamadas de públicos) têm suporte em aplicativos.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Tipos de contas com suporte nos aplicativos da plataforma Microsoft Identity

Na nuvem pública Microsoft Azure, a maioria dos tipos de aplicativos pode conectar usuários com qualquer público-alvo:

- Se você estiver escrevendo um aplicativo de linha de negócios (LOB), poderá conectar usuários em sua própria organização. Esse aplicativo, às vezes, é chamado de **locatário único**.
- Se você for um ISV, poderá escrever um aplicativo que faz logon de usuários:

  - Em qualquer organização. Esse aplicativo é chamado de aplicativo Web **multilocatário** . Às vezes, você lerá que ele entra em usuários com suas contas corporativas ou de estudante.
  - Com suas conta Microsoft pessoais ou de trabalho ou de estudante.
  - Somente com conta Microsoft pessoais.
    > [!NOTE]
    > Atualmente, a plataforma de identidade da Microsoft dá suporte a contas pessoais da Microsoft apenas registrando um aplicativo para **contas pessoais corporativas ou de estudante ou da Microsoft**e, em seguida, restringindo a entrada no código para o aplicativo, especificando uma autoridade do Azure AD, ao criar o aplicativo, como `https://login.microsoftonline.com/consumers`.

- Se você estiver escrevendo um aplicativo de negócios para consumidores, também poderá conectar usuários com suas identidades sociais, usando Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Determinados fluxos de autenticação não dão suporte a todos os tipos de conta

Alguns tipos de conta não podem ser usados com determinados fluxos de autenticação. Por exemplo, em aplicativos de área de trabalho, UWP ou daemon:

- Os aplicativos daemon só podem ser usados com Azure Active Directory organizações. Não faz sentido tentar usar aplicativos de daemon para manipular contas pessoais da Microsoft (o consentimento do administrador nunca será concedido).  
- Você só pode usar o fluxo de autenticação do Windows integrado com contas corporativas ou de estudante (em sua organização ou em qualquer organização). Na verdade, a autenticação integrada do Windows funciona com contas de domínio e exige que as máquinas sejam ingressadas no domínio ou no Azure AD. Esse fluxo não faz sentido para contas pessoais da Microsoft.
- A [concessão de senha do proprietário do recurso](./v2-oauth-ropc.md) (nome de usuário/senha) não pode ser usada com contas pessoais da Microsoft. Na verdade, as contas pessoais da Microsoft exigem que o usuário consentisse a acessar recursos pessoais em cada sessão de entrada. É por isso que esse comportamento não é compatível com fluxos não interativos.
- O fluxo de código do dispositivo ainda não funciona com contas pessoais da Microsoft.

## <a name="supported-account-types-in-national-clouds"></a>Tipos de conta com suporte em nuvens nacionais

 Os aplicativos também podem entrar em usuários em [nuvens nacionais](authentication-national-cloud.md). No entanto, as contas pessoais da Microsoft não têm suporte nessas nuvens (por definição dessas nuvens). É por isso que os tipos de conta com suporte são reduzidos, para essas nuvens, para sua organização (locatário único) ou para quaisquer organizações (aplicativos multilocatários).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [aluguel no Azure Active Directory](./single-and-multi-tenant-apps.md)
- Saiba mais sobre as [nuvens nacionais](./authentication-national-cloud.md)

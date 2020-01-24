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
ms.openlocfilehash: de90c81f56b6017b2d53ecbfb2c400a4c9f05d81
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700061"
---
# <a name="supported-account-types"></a>Tipos de conta suportados

Este artigo explica quais tipos de contas (às vezes chamadas de públicos) têm suporte em aplicativos.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Tipos de contas com suporte nos aplicativos da plataforma Microsoft Identity

Na nuvem pública Microsoft Azure, a maioria dos tipos de aplicativos pode conectar usuários com qualquer público-alvo:

- Se você estiver escrevendo um aplicativo de linha de negócios (LOB), poderá conectar usuários em sua própria organização. Tal aplicação é por vezes nomeada **como inquilino único.**
- Se você for um ISV, poderá escrever um aplicativo que faz logon de usuários:

  - Em qualquer organização. Tal aplicação é nomeada uma aplicação web **multi-inquilino.** Às vezes, você lerá que ele entra em usuários com suas contas corporativas ou de estudante.
  - Com suas conta Microsoft pessoais ou de trabalho ou de estudante.
  - Somente com conta Microsoft pessoais.
    > [!NOTE]
    > Atualmente, a plataforma de identidade da Microsoft suporta contas pessoais da Microsoft apenas através do registo de uma aplicação para **contas pessoais**de trabalho ou escola ou Microsoft , e depois restringe o registo no código da aplicação, especificando uma autoridade da AD Azure, ao construir a aplicação, como `https://login.microsoftonline.com/consumers`.

- Se você estiver escrevendo um aplicativo de negócios para consumidores, também poderá conectar usuários com suas identidades sociais, usando Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Determinados fluxos de autenticação não dão suporte a todos os tipos de conta

Alguns tipos de conta não podem ser usados com determinados fluxos de autenticação. Por exemplo, em aplicativos de área de trabalho, UWP ou daemon:

- Os aplicativos daemon só podem ser usados com Azure Active Directory organizações. Não faz sentido tentar usar aplicativos de daemon para manipular contas pessoais da Microsoft (o consentimento do administrador nunca será concedido).  
- Você só pode usar o fluxo de autenticação do Windows integrado com contas corporativas ou de estudante (em sua organização ou em qualquer organização). Na verdade, a autenticação integrada do Windows funciona com contas de domínio e exige que as máquinas sejam ingressadas no domínio ou no Azure AD. Esse fluxo não faz sentido para contas pessoais da Microsoft.
- A Bolsa de [Palavra-passe do Proprietário](./v2-oauth-ropc.md) de Recursos (Username/Password), não pode ser utilizada com contas pessoais da Microsoft. Na verdade, as contas pessoais da Microsoft exigem que o usuário consentisse a acessar recursos pessoais em cada sessão de entrada. É por isso que esse comportamento não é compatível com fluxos não interativos.
- O fluxo de código do dispositivo ainda não funciona com contas pessoais da Microsoft.

## <a name="supported-account-types-in-national-clouds"></a>Tipos de conta com suporte em nuvens nacionais

 As aplicações também podem inscrever utilizadores em [nuvens nacionais.](authentication-national-cloud.md) No entanto, as contas pessoais da Microsoft não têm suporte nessas nuvens (por definição dessas nuvens). É por isso que os tipos de conta com suporte são reduzidos, para essas nuvens, para sua organização (locatário único) ou para quaisquer organizações (aplicativos multilocatários).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Arrendamento no Diretório Ativo Azure](./single-and-multi-tenant-apps.md)
- Saiba mais sobre [as Nuvens Nacionais](./authentication-national-cloud.md)

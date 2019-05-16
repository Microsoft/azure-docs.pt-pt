---
title: Contas suportadas nas aplicações (público) - a plataforma de identidade da Microsoft
description: Documentação conceptual sobre públicos-alvo e tipos de conta suportado em aplicativos
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 908ba764dfee7c164c3e6f0ff24d2bbf6a0df287
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544833"
---
# <a name="supported-account-types"></a>Tipos de contas suportadas

Este artigo explica os tipos de contas (por vezes denominados públicos-alvo) são suportados nas aplicações

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Tipos de contas suportadas em aplicativos do Microsoft Identity platform

Na Cloud pública do Microsoft Azure, a maioria dos tipos de aplicações podem iniciar sessão dos utilizadores com qualquer audiência:

- Se estiver escrevendo um aplicativo de linha de negócio (LOB), pode iniciar sessão de utilizadores na sua organização. Esse aplicativo é, às vezes, denominado **único inquilino**.
- Se for um ISV, pode escrever um aplicativo que utilizadores inicia sessão:

  - Em qualquer organização. Esse aplicativo é denominado um **multi-inquilino** aplicação web. Às vezes, irá ler que os utilizadores inicia sessão com as suas contas escolares ou profissionais.
  - Com o seu trabalho ou escola ou conta Microsoft pessoal.
  - Com apenas conta pessoal Microsoft.
    > [!NOTE]
    > Atualmente, a plataforma de identidade da Microsoft suporta contas Microsoft pessoais apenas ao registar uma aplicação para **trabalho ou escola ou contas pessoais da Microsoft**e, em seguida, restringir o início de sessão no código do aplicativo especificando uma autoridade do Azure AD, ao criar a aplicação, tais como `https://login.onmicrosoftonline.com/consumers`.

- Se estiver escrevendo uma empresa à aplicação de consumidores, também pode iniciar sessão de utilizadores com as suas identidades sociais, com o Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Alguns fluxos de autenticação não oferecem suporte a todos os tipos de conta

Alguns tipos de conta não podem ser utilizados com determinados fluxos de autenticação. Por exemplo, na área de trabalho, aplicações do UWP ou aplicações de daemon:

- Aplicações daemon só podem ser utilizadas com organizações do Azure Active Directory. Não faz sentido para tentar usar aplicativos de daemon para manipular contas pessoais da Microsoft (nunca será concedido o consentimento de administrador).  
- Só pode utilizar o fluxo de autenticação integrada do Windows com contas escolares ou profissionais (na sua organização ou de qualquer organização). Na verdade, a autenticação integrada do Windows funciona com contas de domínio e requer computadores a ser associado a um domínio ou associado ao Azure AD. Este fluxo não faz sentido Accounts Microsoft pessoais.
- O [concessão de palavra-passe de proprietário do recurso](./v2-oauth-ropc.md) (nome de usuário/senha), não pode ser utilizado com contas pessoais da Microsoft. Na verdade, contas Microsoft pessoais exigem que o utilizador dá consentimento a aceder a recursos pessoais em cada início de sessão. É por isso que, esse comportamento não é compatível com fluxos não interativo.
- Fluxo de código de dispositivo ainda não funciona com contas pessoais da Microsoft.

## <a name="supported-account-types-in-national-clouds"></a>Tipos de conta suportado em clouds nacionais

 Aplicações também podem iniciar sessão dos utilizadores no [clouds nacionais](authentication-national-cloud.md). No entanto, contas pessoais da Microsoft não são suportadas nessas nuvens (por definição nessas nuvens). É por isso que diminui os tipos de conta suportado, para estas clouds para sua organização (único inquilino) ou qualquer organizações (aplicações multi-inquilino).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [inquilinos no Azure Active Directory](./single-and-multi-tenant-apps.md)
- Saiba mais sobre [Clouds nacionais](./authentication-national-cloud.md)
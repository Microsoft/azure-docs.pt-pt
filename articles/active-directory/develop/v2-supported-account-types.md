---
title: Tipos de conta suportados - Plataforma de identidade da Microsoft / Azure
description: Documentação conceptual sobre audiências e tipos de conta suportados em aplicações
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d19381094e027bd567ffc503d32f9212ef56a948
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583097"
---
# <a name="supported-account-types"></a>Tipos de conta suportados

Este artigo explica quais os tipos de conta (por vezes *chamados de audiências)* são suportados nas aplicações da plataforma de identidade da Microsoft.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>Tipos de conta na nuvem pública

Na nuvem pública do Microsoft Azure, a maioria dos tipos de aplicações podem inscrever-se em utilizadores com qualquer público:

- Se estiver a escrever uma aplicação de linha de negócio (LOB), pode inscrever utilizadores na sua própria organização. Tal aplicação é por vezes chamada *de inquilino único.*
- Se for isv, pode escrever uma aplicação que assina nos utilizadores:

  - Em qualquer organização. Tal aplicação é chamada de aplicação web *multiarrendatária.* Às vezes lê-se que assina nos utilizadores com o seu trabalho ou contas escolares.
  - Com o seu trabalho ou escola ou contas pessoais da Microsoft.
  - Com apenas contas pessoais da Microsoft.
    
- Se estiver a escrever uma aplicação entre empresas e consumidores, também pode inscrever utilizadores com as suas identidades sociais, utilizando o Azure Ative Directory B2C (Azure AD B2C).

## <a name="account-type-support-in-authentication-flows"></a>Suporte do tipo de conta nos fluxos de autenticação

Alguns tipos de conta não podem ser usados com certos fluxos de autenticação. Por exemplo, em aplicações de desktop, UWP ou daemon:

- As aplicações da Daemon só podem ser usadas com organizações da AD Azure. Não faz sentido tentar usar aplicações daemon para manipular contas pessoais da Microsoft. O consentimento do administrador nunca será concedido.
- Só pode utilizar o fluxo integrado de autenticação do Windows com contas de trabalho ou escola (na sua organização ou em qualquer organização). A Autenticação Integrada do Windows funciona com contas de domínio, e exige que as máquinas sejam unidas ao domínio ou a adesão do Azure AD. Este fluxo não faz sentido para contas pessoais da Microsoft.
- A bolsa de credenciais de [palavra-passe do proprietário](./v2-oauth-ropc.md) de recursos (nome de utilizador/palavra-passe) não pode ser utilizada com contas pessoais da Microsoft. As contas pessoais da Microsoft exigem que o utilizador autorize o acesso a recursos pessoais em cada sessão de início de sessão de sessão de acesso. É por isso que este comportamento não é compatível com fluxos não interativos.
- O fluxo de código do dispositivo não funciona com contas pessoais da Microsoft.

## <a name="account-types-in-national-clouds"></a>Tipos de conta em nuvens nacionais

As aplicações também podem inscrever utilizadores em [nuvens nacionais.](authentication-national-cloud.md) No entanto, as contas pessoais da Microsoft não são suportadas nestas nuvens. É por isso que os tipos de conta suportados são reduzidos, para estas nuvens, para a sua organização (inquilino único) ou quaisquer organizações (aplicações multiarrendatárias).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o arrendamento no Diretório Ativo Azure.](./single-and-multi-tenant-apps.md)
- Saiba mais sobre [as nuvens nacionais.](./authentication-national-cloud.md)

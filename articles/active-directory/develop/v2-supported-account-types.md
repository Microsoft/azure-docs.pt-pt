---
title: Tipos de contas suportadas - Plataforma de identidade da Microsoft Azure
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
ms.openlocfilehash: a6c9ccb3dc29865c790e5d8cc9cfc35e68f5561d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886148"
---
# <a name="supported-account-types"></a>Tipos de conta suportados

Este artigo explica quais os tipos de contas (às vezes nomeados audiências) são suportados em aplicações.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Tipos de contas suportadas nas aplicações da plataforma Microsoft Identity

Na Cloud pública do Microsoft Azure, a maioria dos tipos de aplicações podem inscrever-se em utilizadores com qualquer público:

- Se estiver a escrever uma aplicação Line of Business (LOB), pode inscrever utilizadores na sua própria organização. Tal aplicação é por vezes nomeada **como inquilino único.**
- Se for isv, pode escrever uma aplicação que insere os utilizadores:

  - Em qualquer organização. Tal aplicação é nomeada uma aplicação web **multi-inquilino.** Às vezes lê-se que inscreve utentes com o seu trabalho ou contas escolares.
  - Com o seu trabalho ou escola ou conta pessoal da Microsoft.
  - Com apenas uma conta pessoal da Microsoft.
    > [!NOTE]
    > Atualmente, a plataforma de identidade da Microsoft suporta contas pessoais da Microsoft apenas através do registo de uma aplicação para **contas pessoais**de trabalho ou escola ou `https://login.microsoftonline.com/consumers`Microsoft , e depois restringe o registo no código da aplicação, especificando uma autoridade da AD Azure, ao construir a aplicação, como .

- Se estiver a escrever um negócio para a aplicação de consumidores, também pode inscrever utilizadores com as suas identidades sociais, utilizando o Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Certos fluxos de autenticação não suportam todos os tipos de conta

Alguns tipos de conta não podem ser usados com certos fluxos de autenticação. Por exemplo, em desktop, aplicações UWP ou aplicações daemon:

- As aplicações Daemon só podem ser usadas com organizações de Diretórios Ativos Azure. Não faz sentido tentar usar aplicações daemon para manipular contas pessoais da Microsoft (o consentimento do administrador nunca será concedido).  
- Só pode utilizar o fluxo integrado de autenticação do Windows com contas de trabalho ou escola (na sua organização ou em qualquer organização). Na verdade, a Autenticação Integrada do Windows funciona com contas de domínio, e requer que as máquinas sejam unidas ao domínio ou a AD Azure aderiu. Este fluxo não faz sentido para contas pessoais da Microsoft.
- A Bolsa de [Palavra-passe do Proprietário](./v2-oauth-ropc.md) de Recursos (Username/Password), não pode ser utilizada com contas pessoais da Microsoft. Na verdade, as contas pessoais da Microsoft exigem que o utilizador autorize o acesso a recursos pessoais em cada sessão de início de sessão de sessão de sessão de acesso. É por isso que este comportamento não é compatível com fluxos não interativos.
- O fluxo de código do dispositivo ainda não funciona com contas pessoais da Microsoft.

## <a name="supported-account-types-in-national-clouds"></a>Tipos de conta suportados em nuvens nacionais

 As aplicações também podem inscrever utilizadores em [nuvens nacionais.](authentication-national-cloud.md) No entanto, as contas pessoais da Microsoft não são suportadas nestas nuvens (por definição destas nuvens). É por isso que os tipos de conta suportados são reduzidos, para estas nuvens, para a sua organização (inquilino único) ou quaisquer organizações (aplicações multi-inquilinos).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Arrendamento no Diretório Ativo Azure](./single-and-multi-tenant-apps.md)
- Saiba mais sobre [as Nuvens Nacionais](./authentication-national-cloud.md)

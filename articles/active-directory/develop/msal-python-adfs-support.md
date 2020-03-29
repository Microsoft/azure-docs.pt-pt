---
title: Suporte Azure AD FS (MSAL Python)
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte da Federação de Diretórios Ativos (AD FS) na Microsoft Authentication Library for Python
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699551"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Apoio dos Serviços da Federação de Diretório Ativo na MSAL para python

Os Serviços da Federação de Diretórios Ativos (AD FS) no Windows Server permitem-lhe adicionar autenticação e autorização baseadas em OpenID Connect e OAuth 2.0 para as suas apps utilizando a Microsoft Authentication Library (MSAL) para Python. Utilizando o MSAL para a biblioteca Python, a sua aplicação pode autenticar os utilizadores diretamente contra AD FS. Para obter mais informações sobre cenários, consulte [Cenários AD FS para Desenvolvedores](/windows-server/identity/ad-fs/ad-fs-development).

Existem geralmente duas formas de autenticar contra a AD FS:

- A MSAL Python fala com o Azure Ative Directory, que por si só é federado com outros fornecedores de identidade. A federação acontece através da AD FS. A MSAL Python liga-se ao Azure AD, que assina em utilizadores que são geridos em Azure AD (utilizadores geridos) ou utilizadores geridos por outro fornecedor de identidade, como a AD FS (utilizadores federados). MSAL Python não sabe que um utilizador é federado. Simplesmente fala com a AD Azure. A [autoridade](msal-client-application-configuration.md#authority) que usa neste caso é a autoridade habitual (nome anfitrião da autoridade + inquilino, comum ou organizações).
- MSAL Python fala diretamente com uma autoridade da AD FS. Isto só é apoiado pela AD FS 2019 e mais tarde.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Ligue-se ao Diretório Ativo federado com AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Adquirir um símbolo interativamente para um utilizador federado

O seguinte aplica-se quer se conectem diretamente com os Serviços da Federação de Diretórios Ativos (AD FS) ou através do Diretório Ativo.

Quando liga `acquire_token_by_authorization_code` `acquire_token_by_device_flow`ou , a experiência do utilizador é tipicamente a seguinte:

1. O utilizador introduz o ID da conta.
2. A Azure AD exibe brevemente a mensagem "Levando-o para a página da sua organização" e o utilizador é redirecionado para a página de entrada do fornecedor de identidade. A página de login é geralmente personalizada com o logótipo da organização.

As versões AD FS suportadas neste cenário federado são:
- Serviços da Federação de Diretório SFS v2
- Serviços da Federação de Diretórios Ativos v3 (Windows Server 2012 R2)
- Serviços da Federação de Diretório Ativo v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Adquirir um símbolo através do nome de utilizador e da palavra-passe

O seguinte aplica-se quer se conectem diretamente com os Serviços da Federação de Diretórios Ativos (AD FS) ou através do Diretório Ativo.

Ao adquirir um símbolo `acquire_token_by_username_password`utilizando, a MSAL Python recebe o fornecedor de identidade a contactar com base no nome de utilizador. MSAL Python recebe um [símbolo SAML 1.1](reference-saml-tokens.md) do fornecedor de identidade, que depois fornece à Azure AD que devolve o JSON Web Token (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Ligação diretamente ao AD FS

Quando ligar o diretório ao AD FS, a autoridade que vai querer usar para construir a sua aplicação será algo como`https://somesite.contoso.com/adfs/`

MSAL Python apoia a ADFS 2019.

Não suporta uma ligação direta com a ADFS 2016 ou a ADFS v2. Se precisar de apoiar cenários que exijam uma ligação direta com a ADFS 2016, utilize a versão mais recente do ADAL Python. Depois de ter atualizado o seu sistema no local para ADFS 2019, pode utilizar o MSAL Python.

## <a name="next-steps"></a>Passos seguintes

- Para o caso federado, consulte [configure Azure Ative Directory sign in behavior for a application using a Home Realm Discovery policy](../manage-apps/configure-authentication-for-federated-users-portal.md)
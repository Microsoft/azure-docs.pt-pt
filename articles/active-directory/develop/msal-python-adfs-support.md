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
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: ad874a4b117b7a7ccecea0e1800ca18c6fce8aaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87846212"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Apoio ativo dos Serviços da Federação de Diretórios no MSAL para Python

Os Serviços da Federação de Diretórios Ativos (AD FS) no Windows Server permitem-lhe adicionar autenticação e autorização baseadas em OpenID Connect e OAuth 2.0 às suas apps utilizando a Microsoft Authentication Library (MSAL) para Python. Utilizando o MSAL para biblioteca Python, a sua aplicação pode autenticar os utilizadores diretamente contra AD FS. Para obter mais informações sobre cenários, consulte [os Cenários FS da AD para Desenvolvedores.](/windows-server/identity/ad-fs/ad-fs-development)

Existem geralmente duas formas de autenticação contra a AD FS:

- A MSAL Python fala com o Azure Ative Directory, que por si só é federado com outros fornecedores de identidade. A federação acontece através da AD FS. O MSAL Python liga-se ao Azure AD, que assina em utilizadores geridos em AZure AD (utilizadores geridos) ou utilizadores geridos por outro fornecedor de identidade, como o AD FS (utilizadores federados). MSAL Python não sabe que um utilizador é federado. Simplesmente fala com a Azure AD. A [autoridade](msal-client-application-configuration.md#authority) que você usa neste caso é a autoridade habitual (nome de anfitrião de autoridade + inquilino, comum, ou organizações).
- MSAL Python fala diretamente com uma autoridade da AD FS. Isto só é suportado pela AD FS 2019 e posteriormente.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Ligue-se ao Diretório Ativo federado com FS AD

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Adquira um símbolo interativamente para um utilizador federado

O seguinte aplica-se quer se ligue diretamente aos Serviços da Federação de Diretórios Ativos (AD FS) ou através do Ative Directory.

Quando telefona `acquire_token_by_authorization_code` `acquire_token_by_device_flow` ou, a experiência do utilizador é tipicamente a seguinte:

1. O utilizador introduz o ID da conta.
2. O Azure AD exibe brevemente a mensagem "Levá-lo para a página da sua organização" e o utilizador é redirecionado para a página de inscrição do fornecedor de identidade. A página de início de saúde é normalmente personalizada com o logótipo da organização.

As versões AD FS suportadas neste cenário federado são:
- Serviços da Federação De Diretório Ativo FS v2
- Serviços da Federação de Diretório Ativo v3 (Windows Server 2012 R2)
- Serviços da Federação De Diretório Ativo v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Adquirir um token via nome de utilizador e senha

O seguinte aplica-se quer se ligue diretamente aos Serviços da Federação de Diretórios Ativos (AD FS) ou através do Ative Directory.

Ao adquirir um token `acquire_token_by_username_password` usando, o MSAL Python consegue que o fornecedor de identidade contacte com base no nome de utilizador. A MSAL Python recebe um [token SAML 1.1](reference-saml-tokens.md) do fornecedor de identidade, que depois fornece à Azure AD que devolve o JSON Web Token (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Ligação direta ao AD FS

Quando ligar o diretório ao AD FS, a autoridade que vai querer usar para construir a sua aplicação será algo como `https://somesite.contoso.com/adfs/`

MSAL Python suporta ADFS 2019.

Não suporta uma ligação direta à ADFS 2016 ou à ADFS v2. Se precisar de suportar cenários que exijam uma ligação direta ao ADFS 2016, utilize a versão mais recente do ADAL Python. Uma vez atualizado o seu sistema no local para ADFS 2019, pode utilizar o MSAL Python.

## <a name="next-steps"></a>Passos seguintes

- Para o caso federado, consulte o [sinal de Diretório Ativo Configure Azure em comportamento para uma aplicação utilizando uma política de Descoberta do Home Realm](../manage-apps/configure-authentication-for-federated-users-portal.md)

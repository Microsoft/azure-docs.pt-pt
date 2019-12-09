---
title: Suporte AD FS do Azure na biblioteca de autenticação da Microsoft para Python
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte a Serviços de Federação do Active Directory (AD FS) (AD FS) na biblioteca de autenticação da Microsoft para Python
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: d83148f6bfff038f7310ff6fa1f3e1f5a4f8f066
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921437"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Suporte Serviços de Federação do Active Directory (AD FS) no MSAL para Python

O Serviços de Federação do Active Directory (AD FS) (AD FS) no Windows Server permite adicionar o OpenID Connect e a autenticação e autorização baseadas em OAuth 2,0 a seus aplicativos usando a MSAL (biblioteca de autenticação da Microsoft) para Python. Usando o MSAL para a biblioteca do Python, seu aplicativo pode autenticar usuários diretamente no AD FS. Para obter mais informações sobre cenários, consulte [cenários de AD FS para desenvolvedores](/windows-server/identity/ad-fs/ad-fs-development).

Geralmente, há duas maneiras de se autenticar no AD FS:

- MSAL Python se comunica com Azure Active Directory, que em si é federado com outros provedores de identidade. A Federação ocorre por meio de AD FS. MSAL Python conecta-se ao Azure AD, que entra em usuários que são gerenciados no Azure AD (usuários gerenciados) ou usuários gerenciados por outro provedor de identidade, como AD FS (usuários federados). MSAL Python não sabe que um usuário é federado. Ele simplesmente se comunica com o Azure AD. A [autoridade](msal-client-application-configuration.md#authority) que você usa nesse caso é a autoridade usual (nome do host da autoridade + locatário, comum ou organizações).
- MSAL Python se comunica diretamente com uma autoridade de AD FS. Isso só é suportado pelo AD FS 2019 e posterior.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Conectar-se ao Active Directory federado com AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Adquirir um token interativamente para um usuário federado

O seguinte se aplica se você se conectar diretamente ao Serviços de Federação do Active Directory (AD FS) (AD FS) ou ao Active Directory.

Quando você chama `acquire_token_by_authorization_code` ou `acquire_token_by_device_flow`, a experiência do usuário normalmente é a seguinte:

1. O usuário insere a ID da conta.
2. O Azure AD exibe brevemente a mensagem "levando você para a página da sua organização" e o usuário é redirecionado para a página de entrada do provedor de identidade. A página de entrada geralmente é personalizada com o logotipo da organização.

As versões de AD FS com suporte nesse cenário federado são:
- Serviços de Federação do Active Directory (AD FS) FS v2
- Serviços de Federação do Active Directory (AD FS) v3 (Windows Server 2012 R2)
- Serviços de Federação do Active Directory (AD FS) V4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Adquirir um token por nome de usuário e senha

O seguinte se aplica se você se conectar diretamente ao Serviços de Federação do Active Directory (AD FS) (AD FS) ou ao Active Directory.

Quando você adquire um token usando `acquire_token_by_username_password`, o Python MSAL Obtém o provedor de identidade para entrar em contato com base no nome de usuário. MSAL Python Obtém um [token 1,1 SAML](reference-saml-tokens.md) do provedor de identidade, que ele fornece ao Azure AD, que retorna o JWT (token Web JSON).

## <a name="connecting-directly-to-ad-fs"></a>Conectando diretamente a AD FS

Quando você conecta o diretório ao AD FS, a autoridade que você deseja usar para criar seu aplicativo será algo como `https://somesite.contoso.com/adfs/`

MSAL Python dá suporte ao ADFS 2019.

Ele não dá suporte a uma conexão direta com o ADFS 2016 ou ADFS v2. Se você precisar dar suporte a cenários que exigem uma conexão direta com o ADFS 2016, use a versão mais recente do Python do ADAL. Depois de atualizar seu sistema local para o ADFS 2019, você poderá usar o MSAL Python.

## <a name="next-steps"></a>Passos seguintes

- Para o caso federado, consulte [Configurar o comportamento de entrada Azure Active Directory para um aplicativo usando uma política de descoberta de realm inicial](../manage-apps/configure-authentication-for-federated-users-portal.md)
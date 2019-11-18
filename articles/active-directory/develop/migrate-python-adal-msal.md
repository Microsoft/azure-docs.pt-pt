---
title: Guia de migração do ADAL para MSAL para Python | Azure
description: Saiba como migrar seu aplicativo Python da ADAL (biblioteca de autenticação do Azure Active Directory) para a MSAL (biblioteca de autenticação da Microsoft) para Python.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: henrikm
editor: twhitney
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f4f19b1a9f6ee77fef2ca2634fb346025f41a6d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152752"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Guia de migração do ADAL para MSAL para Python

Este artigo realça as alterações que você precisa fazer para migrar um aplicativo que usa a ADAL (biblioteca de autenticação Azure Active Directory) para usar a MSAL (biblioteca de autenticação da Microsoft).

## <a name="difference-highlights"></a>Destaques da diferença

A ADAL funciona com o ponto de extremidade do Azure Active Directory v 1.0. A MSAL (biblioteca de autenticação da Microsoft) funciona com a plataforma de identidade da Microsoft, conhecida anteriormente como ponto de extremidade do Azure Active Directory v 2.0. A plataforma Microsoft Identity é diferente do Azure Active Directory v 1.0, pois:

Suportar
  - Contas corporativas e de estudante (contas do Azure AD provisionadas)
  - Contas pessoais (como Outlook.com ou Hotmail.com)
  - Seus clientes que trazem seu próprio email ou identidade social (como LinkedIn, Facebook, Google) por meio da oferta de Azure AD B2C

- Os padrões são compatíveis com:
  - OAuth v 2.0
  - OpenID Connect (OIDC)

Veja [o que há de diferente no ponto de extremidade da plataforma Microsoft Identity (v 2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison) para obter mais detalhes.

### <a name="scopes-not-resources"></a>Escopos não recursos

O Python ADAL adquire tokens para recursos, mas o Python MSAL adquire tokens para escopos. A superfície de API no MSAL Python não tem mais o parâmetro de recurso. Você precisaria fornecer escopos como uma lista de cadeias de caracteres que declaram as permissões e os recursos desejados que são solicitados. Para ver alguns exemplos de escopos, consulte [escopos de Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="error-handling"></a>Processamento de erros

A ADAL (biblioteca de autenticação Azure Active Directory) para Python usa a exceção `AdalError` para indicar que há um problema. O MSAL para o Python normalmente usa códigos de erro, em vez disso. Para obter mais informações, consulte [MSAL para tratamento de erros do Python](msal-handling-exceptions.md#msal-for-python-error-handling).

### <a name="api-changes"></a>Alterações de API

A tabela a seguir lista uma API no ADAL para Python e aquela a ser usada em seu lugar no MSAL para Python:

| ADAL para API do Python  | MSAL para API do Python |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication ou ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/D  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/A (consulte a seção acima) |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) e [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) e [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/D | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/D | Cache com persistência, disponível em [extensões MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrar tokens de atualização existentes para Python MSAL

A MSAL (biblioteca de autenticação da Microsoft) abstrai o conceito de tokens de atualização. O MSAL Python fornece um cache de token na memória por padrão para que você não precise armazenar, Pesquisar ou atualizar tokens de atualização. Os usuários também verão menos prompts de entrada, pois os tokens de atualização geralmente podem ser atualizados sem a intervenção do usuário. Para obter mais informações sobre o cache de token, consulte [serialização personalizada de cache de token em MSAL para Python](msal-python-token-cache-serialization.md).

O código a seguir o ajudará a migrar seus tokens de atualização gerenciados por outra biblioteca do OAuth2 (incluindo, mas não se limitando ao Python do ADAL) para ser gerenciado pelo MSAL para Python. Um motivo para migrar esses tokens de atualização é impedir que os usuários existentes precisem entrar novamente ao migrar seu aplicativo para o MSAL para Python.

O método para migrar um token de atualização é usar o MSAL para Python para adquirir um novo token de acesso usando o token de atualização anterior. Quando o novo token de atualização for retornado, o MSAL para Python irá armazená-lo no cache. Aqui está um exemplo de como fazer isso:

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [comparação v 1.0 e v 2.0](active-directory-v2-compare.md).

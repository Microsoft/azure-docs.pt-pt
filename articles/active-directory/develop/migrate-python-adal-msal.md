---
title: Python ADAL para guia de migração MSAL Azure
description: Aprenda a migrar a sua app Deautenticação de Diretório Sonáutica Azure Ative (ADAL) python para a Microsoft Authentication Library (MSAL) para Python.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696565"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Guia de migração DaAL para MSAL para Python

Este artigo destaca as alterações que precisa de fazer para migrar uma aplicação que utiliza a Biblioteca de Autenticação de Diretórios Ativos Azure (ADAL) para utilizar a Biblioteca de Autenticação da Microsoft (MSAL).

## <a name="difference-highlights"></a>Destaques da diferença

A ADAL trabalha com o Azure Ative Directory (Azure AD) v1.0 endpoint. A Microsoft Authentication Library (MSAL) trabalha com a plataforma de identidade Microsoft, anteriormente conhecida como O Ponto Final do Diretório Ativo Azure v2.0. A plataforma de identidade da Microsoft difere do Azure AD v1.0 na medida em que:

Suportes:
  - Contas de trabalho e escola (contas ads do Azure)
  - Contas pessoais (tais como Outlook.com ou Hotmail.com)
  - Os seus clientes que trazem o seu próprio e-mail ou identidade social (como LinkedIn, Facebook, Google) através da oferta Azure AD B2C

- As normas são compatíveis com:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Veja [o que há de diferente no ponto final da plataforma de identidade da Microsoft (v2.0)](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison) para mais detalhes.

### <a name="scopes-not-resources"></a>Âmbitos não recursos

A ADAL Python adquire fichas para recursos, mas a MSAL Python adquire fichas para âmbitos. A superfície DaPI em MSAL Python já não tem parâmetro de recurso. Você precisaria fornecer âmbitos como uma lista de cordas que declaram as permissões e recursos desejados que são solicitados. Para ver alguns exemplos de âmbitos, consulte [os âmbitos do Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="error-handling"></a>Processamento de erros

A Biblioteca de Autenticação de Diretórios Ativos Azure (ADAL) para python usa a exceção `AdalError` para indicar que houve um problema. MSAL para Python normalmente usa códigos de erro, em vez disso. Para mais informações, consulte [o MSAL para o tratamento de erros python](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python).

### <a name="api-changes"></a>Alterações da API

A tabela seguinte lista uma API em ADAL para Python, e a que deve ser utilizada no seu lugar em MSAL para Python:

| ADAL para Python API  | MSAL para Python API |
| ------------------- | ---------------------------------- |
| [AutenticaçãoContexto](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication ou ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/D  | [get_authorization_request_url()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/D |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) e [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) e [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/D | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/D | Cache com persistência, disponível a partir de [Extensões MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrar tokens de atualização existentes para MSAL Python

A biblioteca de autenticação da Microsoft (MSAL) abstrai o conceito de tokens de atualização. A MSAL Python fornece uma cache de ficha sinuosa por padrão para que não seja necessário armazenar, procurar ou atualizar tokens de atualização. Os utilizadores também verão menos indicações de sessão, porque as fichas de atualização podem geralmente ser atualizadas sem a intervenção do utilizador. Para obter mais informações sobre a cache token, consulte a serialização de [cache token personalizada em MSAL para Python](msal-python-token-cache-serialization.md).

O seguinte código irá ajudá-lo a migrar as suas fichas de atualização geridas por outra biblioteca OAuth2 (incluindo, mas não se limitando a ADAL Python) a ser gerida pela MSAL para Python. Uma das razões para migrar esses tokens de atualização é evitar que os utilizadores existentes precisem de voltar a inscrever-se quando migrarem a sua app para o MSAL para python.

O método para migrar um token refrescante é usar mSAL para Python para adquirir um novo sinal de acesso usando o token de atualização anterior. Quando o novo token de atualização for devolvido, a MSAL para python irá armazená-lo na cache. Aqui está um exemplo de como fazê-lo:

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

Para mais informações, consulte a [comparação v1.0 e v2.0](active-directory-v2-compare.md).

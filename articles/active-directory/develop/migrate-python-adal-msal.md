---
title: Python ADAL para guia de migração MSAL | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como migrar a sua app Azure Ative Directory Authentication Library (ADAL) Python para a Microsoft Authentication Library (MSAL) para Python.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 60d6e40b568c1189cdc01ef4239612d3717063b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578843"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Guia de migração ADAL para MSAL para Python

Este artigo destaca as alterações que precisa de fazer para migrar uma aplicação que utiliza a Biblioteca de Autenticação ativa do Azure Ative (ADAL) para utilizar a Biblioteca de Autenticação do Microsoft (MSAL).

## <a name="difference-highlights"></a>Destaques da diferença

A ADAL trabalha com o Azure Ative Directory (Azure AD) v1.0 endpoint. A Microsoft Authentication Library (MSAL) trabalha com a plataforma de identidade da Microsoft, anteriormente conhecida como o ponto final do Azure Ative Directory v2.0. A plataforma de identidade da Microsoft difere do Azure AD v1.0 na medida em que:

Suportes:
  - Contas de trabalho e escolas (contas azure ad)
  - Contas pessoais (como Outlook.com ou Hotmail.com)
  - Os seus clientes que trazem o seu próprio e-mail ou identidade social (como LinkedIn, Facebook, Google) através da oferta Azure AD B2C

- As normas são compatíveis com:
  - OAuth v2.0
  - Ligação OpenID (OIDC)

Veja [o que há de diferente na plataforma de identidade da Microsoft para](../azuread-dev/azure-ad-endpoint-comparison.md) mais detalhes.

### <a name="scopes-not-resources"></a>Âmbitos não recursos

ADAL Python adquire fichas para recursos, mas a MSAL Python adquire fichas para miras. A superfície da API em MSAL Python já não tem parâmetro de recurso. Você precisaria fornecer âmbitos como uma lista de cordas que declaram as permissões e recursos desejados que são solicitados. Para ver alguns exemplos de âmbitos, consulte os [âmbitos do Microsoft Graph](/graph/permissions-reference).

Pode adicionar o `/.default` sufixo de âmbito ao recurso para ajudar a migrar as suas aplicações do ponto final v1.0 (ADAL) para a plataforma de identidade da Microsoft (MSAL). Por exemplo, para o valor de recursos `https://graph.microsoft.com` de, o valor de âmbito equivalente é `https://graph.microsoft.com/.default` .  Se o recurso não estiver no formulário URL, mas sim num ID de recurso do `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` formulário, ainda pode utilizar o valor de âmbito como `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

Para obter mais detalhes sobre os diferentes tipos de âmbitos, consulte [permissões e consentimento na plataforma de identidade da Microsoft](./v2-permissions-and-consent.md) e nos [Âmbitos para uma API Web aceitando artigos de fichas v1.0.](./msal-v1-app-scopes.md)

### <a name="error-handling"></a>Processamento de erros

AZure Ative Directory Authentication Library (ADAL) para Python usa a exceção `AdalError` para indicar que houve um problema. MSAL para Python normalmente usa códigos de erro, em vez disso. Para obter mais informações, consulte [o MSAL para o tratamento de erros python](msal-error-handling-python.md).

### <a name="api-changes"></a>Alterações na API

A tabela a seguir lista uma API em ADAL para Python, e a que deve ser utilizada no seu lugar no MSAL para Python:

| ADAL para Python API  | MSAL para Python API |
| ------------------- | ---------------------------------- |
| [AutenticaçãoContexto](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplicação ou ConfidencialClientApplicação](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
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
| N/D | Cache com persistência, disponível a partir de [extensões MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrar fichas de atualização existentes para MSAL Python

A Microsoft Authentication Library (MSAL) abstra o conceito de tokens de atualização. O MSAL Python fornece uma cache simbólica na memória por padrão para que não precise de armazenar, procurar ou atualizar fichas de atualização. Os utilizadores também verão menos pedidos de inscrição porque os tokens de atualização podem normalmente ser atualizados sem a intervenção do utilizador. Para obter mais informações sobre a cache simbólica, consulte [a serialização de cache de ficha personalizada em MSAL para Python](msal-python-token-cache-serialization.md).

O seguinte código irá ajudá-lo a migrar os seus tokens de atualização geridos por outra biblioteca OAuth2 (incluindo, mas não se limitando a ADAL Python) para ser gerido pela MSAL para Python. Uma das razões para migrar esses tokens de atualização é evitar que os utilizadores existentes precisem de se inscrever novamente quando migrar a sua app para a MSAL para Python.

O método para migrar um token de atualização é usar o MSAL para python para adquirir um novo token de acesso usando o token de atualização anterior. Quando o novo token de renovação for devolvido, a MSAL para Python irá armazená-la na cache.
Desde o MSAL Python 1.3.0, fornecemos uma API dentro da MSAL para este fim.
Por favor, consulte o seguinte corte de código, citado a partir de [uma amostra completa de tokens de atualização migratória com MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67)

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to the Microsoft identity platform,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte a [comparação v1.0 e v2.0](../azuread-dev/azure-ad-endpoint-comparison.md).

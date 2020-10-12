---
title: Azure API para validação de fichas de acesso fHIR
description: Percorre validação simbólica e dá dicas sobre como resolver problemas de acesso
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 426ec0c2d6cc274aa0b6829eb4a30fd29b9ba8e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844665"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Azure API para validação de fichas de acesso fHIR

A forma como a Azure API para fHIR valida o token de acesso dependerá da implementação e configuração. Neste artigo, vamos percorrer os passos de validação, o que pode ser útil na resolução de problemas de acesso.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>Validar token não tem problemas com o fornecedor de identidade

O primeiro passo na validação do token é verificar se o token foi emitido pelo fornecedor de identidade correto e que não foi modificado. O servidor FHIR será configurado para utilizar um fornecedor de identidade específico conhecido como autoridade `Authority` . O servidor FHIR irá obter informações sobre o fornecedor de identidade a partir do `/.well-known/openid-configuration` ponto final. Ao utilizar o Azure AD, o URL completo seria:

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

onde `<TENANT-ID>` é o inquilino específico da Ad Azure (ou um ID do inquilino ou um nome de domínio).

A Azure AD devolverá um documento como o que está abaixo no servidor FHIR.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
As propriedades importantes para o servidor FHIR são `jwks_uri` , que diz ao servidor onde buscar as chaves de encriptação necessárias para validar a assinatura simbólica e `issuer` , que diz ao servidor o que estará na reclamação do emitente ( `iss` ) de fichas emitidas por este servidor. O servidor FHIR pode usar isto para validar que está a receber um token autêntico.

## <a name="validate-claims-of-the-token"></a>Validar reclamações do token

Uma vez que o servidor tenha verificado a autenticidade do token, o servidor FHIR procederá então à validação de que o cliente tem as reclamações necessárias para aceder ao token.

Ao utilizar a API Azure para FHIR, o servidor validará:

1. O símbolo tem o direito `Audience` `aud` (reivindicação).
1. O utilizador ou principal para o qual o token foi emitido está autorizado a aceder ao plano de dados do servidor FHIR. A `oid` alegação do token contém um ID de objeto de identidade, que identifica exclusivamente o utilizador ou o principal.

Recomendamos que o serviço FHIR seja [configurado para utilizar o Azure RBAC](configure-azure-rbac.md) para gerir as atribuições de funções de data plane. Mas também pode [configurar o RBAC local](configure-local-rbac.md) se o seu serviço FHIR utilizar um inquilino externo ou secundário do Azure Ative Directory. 

Ao utilizar o servidor OSS Microsoft FHIR para Azure, o servidor validará:

1. O símbolo tem o direito `Audience` `aud` (reivindicação).
1. O token tem um papel na `roles` reivindicação, que é permitido o acesso ao servidor FHIR.

Consulte detalhes sobre como [definir funções no servidor FHIR](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md).

Um servidor FHIR também pode validar que um token de acesso tem os âmbitos (em alegação `scp` simbólica) para aceder à parte da API FHIR a que um cliente está a tentar aceder. Atualmente, a AZure API para fHIR e o servidor FHIR para Azure não validam os âmbitos simbólicos.

## <a name="next-steps"></a>Passos seguintes
Agora que sabe como passar pela validação simbólica, pode completar o tutorial para criar uma aplicação JavaScript e ler dados de FHIR.

>[!div class="nextstepaction"]
>[Tutorial de aplicação web](tutorial-web-app-fhir-server.md)
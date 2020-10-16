---
title: Configure um fornecedor OpenID Connect (Pré-visualização)
description: Saiba como configurar um fornecedor OpenID Connect como fornecedor de identidade para o seu Serviço de Aplicações ou aplicação Azure Functions.
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: e8112f2dc20175e81cfa8388440b2d9aef6a419c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983874"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Configurar a aplicação do Serviço de Aplicações ou das Funções do Azure para iniciar sessão com um fornecedor do OpenID Connect (Pré-visualização)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra-lhe como configurar o Azure App Service ou Azure Functions para utilizar um fornecedor de autenticação personalizado que adere à [especificação OpenID Connect](https://openid.net/connect/). OpenID Connect (OIDC) é um padrão da indústria usado por muitos fornecedores de identidade (IDPs). Não é necessário compreender os detalhes da especificação para configurar a sua app para utilizar um IDP aderente.

Pode configurar a sua aplicação para utilizar um ou mais fornecedores OIDC. Cada um deve receber um nome único na configuração, e apenas um pode servir como o alvo de redirecionamento padrão.

> [!CAUTION]
> Permitir um prestador openID Connect irá desativar a gestão da funcionalidade de Autenticação/Autorização do Serviço de Aplicações para a sua aplicação através de alguns clientes, como o portal Azure CLI e a Azure PowerShell. A funcionalidade baseia-se numa nova superfície da API que, durante a pré-visualização, ainda não é contabilizada em todas as experiências de gestão.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Registe o seu pedido junto do fornecedor de identidade

O seu fornecedor exigirá que registe os detalhes da sua candidatura com a sua candidatura. Consulte as instruções relevantes para o fornecedor. Terá de recolher uma **identificação de cliente** e **um segredo** de cliente para a sua aplicação.

> [!IMPORTANT]
> O segredo da aplicação é uma importante credencial de segurança. Não partilhe este segredo com ninguém nem o distribua dentro de uma aplicação ao cliente.
>

> [!NOTE]
> Alguns fornecedores podem exigir passos adicionais para a sua configuração e como utilizar os valores que fornecem. Por exemplo, a Apple fornece uma chave privada que não é usada como o segredo do cliente OIDC, e em vez disso deve usá-lo para criar um JWT que é tratado como o segredo que fornece na sua app config (ver a secção "Criar o Segredo do Cliente" do [Signo com documentação](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)da Apple)
>

Adicione o segredo do cliente como configuração de [aplicação](./configure-common.md#configure-app-settings) para a aplicação, usando um nome de definição à sua escolha. Tome nota deste nome para mais tarde.

Além disso, necessitará dos metadados OpenID Connect para o fornecedor. Isto é frequentemente exposto através de um [documento de metadados](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)de configuração , que é o URL do emitente do fornecedor sufixado com `/.well-known/openid-configuration` . Reúna este URL de configuração.

Se não conseguir utilizar um documento de metadados de configuração, terá de recolher separadamente os seguintes valores:

- O URL do emitente (às vezes mostrado `issuer` como)
- [O ponto final de autorização OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-3.1) (por vezes apresentado como `authorization_endpoint` )
- [O ponto final de OAuth 2.0 Token](https://tools.ietf.org/html/rfc6749#section-3.2) (às vezes mostrado `token_endpoint` como)
- O URL do documento [OAuth 2.0 JSON Web Key set](https://tools.ietf.org/html/rfc8414#section-2) (às vezes mostrado como `jwks_uri` )

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Adicione informações do fornecedor à sua aplicação

> [!NOTE]
> A configuração requerida encontra-se num novo formato API, atualmente suportado apenas por [configuração baseada em ficheiros (pré-visualização)](.\app-service-authentication-how-to.md#config-file). Terá de seguir os passos abaixo utilizando tal ficheiro.

Esta secção irá acompanhar-te através da atualização da configuração para incluir o teu novo IDP. Segue-se uma configuração de exemplo.

1. Dentro do `identityProviders` objeto, adicione um `openIdConnectProviders` objeto se já não existir.
1. Dentro do `openIdConnectProviders` objeto, adicione uma chave para o seu novo fornecedor. Este é um nome amigável usado para fazer referência ao fornecedor no resto da configuração. Por exemplo, se quisesse exigir que todos os pedidos fossem autenticados com este fornecedor, definiria `globalValidation.unauthenticatedClientAction` para "RedirectToLoginPage" e `redirectToProvider` definiria para este mesmo nome amigável.
1. Atribua um objeto a essa chave com um `registration` objeto dentro dela, e opcionalmente um `login` objeto:
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scope": [],
             "loginParameterNames": [],
       }
    }
    ```

1. Dentro do objeto de registo, defina `clientId` o ID do cliente que recolheu, defina `clientCredential.secretSettingName` o nome da configuração da aplicação onde guardou o segredo do cliente e crie um `openIdConnectConfiguration` objeto:

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. Dentro do `openIdConnectConfiguration` objeto, forneça os metadados OpenID Connect recolhidos anteriormente. Existem duas opções para isso, com base nas informações que recolheu:

    - Defina a `wellKnownOpenIdConfiguration` propriedade para o URL de metadados de configuração que recolheu anteriormente.
    - Em alternativa, dedi o conjunto dos quatro valores individuais recolhidos da seguinte forma:
        - Definir `issuer` para o URL do emitente
        - Definir `authorizationEndpoint` para a autorização Endpoint
        - Definido `tokenEndpoint` para o ponto final simbólico
        - Definido `certificationUri` para o URL do documento do conjunto de chaves web JSON

    Estas duas opções são mutuamente exclusivas.

Uma vez definida esta configuração, está pronto a utilizar o seu fornecedor OpenID Connect para autenticação na sua aplicação.

Uma configuração de exemplo pode parecer o seguinte (usando o Sign in com a Apple como exemplo, onde o APPLE_GENERATED_CLIENT_SECRET pontos de definição para um JWT gerado de acordo com [a documentação da Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scope": [],
                    "loginParameterNames": []
                }
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Passos seguintes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

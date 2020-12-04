---
title: Configurar iniciar sação com a Apple (Pré-visualização)
description: Saiba como configurar iniciar sômito com a Apple como fornecedor de identidade para o seu Serviço de Aplicações ou app Azure Functions.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603362"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>Configure o seu Serviço de Aplicações ou app Azure Functions para iniciar sôms usando um Sign in com o fornecedor Apple (Pré-visualização)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra-lhe como configurar o Azure App Service ou Azure Functions para utilizar o Iniciar sação com a Apple como fornecedor de autenticação. 

Para completar o procedimento neste artigo, deve ter-se inscrito no programa de desenvolvimento da Apple. Para se inscrever no programa de desenvolvimento da Apple, vá a [developer.apple.com/programs/enroll](https://developer.apple.com/programs/enroll/).

> [!CAUTION]
> Ativar o Login com a Apple irá desativar a gestão da funcionalidade de Autenticação/Autorização do Serviço de Aplicações para a sua aplicação através de alguns clientes, como o portal Azure CLI e a Azure PowerShell. A funcionalidade baseia-se numa nova superfície da API que, durante a pré-visualização, ainda não é contabilizada em todas as experiências de gestão.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Criar uma aplicação no portal Apple Developer
Terá de criar um ID de aplicações e um ID de serviço no portal Apple Developer.

1. No portal Apple Developer, aceda a **Certificados, Identificadores, perfis de &.**
2. No **separador Identifiers,** selecione o botão **(+).**
3. Na página **Registar um Novo Identificador,** escolha **IDs de aplicação** e selecione **Continue**. (Os IDs de aplicativo incluem um ou mais IDs de serviço.) ![Registar um novo identificador de aplicações no Portal do Programador da Apple](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. Na página **Registar uma página de ID da aplicação,** forneça uma descrição e um iD de pacote, e selecione Iniciar **sôs com** a Apple na lista de capacidades. Em seguida, selecione **Continuar**. Tome nota do **prefixo ID da app (Team ID)** a partir deste passo, vai precisar dele mais tarde.
![Configurar um novo identificador de aplicações no Portal do Programador da Apple](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. Reveja as informações de registo da aplicação e **selecione Register.**
6. Novamente, no **separador Identifiers,** selecione o botão **(+).**
![Criar um novo identificador de serviços no Portal do Programador da Apple](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. Na página **Registar um Novo Identificador,** escolha **IDs de Serviços** e selecione **Continue**.
![Registar um novo identificador de serviços no Portal do Programador da Apple](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. Na página **Registar um ID de serviços,** forneça uma descrição e um identificador. A descrição é o que será mostrado ao utilizador no ecrã de consentimento. O identificador será o seu ID do cliente utilizado na configuração do fornecedor da Apple com o seu serviço de aplicações. Em seguida, **selecione Configure**.
![Fornecendo uma descrição e um identificador](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. Na janela pop-up, defina o ID da Aplicação Primária para o Id da Aplicação que criou anteriormente. Especifique o domínio da sua aplicação na secção de domínio. Para o URL de retorno, utilize o URL `<app-url>/.auth/login/apple/callback` . Por exemplo, `https://contoso.azurewebsites.net/.auth/login/apple/callback`. Em seguida, **selecione Adicionar** e **Guardar**.
![Especificando o domínio e URL de devolução para o registo](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. Reveja as informações de registo do serviço e **selecione Save.**

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>Gerar o segredo do cliente
A Apple exige que os desenvolvedores de aplicações criem e assinem um token JWT como valor secreto do cliente. Para gerar este segredo, primeiro gere e descarregue uma chave privada curva elíptica a partir do portal Apple Developer. Em seguida, utilize a chave para [assinar um JWT](#sign-the-client-secret-jwt) com uma [carga útil específica](#structure-the-client-secret-jwt).

### <a name="create-and-download-the-private-key"></a>Criar e baixar a chave privada
1. No separador **Chaves** no portal Apple Developer, escolha **Criar uma chave** ou selecionar o botão **(+).**
2. Na página **Registar uma nova chave** dê um nome à chave, verifique a caixa ao lado de Iniciar **sação com a Apple** e selecione **Configure**.
3. Na página **Chave configurar,** ligue a chave ao ID da aplicação primária que criou anteriormente e selecione **Save**.
4. Termine de criar a chave confirmando as informações e selecionando **Continue** e, em seguida, reveja as informações e selecione **Registar.**
5. Na página Baixar A **Chave,** descarregue a chave. Será descarregado como um `.p8` ficheiro (PKCS#8) - utilizará o conteúdo do ficheiro para assinar o JWT secreto do seu cliente.

### <a name="structure-the-client-secret-jwt"></a>Estruturar o segredo do cliente JWT
A Apple exige que o segredo do cliente seja a codificação base de um símbolo JWT. O token JWT descodificado deve ter uma carga útil estruturada como este exemplo:
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **sub**: O ID do Cliente apple (também o ID de serviço)
- **iss**: Your Apple Developer Team ID
- **aud**: Apple está recebendo o símbolo, então eles são o público
- **exp**: Não mais de seis meses após **a NBF**

A versão codificada de base64 da carga útil acima é assim: ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_Nota: A Apple não aceita JWTs secretos do cliente com uma data de validade mais de seis meses após a data de criação (ou nbf). Isso significa que terás de rodar o segredo do teu cliente, no mínimo, a cada seis meses._

Mais informações sobre a geração e validação de fichas podem ser encontradas na [documentação do desenvolvedor da Apple.](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens) 

### <a name="sign-the-client-secret-jwt"></a>Assine o segredo do cliente JWT
Você usará o `.p8` ficheiro que descarregou anteriormente para assinar o segredo do cliente JWT. Este ficheiro é um [ficheiro PCKS#8](https://en.wikipedia.org/wiki/PKCS_8) que contém a chave de assinatura privada no formato PEM. Há muitas bibliotecas que podem criar e assinar o JWT para si. 

Existem diferentes tipos de bibliotecas de código aberto disponíveis online para criar e assinar fichas JWT. Para obter mais informações sobre a geração de fichas JWT, consulte jwt.io. Por exemplo, uma forma de gerar o segredo do cliente é importando o [pacote Microsoft.IdentityModel.Tokens NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) e executando uma pequena quantidade de código C# mostrado abaixo.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamId**: O seu ID da equipa de desenvolvedores da Apple
- **clientId**: O ID do Cliente da Apple (também o ID do serviço)
- **p8key**: A tecla de formato PEM - pode obter a chave abrindo o `.p8` ficheiro num editor de texto e copiando tudo entre e sem `-----BEGIN PRIVATE KEY-----` `-----END PRIVATE KEY-----` quebras de linha
- **keyId**: O ID da chave descarregada

Este token devolvido é o valor secreto do cliente que você usará para configurar o fornecedor apple.

> [!IMPORTANT]
> O segredo do cliente é uma importante credencial de segurança. Não partilhe este segredo com ninguém nem o distribua dentro de uma aplicação ao cliente.
>

Adicione o segredo do cliente como configuração de [aplicação](./configure-common.md#configure-app-settings) para a aplicação, usando um nome de definição à sua escolha. Tome nota deste nome para mais tarde.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Adicione informações do fornecedor à sua aplicação

> [!NOTE]
> A configuração requerida encontra-se num novo formato API, atualmente suportado apenas por [configuração baseada em ficheiros (pré-visualização)](.\app-service-authentication-how-to.md#config-file). Terá de seguir os passos abaixo utilizando tal ficheiro.

Esta secção irá acompanhar-te através da atualização da configuração para incluir o teu novo IDP. Segue-se uma configuração de exemplo.

1. Dentro do `identityProviders` objeto, adicione um `apple` objeto se já não existir.
2. Atribua um objeto a essa chave com um `registration` objeto dentro dela, e opcionalmente um `login` objeto:
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. Dentro do `registration` objeto, desajei `clientId` a identificação do cliente que recolheu.
    
    b. Dentro do `registration` objeto, definido `clientSecretSettingName` para o nome da definição de aplicação onde guardou o segredo do cliente.
    
    c. Dentro do `login` objeto, pode optar por definir a `scopes` matriz para incluir uma lista de âmbitos utilizados na autenticação com a Apple, como "nome" e "email". Se os âmbitos estiverem configurados, serão explicitamente solicitados no ecrã de consentimento quando os utilizadores iniciarem sedudo pela primeira vez.

Uma vez definida esta configuração, está pronto para utilizar o seu fornecedor Apple para autenticação na sua aplicação.

Uma configuração completa pode parecer o exemplo seguinte (onde o APPLE_GENERATED_CLIENT_SECRET pontos de definição para uma definição de aplicação contendo um JWT gerado):

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
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
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

## <a name="next-steps"></a><a name="related-content"> </a>Próximos passos

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

---
title: Configurar o fluxo de credenciais de palavra-passe de proprietário do recurso no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como configurar o fluxo de credenciais de palavra-passe de proprietário do recurso no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 265f1405a8779cdca101f18cf37b64f1933c2ff3
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400750"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Configurar o recurso proprietário palavra-passe fluxo de credenciais no Azure Active Directory B2C utilizando uma política personalizada

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

No Azure Active Directory (Azure AD) B2C, o fluxo de credenciais (ROPC) de palavra-passe de proprietário do recurso é um fluxo de autenticação padrão do OAuth. Neste fluxo, um aplicativo, também conhecido como a entidade confiadora, troca de credenciais válidas para tokens. As credenciais de incluem um ID de utilizador e palavra-passe. Os tokens devolvidos são um token de ID, o token de acesso e um token de atualização.

O fluxo ROPC são suportadas as seguintes opções:

- **Cliente nativo** -interação do usuário durante a autenticação ocorre quando o código é executado num dispositivo do lado do usuário.
- **Fluxo de cliente público** -apenas o utilizador as credenciais que são recolhidas por um aplicativo são enviados na chamada de API. As credenciais do aplicativo não são enviadas.
- **Adicionar nova afirmações** -conteúdo o ID de token pode ser alterada para adicionar nova afirmações.

Não são suportados os seguintes fluxos:

- **Para-servidor** -o sistema de proteção de identidade tem um endereço IP fiável recolhido do chamador (o cliente nativo) como parte da interação. Numa chamada de API do lado do servidor, é utilizado apenas o endereço IP do servidor. Se inícios de sessão demasiadas falharem, o sistema de proteção de identidade pode examinar um endereço IP repetido como um atacante.
- **A única aplicação de página** -uma aplicação de front-end é principalmente escrito em JavaScript. Muitas vezes, o aplicativo é escrito usando uma estrutura como AngularJS, ember ou Durandal.
- **Fluxo de cliente confidencial** - o ID de cliente da aplicação é validado, mas o segredo de aplicação não é.

## <a name="prerequisites"></a>Pré-requisitos

Conclua os passos na [introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registar uma aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Add**.
5. Introduza um nome para a aplicação, tal como *ROPC_Auth_app*.
6. Selecione **não** para **API da Web/aplicação Web**e, em seguida, selecione **Sim** para **cliente nativo**.
7. Deixe todos os outros valores que são e, em seguida, selecionam **criar**.
8. Selecione a nova aplicação e registe o ID da aplicação para utilização posterior.

##  <a name="create-a-resource-owner-policy"></a>Criar uma política de proprietário do recurso

1. Abra o *TrustFrameworkExtensions.xml* ficheiro.
2. Se ainda não exista, adicione uma **ClaimsSchema** elemento e seus elementos filho, como o primeiro elemento sob o **BuildingBlocks** elemento:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. Após **ClaimsSchema**, adicione um **ClaimsTransformations** elemento e seus elementos subordinados para o **BuildingBlocks** elemento:

    ```XML
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>
    
      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Localize a **ClaimsProvider** elemento que tenha um **DisplayName** de `Local Account SignIn` e adicionar o perfil técnico a seguir:

    ```XML
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="00000000-0000-0000-0000-000000000000" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="00000000-0000-0000-0000-000000000000" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Substitua a **DefaultValue** dos **client_id** e **resource_id** com o ID da aplicação do aplicativo ProxyIdentityExperienceFramework que criou no tutorial de pré-requisitos.

5. Adicionar seguintes **ClaimsProvider** elementos com os seus perfis técnicos para o **ClaimsProviders** elemento:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. Adicionar uma **UserJourneys** elemento e seus elementos subordinados para o **TrustFrameworkPolicy** elemento:

    ```XML
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. Sobre o **políticas personalizadas** página no seu inquilino do Azure AD B2C, selecione **carregar política**.
8. Ativar **substituir a política, se existir**e, em seguida, procure e selecione o *TrustFrameworkExtensions.xml* ficheiro.
9. Clique em **Carregar**.

## <a name="create-a-relying-party-file"></a>Crie um ficheiro da entidade confiadora de terceiros

Em seguida, atualize o ficheiro da entidade confiadora de terceiros que inicia o percurso do utilizador que criou:

1. Faça uma cópia deles *SignUpOrSignin.xml* do ficheiro no seu diretório de trabalho e mude o nome para *ROPC_Auth.xml*.
2. Abra o ficheiro novo e alterar o valor do **PolicyId** atributo **TrustFrameworkPolicy** para um valor exclusivo. O ID de política é o nome da sua política. Por exemplo, **B2C_1A_ROPC_Auth**.
3. Alterar o valor do **ReferenceId** atributo **DefaultUserJourney** para `ResourceOwnerPasswordCredentials`.
4. Alteração da **OutputClaims** elemento para conter apenas as seguintes declarações:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Sobre o **políticas personalizadas** página no seu inquilino do Azure AD B2C, selecione **carregar política**.
6. Ativar **substituir a política, se existir**e, em seguida, procure e selecione o *TrustFrameworkExtensions.xml* ficheiro.
7. Clique em **Carregar**.

## <a name="test-the-policy"></a>Testar a política

Utilize a sua aplicação de desenvolvimento de API favorita para gerar uma chamada à API e rever a resposta para depurar a sua política. Construa uma chamada como este exemplo com as seguintes informações como o corpo da solicitação POST:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Substitua `your-tenant-name` com o nome do seu inquilino do Azure AD B2C.
- Substitua `B2C_1A_ROPC_Auth` com o nome completo da sua política de credenciais de palavra-passe de proprietário do recurso.

| Chave | Value |
| --- | ----- |
| o nome de utilizador | `user-account` |
| palavra-passe | `password1` |
| grant_type | palavra-passe |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- Substitua `user-account` com o nome de uma conta de utilizador no seu inquilino.
- Substitua `password1` com a palavra-passe da conta de utilizador.
- Substitua `application-id` com o ID de aplicação a partir de *ROPC_Auth_app* registo.
- *Offline_access* é opcional, se pretender receber um token de atualização.

O pedido POST real terá um aspeto semelhante ao seguinte exemplo:

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Uma resposta com êxito com acesso offline terá um aspeto semelhante ao seguinte exemplo:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Utilizar um token de atualização

Construa uma chamada POST, como o mostrado aqui. Utilize as informações na tabela seguinte como o corpo do pedido:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Substitua `your-tenant-name` com o nome do seu inquilino do Azure AD B2C.
- Substitua `B2C_1A_ROPC_Auth` com o nome completo da sua política de credenciais de palavra-passe de proprietário do recurso.

| Chave | Value |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| Recurso | `application-id` |
| refresh_token | `refresh-token` |

- Substitua `application-id` com o ID de aplicação a partir de *ROPC_Auth_app* registo.
- Substitua `refresh-token` com o **refresh_token** que foi enviada de volta na resposta anterior.

Uma resposta com êxito, terá um aspeto semelhante ao seguinte exemplo:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Utilizar um SDK ou autenticação da aplicação nativa

O Azure AD B2C atende aos padrões de OAuth 2.0 para credenciais de palavra-passe do proprietário de recursos do cliente público e deve ser compatível com a maioria dos SDKs de cliente. Para obter as informações mais recentes, consulte [SDK da aplicação nativa para OAuth 2.0 e OpenID Connect implementando práticas recomendadas modernos](https://appauth.io/).

## <a name="next-steps"></a>Passos Seguintes

- Veja um exemplo completo deste cenário na [pacote de iniciante de política personalizada do Azure Active Directory B2C](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc).
- Saiba mais sobre os tokens que são utilizados pelo Azure Active Directory B2C no [referência de Token](active-directory-b2c-reference-tokens.md).

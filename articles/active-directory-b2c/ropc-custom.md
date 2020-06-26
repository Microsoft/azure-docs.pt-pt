---
title: Configure o fluxo de credenciais de palavra-passe do proprietário do recurso com políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como configurar o fluxo de credenciais de senha do proprietário do recurso (ROPC) utilizando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 03533bba588560f9c0058631d35508157f8bec5e
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85385370"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Configure as credenciais de senha do proprietário do recurso fluem no Azure Ative Directory B2C usando uma política personalizada

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

No Azure Ative Directory B2C (Azure AD B2C), o fluxo de credenciais de senha do proprietário do recurso (ROPC) é um fluxo de autenticação padrão OAuth. Neste fluxo, uma aplicação, também conhecida como a parte dependente, troca credenciais válidas por fichas. As credenciais incluem um ID do utilizador e uma senha. Os tokens devolvidos são um símbolo de identificação, ficha de acesso e um token refrescante.

[!INCLUDE [active-directory-b2c-ropc-notes](../../includes/active-directory-b2c-ropc-notes.md)]

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Começar com políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Registar uma aplicação

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

##  <a name="create-a-resource-owner-policy"></a>Criar uma política de proprietário de recursos

1. Abra o ficheiro *TrustFrameworkExtensions.xml.*
2. Se já não existir, adicione um elemento **ClaimsSchema** e os seus elementos infantis como o primeiro elemento sob o elemento **BuildingBlocks:**

    ```xml
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

3. Após **reclamaçõesSchema,** adicione um elemento **de SinistrosTransformações** e seus elementos infantis ao elemento **Blocos de Construção:**

    ```xml
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

4. Localize o elemento **ClaimsProvider** que tem um **DisplayName** `Local Account SignIn` e adicione o seguinte perfil técnico:

    ```xml
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

    Substitua o **DefaultValue** de **client_id** pelo ID de aplicação da aplicação ProxyIdentityExperienceFramework que criou no tutorial pré-requisito. Em seguida, **substitua o DefaultValue** of **resource_id** pelo ID de aplicação da aplicação IdentityExperienceFramework que também criou no tutorial pré-requisito.

5. Adicione os seguintes elementos **DoProvider de Reclamações** com os seus perfis técnicos ao elemento **ClaimsProviders:**

    ```xml
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

6. Adicione um elemento **UserJourneys** e os seus elementos infantis ao elemento **TrustFrameworkPolicy:**

    ```xml
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

7. Na página **'Políticas Personalizadas'** no seu inquilino Azure AD B2C, selecione **'Política de Upload'.**
8. Ativar **a política em caso de existência**e, em seguida, navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
9. Clique em **Carregar**.

## <a name="create-a-relying-party-file"></a>Criar um ficheiro de partido de confiante

Em seguida, atualize o ficheiro do partido que inicia a jornada do utilizador que criou:

1. Faça uma cópia do *ficheiroSignUpOrSignin.xml* no seu diretório de trabalho e rebatize-o para *ROPC_Auth.xml*.
2. Abra o novo ficheiro e altere o valor do atributo **PolicyId** para **a TrustFrameworkPolicy** para um valor único. A identificação da apólice é o nome da sua apólice. Por exemplo, **B2C_1A_ROPC_Auth.**
3. Alterar o valor do atributo **ReferenceId** em **DefaultUserJourney** para `ResourceOwnerPasswordCredentials` .
4. Alterar o elemento **OutputClaims** para conter apenas as seguintes alegações:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Na página **'Políticas Personalizadas'** no seu inquilino Azure AD B2C, selecione **'Política de Upload'.**
6. Ativar **a política em caso de existência**e, em seguida, navegar e selecionar o ficheiro *ROPC_Auth.xml.*
7. Clique em **Carregar**.

## <a name="test-the-policy"></a>Testar a política

Use a sua aplicação de desenvolvimento API favorita para gerar uma chamada de API e reveja a resposta para depurar a sua política. Construa uma chamada como este exemplo com as seguintes informações como o corpo do pedido DOM:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_ROPC_Auth/oauth2/v2.0/token`

- `<tenant-name>`Substitua-o pelo nome do seu inquilino Azure AD B2C.
- `B2C_1A_ROPC_Auth`Substitua-a pelo nome completo da política de credenciais de senha do seu proprietário de recursos.

| Chave | Valor |
| --- | ----- |
| o nome de utilizador | `user-account` |
| palavra-passe | `password1` |
| grant_type | palavra-passe |
| scope | offline_access aberto `application-id` |
| client_id | `application-id` |
| response_type | id_token simbólico |

- `user-account`Substitua-o pelo nome de uma conta de utilizador no seu inquilino.
- `password1`Substitua-a pela palavra-passe da conta de utilizador.
- `application-id`Substitua-se pelo ID do registo de *ROPC_Auth_app.*
- *Offline_access* é opcional se quiser receber um token de atualização.

O pedido do POST real parece ser o seguinte exemplo:

```https
POST /<tenant-name>.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Uma resposta bem sucedida com acesso offline parece ser o seguinte exemplo:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Resgatar um token refresh

Construa uma chamada post como a mostrada aqui. Utilize as informações no quadro seguinte como o corpo do pedido:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_ROPC_Auth/oauth2/v2.0/token`

- `<tenant-name>`Substitua-o pelo nome do seu inquilino Azure AD B2C.
- `B2C_1A_ROPC_Auth`Substitua-a pelo nome completo da política de credenciais de senha do seu proprietário de recursos.

| Chave | Valor |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| recurso | `application-id` |
| refresh_token | `refresh-token` |

- `application-id`Substitua-se pelo ID do registo de *ROPC_Auth_app.*
- `refresh-token`Substitua-se pela **refresh_token** que foi devolvida na resposta anterior.

Uma resposta bem sucedida parece ser o seguinte exemplo:

```json
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

## <a name="use-a-native-sdk-or-app-auth"></a>Use um SDK nativo ou App-Auth

O Azure AD B2C cumpre os padrões OAuth 2.0 para credenciais de senha do proprietário de recursos públicos e deve ser compatível com a maioria dos SDKs do cliente. Para obter as informações mais recentes, consulte [Native App SDK para OAuth 2.0 e OpenID Connect implementando as melhores práticas modernas.](https://appauth.io/)

## <a name="next-steps"></a>Passos seguintes

- Veja um exemplo completo deste cenário no pacote de [arranque de política personalizada Azure Ative Directory B2C](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc).
- Saiba mais sobre os tokens que são usados pelo Azure Ative Directory B2C na [referência Token](tokens-overview.md).

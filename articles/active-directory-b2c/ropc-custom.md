---
title: Configure o fluxo de credenciais de senha do proprietário de recursos com políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como configurar o fluxo de credenciais de senha do proprietário de recursos (ROPC) utilizando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 95601735064451a91530907e5e6b59f579ff0e28
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840269"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Configure as credenciais de senha do proprietário de recursos fluem no Azure Ative Directory B2C usando uma política personalizada

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

No Azure Ative Directory B2C (Azure AD B2C), o fluxo de credenciais de senha do proprietário de recursos (ROPC) é um fluxo de autenticação padrão OAuth. Neste fluxo, uma aplicação, também conhecida como parte de fideiro, troca credenciais válidas para fichas. As credenciais incluem uma identificação do utilizador e uma senha. Os símbolos devolvidos são um símbolo de identificação, ficha de acesso e um token refrescante.

As seguintes opções são suportadas no fluxo ROPC:

- **Cliente Nativo** - A interação do utilizador durante a autenticação ocorre quando o código é executado num dispositivo do lado do utilizador.
- **Fluxo** público de clientes - Apenas as credenciais de utilizador recolhidas por uma aplicação são enviadas na chamada DaPI. As credenciais do pedido não são enviadas.
- **Adicione novas reclamações** - O conteúdo do token ID pode ser alterado para adicionar novas reclamações.

Os seguintes fluxos não são suportados:

- **Servidor-a-servidor** - O sistema de proteção de identidade precisa de um endereço IP fiável recolhido do chamador (o cliente nativo) como parte da interação. Numa chamada API do lado do servidor, apenas é utilizado o endereço IP do servidor. Se muitas inscrições falharem, o sistema de proteção de identidade pode olhar para um endereço IP repetido como um intruso.
- **Aplicação** de página única - Uma aplicação frontal que está escrita principalmente no JavaScript. Muitas vezes, a aplicação é escrita usando uma estrutura como AngularJS, Ember.js ou Durandal.
- **Fluxo de cliente confidencial** - O ID do cliente da aplicação é validado, mas o segredo da aplicação não é.

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Get started com políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Registar uma aplicação

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

##  <a name="create-a-resource-owner-policy"></a>Criar uma política de proprietário de recursos

1. Abra o ficheiro *TrustFrameworkExtensions.xml.*
2. Se já não existir, adicione um elemento **ClaimsSchema** e os seus elementos infantis como o primeiro elemento sob o elemento **BuildingBlocks:**

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

3. Depois de **ClaimsSchema,** adicione um elemento **DeTransformações de Reclamações** e seus elementos infantis ao elemento **BuildingBlocks:**

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

4. Localize o elemento **ClaimsProvider** que tem um Nome de **visualização** de `Local Account SignIn` e adicione o seguinte perfil técnico:

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

    Substitua o **Valor Padrão** de **client_id** pelo ID de aplicação da aplicação ProxyIdentityExperienceFramework que criou no tutorial pré-requisito. Em seguida, substitua **o DefaultValue** of **resource_id** com o ID de aplicação da aplicação IdentityExperienceFramework que também criou no tutorial pré-requisito.

5. Adicione os seguintes elementos **Do Fornecedor de Sinistros** com os seus perfis técnicos ao elemento **ClaimsProviders:**

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

6. Adicione um elemento **UserJourneys** e seus elementos infantis ao elemento **TrustFrameworkPolicy:**

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

7. Na página **Políticas Personalizadas** do seu inquilino Azure AD B2C, selecione **Política de Upload**.
8. Ativar **a sobreposição da apólice se ela existir,** e depois navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
9. Clique em **Carregar**.

## <a name="create-a-relying-party-file"></a>Criar um ficheiro de festa de base

Em seguida, atualize o ficheiro do partido que inicia a viagem de utilizador que criou:

1. Faça uma cópia do ficheiro *SignUpOrSignin.xml* no seu diretório de trabalho e mude o nome para *ROPC_Auth.xml*.
2. Abra o novo ficheiro e altere o valor do atributo **PolicyId** para **TrustFrameworkPolicy** para um valor único. A identificação da apólice é o nome da sua apólice. Por exemplo, **B2C_1A_ROPC_Auth.**
3. Alterar o valor do atributo **ReferenceId** no **DefaultUserJourney** para `ResourceOwnerPasswordCredentials`.
4. Alterar o elemento **OutputClaims** para conter apenas as seguintes reclamações:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Na página **Políticas Personalizadas** do seu inquilino Azure AD B2C, selecione **Política de Upload**.
6. Ativar **A sobreposição da apólice se ela existir,** e depois navegar e selecionar o ficheiro *ROPC_Auth.xml.*
7. Clique em **Carregar**.

## <a name="test-the-policy"></a>Testar a política

Use a sua aplicação de desenvolvimento aPi favorita para gerar uma chamada API e reveja a resposta para desinserm a sua política. Construa uma chamada como este exemplo com as seguintes informações como o corpo do pedido do POST:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Substitua `your-tenant-name` com o nome do seu inquilino do Azure AD B2C.
- Substitua `B2C_1A_ROPC_Auth` com o nome completo da sua política de credenciais de senha do proprietário de recursos.

| Chave | Valor |
| --- | ----- |
| o nome de utilizador | `user-account` |
| palavra-passe | `password1` |
| grant_type | palavra-passe |
| scope | `application-id` offline_access aberto |
| client_id | `application-id` |
| response_type | id_token simbólico |

- Substitua `user-account` com o nome de uma conta de utilizador no seu inquilino.
- Substitua `password1` com a palavra-passe da conta de utilizador.
- Substitua `application-id` com o ID de inscrição do *registo ROPC_Auth_app.*
- *Offline_access* é opcional se quiser receber um token refrescante.

O pedido post real parece ser o seguinte exemplo:

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Uma resposta bem sucedida com acesso offline parece o seguinte exemplo:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Resgatar um token refrescante

Construa uma chamada postacomo a mostrada aqui. Utilize as informações no quadro seguinte como o corpo do pedido:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Substitua `your-tenant-name` com o nome do seu inquilino do Azure AD B2C.
- Substitua `B2C_1A_ROPC_Auth` com o nome completo da sua política de credenciais de senha do proprietário de recursos.

| Chave | Valor |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- Substitua `application-id` com o ID de inscrição do *registo ROPC_Auth_app.*
- Substitua `refresh-token` pelo **refresh_token** que foi devolvido na resposta anterior.

Uma resposta bem sucedida parece o seguinte exemplo:

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

## <a name="use-a-native-sdk-or-app-auth"></a>Use um SDK nativo ou App-Auth

O Azure AD B2C cumpre os padrões OAuth 2.0 para credenciais de senha do proprietário de recursos públicos e deve ser compatível com a maioria dos SDKs dos clientes. Para obter as mais recentes informações, consulte [Native App SDK para OAuth 2.0 e OpenID Connect implementando as melhores práticas modernas.](https://appauth.io/)

## <a name="next-steps"></a>Passos seguintes

- Veja um exemplo completo deste cenário no pacote de arranque de [política personalizada Azure Ative Directory B2C](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc).
- Saiba mais sobre as fichas que são usadas pelo Azure Ative Directory B2C na [referência Token](tokens-overview.md).

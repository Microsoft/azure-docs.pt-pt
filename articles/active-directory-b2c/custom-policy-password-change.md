---
title: Configure a alteração da palavra-passe usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como permitir que os utilizadores alterem a sua palavra-passe utilizando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2c351f8a95110a32c53c68c5eb6095918578bc5b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189179"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Configure a alteração da palavra-passe utilizando políticas personalizadas no Diretório Ativo Do Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Ative Directory B2C (Azure AD B2C), pode permitir aos utilizadores que estão inscritos numa conta local alterar a sua palavra-passe sem terem de provar a sua autenticidade através da verificação de email. Se a sessão expirar quando o utilizador chegar ao fluxo de alteração da palavra-passe, é solicitado que volte a iniciar sessão. Este artigo mostra-lhe como configurar a mudança de palavra-passe em [políticas personalizadas.](custom-policy-overview.md) Também é possível configurar o [reset de palavra-passe self-service](user-flow-self-service-password-reset.md) para fluxos de utilizador.

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Get started com políticas personalizadas no Diretório Ativo B2C](custom-policy-get-started.md).

## <a name="add-the-elements"></a>Adicione os elementos

1. Abra o ficheiro *TrustframeworkExtensions.xml* e adicione o seguinte elemento **ClaimType** com um identificador de `oldPassword` ao elemento [ClaimsSchema:](claimsschema.md)

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Um elemento [ClaimsProvider](claimsproviders.md) contém o perfil técnico que autentica o utilizador. Adicione os seguintes fornecedores de sinistros ao elemento **ClaimsProviders:**

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Substitua `IdentityExperienceFrameworkAppId` com o ID de aplicação da aplicação IdentityExperienceFramework que criou no tutorial pré-requisito. Substitua `ProxyIdentityExperienceFrameworkAppId` com o ID de aplicação da aplicação ProxyIdentityExperienceFramework que também criou anteriormente.

3. O elemento [UserJourney](userjourneys.md) define o caminho que o utilizador toma ao interagir com a sua aplicação. Adicione o elemento **UserJourneys** se não existir com a **UserJourney** identificada como `PasswordChange`:

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Guarde o ficheiro de política *TrustFrameworkExtensions.xml.*
5. Copie o ficheiro *ProfileEdit.xml* que descarregou com o pacote de arranque e nomeie-o *ProfileEditPasswordChange.xml*.
6. Abra o novo ficheiro e atualize o atributo **PolicyId** com um valor único. Este valor é o nome da sua política. Por exemplo, *B2C_1A_profile_edit_password_change.*
7. Modifique o atributo **ReferenceId** em `<DefaultUserJourney>` para corresponder ao ID da nova viagem de utilizador que criou. Por exemplo, *PasswordChange*.
8. Guarde as alterações.

Pode encontrar a política da amostra [aqui.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)

## <a name="test-your-policy"></a>Teste a sua política

Ao testar as suas aplicações em Azure AD B2C, pode ser útil que o token Azure AD B2C seja devolvido à `https://jwt.ms` para poder rever as reclamações nele.

### <a name="upload-the-files"></a>Faça upload dos ficheiros

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione Quadro de **Experiência de Identidade**.
5. Na página Políticas Personalizadas, clique na **Política de Upload**.
6. **Selecione Sobrepor a apólice se ela existir**, e depois procurar e selecionar o ficheiro *TrustframeworkExtensions.xml.*
7. Clique em **Carregar**.
8. Repita os passos 5 a 7 para o ficheiro de parte de fiação, como *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Executar a política

1. Abre a política que mudaste. Por exemplo, *B2C_1A_profile_edit_password_change.*
2. Para **Aplicação,** selecione a sua aplicação que registou anteriormente. Para ver o símbolo, o **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar agora**. Inscreva-se com o aveia que criou anteriormente. Agora deve ter a oportunidade de alterar a senha.

## <a name="next-steps"></a>Passos seguintes

- Saiba como pode configurar a complexidade da [palavra-passe utilizando políticas personalizadas no Diretório Ativo Azure B2C](custom-policy-password-complexity.md).

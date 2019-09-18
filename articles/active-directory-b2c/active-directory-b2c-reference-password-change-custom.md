---
title: Configurar a alteração de senha usando políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: Saiba como permitir que os usuários alterem sua senha usando políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0775920e1d6572223253edbfc066123a515b5480
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065543"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a alteração de senha usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Active Directory B2C (Azure AD B2C), você pode habilitar os usuários que entraram com uma conta local para alterar a senha sem precisar provar sua autenticidade por verificação de email. Se a sessão expirar até o momento em que o usuário chegar ao fluxo de alteração de senha, ele será solicitado a entrar novamente. Este artigo mostra como configurar a alteração de senha em [políticas personalizadas](active-directory-b2c-overview-custom.md). Também é possível configurar a [redefinição de senha de autoatendimento](active-directory-b2c-reference-sspr.md) para fluxos de usuário.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em introdução [às políticas personalizadas no Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Adicionar os elementos

1. Abra o arquivo *TrustframeworkExtensions. xml* e adicione o seguinte elemento **ClaimType** com um identificador de `oldPassword` para o elemento [ClaimsSchema](claimsschema.md) :

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

2. Um elemento [claimprovider](claimsproviders.md) contém o perfil técnico que autentica o usuário. Adicione os seguintes provedores de declarações ao elemento **ClaimsProviders** :

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

    Substitua `IdentityExperienceFrameworkAppId` pela ID do aplicativo IdentityExperienceFramework que você criou no tutorial de pré-requisito. Substitua `ProxyIdentityExperienceFrameworkAppId` pela ID do aplicativo ProxyIdentityExperienceFramework que você também criou anteriormente.

3. O elemento [userjornada](userjourneys.md) define o caminho que o usuário usa ao interagir com seu aplicativo. Adicione o elemento **Userjornadas** se ele não existir com o **userjornada** identificado como `PasswordChange`:

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

4. Salve o arquivo de política *TrustFrameworkExtensions. xml* .
5. Copie o arquivo *ProfileEdit. xml* que você baixou com o pacote inicial e nomeie-o como *ProfileEditPasswordChange. xml*.
6. Abra o novo arquivo e atualize o atributo **PolicyId** com um valor exclusivo. Esse valor é o nome da sua política. Por exemplo, *B2C_1A_profile_edit_password_change*.
7. Modifique o atributo **referenceid** em `<DefaultUserJourney>` para corresponder à ID da nova jornada do usuário que você criou. Por exemplo, *PasswordChange*.
8. Guarde as alterações.

Você pode encontrar a política de exemplo [aqui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).

## <a name="test-your-policy"></a>Testar sua política

Ao testar seus aplicativos no Azure ad B2C, pode ser útil ter o token de Azure ad B2C retornado `https://jwt.ms` para poder revisar as declarações nele.

### <a name="upload-the-files"></a>Carregar os arquivos

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **Identity Experience Framework**.
5. Na página políticas personalizadas, clique em **carregar política**.
6. Selecione **substituir a política, se ela existir**, e, em seguida, procure e selecione o arquivo *TrustframeworkExtensions. xml* .
7. Clique em **Carregar**.
8. Repita as etapas de 5 a 7 para o arquivo de terceira parte confiável, como *ProfileEditPasswordChange. xml*.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política que você alterou. Por exemplo, *B2C_1A_profile_edit_password_change*.
2. Para **aplicativo**, selecione seu aplicativo que você registrou anteriormente. Para ver o token, a **URL de resposta** deve `https://jwt.ms`ser mostrada.
3. Clique em **Executar agora**. Entre com o acouunt que você criou anteriormente. Agora você deve ter a oportunidade de alterar a senha.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como você pode [Configurar a complexidade de senha usando políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-reference-password-complexity-custom.md).

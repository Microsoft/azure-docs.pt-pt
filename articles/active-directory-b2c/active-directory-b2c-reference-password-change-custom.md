---
title: Configurar a alteração de palavra-passe com as políticas personalizadas no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como permitir que os utilizadores alterar a palavra-passe com as políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a9e58139d248daed19f4fe35c7d33ede9dfe64b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317195"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a alteração de palavra-passe com as políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Active Directory (Azure AD) B2C, pode ativar os utilizadores que têm sessão iniciados com uma conta local para alterar a palavra-passe sem ter de provar a sua autenticidade pela verificação do e-mail. Se a sessão expirar, o tempo que o utilizador obtém para a palavra-passe de fluxo de alteração, são-lhes pedido que inicie sessão novamente. Este artigo mostra-lhe como configurar a alteração de palavra-passe na [políticas personalizadas](active-directory-b2c-overview-custom.md). Também é possível configurar [reposição de palavra-passe self-service](active-directory-b2c-reference-sspr.md) para fluxos de utilizador.

## <a name="prerequisites"></a>Pré-requisitos

Conclua os passos na [introdução às políticas personalizadas no Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Adicionar os elementos 

1. Abra sua *TrustframeworkExtensions.xml* de ficheiros e adicione as seguintes **ClaimType** elemento com um identificador de `oldPassword` para o [ClaimsSchema](claimsschema.md) elemento: 

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

2. R [ClaimsProvider](claimsproviders.md) elemento contém o perfil técnico que autentica o utilizador. Adicione as seguintes afirmações provedores para o **ClaimsProviders** elemento:

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

    Substitua `IdentityExperienceFrameworkAppId` com o ID da aplicação do aplicativo IdentityExperienceFramework que criou no tutorial de pré-requisito. Substitua `ProxyIdentityExperienceFrameworkAppId` com o ID da aplicação do aplicativo ProxyIdentityExperienceFramework que criou anteriormente também.

3. O [UserJourney](userjourneys.md) elemento define o caminho que o utilizador tira ao interagir com a sua aplicação. Adicionar a **UserJourneys** elemento se não existir com o **UserJourney** identificado como `PasswordChange`:

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

4. Guardar a *TrustFrameworkExtensions.xml* ficheiro de política.
5. Copiar o *ProfileEdit.xml* que transferiu com o pacote de iniciante e um nome de ficheiro *ProfileEditPasswordChange.xml*.
6. Abra o ficheiro novo e a atualização do **PolicyId** atributo com um valor exclusivo. Este valor é o nome da sua política. Por exemplo, *B2C_1A_profile_edit_password_change*.
7. Modificar a **ReferenceId** atributo `<DefaultUserJourney>` de acordo com o ID do novo percurso do utilizador que criou. Por exemplo, *PasswordChange*.
8. Guarde as alterações.

Pode encontrar a política de exemplo [aqui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 

## <a name="test-your-policy"></a>Testar a política

Ao testar as suas aplicações no Azure AD B2C, pode ser útil ter o token do Azure AD B2C retornado para `https://jwt.ms` para conseguir analisar as afirmações no mesmo.

### <a name="upload-the-files"></a>Carregue os ficheiros

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **arquitetura de experiências de identidade**.
5. Na página de políticas personalizadas, clique em **carregar política**.
6. Selecione **substituir a política, se existir**e, em seguida, procure e selecione o *TrustframeworkExtensions.xml* ficheiro.
7. Clique em **Carregar**.
8. Repita os passos 5 a 7 para o ficheiro da entidade confiadora de terceiros, como *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política que alterou. Por exemplo, *B2C_1A_profile_edit_password_change*.
2. Para **aplicativo**, selecione a aplicação que registou anteriormente. Para ver o token, o **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar agora**. Inicie sessão com o acouunt que criou anteriormente. Agora, deve ter a oportunidade de alterar a palavra-passe. 

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como pode [configurar a complexidade de palavra-passe com as políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-reference-password-complexity-custom.md). 

---
title: Configurar a mudança de senha usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como permitir que os utilizadores alterem a sua palavra-passe utilizando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/17/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a42cb97d123d0943dab02bf1f70fcf306d6bcd96
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629136"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a mudança de senha usando políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Ative Directory B2C (Azure AD B2C), pode permitir que os utilizadores que se inscrevem com uma conta local alterem a sua palavra-passe sem terem de provar a sua autenticidade através de verificação de email. O fluxo de alteração da palavra-passe envolve os seguintes passos:

1. Inscreva-se com uma conta local. Se a sessão ainda estiver ativa, o Azure AD B2C autoriza o utilizador e salta para o passo seguinte.
1. Os utilizadores devem verificar a **senha antiga,** criar e confirmar a **nova palavra-passe**.

![Fluxo de mudança de senha](./media/add-password-change-policy/password-change-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

* Complete os passos em [Começar com políticas personalizadas no Ative Directory B2C.](custom-policy-get-started.md)
* Se ainda não o fez, [registe uma aplicação web no Azure Ative Directory B2C](tutorial-register-applications.md).

## <a name="add-the-elements"></a>Adicione os elementos

1. Abra o seu ficheiro *TrustframeworkExtensions.xml* e adicione o seguinte elemento **ClaimType** com um identificador do `oldPassword` elemento [ClaimsSchema:](claimsschema.md)

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Um elemento [ClaimsProvider](claimsproviders.md) contém o perfil técnico que autentica o utilizador. Adicione os seguintes fornecedores de sinistros ao elemento **ClaimsProviders:**

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
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

3. O elemento [UserJourney](userjourneys.md) define o caminho que o utilizador toma ao interagir com a sua aplicação. Adicione o elemento **UserJourneys** se não existir com a **Jornada do Utilizador** identificada `PasswordChange` como:

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
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
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Guarde o ficheiro de política *TrustFrameworkExtensions.xml.*
5. Copie o ficheiro *ProfileEdit.xml* que descarregou com o pacote inicial e nomeie-o *ProfileEditPasswordChange.xml*.
6. Abra o novo ficheiro e atualize o atributo **PolicyId** com um valor único. Este valor é o nome da sua apólice. Por exemplo, *B2C_1A_profile_edit_password_change.*
7. Modifique o atributo **ReferenceId** `<DefaultUserJourney>` para corresponder ao ID da nova jornada do utilizador que criou. Por exemplo, *PasswordChange*.
8. Guarde as alterações.

## <a name="upload-and-test-the-policy"></a>Faça upload e teste da política

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **o Quadro de Experiência de Identidade.**
5. Na página 'Políticas Personalizadas', clique em **'Carregar' Política**.
6. Selecione **Overwrite a política se ela existe** e, em seguida, procure e selecione o ficheiro *TrustframeworkExtensions.xml.*
7. Clique em **Carregar**.
8. Repita os passos 5 a 7 para o ficheiro do partido, como *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Executar a política

1. Abre a política que mudaste. Por exemplo, *B2C_1A_profile_edit_password_change.*
2. Para **Candidatura**, selecione a sua candidatura que já se registou anteriormente. Para ver o símbolo, a **URL de resposta** deve mostrar `https://jwt.ms` .
3. Clique em **Executar agora**. Faça o s.a.S. Inscreva-se na conta que criou anteriormente. Deve agora ter a oportunidade de alterar a palavra-passe.

## <a name="next-steps"></a>Passos seguintes

- Encontre a política de amostras no [GitHub.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)
- Saiba como pode [configurar a complexidade da palavra-passe em Azure AD B2C](password-complexity.md).
- Configurar um [fluxo de reset de palavra-passe](add-password-reset-policy.md).

::: zone-end

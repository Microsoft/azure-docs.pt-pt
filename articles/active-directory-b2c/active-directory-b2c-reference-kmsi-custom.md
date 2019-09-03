---
title: Mantenha-me conectado no Azure Active Directory B2C
description: Saiba como configurar Mantenha-me conectado (KMSI) em Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 29cdf5e7723113b4673945bf5db3158680a44b79
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147043"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Habilitar manter-me conectado (KMSI) no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Você pode habilitar a funcionalidade KMSI (Mantenha-me conectado) para usuários de seus aplicativos Web e nativos que têm contas locais em seu diretório Azure Active Directory B2C (Azure AD B2C). Esse recurso concede acesso a usuários que retornam ao seu aplicativo sem solicitar que eles reinsiram seu nome de usuário e senha. Esse acesso é revogado quando um usuário se desconecta.

Os usuários não devem habilitar essa opção em computadores públicos.

![Exemplo de página de entrada de inscrição mostrando uma caixa de seleção Mantenha-me conectado](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Pré-requisitos

Um locatário Azure AD B2C configurado para permitir a entrada da conta local. KMSI não tem suporte para contas de provedor de identidade externa.

Se você não tiver um locatário, poderá criar um usando as etapas no [tutorial: Criar um locatário](tutorial-create-tenant.md)Azure Active Directory B2C.

## <a name="add-a-content-definition-element"></a>Adicionar um elemento de definição de conteúdo

No elemento **BuildingBlocks** do seu arquivo de extensão, adicione um elemento **ContentDefinitions** .

1. No elemento **ContentDefinitions** , adicione um elemento **ContentDefinition** com um identificador de `api.signuporsigninwithkmsi`.
2. No elemento **ContentDefinition** , adicione os elementos **LoadUri**, **RecoveryUri**e **DataUri** . O `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` valor do elemento **DataUri** é um identificador compreensível do computador que abre uma caixa de seleção KMSI nas páginas de entrada. Esse valor não deve ser alterado.

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Adicionar um provedor de declarações de entrada para uma conta local

Você pode definir a entrada da conta local como um provedor de declarações usando o elemento Claims no arquivo de extensão da política:

1. Abra o arquivo *TrustFrameworkExtensions. xml* do seu diretório de trabalho.
2. Localize o elemento **ClaimsProviders** . Se ele não existir, adicione-o sob o elemento raiz. O [pacote de início](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) inclui um provedor de declarações de entrada de conta local.
3. Adicione um elemento claimprovider com o **DisplayName** e o **TechnicalProfile** , conforme mostrado no exemplo a seguir:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive">
            <Metadata>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Adicionar os identificadores de aplicativo à política personalizada

Adicione os identificadores de aplicativo ao arquivo *TrustFrameworkExtensions. xml* .

1. No arquivo *TrustFrameworkExtensions. xml* , localize o elemento **TechnicalProfile** com `login-NonInteractive` o identificador de e o elemento **TechnicalProfile** com um identificador de `login-NonInteractive-PasswordChange` e substitua todos os valores de `IdentityExperienceFrameworkAppId` com o identificador de aplicativo do aplicativo Identity Experience Framework, conforme descrito em [Getting Started](active-directory-b2c-get-started-custom.md).

    ```XML
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Substitua todos os valores `ProxyIdentityExperienceFrameworkAppId` de pelo identificador do aplicativo do proxy Identity Experience Framework conforme descrito em [introdução](active-directory-b2c-get-started-custom.md).
3. Salve o arquivo de extensões.

## <a name="create-a-kmsi-enabled-user-journey"></a>Criar um percurso de usuário habilitado para KMSI

Adicione o provedor de declarações de entrada para uma conta local à sua jornada do usuário.

1. Abra o arquivo base da sua política. Por exemplo, *TrustFrameworkBase. xml*.
2. Localize o elemento userjornadas e copie todo o conteúdo do elemento **userjornada** que usa o identificador de `SignUpOrSignIn`.
3. Abra o arquivo de extensão. Por exemplo, *TrustFrameworkExtensions. xml* e localize o elemento userjornadas. Se o elemento não existir, adicione um.
4. Cole todo o elemento userjornada que você copiou como um filho do elemento userjornadas.
5. Altere o valor do identificador para o novo percurso do usuário. Por exemplo, `SignUpOrSignInWithKmsi`.
6. Finalmente, na primeira etapa de orquestração, altere o valor de ContentDefinitionReferenceId `api.signuporsigninwithkmsi`para. A configuração desse valor habilita a caixa de seleção no percurso do usuário.
7. Salve e carregue o arquivo de extensão e certifique-se de que todas as validações tenham sucesso.

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>Criar um arquivo de terceira parte confiável

Atualize o arquivo RP (terceira parte confiável) que inicia o percurso do usuário que você criou.

1. Faça uma cópia do arquivo *SignUpOrSignIn. xml* em seu diretório de trabalho e, em seguida, renomeie-o. Por exemplo, *SignUpOrSignInWithKmsi. xml*.
2. Abra o novo arquivo e atualize o atributo PolicyId para o **TrustFrameworkPolicy** com um valor exclusivo. Este é o nome da sua política. Por exemplo, `SignUpOrSignInWithKmsi`.
3. Altere o atributo referenceid do elemento **DefaultUserJourney** para corresponder ao identificador do novo percurso do usuário que você criou. Por exemplo, `SignUpOrSignInWithKmsi`.

    KMSI é configurado usando o elemento **UserJourneyBehaviors** com **logon único**, **SessionExpiryType**e **SessionExpiryInSeconds** como seus primeiros elementos filho. O atributo **KeepAliveInDays** controla por quanto tempo o usuário permanece conectado. No exemplo a seguir, a sessão KMSI expira automaticamente após `7` dias, independentemente da frequência com que o usuário executa a autenticação silenciosa. Definir o valor de KeepAliveInDays `0` como desativa a funcionalidade KMSI. Por padrão, esse valor é `0`. Se o valor de **SessionExpiryType** for `Rolling`, a sessão KMSI será estendida `7` por dias toda vez que o usuário executar a autenticação silenciosa.  Se `Rolling` estiver selecionado, você deve manter o número de dias no mínimo.

    O valor de **SessionExpiryInSeconds** representa o tempo de expiração de uma sessão de SSO. Isso é usado internamente pelo Azure AD B2C para verificar se a sessão para KMSI está expirada ou não. O valor de **KeepAliveInDays** determina o valor de expirações/Max-age do cookie de SSO no navegador da Web. Ao contrário de **SessionExpiryInSeconds**, **KeepAliveInDays** é usado para impedir que o navegador Limpe o cookie quando ele é fechado. Um usuário pode entrar silenciosamente somente se o cookie de sessão de SSO existir, que é controlado por **KeepAliveInDays**e não expira, que é controlado pelo **SessionExpiryInSeconds**.

    Se um usuário não habilitar o **Keep me conectado** na página de inscrição e entrada, uma sessão expirará depois que o horário indicado pelo **SessionExpiryInSeconds** tiver passado ou o navegador estiver fechado. Se um usuário permitir que o **Mantenha-me conectado**, o valor de **KeepAliveInDays** substituirá o valor de **SessionExpiryInSeconds** e determinará o tempo de expiração da sessão. Mesmo que os usuários fechem o navegador e o abram novamente, eles ainda podem entrar silenciosamente, desde que esteja dentro do tempo de **KeepAliveInDays**. É recomendável que você defina o valor de **SessionExpiryInSeconds** como um período curto (1200 segundos), enquanto o valor de **KeepAliveInDays** pode ser definido como um período relativamente longo (7 dias), conforme mostrado no exemplo a seguir:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
        <SessionExpiryType>Absolute</SessionExpiryType>
        <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
      </UserJourneyBehaviors>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. Salve as alterações e, em seguida, carregue o arquivo.
5. Para testar a política personalizada que você carregou, na portal do Azure, vá até a página de política e selecione **executar agora**.

Você pode encontrar a política de exemplo [aqui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).

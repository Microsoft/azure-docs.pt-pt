---
title: Manter sessão iniciada no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como configurar a manter-Me com sessão iniciada em (KMSI) no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0ae6f19ea9a04aa6b2547fa031dbb09d03b887c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509432"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Ativar manter sessão iniciada (KMSI) no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pode ativar a funcionalidade de manter-Me com sessão iniciada em (KMSI) para a web e aplicativos nativos no Azure Active Directory (Azure AD) B2C. Esta funcionalidade concede acesso a utilizadores a devolver para o aplicativo sem pedir aos mesmos para reintroduzir o seu nome de utilizador e palavra-passe. Este acesso é revogado quando o utilizador termina sessão. 

Os utilizadores não devem ativar esta opção em computadores públicos. 

![Ativar a opção Manter a sessão iniciada](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Pré-requisitos

Um inquilino do Azure AD B2C que está configurado para permitir a inscrição e início de sessão de conta local. Se não tiver um inquilino, pode criar um através dos passos no [Tutorial: Criar um inquilino do Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="add-a-content-definition-element"></a>Adicionar um elemento de definição de conteúdo 

Sob o **BuildingBlocks** elemento do seu ficheiro de extensão, adicione um **ContentDefinitions** elemento. 

1. Sob o **ContentDefinitions** elemento, adicione um **ContentDefinition** elemento com um identificador de `api.signuporsigninwithkmsi`.
2. Sob o **ContentDefinition** elemento, adicione o **LoadUri**, **RecoveryUri**, e **URI de dados** elementos. O `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` valor de **URI de dados** elemento é um identificador de compreensíveis de máquina que abre uma caixa de verificação KMSI nas páginas de início de sessão. Este valor não pode ser alterado. 

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

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Adicionar um fornecedor de afirmações de início de sessão para uma conta local  

Pode definir início de sessão na conta local como um fornecedor de afirmações com o **ClaimsProvider** elemento no arquivo de extensão da política:

1. Abra o *TrustFrameworkExtensions.xml* ficheiro a partir do seu diretório de trabalho. 
2. Encontrar o **ClaimsProviders** elemento. Se não existir, adicione-o sob o elemento raiz. O [pacote de iniciante](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) inclui um fornecedor de afirmações de início de sessão de conta local. 
3. Adicionar uma **ClaimsProvider** elemento com o **DisplayName** e **TechnicalProfile** conforme mostrado no exemplo a seguir:

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

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Adicionar os identificadores de aplicação à sua política personalizada

Adicionar os identificadores de aplicativo para o *TrustFrameworkExtensions.xml* ficheiro.

1. Na *TrustFrameworkExtensions.xml* do ficheiro, encontre o **TechnicalProfile** elemento com o identificador do `login-NonInteractive` e a **TechnicalProfile** elemento com um identificador de `login-NonInteractive-PasswordChange` e substitua todos os valores da `IdentityExperienceFrameworkAppId` com o identificador da aplicação da aplicação de arquitetura de experiências de identidade, conforme descrito na [introdução](active-directory-b2c-get-started-custom.md).

    ```
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Substitua todos os valores da `ProxyIdentityExperienceFrameworkAppId` com o identificador da aplicação do aplicativo Framework de experiência de identidade de Proxy, tal como descrito na [introdução](active-directory-b2c-get-started-custom.md).
3. Guarde o ficheiro de extensões.

## <a name="create-a-kmsi-enabled-user-journey"></a>Criar um percurso do utilizador ativado KMSI

Adicione o fornecedor de afirmações de início de sessão de uma conta local para o seu percurso do utilizador. 

1. Abra o ficheiro de base da sua política. Por exemplo, *TrustFrameworkBase.xml*.
2. Encontrar o **UserJourneys** elemento e copie o conteúdo inteiro do **UserJourney** elemento que utiliza o identificador do `SignUpOrSignIn`.
3. Abra o ficheiro de extensão. Por exemplo, *TrustFrameworkExtensions.xml* e localize a **UserJourneys** elemento. Se o elemento não existir, adicione um.
4. Cole a toda **UserJourney** elemento que copiou como subordinado da **UserJourneys** elemento.
5. Altere o valor do identificador para o percurso do utilizador novo. Por exemplo, `SignUpOrSignInWithKmsi`.
6. Por fim, no primeiro passo de orquestração, altere o valor de **ContentDefinitionReferenceId** para `api.signuporsigninwithkmsi`. A definição deste valor permite que a caixa de verificação no percurso do utilizador. 
7. Salvar e carregar o ficheiro de extensão e certifique-se de que todas as validações tenha êxito.

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

## <a name="create-a-relying-party-file"></a>Crie um ficheiro da entidade confiadora de terceiros

Atualize o ficheiro da entidade confiadora de terceiros (RP) que inicia o percurso do utilizador que criou.

1. Faça uma cópia deles *SignUpOrSignIn.xml* de ficheiros no diretório de trabalho e, em seguida, mude o nome. Por exemplo, *SignUpOrSignInWithKmsi.xml*.
2. Abra o ficheiro novo e a atualização do **PolicyId** atributo para o **TrustFrameworkPolicy** com um valor exclusivo. Este é o nome da sua política. Por exemplo, `SignUpOrSignInWithKmsi`.
3. Alteração da **ReferenceId** atributo para o **DefaultUserJourney** elemento de acordo com o identificador do novo percurso do utilizador que criou. Por exemplo, `SignUpOrSignInWithKmsi`.

    KMSI está configurado com o **UserJourneyBehaviors** elemento com **SingleSignOn**, **SessionExpiryType**, e **SessionExpiryInSeconds** como elementos filho primeiro. O **KeepAliveInDays** atributo controla o tempo que o usuário permanece com sessão iniciada. No exemplo seguinte, a sessão KMSI automaticamente expira após `7` dias, independentemente da frequência com que o utilizador efetua a autenticação silenciosa. Definindo a **KeepAliveInDays** valor a `0` desativa a funcionalidade KMSI. Por predefinição, este valor é `0`. Se o valor de **SessionExpiryType** é `Rolling`, a sessão KMSI é ampliada pela `7` dias sempre que o utilizador efetua a autenticação silenciosa.  Se `Rolling` é selecionado, deve manter o número de dias para mínimo. 

    O valor de **SessionExpiryInSeconds** representa a hora de expiração de uma sessão SSO. Isso é utilizado internamente pelo Azure AD B2C para verificar se a sessão para KMSI expirou ou não. O valor de **KeepAliveInDays** determina o valor de Expires/Max-Age do cookie SSO no navegador da web. Ao contrário **SessionExpiryInSeconds**, **KeepAliveInDays** serve para impedir que o navegador de limpar o cookie quando está fechado. Um utilizador silenciosamente pode iniciar sessão apenas se existir o cookie de sessão SSO, que é controlada pela **KeepAliveInDays**e não está expirado, que é controlado pelas **SessionExpiryInSeconds**. 
    
    Se um utilizador não permite **manter sessão iniciada** na página de inscrição e início de sessão, uma sessão expira após o tempo indicado pelo **SessionExpiryInSeconds** passou ou o browser é fechado. Se um utilizador ativa **manter sessão iniciada**, o valor de **KeepAliveInDays** substitui o valor de **SessionExpiryInSeconds** e determina o tempo de expiração da sessão. Mesmo que os utilizadores feche o browser e abra-o novamente, eles podem ainda silenciosamente início de sessão, desde que esteja dentro do momento da **KeepAliveInDays**. É recomendado que defina o valor de **SessionExpiryInSeconds** um período curto (segundos 1200), enquanto o valor de **KeepAliveInDays** pode ser definida para um período relativamente longo (7 dias), como mostra a exemplo a seguir:

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

4. Guardar as alterações e, em seguida, carregue o ficheiro.
5. Para testar a política personalizada que carregou, no portal do Azure, vá para a página de política e, em seguida, selecione **executar agora**.

Pode encontrar a política de exemplo [aqui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).









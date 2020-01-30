---
title: Mantenha-me assinado no Diretório Ativo Azure B2C
description: Saiba como configurar Keep Me Signed In (KMSI) no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 052e1bc4e9a14b34e21b0bfeb4193fbd0b2b1a22
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848905"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Enable Keep me signed in (KMSI) in Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pode ativar a funcionalidade Keep Me Signed In (KMSI) para utilizadores da sua web e aplicações nativas que possuam contas locais no seu diretório Azure Ative Directory B2C (Azure AD B2C). Esta funcionalidade permite o acesso aos utilizadores que regressam à sua aplicação sem os pedir para reintroduzirem o seu nome de utilizador e senha. Este acesso é revogado quando um utilizador assina.

Os utilizadores não devem ativar esta opção em computadores públicos.

![Página de inscrição de exemplo mostrando um Mantenha-me assinado na caixa de verificação](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Pré-requisitos

Um inquilino Azure AD B2C que está configurado para permitir o início de sessão de conta local. A KMSI não é apoiada para contas de fornecedores de identidade externa.

Se não tiver um inquilino, pode criar um usando os passos em [Tutorial: Crie um inquilino B2C do Diretório Ativo Azure.](tutorial-create-tenant.md)

## <a name="add-a-content-definition-element"></a>Adicione um elemento de definição de conteúdo

Sob o elemento **BuildingBlocks** do seu ficheiro de extensão, adicione um elemento **ContentDefinitions.**

1. No elemento Definições de **Conteúdo,** adicione um elemento **ContentDefinition** com um identificador de `api.signuporsigninwithkmsi`.
2. Sob o elemento **ContentDefinition,** adicione os elementos **LoadUri,** **RecoveryUri**e **DataUri.** O valor `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` do elemento **DataUri** é um identificador compreensível de máquina que traz uma caixa de verificação KMSI nas páginas de entrada. Este valor não deve ser alterado.

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

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Adicione um prestador de reclamações de sessão para uma conta local

Pode definir o registo de conta local como um fornecedor de sinistros utilizando o elemento **Reclamações Fornecedor** no ficheiro de extensão da sua apólice:

1. Abra o ficheiro *TrustFrameworkExtensions.xml* do seu diretório de trabalho.
2. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz. O [pacote de entrada](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) inclui um fornecedor local de reclamações de conta.
3. Adicione um elemento **Reclamações Fornecedor** com o Nome de **Exibição** e **Perfil Técnico,** como mostra o seguinte exemplo:

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

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Adicione os identificadores de aplicação à sua política personalizada

Adicione os identificadores de aplicação ao ficheiro *TrustFrameworkExtensions.xml.*

1. No ficheiro *TrustFrameworkExtensions.xml,* encontre o elemento **TechnicalProfile** com o identificador de `login-NonInteractive` e o elemento **TechnicalProfile** por um identificador de `login-NonInteractive-PasswordChange` e substitua todos os valores de `IdentityExperienceFrameworkAppId` pelo identificador de aplicação da aplicação Identity Experience Framework, conforme descrito no [Início.](custom-policy-get-started.md)

    ```XML
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Substitua todos os valores de `ProxyIdentityExperienceFrameworkAppId` pelo identificador de aplicação da aplicação Proxy Identity Experience Framework, conforme descrito no [Início.](custom-policy-get-started.md)
3. Salve o arquivo de extensões.

## <a name="create-a-kmsi-enabled-user-journey"></a>Criar uma viagem de utilizador ativada por KMSI

Adicione o fornecedor de reclamações de sessão para uma conta local à sua viagem de utilizador.

1. Abra o arquivo base da sua política. Por exemplo, *TrustFrameworkBase.xml*.
2. Encontre o elemento **UserJourneys** e copie todo o conteúdo do elemento **UserJourney** que utiliza o identificador de `SignUpOrSignIn`.
3. Abra o arquivo de extensão. Por exemplo, *TrustFrameworkExtensions.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Colhe todo o elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Alterar o valor do identificador para a nova viagem do utilizador. Por exemplo, `SignUpOrSignInWithKmsi`.
6. Finalmente, no primeiro passo de orquestração, altere o valor do **ContentDefinitionReferenceId** para `api.signuporsigninwithkmsi`. A definição deste valor permite a caixa de verificação na viagem do utilizador.
7. Guarde e carregue o ficheiro de extensão e certifique-se de que todas as validações são bem sucedidas.

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

## <a name="create-a-relying-party-file"></a>Criar um ficheiro de festa de base

Atualize o ficheiro da parte de fiação (RP) que inicia a viagem de utilizador que criou.

1. Faça uma cópia do ficheiro *SignUpOrSignIn.xml* no seu diretório de trabalho e, em seguida, mude o nome. Por exemplo, *SignUpOrSignInWithKmsi.xml*.
2. Abra o novo ficheiro e atualize o atributo **PolicyId** para a **TrustFrameworkPolicy** com um valor único. Este é o nome da sua apólice. Por exemplo, `SignUpOrSignInWithKmsi`.
3. Altere o atributo **ReferenceId** para o elemento **DefaultUserJourney** para corresponder ao identificador da nova viagem de utilizador que criou. Por exemplo, `SignUpOrSignInWithKmsi`.

    O KMSI é configurado utilizando o elemento **UserJourneyBehaviors** com **SingleSignOn,** **SessionExpiryType**e **SessionExpiryInSeconds** como os seus primeiros elementos infantis. O atributo **KeepAliveInDays** controla quanto tempo o utilizador permanece inscrito. No exemplo seguinte, a sessão KMSI expira automaticamente após `7` dias, independentemente da frequência com que o utilizador executa a autenticação silenciosa. Definir o valor **KeepAliveInDays** para `0` desliga a funcionalidade KMSI. Por predefinição, este valor é `0`. Se o valor do **SessionExpiryType** for `Rolling`, a sessão KMSI é prolongada por `7` dias sempre que o utilizador executa a autenticação silenciosa.  Se `Rolling` for selecionado, deverá manter o número de dias no mínimo.

    O valor do **SessionExpiryInSeconds** representa o tempo de validade de uma sessão SSO. Isto é utilizado internamente pelo Azure AD B2C para verificar se a sessão para KMSI está expirada ou não. O valor do **KeepAliveInDays** determina o valor Expirado/Idade Máxima do cookie SSO no navegador web. Ao contrário do **SessionExpiryInSeconds**, **o KeepAliveInDays** é usado para impedir que o navegador limpe o cookie quando está fechado. Um utilizador só pode iniciar sessão silenciosamente se existir o cookie de sessão SSO, que é controlado pela **KeepAliveInDays**, e não tiver expirado, que é controlado pelo **SessionExpiryInSeconds**.

    Se um utilizador não ativar **Manter-me inscrito** na página de inscrição e inscrição, uma sessão expira após o tempo indicado pelo **SessionExpiryInSeconds** ou o navegador está fechado. Se um utilizador **ativar Mantenha-me inscrito,** o valor do **KeepAliveInDays** sobrepõe-se ao valor do **SessionExpiryInSeconds** e dita o tempo de validade da sessão. Mesmo que os utilizadores fechem o navegador e o abram novamente, ainda podem iniciar sessão silenciosamente desde que esteja dentro do tempo de **KeepAliveInDays**. Recomenda-se que desloque o valor do **SessionExpiryInSeconds** para um curto período (1200 segundos), enquanto o valor do **KeepAliveInDays** pode ser definido para um período relativamente longo (7 dias), como mostra o seguinte exemplo:

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

4. Guarde as suas alterações e, em seguida, faça o upload do ficheiro.
5. Para testar a política personalizada que carregou, no portal Azure, vá à página de política e, em seguida, selecione **Run agora**.

Pode encontrar a política da amostra [aqui.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)

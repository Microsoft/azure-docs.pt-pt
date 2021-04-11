---
title: Configurar inscrição e iniciar sôm-in com uma conta LinkedIn
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição a clientes com contas LinkedIn nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 51a9635d42b07eb27b05312d292ca890c7963b4f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028235"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta LinkedIn utilizando o Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>Criar uma aplicação LinkedIn

Para permitir o sessão de sessão para utilizadores com uma conta LinkedIn no Azure Ative Directory B2C (Azure AD B2C), é necessário criar uma aplicação no [website do LinkedIn Developers.](https://www.developer.linkedin.com/) Para mais informações, consulte [o Código de Autorização Flow](/linkedin/shared/authentication/authorization-code-flow). Se ainda não tiver uma conta LinkedIn, pode inscrever-se em [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Inscreva-se no site do [LinkedIn Developers](https://www.developer.linkedin.com/) com as suas credenciais de conta LinkedIn.
1. Selecione **as minhas apps** e, em seguida, clique em **Criar app**.
1. Insira **o nome da App**, **LinkedIn Page,** **URL de política de privacidade** e logotipo da **App.**
1. Concorde com os **Termos de Utilização da API do** LinkedIn e clique em Criar **app.**
1. Selecione o separador **Auth.** Em **Teclas de Autenticação,** copie os valores para **Identificação do Cliente** e Segredo do **Cliente.** Vai precisar de ambos para configurar o LinkedIn como fornecedor de identidade no seu inquilino. **A Client Secret** é uma importante credencial de segurança.
1. Selecione o lápis de edição ao lado **de URLs de redirecionamento autorizados para a sua aplicação** e, em seguida, selecione **Adicionar URL de redirecionamento**. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Se utilizar um [domínio personalizado,](custom-domain.md)insira `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-tenant-name`Substitua-o pelo nome do seu inquilino e `your-domain-name` pelo seu domínio personalizado. Você precisa usar todas as letras minúsculas ao introduzir o seu nome de inquilino, mesmo que o inquilino seja definido com letras maiúsculas em Azure AD B2C. Selecione **Atualizar**.
1. Por padrão, a sua aplicação LinkedIn não está aprovada para os âmbitos relacionados com a inscrição. Para solicitar uma revisão, selecione o separador **Produtos** e, em seguida, selecione **Iniciar sôs com o LinkedIn**. Quando a revisão estiver concluída, os âmbitos necessários serão adicionados à sua aplicação.
   > [!NOTE]
   > Pode ver os âmbitos que são atualmente permitidos para a sua aplicação no separador **Auth** na secção **de âmbitos OAuth 2.0.**

::: zone pivot="b2c-user-flow"

## <a name="configure-linkedin-as-an-identity-provider"></a>Configure o LinkedIn como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade** e, em seguida, selecione **LinkedIn**.
1. Insira um **nome**. Por exemplo, *LinkedIn*.
1. Para o ID do **Cliente,** insira o ID do Cliente da aplicação LinkedIn que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo de Cliente que gravou.
1. Selecione **Guardar**.

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>Adicionar fornecedor de identidade LinkedIn a um fluxo de utilizador 

Neste momento, o fornecedor de identidade LinkedIn foi criado, mas ainda não está disponível em nenhuma das páginas de inscrição. Para adicionar o fornecedor de identidade LinkedIn a um fluxo de utilizador:

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Clique no fluxo de utilizador que pretende adicionar ao fornecedor de identidade LinkedIn.
1. Sob os **fornecedores de identidade social,** selecione **LinkedIn**.
1. Selecione **Guardar**.
1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **de fluxo do utilizador Executar.**
1. A partir da página de inscrição ou de inscrição, selecione **LinkedIn** para iniciar scontabilidade do LinkedIn.

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do cliente que você já gravou no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Na página 'Visão Geral', selecione **Identity Experience Framework**.
5. Selecione **as teclas de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções,** escolha `Manual` .
7. Insira um **Nome** para a chave de política. Por exemplo, `LinkedInSecret`. O prefixo *B2C_1A_* é adicionado automaticamente ao nome da sua chave.
8. Em **Segredo,** insira o segredo do cliente que gravou anteriormente.
9. Para **a utilização da chave**, selecione `Signature` .
10. Clique em **Criar**.

## <a name="configure-linkedin-as-an-identity-provider"></a>Configure o LinkedIn como fornecedor de identidade

Para permitir que os utilizadores assinem através de uma conta LinkedIn, é necessário definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Defina uma conta LinkedIn como fornecedor de sinistros adicionando-a ao elemento **ClaimsProviders** no ficheiro de extensão da sua política.

1. Abra o ficheiro *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml*** no seu editor. Este ficheiro está no [pacote de arranque de política personalizado][starter-pack] que descarregou como parte de um dos pré-requisitos.
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Provider de Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAuth2">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Substitua o valor de **client_id** pelo ID do cliente da aplicação LinkedIn que registou anteriormente.
1. Guarde o ficheiro.

### <a name="add-the-claims-transformations"></a>Adicione as transformações de sinistros

O perfil técnico linkedIn requer que o **ExtractGivenNameFromLinkedInResponse** e **extractSurNameFromLinkedInResponse** alega que as transformações sejam adicionadas à lista de SinistrosTransformações. Se não tiver um elemento **Dedestransformações definidas** no seu ficheiro, adicione os elementos XML dos pais, conforme mostrado abaixo. As transformações de sinistros também precisam de um novo tipo de reclamação definido chamado **nullStringClaim**.

Adicione o elemento **BuildingBlocks** perto da parte superior do ficheiro *TrustFrameworkExtensions.xml.* Veja *TrustFrameworkBase.xml* um exemplo.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Teste a sua política personalizada

1. Selecione a sua política partidária de dependência, por exemplo `B2C_1A_signup_signin` .
1. Para **Aplicação**, selecione uma aplicação web que já [se registou anteriormente.](tutorial-register-applications.md) A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **Executar agora.**
1. A partir da página de inscrição ou de inscrição, selecione **LinkedIn** para iniciar scontabilidade do LinkedIn.

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

## <a name="migration-from-v10-to-v20"></a>Migração de v1.0 para v2.0

O LinkedIn atualizou recentemente [as suas APIs de v1.0 a v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Para migrar a configuração existente para a nova configuração, utilize as informações nas seguintes secções para atualizar os elementos do perfil técnico.

### <a name="replace-items-in-the-metadata"></a>Substitua os itens nos Metadados

No elemento **metadados** existente do **Ficheiro Técnico, atualize** os seguintes elementos **do Item** a partir de:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Para:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Adicionar itens aos Metadados

Nos **Metadados** do **Ficheiro Técnico,** adicione os seguintes elementos **do item:**

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Atualizar as SaídasClaims

Nas **saídas existentes, os resultados** do **Programa Técnico,** atualize os seguintes elementos **OutputClaim** a partir de:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Para:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Adicionar novos elementos de saídaClaimsTransformation

Nas **saídasClaimsTransformações** do **Ficheiro Técnico,** adicione os **seguintes elementos de saídaClaimsTransformation:**

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definir as novas transformações de sinistros e tipo de reclamação

No último passo, adicionou novas transformações de reivindicações que precisam de ser definidas. Para definir as transformações de sinistros, adicione-as à lista de **SinistrosTransformações**. Se não tiver um elemento **Dedestransformações definidas** no seu ficheiro, adicione os elementos XML dos pais, conforme mostrado abaixo. As transformações de sinistros também precisam de um novo tipo de reclamação definido chamado **nullStringClaim**.

O elemento **Blocos de Construção** deve ser adicionado perto da parte superior do ficheiro. Veja o *TrustframeworkBase.xml* como um exemplo.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Obtenha um endereço de e-mail

Como parte da migração do LinkedIn de v1.0 para v2.0, é necessária uma chamada adicional para outra API para obter o endereço de e-mail. Se precisar de obter o endereço de e-mail durante a inscrição, faça o seguinte:

1. Complete os passos acima para permitir que a Azure AD B2C federa com o LinkedIn deixe o utilizador iniciar sação. Como parte da federação, a Azure AD B2C recebe o token de acesso para o LinkedIn.
1. Guarde o token de acesso LinkedIn numa reclamação. [Consulte as instruções aqui.](idp-pass-through-user-flow.md)
1. Adicione o seguinte provedor de reclamações que faz o pedido à API do `/emailAddress` LinkedIn. Para autorizar este pedido, precisa do token de acesso linkedIn.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Adicione o seguinte passo de orquestração na sua jornada de utilizador, de modo a que o provedor de reclamações da API seja acionado quando um utilizador assina a utilização do LinkedIn. Certifique-se de atualizar o `Order` número adequadamente. Adicione este passo imediatamente após o passo de orquestração que desencadeia o perfil técnico linkedIn.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

A obtenção do endereço de e-mail do LinkedIn durante a inscrição é opcional. Se optar por não obter o e-mail do LinkedIn mas necessitar de um durante a inscrição, o utilizador é obrigado a introduzir manualmente o endereço de e-mail e validá-lo.

Para obter uma amostra completa de uma política que utilize o fornecedor de identidade LinkedIn, consulte o [Pacote de Iniciadores de Políticas Personalizadas](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>Migração de v1.0 para v2.0

O LinkedIn atualizou recentemente [as suas APIs de v1.0 a v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Como parte da migração, o Azure AD B2C só é capaz de obter o nome completo do utilizador linkedIn durante a inscrição. Se um endereço de e-mail for um dos atributos recolhidos durante a inscrição, o utilizador deve introduzir manualmente o endereço de e-mail e validá-lo.

::: zone-end
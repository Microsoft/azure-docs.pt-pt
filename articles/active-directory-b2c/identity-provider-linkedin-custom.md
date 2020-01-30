---
title: Configurar o início de sessão com uma conta LinkedIn utilizando políticas personalizadas
titleSuffix: Azure AD B2C
description: Instale o início de sessão com uma conta LinkedIn no Azure Ative Directory B2C utilizando políticas personalizadas.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f4ad74104d34e7e2c3f4d6aafc05b36574f8e287
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847436"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o início de sessão com uma conta LinkedIn utilizando políticas personalizadas no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão para utilizadores a partir de uma conta LinkedIn utilizando [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos em [Get started com políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md).
- Conta LinkedIn - Se ainda não tiver uma, [crie uma conta](https://www.linkedin.com/start/join).
- Página linkedIn - Precisa de uma [página do LinkedIn](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) para se associar à aplicação LinkedIn que cria na secção seguinte.

## <a name="create-an-application"></a>Criar uma aplicação

Para utilizar o LinkedIn como fornecedor de identidade no Azure AD B2C, é necessário criar uma aplicação LinkedIn.

### <a name="create-app"></a>Criar app

1. Inscreva-se no site de gestão de [aplicações do LinkedIn](https://www.linkedin.com/secure/developer?newapp=) com as credenciais da conta LinkedIn.
1. Selecione **Criar app**.
1. Introduza um **nome de aplicação**.
1. Introduza um nome **de empresa** correspondente a um nome de página linkedIn. Crie uma página do LinkedIn se ainda não tiver uma.
1. (Opcional) Introduza um URL de política de **privacidade**. Deve ser um URL válido, mas não precisa de ser um ponto final acessível.
1. Insira um **e-mail de Negócios.**
1. Faça upload de uma imagem do logotipo da **App.** A imagem do logotipo deve ser quadrada e as suas dimensões devem ser de pelo menos 100x100 pixels.
1. Deixe as definições predefinidas na secção **Produtos.**
1. Reveja a informação apresentada em **termos legais.** Se concordar com os termos, verifique a caixa.
1. Selecione **Criar app**.

### <a name="configure-auth"></a>Configurar a autenticação

1. Selecione o **separador Auth.**
1. Grave o ID do **Cliente.**
1. Revelar e gravar o Segredo do **Cliente.**
1. Sob **as definições oAuth 2.0,** adicione o seguinte **URL de redirecionamento**. Substitua `your-tenant` pelo nome do seu inquilino. Use **todas as letras minúsculas** para o nome do inquilino, mesmo que seja definido com letras maiúsculas em Azure AD B2C.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>Criar uma chave política

Você precisa armazenar o segredo do cliente que você gravou anteriormente no seu inquilino Azure AD B2C.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Na página 'Visão Geral', selecione Quadro de **Experiência de Identidade**.
5. Selecione **teclas Policy** e, em seguida, selecione **Adicionar**.
6. Para **Opções**, escolha `Manual`.
7. Introduza um **nome** para a chave política. Por exemplo, `LinkedInSecret`. O prefixo *B2C_1A_* é adicionado automaticamente ao nome da sua chave.
8. Em **Segredo,** insira o segredo do cliente que gravou anteriormente.
9. Para **a utilização da chave,** selecione `Signature`.
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se pretender que os utilizadores assinem através de uma conta LinkedIn, tem de definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são utilizadas pelo Azure AD B2C para verificar se um utilizador específico se autenticou.

Defina uma conta LinkedIn como fornecedor de sinistros adicionando-a ao elemento **Reclamações Fornecedores** no ficheiro de extensão da sua apólice.

1. Abra o arquivo *SocialAndLocalAccounts/ **TrustFrameworkExtensions. xml*** em seu editor. Este ficheiro está no pacote de arranque de [política personalizado][starter-pack] que descarregou como parte de um dos pré-requisitos.
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo Fornecedor de **Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
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
            <Item Key="UsePolicyInRedirectUri">0</Item>
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

1. Substitua o valor da **client_id** pelo ID do cliente da aplicação LinkedIn que registou anteriormente.
1. Guarde o ficheiro.

### <a name="add-the-claims-transformations"></a>Adicione as transformações de sinistros

O perfil técnico do LinkedIn requer que os **ExtractgivenNameFromLinkedInResponse** e **ExtractSurNameFromLinkedInResponse** afirmam que as transformações devem ser adicionadas à lista de Transformações de Reclamações. Se não tiver um elemento **Desqualificações** de Reclamações definido no seu ficheiro, adicione os elementos XML parentais como mostrado abaixo. As transformações de sinistros também precisam de um novo tipo de reclamação definido chamado **nullStringClaim**.

Adicione o elemento **BuildingBlocks** perto da parte superior do ficheiro *TrustFrameworkExtensions.xml.* Consulte *TrustFrameworkBase.xml,* por exemplo.

```XML
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

### <a name="upload-the-extension-file-for-verification"></a>Faça upload do ficheiro de extensão para verificação

Tem agora uma política configurada para que o Azure AD B2C saiba comunicar com a sua conta LinkedIn. Tente carregar o ficheiro de extensão da sua apólice para confirmar que não tem quaisquer problemas até agora.

1. Na página **Políticas Personalizadas** do seu inquilino Azure AD B2C, selecione **Política de Upload**.
2. Ativar **a sobreposição da apólice se ela existir,** e depois navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registe o prestador de sinistros

Neste momento, o fornecedor de identidade foi criado, mas não está disponível em nenhum dos ecrãs de inscrição ou inscrição. Para disponibilizá-lo, cria-se uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modificá-la de modo a que também tenha o fornecedor de identidade LinkedIn.

1. Abra o ficheiro *TrustFrameworkBase.xml* no pacote de arranque.
2. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclua `Id="SignUpOrSignIn"`.
3. Abra as *Extensões TrustFramework.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Colhe todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Mude o nome da identificação da viagem do utilizador. Por exemplo, `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição ou de inscrição. Se adicionar um elemento **ClaimsProviderSelection** para uma conta LinkedIn, aparece um novo botão quando um utilizador aterra na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na viagem de utilizador que criou.
2. Ao abrigo **das seleções ClaimsProviderS,** adicione o seguinte elemento. Defino o valor do **TargetClaimsExchangeId** para um valor adequado, por exemplo, `LinkedInExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de ligá-lo a uma ação. A ação, neste caso, é que o Azure AD B2C comunique com uma conta LinkedIn para receber um símbolo.

1. Encontre a **OrquestrationStep** que inclui `Order="2"` na viagem do utilizador.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para o ID que utilizou para **targetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Atualize o valor do **TechnicalProfileReferenceId** para o ID do perfil técnico que criou anteriormente. Por exemplo, `LinkedIn-OAUTH`.

3. Guarde o ficheiro *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação Azure AD B2C

A comunicação com o Azure AD B2C ocorre através de uma aplicação que se regista no seu inquilino B2C. Esta secção lista os passos opcionais que pode completar para criar uma aplicação de teste se ainda não o fez.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que depende

Atualize o ficheiro da parte de fiação (RP) que inicia a viagem de utilizador que criou.

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho e mude o nome. Por exemplo, mude o nome para *SignUpSignInLinkedIn.xml*.
2. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInLinkedIn`.
3. Atualizar o valor da **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da nova viagem de utilizador que criou (SignUpSignLinkedIn).
5. Guarde as suas alterações, faça o upload do ficheiro e, em seguida, selecione a nova política na lista.
6. Certifique-se de que a aplicação Azure AD B2C que criou é selecionada no campo de **aplicação Select** e, em seguida, testá-la clicando agora em **Executar**.

## <a name="migration-from-v10-to-v20"></a>Migração de v1.0 para v2.0

O LinkedIn atualizou recentemente [as suas APIs de v1.0 para v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Para migrar a configuração existente para a nova configuração, utilize a informação nas seguintes secções para atualizar os elementos no perfil técnico.

### <a name="replace-items-in-the-metadata"></a>Substitua itens nos Metadados

No elemento **Metadados** existente do **Perfil Técnico,** atualize os seguintes elementos **do Item** a partir de:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Para:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Adicione itens aos Metadados

Nos **Metadados** do **Perfil Técnico,** adicione os seguintes elementos **do Item:**

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Atualizar as OutputClaims

Na saída **existenteReclamações** do **Perfil Técnico,** atualize os seguintes elementos **OutputClaim** de:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Para:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Adicione novos elementos outputClaimsTransformation

No **OutputClaimsTransformations** of the **TechnicalProfile,** adicione os seguintes elementos **outputClaimsTransformation:**

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definir as novas transformações de sinistros e o tipo de reclamação

No último passo, adicionou novas transformações de reivindicações que precisam de ser definidas. Para definir as transformações de sinistros, adicione-as à lista de **Transformações de Reclamações.** Se não tiver um elemento **Desqualificações** de Reclamações definido no seu ficheiro, adicione os elementos XML parentais como mostrado abaixo. As transformações de sinistros também precisam de um novo tipo de reclamação definido chamado **nullStringClaim**.

O elemento **BuildingBlocks** deve ser adicionado perto da parte superior do ficheiro. Consulte o *TrustframeworkBase.xml* como um exemplo.

```XML
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

1. Complete os passos acima para permitir que o Azure AD B2C federar com o LinkedIn para permitir que o utilizador assine. Como parte da federação, o Azure AD B2C recebe o sinal de acesso para o LinkedIn.
2. Guarde o sinal de acesso LinkedIn numa reclamação. [Consulte as instruções aqui.](idp-pass-through-custom.md)
3. Adicione o seguinte fornecedor de reclamações que efaz o pedido à API `/emailAddress` do LinkedIn. Para autorizar este pedido, necessita do sinal de acesso LinkedIn.

    ```XML
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

4. Adicione o seguinte passo de orquestração na sua jornada de utilizador, de modo a que o fornecedor de reclamações da API seja acionado quando um utilizador assina na utilização do LinkedIn. Certifique-se de atualizar o número `Order` adequadamente. Adicione este passo imediatamente após o passo de orquestração que desencadeia o perfil técnico do LinkedIn.

    ```XML
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

A obtenção do endereço de e-mail do LinkedIn durante a inscrição é opcional. Se optar por não obter o e-mail do LinkedIn, mas necessitar de um durante a inscrição, o utilizador é obrigado a introduzir manualmente o endereço de e-mail e validá-lo.

Para obter uma amostra completa de uma política que utiliza o fornecedor de identidade LinkedIn, consulte o Pacote de Arranque de [Política Personalizada](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

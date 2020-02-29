---
title: Configurar o início de sessão com uma conta de Twitter utilizando políticas personalizadas
titleSuffix: Azure AD B2C
description: Instale o início de sessão com uma conta no Twitter utilizando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85af3457f83f06c107f8b4aa9bd88a9f915c776f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187938"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o início de sessão com uma conta no Twitter utilizando políticas personalizadas no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão para os utilizadores de uma conta de Twitter utilizando [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos em [Get started com políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md).
- Se ainda não tem uma conta no Twitter, crie uma na [página de inscrição](https://twitter.com/signup)no Twitter .

## <a name="create-an-application"></a>Criar uma aplicação

Para utilizar o Twitter como fornecedor de identidade no Azure AD B2C, é necessário criar uma aplicação no Twitter.

1. Inscreva-se no site do [Twitter Developers](https://developer.twitter.com/en/apps) com as credenciais da sua conta do Twitter.
2. Selecione **Criar uma aplicação**.
3. Introduza um **nome de aplicação** e uma descrição da **Aplicação.**
4. No **URL do Site,** introduza `https://your-tenant.b2clogin.com`. Substitua `your-tenant` pelo nome do seu inquilino. Por exemplo, https://contosob2c.b2clogin.com.
5. Para o **URL de chamada,** introduza `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`. Substitua `your-tenant` pelo nome do seu nome de inquilino e `your-policy-Id` pelo identificador da sua apólice. Por exemplo, `b2c_1A_signup_signin_twitter`. Você precisa usar todas as letras minúsculas ao introduzir o nome do seu inquilino, mesmo que o inquilino seja definido com letras maiúsculas em Azure AD B2C.
6. Na parte inferior da página, leia e aceite os termos e, em seguida, selecione **Criar**.
7. Na página de detalhes da **App,** selecione **Editar > Editar detalhes,** verifique a caixa para **ativar o 'Iniciar sessão' com**o Twitter , e depois selecione **Guardar**.
8. Selecione **Teclas e fichas** e grave a **Chave API** do Consumidor e os valores-chave secretos da **API** do consumidor a utilizar mais tarde.

## <a name="create-a-policy-key"></a>Criar uma chave política

Você precisa armazenar a chave secreta que você gravou anteriormente no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Na página 'Visão Geral', selecione Quadro de **Experiência de Identidade**.
5. Selecione **Teclas de política** e, em seguida, selecione **Adicionar**.
6. Para **opções,** escolha `Manual`.
7. Introduza um **nome** para a chave política. Por exemplo, `TwitterSecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Em **Segredo,** insira o seu segredo de cliente que gravou anteriormente.
9. Para **a utilização da chave,** selecione `Encryption`.
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se pretender que os utilizadores assinem através de uma conta no Twitter, tem de definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são utilizadas pelo Azure AD B2C para verificar se um utilizador específico se autenticou.

Pode definir uma conta de Twitter como fornecedor de reclamações adicionando-a ao elemento **Reclamadores** no ficheiro de extensão da sua apólice.

1. Abra as *Extensões TrustFramework.xml*.
2. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo Fornecedor de **Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. Substitua o valor da **client_id** pela chave de consumo que registou anteriormente.
5. Guarde o ficheiro.

### <a name="upload-the-extension-file-for-verification"></a>Faça upload do ficheiro de extensão para verificação

Por esta altura, já configuraste a tua política para que o Azure AD B2C saiba comunicar com a sua conta de Twitter. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **Políticas Personalizadas** do seu inquilino Azure AD B2C, selecione **Política de Upload**.
2. Ativar **a sobreposição da apólice se ela existir,** e depois navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registe o prestador de sinistros

Neste momento, o fornecedor de identidade foi criado, mas não está disponível em nenhum dos ecrãs de inscrição ou inscrição. Para disponibilizá-lo, cria-se uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modificá-la de modo a que também tenha o fornecedor de identidade do Twitter.

1. Abra o ficheiro *TrustFrameworkBase.xml* a partir do pacote de arranque.
2. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclua `Id="SignUpOrSignIn"`.
3. Abra as *Extensões TrustFramework.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Colhe todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Mude o nome da identificação da viagem do utilizador. Por exemplo, `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição ou de inscrição. Se adicionar um elemento **ClaimsProviderSelection** para uma conta de Twitter, aparece um novo botão quando um utilizador aterra na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na viagem de utilizador que criou.
2. Em **ClaimsProviderSelects,** adicione o seguinte elemento. Defino o valor do **TargetClaimsExchangeId** para um valor adequado, por exemplo, `TwitterExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de ligá-lo a uma ação. A ação, neste caso, é que o Azure AD B2C comunique com uma conta de Twitter para receber um símbolo.

1. Encontre a **OrquestrationStep** que inclui `Order="2"` na viagem do utilizador.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para o ID que utilizou para **targetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Atualize o valor do **TechnicalProfileReferenceId** para o ID do perfil técnico que criou anteriormente. Por exemplo, `Twitter-OAUTH1`.

3. Guarde o ficheiro *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação Azure AD B2C

A comunicação com o Azure AD B2C ocorre através de uma aplicação que se regista no seu inquilino B2C. Esta secção lista os passos opcionais que pode completar para criar uma aplicação de teste se ainda não o fez.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que depende

Atualize o ficheiro da parte de fiação (RP) que inicia a viagem de utilizador que criou.

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho e mude o nome. Por exemplo, mude o nome para *SignUpSignInTwitter.xml*.
2. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInTwitter`.
3. Atualizar o valor da **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da nova viagem de utilizador que criou (SignUpSignTwitter).
5. Guarde as suas alterações, faça o upload do ficheiro e, em seguida, selecione a nova política na lista.
6. Certifique-se de que a aplicação Azure AD B2C que criou é selecionada no campo de **aplicação Select** e, em seguida, testá-la clicando agora em **Executar**.

---
title: Configurar o início de sessão com uma conta Google utilizando políticas personalizadas
titleSuffix: Azure AD B2C
description: Instale o início de sessão com uma conta Google no Azure Ative Directory B2C utilizando políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8d02c86a1ff330aa4003299e1494a164089d8470
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188227"
---
# <a name="set-up-sign-in-with-a-google-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o início de sessão com uma conta Google utilizando políticas personalizadas no Diretório Ativo Do Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão para utilizadores com uma conta Google utilizando [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos no [Get iniciado com políticas personalizadas no Diretório Ativo B2C](custom-policy-get-started.md).
- Se ainda não tem uma conta Google, crie uma na [Create your Google Account](https://accounts.google.com/SignUp).

## <a name="register-the-application"></a>Registar o pedido

Para permitir o sessão de sessão para utilizadores a partir de uma conta da Google, é necessário criar um projeto de aplicação da Google.

1. Inscreva-se na [Consola do Google Developers](https://console.developers.google.com/) com as suas credenciais de conta.
2. Introduza um Nome de **Projeto,** clique em **Criar,** e depois certifique-se de que está a utilizar o novo projeto.
3. Selecione **Credenciais** no menu esquerdo e, em seguida, selecione **Criar credenciais > ID do cliente Oauth**.
4. Selecione o ecrã de **consentimento configurar**.
5. Selecione ou especifique um endereço de **e-mail**válido, forneça um nome de **produto** mostrado aos utilizadores, introduza `b2clogin.com` em **domínios autorizados**e, em seguida, clique em **Guardar**.
6. No **tipo de aplicação,** selecione **aplicação Web**.
7. Insira um **Nome** para a sua candidatura.
8. Nas **origens javaScript autorizadas,** insira `https://your-tenant-name.b2clogin.com` e em **URIs de redirecionamento autorizado,** introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua o seu nome de inquilino pelo nome do seu inquilino. Você precisa usar todas as letras minúsculas ao introduzir o nome do seu inquilino, mesmo que o inquilino seja definido com letras maiúsculas em Azure AD B2C.
8. Clique em **Criar**.
9. Copiar os valores do ID do **Cliente** e do segredo do **Cliente.** Você precisará de ambos para configurar o Google como um fornecedor de identidade no seu inquilino. O segredo do cliente é uma importante credencial de segurança.

## <a name="create-a-policy-key"></a>Criar uma chave política

Você precisa armazenar o segredo do cliente que você gravou anteriormente no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Na página 'Visão Geral', selecione Quadro de **Experiência de Identidade**.
5. Selecione **Teclas de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções,** escolha. `Manual`
7. Introduza um **nome** para a chave política. Por exemplo, `GoogleSecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Em **Segredo,** insira o seu segredo de cliente que gravou anteriormente.
9. Para **a utilização da chave,** selecione `Signature`.
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se pretender que os utilizadores assinem através de uma conta Google, tem de definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são utilizadas pelo Azure AD B2C para verificar se um utilizador específico se autenticou.

Pode definir uma conta Google como fornecedor de reclamações adicionando-a ao elemento **ClaimsProviders** no ficheiro de extensão da sua apólice.

1. Abra as *Extensões TrustFramework.xml*.
2. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo Fornecedor de **Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAUTH">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
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

4. Detete **client_id** ao ID da aplicação a partir do registo de candidatura.
5. Guarde o ficheiro.

### <a name="upload-the-extension-file-for-verification"></a>Faça upload do ficheiro de extensão para verificação

Por esta altura, já configuraste a tua política para que o Azure AD B2C saiba comunicar com o teu diretório Azure AD. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **Políticas Personalizadas** do seu inquilino Azure AD B2C, selecione **Política de Upload**.
2. Ativar **a sobreposição da apólice se ela existir,** e depois navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registe o prestador de sinistros

Neste momento, o fornecedor de identidade foi criado, mas não está disponível em nenhum dos ecrãs de inscrição/inscrição. Para disponibilizá-lo, cria-se uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modificá-la de modo a que também tenha o fornecedor de identidade Azure AD.

1. Abra o ficheiro *TrustFrameworkBase.xml* a partir do pacote de arranque.
2. Encontre e copie todo o conteúdo `Id="SignUpOrSignIn"`do elemento **UserJourney** que inclui .
3. Abra as *Extensões TrustFramework.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Colhe todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Mude o nome da identificação da viagem do utilizador. Por exemplo, `SignUpSignInGoogle`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição/inscrição. Se adicionar um elemento **ClaimsProviderSelection** para uma conta Google, um novo botão aparece quando um utilizador aterra na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na viagem de utilizador que criou.
2. Em **ClaimsProviderSelects,** adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para `GoogleExchange`um valor adequado, por exemplo:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de ligá-lo a uma ação. A ação, neste caso, é que o Azure AD B2C comunique com uma conta da Google para receber um símbolo.

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para ID que utilizou para **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```

    Atualize o valor do **TechnicalProfileReferenceId** para o ID do perfil técnico que criou anteriormente. Por exemplo, `Google-OAuth`.

3. Guarde o ficheiro *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação Azure AD B2C

A comunicação com o Azure AD B2C ocorre através de uma aplicação que se regista no seu inquilino B2C. Esta secção lista os passos opcionais que pode completar para criar uma aplicação de teste se ainda não o fez.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que depende

Atualize o ficheiro da parte de fiação (RP) que inicia a viagem de utilizador que criou.

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho e mude o nome. Por exemplo, mude o nome para *SignUpSignInGoogle.xml*.
2. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInGoogle`.
3. Atualizar o valor da **PublicPolicyUri** com o URI para a política. Por exemplo`http://contoso.com/B2C_1A_signup_signin_google`
4. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da nova viagem de utilizador que criou (SignUpSignGoogle).
5. Guarde as suas alterações, faça o upload do ficheiro e, em seguida, selecione a nova política na lista.
6. Certifique-se de que a aplicação Azure AD B2C que criou é selecionada no campo de **aplicação Select** e, em seguida, testá-la clicando agora em **Executar**.

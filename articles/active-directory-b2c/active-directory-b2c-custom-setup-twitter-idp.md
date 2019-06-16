---
title: Configurar o início de sessão com uma conta do Twitter ao utilizar políticas personalizadas no Azure Active Directory B2C | Documentos da Microsoft
description: Configure o início de sessão com uma conta do Twitter ao utilizar políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cc657e079949b8217031906efeb84049217d6493
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510238"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o início de sessão com uma conta do Twitter ao utilizar políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão dos utilizadores de uma conta do Twitter usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua os passos na [introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Se ainda não tiver uma conta do Twitter, criar um de cada [página de inscrição do Twitter](https://twitter.com/signup).

## <a name="create-an-application"></a>Criar uma aplicação

Para utilizar o Twitter como fornecedor de identidade no Azure AD B2C, terá de criar um aplicativo do Twitter.

1. Entrar para o [Twitter desenvolvedores](https://developer.twitter.com/en/apps) Web site com as suas credenciais de conta do Twitter.
2. Selecione **criar uma aplicação**.
3. Introduza um **nome da aplicação** e uma **descrição da aplicação**.
4. Na **URL do site**, introduza `https://your-tenant.b2clogin.com`. Substitua `your-tenant` com o nome do seu inquilino. Por exemplo, https://contosob2c.b2clogin.com.
5. Para o **URL de chamada de retorno**, introduza `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`. Substitua `your-tenant` com o nome do nome do seu inquilino e `your-policy-Id` com o identificador da sua política. Por exemplo, `b2c_1A_signup_signin_twitter`. Tem de utilizar todas as letras minúsculas, ao introduzir o nome do seu inquilino, mesmo que o inquilino está definido com letras maiúsculas no Azure AD B2C.
6. Na parte inferior da página, leia e aceite os termos e, em seguida, selecione **criar**.
7. Na **detalhes da aplicação** página, selecione **Editar > Editar detalhes**, marque a caixa **ativar o início de sessão com o Twitter**e, em seguida, selecione **guardar**.
8. Selecione **chaves e tokens** e registe o **chave de API de consumidor** e o **chave secreta da API de consumidor** valores a utilizar mais tarde.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Precisa armazenar a chave secreta que registou anteriormente no seu inquilino do Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C. Selecione o **filtro de diretório e subscrição** no menu superior e selecione o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Na página de descrição geral, selecione **arquitetura de experiências de identidade - pré-visualização**.
5. Selecione **chaves de política** e, em seguida, selecione **Add**.
6. Para **opções**, escolha `Manual`.
7. Introduza um **nome** para a chave de política. Por exemplo, `TwitterSecret`. O prefixo `B2C_1A_` é adicionado automaticamente o nome da sua chave.
8. Na **segredo**, introduza o seu segredo de cliente que registou anteriormente.
9. Para **utilização de chave**, selecione `Encryption`.
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um fornecedor de afirmações

Se pretender que os utilizadores iniciem sessão com uma conta do Twitter, terá de definir a conta como um fornecedor de afirmações com Azure AD B2C possam comunicar através de um ponto de extremidade. O ponto final fornece um conjunto de afirmações que são utilizadas pelo Azure AD B2C para verificar que um utilizador específico foi autenticado. 

Pode definir uma conta do Twitter como um fornecedor de afirmações ao adicioná-la para o **ClaimsProviders** elemento no arquivo de extensão da sua política.

1. Abra o *TrustFrameworkExtensions.xml*.
2. Encontrar o **ClaimsProviders** elemento. Se não existir, adicione-o sob o elemento raiz.
3. Adicionar um novo **ClaimsProvider** da seguinte forma:

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

4. Substitua o valor de **client_id** com a chave de consumidor que registou anteriormente.
5. Guarde o ficheiro.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o ficheiro de extensão de verificação

Agora, configurou sua política para que o Azure AD B2C sabe como se comunicar com a sua conta do LinkedIn. Tente carregar o ficheiro de extensão da sua política só para confirmar que ele não tem quaisquer problemas até agora.

1. Sobre o **políticas personalizadas** página no seu inquilino do Azure AD B2C, selecione **carregar política**.
2. Ativar **substituir a política, se existir**e, em seguida, procure e selecione o *TrustFrameworkExtensions.xml* ficheiro.
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de afirmações

Neste momento, o fornecedor de identidade tiver sido configurado, mas não está disponível em qualquer um dos ecrãs de inscrição ou início de sessão. Para disponibilizá-lo, pode criar um duplicado de um percurso do utilizador modelo existente e, em seguida, modificá-lo para que ele também tem o fornecedor de identidade do Twitter.

1. Abra o *TrustFrameworkBase.xml* ficheiro do pacote de iniciante.
2. Localize e copie o conteúdo inteiro dos **UserJourney** elemento que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize a **UserJourneys** elemento. Se o elemento não existir, adicione um.
4. Colar o conteúdo inteiro do **UserJourney** elemento que copiou como subordinado da **UserJourneys** elemento.
5. Mudar o nome o ID do percurso do utilizador. Por exemplo, `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Exibir o botão

O **ClaimsProviderSelection** elemento é semelhante a um botão do fornecedor de identidade numa tela de inscrição ou início de sessão. Se adicionar um **ClaimsProviderSelection** elemento para uma conta do Twitter, um novo botão exibido quando um utilizador que chegam na página.

1. Encontrar o **OrchestrationStep** elemento que inclui `Order="1"` no percurso do utilizador que criou.
2. Sob **ClaimsProviderSelects**, adicione o seguinte elemento. Defina o valor da **TargetClaimsExchangeId** para um valor adequado, por exemplo `TwitterExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligar o botão para uma ação

Agora que tem um botão no local, terá de ligá-lo a uma ação. A ação, neste caso, é para o Azure AD B2C comunicar com uma conta do Twitter para receber um token.

1. Encontrar o **OrchestrationStep** que inclua `Order="2"` no percurso do utilizador.
2. Adicione as seguintes **ClaimsExchange** elemento certificar-se de que utiliza o mesmo valor para o ID que utilizou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```
    
    Atualize o valor de **TechnicalProfileReferenceId** para o ID do perfil técnico que criou anteriormente. Por exemplo, `Twitter-OAUTH1`.

3. Guardar a *TrustFrameworkExtensions.xml* de ficheiro e carregá-lo novamente para a verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação do Azure AD B2C

Comunicação com o Azure AD B2c ocorre por meio de um aplicativo que criar no seu inquilino. Esta secção lista os passos opcionais que pode seguir para criar uma aplicação de teste, se ainda não o tiver feito.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C. Selecione o **filtro de diretório e subscrição** no menu superior e selecione o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Add**.
5. Introduza um nome para o aplicativo, por exemplo *testapp1*.
6. Para **aplicação Web / Web API**, selecione `Yes`e, em seguida, introduza `https://jwt.ms` para o **URL de resposta**.
7. Clique em **Criar**.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro da entidade confiadora de terceiros

Atualize o ficheiro da entidade confiadora de terceiros (RP) que inicia o percurso do utilizador que criou.

1. Faça uma cópia deles *SignUpOrSignIn.xml* no diretório de trabalho e renomeá-lo. Por exemplo, mude o nome para *SignUpSignInTwitter.xml*.
2. Abra o ficheiro novo e atualize o valor do **PolicyId** atributo **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInTwitter`.
3. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Atualize o valor do **ReferenceId** atributo **DefaultUserJourney** de acordo com o ID do percurso do utilizador novo que criou (SignUpSignTwitter).
5. Guardar as alterações, carregue o ficheiro e, em seguida, selecione a nova política na lista.
6. Certifique-se de que a aplicação do Azure AD B2C que criou está selecionada na **selecione aplicativo** campo e, em seguida, testá-lo ao clicar em **executar agora**.

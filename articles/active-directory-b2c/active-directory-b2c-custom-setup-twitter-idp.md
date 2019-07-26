---
title: Configurar a entrada com uma conta do Twitter usando políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: Configure a entrada com uma conta do Twitter usando políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 557d25c4921c9906be75bce03c326903e63432de
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464801"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada com uma conta do Twitter usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários de uma conta do Twitter usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em introdução [às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Se você ainda não tiver uma conta do Twitter, crie uma na [página de inscrição do Twitter](https://twitter.com/signup).

## <a name="create-an-application"></a>Criar uma aplicação

Para usar o Twitter como um provedor de identidade no Azure AD B2C, você precisa criar um aplicativo do Twitter.

1. Entre no site de [desenvolvedores do Twitter](https://developer.twitter.com/en/apps) com suas credenciais de conta do Twitter.
2. Selecione **criar um aplicativo**.
3. Insira um **nome de aplicativo** e uma **Descrição de aplicativo**.
4. Na **URL do site**, `https://your-tenant.b2clogin.com`digite. Substitua `your-tenant` pelo nome do seu locatário. Por exemplo, https://contosob2c.b2clogin.com.
5. Para a **URL de retorno**de `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`chamada, digite. Substitua `your-tenant` pelo nome do seu nome do locatário e `your-policy-Id` pelo identificador da política. Por exemplo, `b2c_1A_signup_signin_twitter`. Você precisa usar todas as letras minúsculas ao inserir o nome do locatário, mesmo que o locatário seja definido com letras maiúsculas no Azure AD B2C.
6. Na parte inferior da página, leia e aceite os termos e, em seguida, selecione **criar**.
7. Na página **detalhes do aplicativo** , selecione **Editar > Editar detalhes**, marque a caixa **Habilitar entrada com o Twitter**e, em seguida, selecione **salvar**.
8. Selecione **chaves e tokens** e registre a **chave de API do consumidor** e os valores de **chave secreta da API do consumidor** a serem usados posteriormente.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar a chave secreta que você registrou anteriormente em seu locatário Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório e o filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Na página Visão geral, selecione **Identity Experience Framework**.
5. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções**, escolha `Manual`.
7. Insira um **nome** para a chave de política. Por exemplo, `TwitterSecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Em **segredo**, insira o segredo do cliente que você registrou anteriormente.
9. Para **uso de chave**, `Encryption`selecione.
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se desejar que os usuários entrem usando uma conta do Twitter, você precisará definir a conta como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir uma conta do Twitter como um provedor de declarações adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão da política.

1. Abra o *TrustFrameworkExtensions. xml*.
2. Localize o elemento **ClaimsProviders** . Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo  Claims da seguinte maneira:

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

4. Substitua o valor de **client_id** pela chave do consumidor que você registrou anteriormente.
5. Guarde o ficheiro.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora, você configurou sua política para que Azure AD B2C saiba como se comunicar com sua conta do Twitter. Tente carregar o arquivo de extensão da política apenas para confirmar que ele não tem nenhum problema até o momento.

1. Na página **políticas personalizadas** no locatário do Azure ad B2C, selecione **carregar política**.
2. Habilite **substituir a política se ela existir**e, em seguida, navegue até e selecione o arquivo *TrustFrameworkExtensions. xml* .
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade foi configurado, mas não está disponível em nenhuma das telas de inscrição ou de entrada. Para disponibilizá-lo, crie uma duplicata de um percurso de usuário de modelo existente e, em seguida, modifique-o para que ele também tenha o provedor de identidade do Twitter.

1. Abra o arquivo *TrustFrameworkBase. xml* do pacote inicial.
2. Localize e copie todo o conteúdo do elemento **userjornada** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions. xml* e localize o  elemento userjornadas. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **userjornada** que você copiou como um filho do elemento userjornadas.
5. Renomeie a ID da jornada do usuário. Por exemplo, `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de provedor de identidade em uma tela de inscrição ou entrada. Se você adicionar um elemento **ClaimsProviderSelection** para uma conta do Twitter, um novo botão será exibido quando um usuário chegar à página.

1. Localize o elemento **OrchestrationStep** que inclui `Order="1"` no percurso do usuário que você criou.
2. Em **ClaimsProviderSelects**, adicione o seguinte elemento. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `TwitterExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que você tem um botão em vigor, é necessário vinculá-lo a uma ação. Nesse caso, a ação é para Azure AD B2C se comunicar com uma conta do Twitter para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` na jornada do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** , certificando-se de usar o mesmo valor para a ID que você usou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para a ID do perfil técnico criado anteriormente. Por exemplo, `Twitter-OAUTH1`.

3. Salve o arquivo *TrustFrameworkExtensions. xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com o Azure AD B2C ocorre por meio de um aplicativo que você cria em seu locatário. Esta seção lista as etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório e o filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo, por exemplo, *testapp1*.
6. Para **aplicativo Web/API Web**, selecione `Yes`e, em seguida `https://jwt.ms` , insira para a **URL de resposta**.
7. Clique em **Criar**.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo RP (terceira parte confiável) que inicia o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn. xml* em seu diretório de trabalho e renomeie-o. Por exemplo, renomeie-o como *SignUpSignInTwitter. xml*.
2. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInTwitter`.
3. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Atualize o valor do atributo **referenceid** em **DefaultUserJourney** para corresponder à ID do novo percurso do usuário que você criou (SignUpSignTwitter).
5. Salve as alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
6. Verifique se Azure AD B2C aplicativo que você criou está selecionado no campo **Selecionar aplicativo** e, em seguida, teste-o clicando em **executar agora**.

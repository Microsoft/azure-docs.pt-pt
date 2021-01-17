---
title: Configurar inscrição e iniciar sôm-in com uma conta WeChat
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição a clientes com contas WeChat nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1dd9af3abd723b3a7adad82f3bbb4a09fc4464f7
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538023"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta WeChat utilizando o Azure Ative Directory B2C


[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-wechat-application"></a>Criar uma aplicação WeChat

Para ativar o sessão de sessão para utilizadores com uma conta WeChat no Azure Ative Directory B2C (Azure AD B2C), é necessário criar uma aplicação no [centro de gestão do WeChat.](https://open.weixin.qq.com/) Se ainda não tiver uma conta WeChat, pode obter informações em [https://kf.qq.com](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) .

### <a name="register-a-wechat-application"></a>Registar uma aplicação WeChat

1. Inscreva-se [https://open.weixin.qq.com/](https://open.weixin.qq.com/) com as suas credenciais WeChat.
1. Selecione **管理中心** (centro de gestão).
1. Siga os passos para registar uma nova candidatura.
1. Introduza `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **授权回调域** (URL de retorno). Por exemplo, se o nome do seu inquilino for contoso, desemosse o `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` URL.
1. Copie o **ID app** e **a CHAVE APP.** Você precisará destes para adicionar o fornecedor de identidade ao seu inquilino.

::: zone pivot="b2c-user-flow"

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configure o WeChat como fornecedor de identidade no seu inquilino

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade** e, em seguida, selecione **WeChat (Preview)**.
1. Insira um **nome**. Por exemplo, *WeChat*.
1. Para o ID do **Cliente,** insira o ID app da aplicação WeChat que criou anteriormente.
1. Para o segredo do **Cliente,** insira a CHAVE APP que gravou.
1. Selecione **Guardar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do cliente que você já gravou no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Na página 'Visão Geral', selecione **Identity Experience Framework**.
5. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções,** escolha `Manual` .
7. Insira um **Nome** para a chave de política. Por exemplo, `WeChatSecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Em **Segredo,** insira o segredo do seu cliente que gravou anteriormente.
9. Para **a utilização da chave**, selecione `Signature` .
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se quiser que os utilizadores entrem através de uma conta WeChat, tem de definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir uma conta WeChat como fornecedor de sinistros adicionando-a ao elemento **ClaimsProviders** no ficheiro de extensão da sua política.

1. Abra a *TrustFrameworkExtensions.xml.*
2. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **Provider de Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>wechat.com</Domain>
      <DisplayName>WeChat (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="WeChat-OAUTH">
          <DisplayName>WeChat</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">wechat</Item>
            <Item Key="authorization_endpoint">https://open.weixin.qq.com/connect/qrconnect</Item>
            <Item Key="AccessTokenEndpoint">https://api.weixin.qq.com/sns/oauth2/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.weixin.qq.com/sns/userinfo</Item>
            <Item Key="scope">snsapi_login</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="AccessTokenResponseFormat">json</Item>
            <Item Key="ClientIdParamName">appid</Item>
            <Item Key="ClientSecretParamName">secret</Item>
            <Item Key="ExtraParamsInAccessTokenEndpointResponse">openid</Item>
            <Item Key="ExtraParamsInClaimsEndpointRequest">openid</Item>
            <Item Key="ResponseErrorCodeParamName">errcode</Item>
            <Item Key="external_user_identity_claim_id">unionid</Item>
          <Item Key="client_id">Your WeChat application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_WeChatSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="unionid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="wechat.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. **Desa** esta client_id ao ID do pedido a partir do registo do pedido.
5. Guarde o ficheiro.

### <a name="upload-the-extension-file-for-verification"></a>Faça o upload do ficheiro de extensão para verificação

Por esta altura, já configuraste a tua política para que o Azure AD B2C saiba comunicar com a tua conta WeChat. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **'Políticas Personalizadas'** no seu inquilino Azure AD B2C, selecione **'Política de Upload'.**
2. Ativar **a política em caso de existência** e, em seguida, navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de sinistros

Neste momento, o fornecedor de identidade foi criado, mas não está disponível em nenhum dos ecrãs de inscrição/inscrição. Para disponibilizá-lo, cria uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modifica-a de modo a que também tenha o fornecedor de identidade WeChat.

1. Abra o ficheiro *TrustFrameworkBase.xml* do pacote de arranque.
2. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"` .
3. Abra a *TrustFrameworkExtensions.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Mude o nome da identificação da viagem de utilizador. Por exemplo, `SignUpSignInWeChat`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição/inscrição. Se adicionar um elemento **ClaimsProviderSelection** para uma conta WeChat, um novo botão aparece quando um utilizador aterra na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada de utilizador que criou.
2. Em **ClaimsProviderSelects**, adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para um valor apropriado, por `WeChatExchange` exemplo:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="WeChatExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de o ligar a uma ação. A ação, neste caso, é para que a Azure AD B2C comunique com uma conta WeChat para receber um token.

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para iD que utilizou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="WeChatExchange" TechnicalProfileReferenceId="WeChat-OAuth" />
    ```

    Atualizar o valor do **TécnicoProfileReferenceD** para o ID do perfil técnico que criou anteriormente. Por exemplo, `WeChat-OAuth`.

3. Guarde o ficheiro *TrustFrameworkExtensions.xml* e faça o upload novamente para verificação.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-wechat-identity-provider-to-a-user-flow"></a>Adicione o fornecedor de identidade WeChat a um fluxo de utilizador 

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Clique no fluxo de utilizador que pretende adicionar ao fornecedor de identidade WeChat.
1. Sob os **fornecedores de identidade social,** selecione **WeChat**.
1. Selecione **Guardar**.
1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador executar**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que conta

Atualize o ficheiro do partido de funções (RP) que inicia a jornada do utilizador que criou.

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho, e mude o nome. Por exemplo, mude-o para *SignUpSignInWeChat.xml*.
1. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **a TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInWeChat`.
1. Atualize o valor da **PublicPolicyUri** com o URI para a apólice. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_WeChat`
1. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da nova jornada do utilizador que criou (SignUpSignWeChat).
1. Guarde as suas alterações, faça o upload do ficheiro.
1. De acordo com **as políticas personalizadas,** selecione **B2C_1A_signup_signin**.
1. Para **Select Application**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione **Executar agora** e selecione WeChat para iniciar sinstura com o WeChat e testar a política personalizada.

::: zone-end

---
title: Configurar inscrição e iniciar sôm-in com uma conta do Google
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas google nas suas aplicações utilizando o Azure Ative Directory B2C.
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
ms.openlocfilehash: 286f4f5ca8a18a67da2ac24beb4c6935de35778d
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538107"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurar o sº de inscrição e o inseúbmento com uma conta google utilizando o Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-google-application"></a>Criar uma aplicação google

Para ativar o sessão para utilizadores com uma conta Google no Azure Ative Directory B2C (Azure AD B2C), é necessário criar uma aplicação na [Consola do Google Developers](https://console.developers.google.com/). Para obter mais informações, consulte [configurar o OAuth 2.0](https://support.google.com/googleapi/answer/6158849). Se ainda não tiver uma conta no Google, pode inscrever-se [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) em .

1. Inscreva-se na Consola do [Google Developers](https://console.developers.google.com/) com as credenciais da sua conta Google.
1. No canto superior esquerdo da página, selecione a lista de projetos e, em seguida, selecione **New Project**.
1. Introduza um **Nome de Projeto,** selecione **Criar**.
1. Certifique-se de que está a utilizar o novo projeto selecionando a queda do projeto no canto superior esquerdo do ecrã, selecione o seu projeto pelo nome e, em seguida, selecione **Open**.
1. Selecione **o ecrã de consentimento OAuth** no menu esquerdo, selecione **External** e, em seguida, selecione **Criar**.
Insira um **Nome** para a sua inscrição. Introduza *b2clogin.com* na secção **de domínios autorizados** e selecione **Guardar**.
1. Selecione **Credenciais** no menu esquerdo e, em seguida, **selecione Criar credenciais**  >  **ID do cliente do autor daauth**.
1. Sob **o tipo de aplicação**, selecione **aplicação Web**.
1. Introduza um **Nome** para a sua aplicação, `https://your-tenant-name.b2clogin.com` introduza nas **origens Autorizadas javaScript**, e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URIs de redirecionamento autorizado**. `your-tenant-name`Substitua-o pelo nome do seu inquilino. Você precisa usar todas as letras minúsculas ao introduzir o seu nome de inquilino, mesmo que o inquilino seja definido com letras maiúsculas em Azure AD B2C.
1. Clique em **Criar**.
1. Copie os valores do **ID** do Cliente e **do segredo do Cliente.** Você precisará de ambos para configurar o Google como um fornecedor de identidade no seu inquilino. **O segredo do cliente** é uma importante credencial de segurança.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurar uma conta google como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade** e, em seguida, selecione **Google**.
1. Insira um **nome**. Por exemplo, *o Google*.
1. Para o ID do **Cliente,** insira o ID do Cliente da aplicação google que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo de Cliente que gravou.
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
7. Insira um **Nome** para a chave de política. Por exemplo, `GoogleSecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Em **Segredo,** insira o segredo do seu cliente que gravou anteriormente.
9. Para **a utilização da chave**, selecione `Signature` .
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se quiser que os utilizadores entrem através de uma conta google, tem de definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir uma conta Google como um fornecedor de sinistros adicionando-a ao elemento **ClaimsProviders** no ficheiro de extensão da sua política.

1. Abra a *TrustFrameworkExtensions.xml.*
2. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **Provider de Reclamações** da seguinte forma:

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
            <Item Key="UsePolicyInRedirectUri">false</Item>
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

4. **Desa** esta client_id ao ID do pedido a partir do registo do pedido.
5. Guarde o ficheiro.

### <a name="upload-the-extension-file-for-verification"></a>Faça o upload do ficheiro de extensão para verificação

Por esta altura, já configuraste a tua política para que o Azure AD B2C saiba comunicar com a tua conta Google. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **'Políticas Personalizadas'** no seu inquilino Azure AD B2C, selecione **'Política de Upload'.**
2. Ativar **a política em caso de existência** e, em seguida, navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de sinistros

Neste momento, o fornecedor de identidade foi criado, mas não está disponível em nenhum dos ecrãs de inscrição/inscrição. Para torná-lo disponível, cria uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modifica-a de modo a que também tenha o fornecedor de identidade da Google.

1. Abra o ficheiro *TrustFrameworkBase.xml* do pacote de arranque.
2. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"` .
3. Abra a *TrustFrameworkExtensions.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
5. Mude o nome da identificação da viagem de utilizador. Por exemplo, `SignUpSignInGoogle`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição/inscrição. Se adicionar um elemento **ClaimsProviderSelection** para uma conta google, um novo botão aparece quando um utilizador aterra na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada de utilizador que criou.
2. Em **ClaimsProviderSelects**, adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para um valor apropriado, por `GoogleExchange` exemplo:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de o ligar a uma ação. A ação, neste caso, é para que a Azure AD B2C comunique com uma conta da Google para receber um token.

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para iD que utilizou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```

    Atualizar o valor do **TécnicoProfileReferenceD** para o ID do perfil técnico que criou anteriormente. Por exemplo, `Google-OAuth`.

3. Guarde o ficheiro *TrustFrameworkExtensions.xml* e faça o upload novamente para verificação.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-google-identity-provider-to-a-user-flow"></a>Adicione o fornecedor de identidade do Google a um fluxo de utilizador 

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Clique no fluxo de utilizador que pretende adicionar ao fornecedor de identidade da Google.
1. Sob os **fornecedores de identidade social,** selecione **Google**.
1. Selecione **Guardar**.
1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador executar**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que conta

Atualize o ficheiro do partido de funções (RP) que inicia a jornada do utilizador que criou.

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho, e mude o nome. Por exemplo, mude-o para *SignUpSignInGoogle.xml*.
1. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **a TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInGoogle`.
1. Atualize o valor da **PublicPolicyUri** com o URI para a apólice. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_google`
1. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da nova jornada de utilizador que criou (SignUpSignGoogle).
1. Guarde as suas alterações, faça o upload do ficheiro.
1. De acordo com **as políticas personalizadas,** selecione **B2C_1A_signup_signin**.
1. Para **Select Application**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione **Executar agora** e selecione o Google para iniciar sinsução com o Google e testar a política personalizada.

::: zone-end

## <a name="next-steps"></a>Passos seguintes

Saiba como passar o [Token do Google para a sua aplicação.](idp-pass-through-user-flow.md)
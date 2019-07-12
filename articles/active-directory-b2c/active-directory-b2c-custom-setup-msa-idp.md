---
title: Adicionar conta da Microsoft (MSA) como um fornecedor de identidade com as políticas personalizadas no Azure Active Directory B2C
description: Exemplo com a Microsoft como fornecedor de identidade através do protocolo do OpenID Connect (OIDC).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9ae944a9d587e71c4be83bd524cf3875a7b52dd1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654155"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o início de sessão com uma conta Microsoft com as políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão para os utilizadores de uma conta Microsoft, utilizando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua os passos na [introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Se ainda não tiver uma conta Microsoft, criar um de cada [ https://www.live.com/ ](https://www.live.com/).

## <a name="add-an-application"></a>Adicionar uma aplicação

Para ativar o início de sessão para os utilizadores com uma conta Microsoft, terá de registar uma aplicação no inquilino do Azure AD. O inquilino do Azure AD não é o mesmo que o inquilino do Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD ao clicar o **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino do Azure AD.
1. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **registos das aplicações**.
1. Selecione **novo registo**.
1. Introduza um **nome** para a sua aplicação. Por exemplo, *MSAapp1*.
1. Sob **tipos de conta suportados**, selecione **contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, o Skype, Xbox, Outlook.com)** .
1. Sob **URI de redirecionamento (opcional)** , selecione **Web** e introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` na caixa de texto. Substitua `your-tenant-name` com o nome do seu inquilino do Azure AD B2C.
1. Selecione **registar**
1. Registo a **ID da aplicação (cliente)** mostrado na página de descrição geral do aplicativo. Precisará disto quando configurar o fornecedor de afirmações numa secção posterior.
1. Selecione **certificados e segredos**
1. Clique em **novo segredo do cliente**
1. Introduza um **Descrição** para o segredo, por exemplo *segredo de cliente da aplicação de MSA*e, em seguida, clique em **Add**.
1. Registrar a palavra-passe de aplicação mostrada os **valor** coluna. Utilize este valor na próxima seção.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Agora que criou a aplicação no seu inquilino do Azure AD, terá de armazenar o segredo do cliente desse aplicativo no seu inquilino do Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C. Selecione o **filtro de diretório e subscrição** no menu superior e selecione o diretório que contém o seu inquilino.
1. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
1. Na página de descrição geral, selecione **arquitetura de experiências de identidade**.
1. Selecione **chaves de política** e, em seguida, selecione **Add**.
1. Para **opções**, escolha `Manual`.
1. Introduza um **nome** para a chave de política. Por exemplo, `MSASecret`. O prefixo `B2C_1A_` é adicionado automaticamente o nome da sua chave.
1. Na **segredo**, introduza o segredo do cliente que registou na secção anterior.
1. Para **utilização de chave**, selecione `Signature`.
1. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um fornecedor de afirmações

Para permitir aos utilizadores iniciar sessão com uma conta Microsoft, terá de definir a conta como um fornecedor de afirmações com Azure AD B2C possam comunicar através de um ponto de extremidade. O ponto final fornece um conjunto de afirmações que são utilizadas pelo Azure AD B2C para verificar que um utilizador específico foi autenticado.

Pode definir do Azure AD como um fornecedor de afirmações ao adicionar o **ClaimsProvider** elemento no arquivo de extensão da sua política.

1. Abra o *TrustFrameworkExtensions.xml* ficheiro de política.
1. Encontrar o **ClaimsProviders** elemento. Se não existir, adicione-o sob o elemento raiz.
1. Adicionar um novo **ClaimsProvider** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. Substitua o valor de **client_id** com a aplicação do Azure AD *ID de aplicação (cliente)* que registou anteriormente.
1. Guarde o ficheiro.

Agora configurar a política para que o Azure AD B2C sabe como se comunicar com a sua aplicação da conta Microsoft no Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o ficheiro de extensão de verificação

Antes de continuar, carregar a política modificada para confirmar que ele não tem quaisquer problemas até agora.

1. Navegue para o inquilino do Azure AD B2C no portal do Azure e selecione **arquitetura de experiências de identidade**.
1. Sobre o **políticas personalizadas** página, selecione **carregar política personalizada**.
1. Ativar **substituir a política, se existir**e, em seguida, procure e selecione o *TrustFrameworkExtensions.xml* ficheiro.
1. Clique em **Carregar**.

Se não são apresentados erros no portal, avance para a secção seguinte.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de afirmações

Neste momento, configurou o fornecedor de identidade, mas ainda não está disponível em qualquer um dos ecrãs de inscrição ou início de sessão. Para disponibilizá-lo, crie um duplicado de um percurso do utilizador modelo existente, em seguida, modificá-lo para que ele também tem o fornecedor de identidade de conta Microsoft.

1. Abra o *TrustFrameworkBase.xml* ficheiro do pacote de iniciante.
1. Localize e copie o conteúdo inteiro dos **UserJourney** elemento que inclui `Id="SignUpOrSignIn"`.
1. Abra o *TrustFrameworkExtensions.xml* e localize a **UserJourneys** elemento. Se o elemento não existir, adicione um.
1. Colar o conteúdo inteiro do **UserJourney** elemento que copiou como subordinado da **UserJourneys** elemento.
1. Mudar o nome o ID do percurso do utilizador. Por exemplo, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Exibir o botão

O **ClaimsProviderSelection** elemento é semelhante a um botão do fornecedor de identidade numa tela de inscrição ou início de sessão. Se adicionar um **ClaimsProviderSelection** elemento para uma conta Microsoft, um novo botão é apresentado quando um utilizador que chegam na página.

1. Na *TrustFrameworkExtensions.xml* do ficheiro, encontre o **OrchestrationStep** elemento que inclui `Order="1"` no percurso do utilizador que criou.
1. Sob **ClaimsProviderSelects**, adicione o seguinte elemento. Defina o valor da **TargetClaimsExchangeId** para um valor adequado, por exemplo `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligar o botão para uma ação

Agora que tem um botão no local, terá de ligá-lo a uma ação. A ação, neste caso, é para o Azure AD B2C comunicar com uma conta Microsoft para receber um token.

1. Encontrar o **OrchestrationStep** que inclua `Order="2"` no percurso do utilizador.
1. Adicione as seguintes **ClaimsExchange** elemento certificar-se de que utiliza o mesmo valor para o ID que utilizou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para fazer a correspondência da `Id` valor no **TechnicalProfile** elemento do fornecedor de afirmações que adicionou anteriormente. Por exemplo, `MSA-OIDC`.

1. Guardar a *TrustFrameworkExtensions.xml* de ficheiro e carregá-lo novamente para a verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação do Azure AD B2C

Comunicação com o Azure AD B2C ocorre por meio de um aplicativo que cria no seu inquilino do Azure AD B2C. Esta secção lista os passos opcionais que pode seguir para criar uma aplicação de teste, se ainda não o tiver feito.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C. Selecione o **filtro de diretório e subscrição** no menu superior e selecione o diretório que contém o seu inquilino.
1. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
1. Selecione **aplicativos**e, em seguida, selecione **Add**.
1. Introduza um nome para o aplicativo, por exemplo *testapp1*.
1. Para **aplicação Web / Web API**, selecione `Yes`e, em seguida, introduza `https://jwt.ms` para o **URL de resposta**.
1. Clique em **Criar**.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro da entidade confiadora de terceiros

Atualize o ficheiro da entidade confiadora de terceiros (RP) que inicia o percurso do utilizador que criou.

1. Faça uma cópia deles *SignUpOrSignIn.xml* no diretório de trabalho e renomeá-lo. Por exemplo, mude o nome para *SignUpSignInMSA.xml*.
1. Abra o ficheiro novo e atualize o valor do **PolicyId** atributo **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInMSA`.
1. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_msa`
1. Atualize o valor do **ReferenceId** atributo **DefaultUserJourney** de acordo com o ID do percurso do utilizador que criou anteriormente (SignUpSignInMSA).
1. Guardar as alterações, carregue o ficheiro e, em seguida, selecione a nova política na lista.
1. Certificar-se de que essa aplicação do Azure AD B2C que criou na secção anterior (ou ao concluir os pré-requisitos, por exemplo *webapp1* ou *testapp1*) está selecionado no **selecione aplicativo** campo e, em seguida, testá-lo ao clicar em **executar agora**.
1. Selecione o **Account Microsoft** botão e iniciar sessão.

    Se a operação de início de sessão for bem-sucedida, é redirecionado para `jwt.ms` que apresenta a descodificar Token, semelhante a:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```

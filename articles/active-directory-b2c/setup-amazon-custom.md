---
title: Configurar o início de sessão com uma conta do Amazon com as políticas personalizadas no Azure Active Directory B2C | Documentos da Microsoft
description: Configure o início de sessão com uma conta do Amazon, no Azure Active Directory B2C, com as políticas personalizadas.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 46b58aad8a5cb71744aca9baaa3a27d4d1efe8e2
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655261"
---
# <a name="set-up-sign-in-with-an-amazon-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o início de sessão com uma conta do Amazon com as políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão para os utilizadores de uma conta da Amazon usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua os passos na [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).
- Se ainda não tiver uma conta da Amazon, criar um de cada [ https://www.amazon.com/ ](https://www.amazon.com/).

## <a name="register-the-application"></a>Registar a aplicação

Para ativar o início de sessão para os utilizadores de uma conta da Amazon, terá de criar uma aplicação da Amazon.

1. Entrar para o [Centro de programadores do Amazon](https://login.amazon.com/) com as suas credenciais de conta da Amazon.
2. Se ainda não o fez, clique em **Inscreva-se**, siga os passos de registo do desenvolvedor e aceite a política.
3. Selecione **registar o novo aplicativo**.
4. Introduza um **Name**, **Descrição**, e **URL de aviso de privacidade**e, em seguida, clique em **guardar**. O aviso de privacidade é uma página que gere que fornece informações de privacidade aos utilizadores.
5. Na **as definições da Web** secção, copie os valores de **ID de cliente**. Selecione **Mostrar segredo** para obter o segredo do cliente e, em seguida, copiá-lo. Terá de ambos para configurar uma conta do Amazon como um fornecedor de identidade no seu inquilino. **Segredo do cliente** é uma credencial de segurança importantes.
6. No **as definições da Web** secção, selecione **editar**e, em seguida, introduza `https://your-tenant-name.b2clogin.com` no **permitido origens do JavaScript** e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` na **permitidos Devolver a URLs**. Substitua `your-tenant-name` com o nome do seu inquilino. Utilize só letras minúsculas, ao introduzir o nome do seu inquilino, mesmo que o inquilino está definido com letras maiúsculas no Azure AD B2C.
7. Clique em **Guardar**.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Tem de armazenar o segredo do cliente que registou anteriormente no seu inquilino do Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Na página de descrição geral, selecione **arquitetura de experiências de identidade**.
5. Selecione **chaves de política** e, em seguida, selecione **Add**.
6. Para **opções**, escolha `Manual`.
7. Introduza um **nome** para a chave de política. Por exemplo, `AmazonSecret`. O prefixo `B2C_1A_` é adicionado automaticamente o nome da sua chave.
8. Na **segredo**, introduza o seu segredo de cliente que registou anteriormente.
9. Para **utilização de chave**, selecione `Signature`.
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um fornecedor de afirmações

Se pretender que os utilizadores iniciem sessão com uma conta da Amazon, terá de definir a conta como um fornecedor de afirmações com Azure AD B2C possam comunicar através de um ponto de extremidade. O ponto final fornece um conjunto de afirmações que são utilizadas pelo Azure AD B2C para verificar que um utilizador específico foi autenticado.

Pode definir uma conta do Amazon como um fornecedor de afirmações ao adicioná-la para o **ClaimsProviders** elemento no arquivo de extensão da sua política.


1. Abra o *TrustFrameworkExtensions.xml*.
2. Encontrar o **ClaimsProviders** elemento. Se não existir, adicione-o sob o elemento raiz.
3. Adicionar um novo **ClaimsProvider** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAUTH">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Definir **client_id** para o ID de aplicação do registo de aplicação.
5. Guarde o ficheiro.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o ficheiro de extensão de verificação

Agora, configurou sua política para que o Azure AD B2C sabe como se comunicar com o diretório do Azure AD. Tente carregar o ficheiro de extensão da sua política só para confirmar que ele não tem quaisquer problemas até agora.

1. Sobre o **políticas personalizadas** página no seu inquilino do Azure AD B2C, selecione **carregar política**.
2. Ativar **substituir a política, se existir**e, em seguida, procure e selecione o *TrustFrameworkExtensions.xml* ficheiro.
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de afirmações

Neste momento, o fornecedor de identidade tiver sido configurado, mas não está disponível em qualquer uma dos ecrãs de início de sessão-inscrição/início de sessão. Para disponibilizá-lo, pode criar um duplicado de um percurso do utilizador modelo existente e, em seguida, modificá-lo para que ele também tem o fornecedor de identidade da Amazon.

1. Abra o *TrustFrameworkBase.xml* ficheiro do pacote de iniciante.
2. Localize e copie o conteúdo inteiro dos **UserJourney** elemento que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize a **UserJourneys** elemento. Se o elemento não existir, adicione um.
4. Colar o conteúdo inteiro do **UserJourney** elemento que copiou como subordinado da **UserJourneys** elemento.
5. Mudar o nome o ID do percurso do utilizador. Por exemplo, `SignUpSignInAmazon`.

### <a name="display-the-button"></a>Exibir o botão

O **ClaimsProviderSelection** elemento é semelhante a um botão do fornecedor de identidade numa tela de início de sessão-inscrição/início de sessão. Se adicionar um **ClaimsProviderSelection** elemento de uma conta da Amazon, um novo botão exibido quando um utilizador que chegam na página.

1. Encontrar o **OrchestrationStep** elemento que inclui `Order="1"` no percurso do utilizador que criou.
2. Sob **ClaimsProviderSelects**, adicione o seguinte elemento. Defina o valor da **TargetClaimsExchangeId** para um valor adequado, por exemplo `AmazonExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligar o botão para uma ação

Agora que tem um botão no local, terá de ligá-lo a uma ação. A ação, neste caso, é para o Azure AD B2C comunicar com uma conta da Amazon para receber um token.

1. Encontrar o **OrchestrationStep** que inclua `Order="2"` no percurso do utilizador.
2. Adicione as seguintes **ClaimsExchange** elemento certificar-se de que utiliza o mesmo valor para o ID que utilizou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para o ID do perfil técnico que criou anteriormente. Por exemplo, `Amazon-OAuth`.

3. Guardar a *TrustFrameworkExtensions.xml* de ficheiro e carregá-lo novamente para a verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação do Azure AD B2C

Comunicação com o Azure AD B2c ocorre por meio de um aplicativo que criar no seu inquilino. Esta secção lista os passos opcionais que pode seguir para criar uma aplicação de teste, se ainda não o tiver feito.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Add**.
5. Introduza um nome para o aplicativo, por exemplo *testapp1*.
6. Para **aplicação Web / Web API**, selecione `Yes`e, em seguida, introduza `https://jwt.ms` para o **URL de resposta**.
7. Clique em **Criar**.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro da entidade confiadora de terceiros

Atualize o ficheiro da entidade confiadora de terceiros (RP) que inicia o percurso do utilizador que criou.

1. Faça uma cópia deles *SignUpOrSignIn.xml* no diretório de trabalho e renomeá-lo. Por exemplo, mude o nome para *SignUpSignInAmazon.xml*.
2. Abra o ficheiro novo e atualize o valor do **PolicyId** atributo **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInAmazon`.
3. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_amazon`
4. Atualize o valor do **ReferenceId** atributo **DefaultUserJourney** de acordo com o ID do percurso do utilizador novo que criou (SignUpSignAmazon).
5. Guardar as alterações, carregue o ficheiro e, em seguida, selecione a nova política na lista.
6. Certifique-se de que a aplicação do Azure AD B2C que criou está selecionada na **selecione aplicativo** campo e, em seguida, testá-lo ao clicar em **executar agora**.

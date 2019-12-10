---
title: Configurar a entrada com uma conta da conta da Microsoft usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Como usar políticas personalizadas para habilitar a MSA (conta da Microsoft) como um provedor de identidade usando o protocolo OpenID Connect (OIDC).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 393e6f0b87cbd6a548825276da3f59863e2833eb
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948373"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada com um conta Microsoft usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários de um conta Microsoft usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory B2C (Azure ad B2C).

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em introdução [às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Se você ainda não tiver uma conta Microsoft, crie uma em [https://www.live.com/](https://www.live.com/).

## <a name="add-an-application"></a>Adicionar uma aplicação

Para habilitar a entrada para usuários com um conta Microsoft, você precisa registrar um aplicativo no locatário do Azure AD. O locatário do Azure AD não é o mesmo que seu Azure AD B2C locatário.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure AD selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário do Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **registros de aplicativo**.
1. Selecione **novo registro**.
1. Insira um **nome** para seu aplicativo. Por exemplo, *MSAapp1*.
1. Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** .
1. Em **URI de redirecionamento (opcional)** , selecione **Web** e insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` na caixa de texto. Substitua `your-tenant-name` pelo nome do locatário do Azure AD B2C.
1. Selecionar **registro**
1. Registre a **ID do aplicativo (cliente)** mostrada na página Visão geral do aplicativo. Você precisará disso quando configurar o provedor de declarações em uma seção posterior.
1. Selecionar **certificados & segredos**
1. Clique em **novo segredo do cliente**
1. Insira uma **Descrição** para o segredo, por exemplo, *segredo do cliente do aplicativo MSA*e clique em **Adicionar**.
1. Registre a senha do aplicativo mostrada na coluna **valor** . Você usará esse valor na próxima seção.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Agora que você criou o aplicativo em seu locatário do Azure AD, você precisa armazenar o segredo do cliente do aplicativo em seu locatário Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém seu locatário.
1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
1. Na página Visão geral, selecione **Identity Experience Framework**.
1. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **nome** para a chave de política. Por exemplo, `MSASecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
1. Em **segredo**, insira o segredo do cliente que você registrou na seção anterior.
1. Para **uso de chave**, selecione `Signature`.
1. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Para permitir que os usuários entrem usando um conta Microsoft, você precisa definir a conta como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure ad como um provedor de declarações adicionando o elemento **Claims** no arquivo de extensão da política.

1. Abra o arquivo de política *TrustFrameworkExtensions. xml* .
1. Localize o elemento **ClaimsProviders** . Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Claims** da seguinte maneira:

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

1. Substitua o valor de **client_id** pelo *ID de aplicativo (cliente)* do aplicativo do Azure AD que você registrou anteriormente.
1. Guarde o ficheiro.

Agora você configurou sua política para que Azure AD B2C saiba como se comunicar com seu aplicativo conta Microsoft no Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Antes de continuar, carregue a política modificada para confirmar que ela não tem nenhum problema até o momento.

1. Navegue até seu locatário do Azure AD B2C no portal do Azure e selecione **Identity Experience Framework**.
1. Na página **políticas personalizadas** , selecione **carregar política personalizada**.
1. Habilite **substituir a política se ela existir**e, em seguida, navegue até e selecione o arquivo *TrustFrameworkExtensions. xml* .
1. Clique em **Carregar**.

Se nenhum erro for exibido no portal, continue para a próxima seção.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, você configurou o provedor de identidade, mas ele ainda não está disponível em nenhuma das telas de inscrição ou entrada. Para disponibilizá-lo, crie uma duplicata de um percurso de usuário de modelo existente e, em seguida, modifique-o para que ele também tenha o provedor de identidade conta Microsoft.

1. Abra o arquivo *TrustFrameworkBase. xml* do pacote inicial.
1. Localize e copie todo o conteúdo do elemento **userjornada** que inclui `Id="SignUpOrSignIn"`.
1. Abra o *TrustFrameworkExtensions. xml* e localize o elemento **userjornadas** . Se o elemento não existir, adicione um.
1. Cole todo o conteúdo do elemento **userjornada** que você copiou como um filho do elemento **userjornadas** .
1. Renomeie a ID da jornada do usuário. Por exemplo, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de provedor de identidade em uma tela de inscrição ou entrada. Se você adicionar um elemento **ClaimsProviderSelection** para um conta Microsoft, um novo botão será exibido quando um usuário chegar à página.

1. No arquivo *TrustFrameworkExtensions. xml* , localize o elemento **OrchestrationStep** que inclui `Order="1"` no percurso do usuário que você criou.
1. Em **ClaimsProviderSelects**, adicione o seguinte elemento. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que você tem um botão em vigor, é necessário vinculá-lo a uma ação. A ação, nesse caso, é para Azure AD B2C se comunicar com um conta Microsoft para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` na jornada do usuário.
1. Adicione o seguinte elemento **ClaimsExchange** , certificando-se de usar o mesmo valor para a ID que você usou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para corresponder ao valor de `Id` no elemento **TechnicalProfile** do provedor de declarações que você adicionou anteriormente. Por exemplo, `MSA-OIDC`.

1. Salve o arquivo *TrustFrameworkExtensions. xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com Azure AD B2C ocorre por meio de um aplicativo que você registra em seu locatário B2C. Esta seção lista as etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo RP (terceira parte confiável) que inicia o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn. xml* em seu diretório de trabalho e renomeie-o. Por exemplo, renomeie-o como *SignUpSignInMSA. xml*.
1. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInMSA`.
1. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_msa`
1. Atualize o valor do atributo **referenceid** em **DefaultUserJourney** para corresponder à ID da jornada do usuário que você criou anteriormente (SignUpSignInMSA).
1. Salve as alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
1. Verifique se Azure AD B2C aplicativo que você criou na seção anterior (ou concluindo os pré-requisitos, por exemplo, *webapp1* ou *testapp1*) está selecionado no campo **Selecionar aplicativo** e, em seguida, teste-o clicando em **executar agora**.
1. Selecione o botão **conta da Microsoft** e entre.

    Se a operação de entrada for bem-sucedida, você será redirecionado para `jwt.ms` que exibe o token decodificado, semelhante a:

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

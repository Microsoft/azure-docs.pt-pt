---
title: Configurar o sôm-in com uma conta da Microsoft utilizando políticas personalizadas
titleSuffix: Azure AD B2C
description: Como utilizar políticas personalizadas para permitir a Conta da Microsoft (MSA) como um fornecedor de identidade utilizando o protocolo OpenID Connect (OIDC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 282d60b1894ffa186a6b5b6b5630aefa9e961572
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345138"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o sôm-in com uma conta Microsoft utilizando políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de saúde dos utilizadores a partir de uma conta microsoft utilizando [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos em [Começar com políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md).
- Se ainda não tiver uma conta Microsoft, crie uma em [https://www.live.com/](https://www.live.com/) .

## <a name="register-an-application"></a>Registar uma aplicação

Para ativar o sôm-in para utilizadores com uma conta Microsoft, é necessário registar uma aplicação dentro do inquilino AZure AD. O inquilino da AD AZure não é o mesmo que o seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
1. Selecione **Novo registo**.
1. Insira um **Nome** para a sua inscrição. Por exemplo, *MSAapp1*.
1. Nos **tipos de contas suportadas**, selecione Contas em qualquer **diretório organizacional (qualquer diretório AD AZure - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)**.
1. Em **Redirecionamento URI (opcional)**, selecione **Web** e `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` introduza na caixa de texto. Substitua `<tenant-name>` o nome do seu inquilino Azure AD B2C.
1. Selecione **Registar-se**
1. Grave o **ID de Aplicação (cliente)** mostrado na página geral da aplicação. Precisa disto quando configurar o fornecedor de sinistros numa secção posterior.
1. Selecione **certificados & segredos**
1. Clique em **Novo segredo do cliente**
1. Introduza uma **Descrição** para o segredo, por exemplo *MSA Application Client Secret*, e, em seguida, clique em **Adicionar**.
1. Grave a palavra-passe da aplicação mostrada na coluna **Valor.** Usa este valor na secção seguinte.

## <a name="configuring-optional-claims"></a>Configurar pedidos opcionais

Se quiser obter as `family_name` `given_name` reclamações da Azure AD, pode configurar reclamações opcionais para a sua aplicação no portal Azure UI ou manifesto de aplicação. Para mais informações, consulte [Como fornecer reclamações opcionais à sua aplicação AD AZure.](../active-directory/develop/active-directory-optional-claims.md)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Procure e selecione **Azure Active Directory**.
1. A partir da secção **Gerir,** selecione **registos de Aplicações.**
1. Selecione a aplicação para a inscrição que pretende configurar pedidos opcionais na lista.
1. A partir da secção **'Gerir',** selecione **a configuração token (pré-visualização)**.
1. **Selecione Adicionar reclamação opcional**.
1. Selecione o tipo de símbolo que deseja configurar.
1. Selecione as reclamações opcionais a adicionar.
1. Clique em **Adicionar**.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Agora que criou a aplicação no seu inquilino AZure AD, precisa guardar o segredo do cliente da aplicação no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Na página 'Visão Geral', selecione **Identity Experience Framework**.
1. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha `Manual` .
1. Insira um **Nome** para a chave de política. Por exemplo, `MSASecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
1. Em **Segredo,** insira o segredo do cliente que gravou na secção anterior.
1. Para **a utilização da chave**, selecione `Signature` .
1. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Para permitir que os seus utilizadores assinem através de uma conta Microsoft, é necessário definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir a Azure AD como fornecedor de sinistros adicionando o elemento **ClaimsProvider** no ficheiro de extensão da sua política.

1. Abra o ficheiro de política *TrustFrameworkExtensions.xml.*
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Provider de Reclamações** da seguinte forma:

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
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
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

1. Substitua o valor de **client_id** pelo ID de *Aplicação (cliente)* da aplicação Azure que registou anteriormente.
1. Guarde o ficheiro.

Agora configuraste a tua política para que o Azure AD B2C saiba comunicar com a tua aplicação de conta Microsoft em Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Faça o upload do ficheiro de extensão para verificação

Antes de continuar, carre deixe de carregar a política modificada para confirmar que não tem quaisquer problemas até agora.

1. Navegue até ao seu inquilino Azure AD B2C no portal Azure e selecione **o Quadro de Experiência de Identidade.**
1. Na página **de políticas personalizadas,** selecione **'Carregar' a política personalizada.**
1. Ativar **a política em caso de existência** e, em seguida, navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
1. Clique em **Carregar**.

Se não forem apresentados erros no portal, continue para a secção seguinte.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de sinistros

Neste momento, criou o fornecedor de identidade, mas ainda não está disponível em nenhum dos ecrãs de inscrição ou inscrição. Para disponibilizá-lo, crie uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modifique-a de modo a que também tenha o fornecedor de identidade da conta microsoft.

1. Abra o ficheiro *TrustFrameworkBase.xml* do pacote de arranque.
1. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"` .
1. Abra a *TrustFrameworkExtensions.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
1. Cole todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
1. Mude o nome da identificação da viagem de utilizador. Por exemplo, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição ou de inscrição. Se adicionar um elemento **ClaimsProviderSelection** para uma conta Microsoft, é apresentado um novo botão quando um utilizador aterra na página.

1. No ficheiro *TrustFrameworkExtensions.xml,* encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada de utilizador que criou.
1. Em **ClaimsProviderSelects**, adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para um valor apropriado, por `MicrosoftAccountExchange` exemplo:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de o ligar a uma ação. A ação, neste caso, é que o Azure AD B2C comunique com uma conta microsoft para receber um token.

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
1. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para o ID que utilizou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Atualizar o valor do **TécnicoProfileReferenceD** para corresponder ao `Id` valor no elemento **TécnicoProfile** do fornecedor de sinistros que adicionou anteriormente. Por exemplo, `MSA-OIDC`.

1. Guarde o ficheiro *TrustFrameworkExtensions.xml* e faça o upload novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação AZure AD B2C

A comunicação com o Azure AD B2C ocorre através de uma aplicação que regista no seu inquilino B2C. Esta secção lista os passos opcionais que pode completar para criar uma aplicação de teste se ainda não o fez.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que conta

Atualize o ficheiro do partido de funções (RP) que inicia a jornada do utilizador que criou.

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho, e mude o nome. Por exemplo, mude-o para *SignUpSignInMSA.xml*.
1. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **a TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInMSA`.
1. Atualize o valor da **PublicPolicyUri** com o URI para a apólice. Por exemplo`http://contoso.com/B2C_1A_signup_signin_msa`
1. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da jornada do utilizador que criou anteriormente (SignUpSignInMSA).
1. Guarde as suas alterações, carre fique no upload do ficheiro e, em seguida, selecione a nova política da lista.
1. Certifique-se de que a aplicação AD B2C AD que criou na secção anterior (ou preenchendo os pré-requisitos, por exemplo *webapp1* ou *testapp1*) é selecionada no campo **de aplicações Select** e, em seguida, testá-la clicando **em Executar agora**.
1. Selecione o botão **Conta Microsoft** e inscreva-se.

    Se a operação de inscrição for bem sucedida, é redirecionado para `jwt.ms` o qual exibe o Token descodificado, semelhante a:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://<tenant-name>.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
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

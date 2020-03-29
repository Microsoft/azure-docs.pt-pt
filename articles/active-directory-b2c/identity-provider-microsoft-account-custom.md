---
title: Configurar o início de sessão com uma conta Microsoft utilizando políticas personalizadas
titleSuffix: Azure AD B2C
description: Como utilizar políticas personalizadas para permitir a Microsoft Account (MSA) como fornecedor de identidade utilizando o protocolo OpenID Connect (OIDC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7d8fbddc86c0d05d7b0d4ce46cb06c5fc92a2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188125"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o início de sessão com uma conta Microsoft utilizando políticas personalizadas no Diretório Ativo Do Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de sessão para utilizadores a partir de uma conta Microsoft utilizando [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos em [Get started com políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md).
- Se ainda não tem uma conta Microsoft, [https://www.live.com/](https://www.live.com/)crie uma em ...

## <a name="register-an-application"></a>Registar uma aplicação

Para ativar o início de sessão para utilizadores com uma conta Microsoft, é necessário registar uma aplicação dentro do inquilino Da Azure. O inquilino da Azure AD não é o mesmo que o seu inquilino Azure AD B2C.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione registos de **Aplicações**.
1. Selecione **Novo registo**.
1. Insira um **Nome** para a sua candidatura. Por exemplo, *MSAapp1*.
1. No âmbito dos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**.
1. Em **Redirecione o URI (opcional)**, selecione **Web** e introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` na caixa de texto. Substitua `your-tenant-name` pelo nome de inquilino Azure AD B2C.
1. Selecione **Registar**
1. Grave o ID de **Aplicação (cliente)** mostrado na página de visão geral da aplicação. Precisa disto quando configurar o fornecedor de sinistros numa secção posterior.
1. Selecione **Certificados & segredos**
1. Clique em **novo segredo de cliente**
1. Introduza uma **Descrição** para o segredo, por exemplo, *MSA Application Client Secret,* e, em seguida, clique em **Adicionar**.
1. Grave a palavra-passe da aplicação mostrada na coluna **Valor.** Usa este valor na secção seguinte.

## <a name="configuring-optional-claims"></a>Configurar reclamações opcionais

Se quiser obter `family_name` as `given_name` reclamações e reclamações da Azure AD, pode configurar reclamações opcionais para a sua aplicação no portal Azure UI ou manifesto de aplicação. Para mais informações, consulte [Como fornecer reclamações opcionais à sua aplicação Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Procure e selecione **Azure Active Directory**.
1. A partir da secção **Gerir,** selecione registos de **Aplicações**.
1. Selecione a aplicação que pretende configurar reclamações opcionais na lista.
1. A partir da secção **Gerir,** selecione **configuração token (pré-visualização)**.
1. **Selecione Adicionar reclamação opcional**.
1. Selecione o tipo de ficha que pretende configurar.
1. Selecione as reclamações opcionais a adicionar.
1. Clique em **Adicionar**.

## <a name="create-a-policy-key"></a>Criar uma chave política

Agora que criou a aplicação no seu inquilino DaD Azure, precisa de guardar o segredo do cliente dessa aplicação no seu inquilino Azure AD B2C.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Na página 'Visão Geral', selecione Quadro de **Experiência de Identidade**.
1. Selecione **Teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha. `Manual`
1. Introduza um **nome** para a chave política. Por exemplo, `MSASecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
1. Em **Segredo,** insira o segredo do cliente que gravou na secção anterior.
1. Para **a utilização da chave,** selecione `Signature`.
1. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Para permitir que os seus utilizadores assinem através de uma conta Microsoft, é necessário definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são utilizadas pelo Azure AD B2C para verificar se um utilizador específico se autenticou.

Pode definir a AD Azure como um fornecedor de sinistros adicionando o elemento **Reclamações Fornecedor** no ficheiro de extensão da sua apólice.

1. Abra o ficheiro de política *TrustFrameworkExtensions.xml.*
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo Fornecedor de **Reclamações** da seguinte forma:

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

1. Substitua o valor da **client_id** com o ID de *Aplicação (cliente)* da aplicação da AD Azure que gravou anteriormente.
1. Guarde o ficheiro.

Configuraa agora a sua política para que o Azure AD B2C saiba comunicar com a sua aplicação de conta Microsoft em Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Faça upload do ficheiro de extensão para verificação

Antes de continuar, faça upload da política modificada para confirmar que não tem quaisquer problemas até agora.

1. Navegue ao seu inquilino Azure AD B2C no portal Azure e selecione **Identity Experience Framework**.
1. Na página **de políticas personalizadas,** selecione **A política personalizada de upload**.
1. Ativar **a sobreposição da apólice se ela existir,** e depois navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
1. Clique em **Carregar**.

Se não houver erros no portal, continue para a secção seguinte.

## <a name="register-the-claims-provider"></a>Registe o prestador de sinistros

Neste momento, criou o fornecedor de identidade, mas ainda não está disponível em nenhum dos ecrãs de inscrição ou inscrição. Para disponibilizá-lo, crie uma duplicação de uma viagem de utilizador modelo existente, em seguida, modifique-a de modo a que também tenha o fornecedor de identidade da conta microsoft.

1. Abra o ficheiro *TrustFrameworkBase.xml* a partir do pacote de arranque.
1. Encontre e copie todo o conteúdo `Id="SignUpOrSignIn"`do elemento **UserJourney** que inclui .
1. Abra as *Extensões TrustFramework.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
1. Colhe todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
1. Mude o nome da identificação da viagem do utilizador. Por exemplo, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade num ecrã de inscrição ou de inscrição. Se adicionar um elemento **ClaimsProviderSelection** para uma conta Microsoft, é apresentado um novo botão quando um utilizador aterra na página.

1. No ficheiro *TrustFrameworkExtensions.xml,* encontre o elemento `Order="1"` **OrchestrationStep** que inclui na viagem de utilizador que criou.
1. Em **ClaimsProviderSelects,** adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para `MicrosoftAccountExchange`um valor adequado, por exemplo:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de ligá-lo a uma ação. A ação, neste caso, é que o Azure AD B2C comunique com uma conta microsoft para receber um símbolo.

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
1. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para o ID que utilizou para **targetClaimsExchangeId:**

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Atualize o valor do **TechnicalProfileReferenceId** para corresponder ao `Id` valor no elemento **TechnicalProfile** do fornecedor de sinistros que adicionou anteriormente. Por exemplo, `MSA-OIDC`.

1. Guarde o ficheiro *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação Azure AD B2C

A comunicação com o Azure AD B2C ocorre através de uma aplicação que se regista no seu inquilino B2C. Esta secção lista os passos opcionais que pode completar para criar uma aplicação de teste se ainda não o fez.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que depende

Atualize o ficheiro da parte de fiação (RP) que inicia a viagem de utilizador que criou.

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho e mude o nome. Por exemplo, mude o nome para *SignUpSignInMSA.xml*.
1. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInMSA`.
1. Atualizar o valor da **PublicPolicyUri** com o URI para a política. Por exemplo`http://contoso.com/B2C_1A_signup_signin_msa`
1. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da viagem de utilizador que criou anteriormente (SignUpSignInMSA).
1. Guarde as suas alterações, faça o upload do ficheiro e, em seguida, selecione a nova política na lista.
1. Certifique-se de que a aplicação Azure AD B2C que criou na secção anterior (ou completando os pré-requisitos, por exemplo *webapp1* ou *testapp1*) é selecionada no campo de **aplicação Select** e, em seguida, testá-la clicando agora em **Executar**.
1. Selecione o botão **Conta Microsoft** e inscreva-se.

    Se a operação de entrada for bem sucedida, `jwt.ms` é redirecionado para o qual exibe o Token Descodificado, semelhante a:

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

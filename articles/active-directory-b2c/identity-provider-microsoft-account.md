---
title: Configurar insinusada e iniciar sôm-in com uma Conta Microsoft
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição a clientes com Contas Microsoft nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 12e8222ad59aae31baa7a549519c97550b711703
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579948"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurar o sº de inscrição e o inseúbmento com uma conta microsoft utilizando o Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-microsoft-account-application"></a>Criar uma aplicação de conta Microsoft

Para permitir o acesso aos utilizadores com uma conta Microsoft no Azure Ative Directory B2C (Azure AD B2C), é necessário criar uma aplicação no [portal Azure](https://portal.azure.com). Para obter mais informações, consulte [Registar uma aplicação com a plataforma de identidade da Microsoft.](../active-directory/develop/quickstart-register-app.md) Se ainda não tiver uma conta Microsoft, pode obter uma em [https://www.live.com/](https://www.live.com/) .

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
1. Selecione **Novo registo**.
1. Insira um **Nome** para a sua inscrição. Por exemplo, *MSAapp1*.
1. Nos **tipos de contas suportadas**, selecione Contas em qualquer **diretório organizacional (qualquer diretório AD AZure - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)**.

   Para obter mais informações sobre as diferentes seleções de tipos de conta, consulte [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](../active-directory/develop/quickstart-register-app.md).
1. Em **Redirecionar URI (opcional)**, selecione **Web** e insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Se utilizar um [domínio personalizado,](custom-domain.md)insira `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-tenant-name`Substitua-o pelo nome do seu inquilino e `your-domain-name` pelo seu domínio personalizado.
1. Selecione **Registar-se**
1. Grave o **ID de Aplicação (cliente)** mostrado na página geral da aplicação. Precisa da identificação do cliente quando configurar o fornecedor de identidade na secção seguinte.
1. Selecione **certificados & segredos**
1. Clique em **Novo segredo do cliente**
1. Introduza uma **Descrição** para o segredo, por *exemplo, aplicação da palavra-passe 1* e, em seguida, clique em **Adicionar**.
1. Grave a palavra-passe da aplicação mostrada na coluna **Valor.** Precisa do segredo do cliente quando configurar o fornecedor de identidade na secção seguinte.

::: zone pivot="b2c-user-flow"

## <a name="configure-microsoft-as-an-identity-provider"></a>Configure a Microsoft como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade** e, em seguida, selecione a Conta **Microsoft**.
1. Insira um **nome**. Por exemplo, *MSA*.
1. Para o ID do **Cliente,** insira o ID de Aplicação (cliente) da aplicação AZure AD que criou anteriormente.
1. Para o segredo do **Cliente,** insira o segredo do cliente que gravou.
1. Selecione **Guardar**.

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Adicione o fornecedor de identidade da Microsoft a um fluxo de utilizador 

Neste momento, o fornecedor de identidade da Microsoft foi criado, mas ainda não está disponível em nenhuma das páginas de inscrição. Para adicionar o fornecedor de identidade da Microsoft a um fluxo de utilizador:

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Clique no fluxo de utilizador que pretende adicionar ao fornecedor de identidade da Microsoft.
1. Sob os **fornecedores de identidade Social**, selecione **Microsoft Account**.
1. Selecione **Guardar**.
1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **de fluxo do utilizador Executar.**
1. A partir da página de inscrição ou de sômis, selecione **Microsoft** para iniciar sôms com a conta Microsoft.

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

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

## <a name="configure-microsoft-as-an-identity-provider"></a>Configure a Microsoft como fornecedor de identidade

Para permitir que os utilizadores entrem a trabalhar através de uma conta Microsoft, é necessário definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir a Azure AD como fornecedor de sinistros adicionando o elemento **ClaimsProvider** no ficheiro de extensão da sua política.

1. Abra o ficheiro de política *TrustFrameworkExtensions.xml.*
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Provider de Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-MicrosoftAccount-OpenIdConnect">
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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-MicrosoftAccount-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Teste a sua política personalizada

1. Selecione a sua política partidária de dependência, por exemplo `B2C_1A_signup_signin` .
1. Para **Aplicação**, selecione uma aplicação web que já [se registou anteriormente.](troubleshoot-custom-policies.md#troubleshoot-the-runtime) A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **Executar agora.**
1. A partir da página de inscrição ou de sômis, selecione **Microsoft** para iniciar sôms com a conta Microsoft.

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

::: zone-end

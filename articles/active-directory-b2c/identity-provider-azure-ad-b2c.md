---
title: Configurar inscrição e inscrição com uma conta Azure AD B2C de outro inquilino Azure AD B2C
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição em clientes com contas Azure AD B2C de outro inquilino nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8a0d69ea57eb5b8b2a074c37d4798a99c576ce95
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538183"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Configurar inscrição e inscrição com uma conta Azure AD B2C de outro inquilino Azure AD B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Descrição Geral

Este artigo descreve como criar uma federação com outro inquilino Azure AD B2C. Quando as suas aplicações estão protegidas com o seu Azure AD B2C, isto permite que os utilizadores de outros Ad B2C's do Azure acedam às suas contas existentes. No diagrama seguinte, os utilizadores podem inscrever-se numa Aplicação protegida pelo Azure AD B2C da *Contoso,* com uma conta gerida pelo inquilino Azure AD B2C da *Fabrikam.* 

![Federação Azure AD B2C com outro inquilino Azure AD B2C](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Criar uma aplicação AZure AD B2C

Para permitir o sôm-in para utilizadores com uma conta de outro inquilino Azure AD B2C (por exemplo, Fabrikam), no seu Azure AD B2C (por exemplo, Contoso):

1. Crie um [fluxo de utilizador,](tutorial-create-user-flows.md)ou uma [política personalizada.](custom-policy-get-started.md)
1. Em seguida, crie uma aplicação no Azure AD B2C, como descreve nesta secção. 

Para criar uma aplicação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu outro inquilino Azure AD B2C (por exemplo, Fabrikam.com).
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App** e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *ContosoApp*.
1. Nos **tipos de conta suportado**, selecione Contas em qualquer fornecedor de identidade ou **diretório organizacional (para autenticar utilizadores com fluxos de utilizador)**.
1. Em **Redirect URI**, selecione **Web**, e, em seguida, insira o seguinte URL em todas as letras minúsculas, onde é substituído pelo nome do seu `your-B2C-tenant-name` inquilino Azure AD B2C (por exemplo, Contoso).

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Sob permissões, selecione o **consentimento de administração Grant para abrir e offline_access caixa de verificação de permissões.**
1. Selecione **Registar**.
1. Na página **Azure AD B2C - Registos de aplicações,** selecione a aplicação que criou, por *exemplo, ContosoApp*.
1. Grave o **ID de Aplicação (cliente)** mostrado na página geral da aplicação. Precisa disto quando configurar o fornecedor de identidade na secção seguinte.
1. No menu esquerdo, em **Manage,** selecione **Certificates & secrets**.
1. Selecione **Novo segredo do cliente**.
1. Insira uma descrição para o segredo do cliente na caixa **Descrição.** Por exemplo, *o segredo de clientes1*.
1. Em **Expira**, selecione uma duração para a qual o segredo é válido e, em seguida, selecione **Adicionar**.
1. Registar o **valor** do segredo. Precisa disto quando configurar o fornecedor de identidade na secção seguinte.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Configure Azure AD B2C como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o inquilino Azure AD B2C que pretende configurar a federação (por exemplo, Contoso). Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C (por exemplo, Contoso).
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade** e, em seguida, selecione novo fornecedor **OpenID Connect**.
1. Insira um **nome**. Por exemplo, insira *Fabrikam.*
1. Para **o url metadados,** insira o seguinte URL substituindo `{tenant}` pelo nome de domínio do seu inquilino Azure AD B2C (por exemplo, Fabrikam). Substitua-o `{policy}` pelo nome de apólice que configurar no outro inquilino:

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. Para **identificação do cliente,** introduza o ID da aplicação que gravou anteriormente.
1. Para **segredo do Cliente,** insira o segredo do cliente que gravou anteriormente.
1. Para o **Âmbito,** insira o `openid` .
1. Deixe os valores predefinidos para o **tipo de resposta** e modo **resposta**.
1. (Opcional) Para a **sugestão de Domínio,** insira o nome de domínio que pretende utilizar para o [sinal direto](direct-signin.md#redirect-sign-in-to-a-social-provider). Por exemplo, *fabrikam.com.*
1. No âmbito **do mapeamento de reclamações do fornecedor de identidade,** selecione as seguintes reclamações:

    - **ID do utilizador**: *sub*
    - **Nome do visor**: *nome*
    - **Nome próprio**: *given_name*
    - **Apelido**: *family_name*
    - **E-mail**: *e-mail*

1. Selecione **Guardar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar a chave de aplicação que você criou anteriormente no seu inquilino Azure AD B2C.

1. Certifique-se de que está a usar o diretório que contém o inquilino Azure AD B2C que pretende configurar a federação (por exemplo, Contoso). Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C (por exemplo, Contoso).
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. No âmbito **de Políticas**, selecione Identity **Experience Framework**.
1. Selecione **as teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha `Manual` .
1. Insira um **Nome** para a chave de política. Por exemplo, `FabrikamAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave quando é criado, pelo que a sua referência no XML na secção seguinte é *B2C_1A_FabrikamAppSecret*.
1. Em **Segredo,** insira o segredo do seu cliente que gravou anteriormente.
1. Para **a utilização da chave**, selecione `Signature` .
1. Selecione **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se quiser que os utilizadores entrem em súb9 utilizando o outro Azure AD B2C (Fabrikam), tem de definir o outro Azure AD B2C como um fornecedor de reclamações com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir o Azure AD B2C como fornecedor de sinistros adicionando Azure AD B2C ao elemento **ClaimsProvider** no ficheiro de extensão da sua política.

1. Abra o ficheiro *TrustFrameworkExtensions.xml.*
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Provider de Reclamações** da seguinte forma:
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Fabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Atualizar os seguintes elementos XML com o valor relevante:

    |Elemento XML  |Valor  |
    |---------|---------|
    |ReclamaçõesProvider\Domínio  | O nome de domínio que é utilizado para [o acesso direto](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Introduza o nome de domínio que pretende utilizar no sinduse direto. Por exemplo, *fabrikam.com.* |
    |TécnicaProfile\DisplayName|Este valor será apresentado no botão de início de sposição no seu ecrã de início de sposição. Por exemplo, *Fabrikam.* |
    |Metadados\client_id|O identificador de aplicação do fornecedor de identidade. Atualize o ID do Cliente com o ID de aplicação que criou anteriormente no outro inquilino Azure AD B2C.|
    |Metadados\METADADOS|Um URL que aponta para um documento de configuração do fornecedor de identidade OpenID Connect, que também é conhecido como ponto final de configuração bem conhecido do OpenID. Introduza o seguinte URL substituindo `{tenant}` pelo nome de domínio do outro inquilino Azure AD B2C (Fabrikam). Substitua-o `{tenant}` pelo nome de apólice que configurar no outro inquilino e pelo nome de `{policy]` política: `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` . Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |Chaves Criptográficas| Atualize o valor do **StorageReferenceId** para o nome da chave de política que criou anteriormente. Por exemplo, `B2C_1A_FabrikamAppSecret`.| 
    

### <a name="upload-the-extension-file-for-verification"></a>Faça o upload do ficheiro de extensão para verificação

Por esta altura, já configuraram a sua política para que a Azure AD B2C saiba comunicar com o outro inquilino Azure AD B2C. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **'Políticas Personalizadas'** no seu inquilino Azure AD B2C, selecione **'Política de Upload'.**
1. Ativar **a política em caso de existência** e, em seguida, navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
1. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de sinistros

Neste momento, o fornecedor de identidade foi criado, mas ainda não está disponível em nenhuma das páginas de inscrição/inscrição. Para disponibilizá-lo, crie uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modifique-a de modo a que também tenha o fornecedor de identidade AZure AD:

1. Abra o ficheiro *TrustFrameworkBase.xml* do pacote de arranque.
1. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"` .
1. Abra a *TrustFrameworkExtensions.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
1. Cole todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
1. Mude o nome da identificação da viagem de utilizador. Por exemplo, `SignUpSignInFabrikam`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade numa página de inscrição/inscrição. Se adicionar um elemento **ClaimsProviderSelection** para Azure AD B2C, um novo botão aparece quando um utilizador pousa na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada de utilizador que criou em *TrustFrameworkExtensions.xml*.
1. Em **SinistrosProviderSeles**, adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para um valor apropriado, por `FabrikamExchange` exemplo:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="FabrikamExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de o ligar a uma ação. A ação, neste caso, é que a Azure AD B2C comunique com o outro Azure AD B2C para receber um token. Ligue o botão a uma ação ligando o perfil técnico do fornecedor de sinistros Azure AD B2C:

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
1. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para **ID** que utilizou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="FabrikamExchange" TechnicalProfileReferenceId="Fabrikam-OpenIdConnect" />
    ```

    Atualize o valor do **TechnicalProfileReferenceD** para o **ID** do perfil técnico que criou anteriormente. Por exemplo, `Fabrikam-OpenIdConnect`.

1. Guarde o ficheiro *TrustFrameworkExtensions.xml* e faça o upload novamente para verificação.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Adicione o fornecedor de identidade Azure AD B2C a um fluxo de utilizador 

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Clique no fluxo do utilizador que pretende adicionar ao fornecedor de identidade Azure AD B2C.
1. Sob os **fornecedores de identidade social,** selecione **Fabrikam**.
1. Selecione **Guardar**.
1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador executar**
1. A partir da página de inscrição ou inscrição, selecione *Fabrikam* para iniciar sação com o outro inquilino Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que conta

Atualize o ficheiro do partido de funções (RP) que inicia a jornada do utilizador que criou.

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho, e mude o nome. Por exemplo, mude-o para *SignUpSignInFabrikam.xml*.
1. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **a TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInFabrikam`.
1. Atualize o valor da **PublicPolicyUri** com o URI para a apólice. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_fabrikam`.
1. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da jornada do utilizador que criou anteriormente. Por exemplo, *SignUpSignInFabrikam*.
1. Guarde as suas alterações e carrete o ficheiro.
1. De acordo com **as políticas personalizadas,** selecione a nova política da lista.
1. No drop-down da **aplicação Select,** selecione a aplicação Azure AD B2C que criou anteriormente. Por exemplo, *testapp1*.
1. Selecione **Executar agora** 
1. A partir da página de inscrição ou inscrição, selecione *Fabrikam* para iniciar sação com o outro inquilino Azure AD B2C.

::: zone-end

## <a name="next-steps"></a>Passos seguintes

Saiba como [passar o outro sinal Azure AD B2C para a sua aplicação.](idp-pass-through-user-flow.md)

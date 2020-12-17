---
title: Configurar o sedundo para uma organização AD Azure
titleSuffix: Azure AD B2C
description: Confiúdico de inscrição para uma organização específica do Azure Ative Directory em Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 05c4d36f266fb526a1d0232cc32f0408e4322c80
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654392"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurar o sº de inscrição para uma organização específica do Azure Ative Directory em Azure Ative Directory B2C

Este artigo mostra-lhe como ativar o início de saúde dos utilizadores de uma organização específica da AZure AD utilizando um fluxo de utilizador em Azure AD B2C.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>Registar uma aplicação do Azure AD

Para ativar o sôm-in para utilizadores de uma organização específica da Azure AD, você precisa registrar uma aplicação dentro do inquilino organizacional Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino organizacional Azure AD (por exemplo, contoso.com). Selecione o **filtro de subscrição Diretório +** no menu superior e, em seguida, escolha o diretório que contém o seu inquilino AD Azure.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
1. Selecione **Novo registo**.
1. Insira um **Nome** para a sua inscrição. Por exemplo, `Azure AD B2C App`.
1. Aceite a seleção padrão de **Contas neste diretório organizacional apenas** para esta aplicação.
1. Para o **Redirect URI,** aceite o valor da **Web,** e introduza o seguinte URL em todas as letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do seu inquilino Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Selecione **Registar**. Grave o **ID da Aplicação (cliente)** para utilização num passo posterior.
1. Selecione **Certificados & segredos** e, em seguida, selecione **Novo segredo de cliente**.
1. Introduza uma **Descrição** para o segredo, selecione uma expiração e, em seguida, **selecione Adicionar**. Grave o **valor** do segredo para utilização num passo posterior.

### <a name="configuring-optional-claims"></a>Configurar pedidos opcionais

Se quiser obter as `family_name` `given_name` reclamações da Azure AD, pode configurar reclamações opcionais para a sua aplicação no portal Azure UI ou manifesto de aplicação. Para mais informações, consulte [Como fornecer reclamações opcionais à sua aplicação AD AZure.](../active-directory/develop/active-directory-optional-claims.md)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Procure e selecione **Azure Active Directory**.
1. A partir da secção **Gerir,** selecione **registos de Aplicações.**
1. Selecione a aplicação para a inscrição que pretende configurar pedidos opcionais na lista.
1. A partir da secção **Gerir,** selecione **a configuração Token**.
1. **Selecione Adicionar reclamação opcional**.
1. Para o **tipo Token**, selecione **ID**.
1. Selecione as reclamações opcionais a adicionar `family_name` e `given_name` .
1. Clique em **Adicionar**.

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configure Azure AD como fornecedor de identidade

1. Certifique-se de que está a usar o diretório que contém o inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade** e, em seguida, selecione novo fornecedor **OpenID Connect**.
1. Insira um **nome**. Por exemplo, *insira Contoso Azure AD*.
1. Para **o url metadados,** introduza o seguinte URL substituindo `{tenant}` pelo nome de domínio do seu inquilino AZure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Por exemplo, `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Para **identificação do cliente,** introduza o ID da aplicação que gravou anteriormente.
1. Para **segredo do Cliente,** insira o segredo do cliente que gravou anteriormente.
1. Para o **Âmbito,** insira o `openid profile` .
1. Deixe os valores predefinidos para o **tipo de resposta** e modo **resposta**.
1. (Opcional) Para a **sugestão de Domínio,** insira `contoso.com` . Para obter mais informações, consulte [Configurar o insusimento direto utilizando o Azure Ative Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. No âmbito **do mapeamento de reclamações do fornecedor de identidade,** selecione as seguintes reclamações:

    - **ID do utilizador**: *oid*
    - **Nome do visor**: *nome*
    - **Nome próprio**: *given_name*
    - **Apelido**: *family_name*
    - **E-mail**: *preferred_username*

1. Selecione **Guardar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar a chave de aplicação que você criou no seu inquilino Azure AD B2C.

1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o **filtro de subscrição Diretório +** no menu superior e, em seguida, escolha o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. No âmbito **de Políticas**, selecione Identity **Experience Framework**.
1. Selecione **as teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha `Manual` .
1. Insira um **Nome** para a chave de política. Por exemplo, `ContosoAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave quando é criado, pelo que a sua referência no XML na secção seguinte é *B2C_1A_ContosoAppSecret*.
1. Em **Segredo,** insira o segredo do seu cliente que gravou anteriormente.
1. Para **a utilização da chave**, selecione `Signature` .
1. Selecione **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se quiser que os utilizadores entrem em súb9 utilizando o Azure AD, tem de definir a AZure AD como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir a Azure AD como fornecedor de sinistros adicionando Azure AD ao elemento **ClaimsProvider** no ficheiro de extensão da sua política.

1. Abra o ficheiro *TrustFrameworkExtensions.xml.*
2. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **Provider de Reclamações** da seguinte forma:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Sob o elemento **ClaimsProvider,** atualize o valor **do Domínio** para um valor único que possa ser usado para distingui-lo de outros fornecedores de identidade. Por exemplo, `Contoso`. Não se põe um `.com` no fim desta definição de domínio.
5. Sob o elemento **ClaimsProvider,** atualize o valor **do DisplayName** para um nome amigável para o fornecedor de sinistros. Este valor não é atualmente utilizado.

### <a name="update-the-technical-profile"></a>Atualizar o perfil técnico

Para obter um sinal do ponto final Azure AD, você precisa definir os protocolos que Azure AD B2C deve usar para comunicar com Azure AD. Isto é feito dentro do elemento **TécnicoProfile** do  **ClaimsProvider**.

1. Atualizar o ID do elemento **TécnicoProfile.** Este ID é utilizado para se referir a este perfil técnico de outras partes da política, por `OIDC-Contoso` exemplo.
1. Atualize o valor **do DisplayName**. Este valor será apresentado no botão de início de sposição no seu ecrã de início de sposição.
1. Atualizar o valor para **descrição**.
1. O Azure AD utiliza o protocolo OpenID Connect, por isso certifique-se de que o valor para **o Protocolo** é `OpenIdConnect` .
1. Valor definido do **METADAS** `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` para, onde `tenant-name` está o nome do seu inquilino AZure AD. Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. **Desa** esta client_id ao ID do pedido a partir do registo do pedido.
1. Em **CryptographicKeys,** atualize o valor do **StorageReferenceId** para o nome da chave de política que criou anteriormente. Por exemplo, `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Faça o upload do ficheiro de extensão para verificação

Por esta altura, já configuraste a tua política para que o Azure AD B2C saiba comunicar com o teu diretório AD Azure. Tente carregar o ficheiro de extensão da sua apólice apenas para confirmar que não tem quaisquer problemas até agora.

1. Na página **'Políticas Personalizadas'** no seu inquilino Azure AD B2C, selecione **'Política de Upload'.**
1. Ativar **a política em caso de existência** e, em seguida, navegar e selecionar o ficheiro *TrustFrameworkExtensions.xml.*
1. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registar o fornecedor de sinistros

Neste momento, o fornecedor de identidade foi criado, mas ainda não está disponível em nenhuma das páginas de inscrição/inscrição. Para disponibilizá-lo, crie uma duplicação de uma viagem de utilizador de modelo existente e, em seguida, modifique-a de modo a que também tenha o fornecedor de identidade AZure AD:

1. Abra o ficheiro *TrustFrameworkBase.xml* do pacote de arranque.
1. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"` .
1. Abra a *TrustFrameworkExtensions.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
1. Cole todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
1. Mude o nome da identificação da viagem de utilizador. Por exemplo, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Mostrar o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de fornecedor de identidade numa página de inscrição/inscrição. Se adicionar um elemento **ClaimsProviderSelection** para Azure AD, um novo botão aparece quando um utilizador pousa na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada de utilizador que criou em *TrustFrameworkExtensions.xml*.
1. Em **SinistrosProviderSeles**, adicione o seguinte elemento. Definir o valor do **TargetClaimsExchangeId** para um valor apropriado, por `ContosoExchange` exemplo:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Ligue o botão a uma ação

Agora que tens um botão no lugar, tens de o ligar a uma ação. A ação, neste caso, é que a Azure AD B2C comunique com a Azure AD para receber um token. Ligue o botão a uma ação ligando o perfil técnico do seu fornecedor de reclamações Azure AD:

1. Encontre a **OrquestraçãoStep** que inclui `Order="2"` na viagem do utilizador.
1. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que utiliza o mesmo valor para **ID** que utilizou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Atualize o valor do **TechnicalProfileReferenceD** para o **ID** do perfil técnico que criou anteriormente. Por exemplo, `OIDC-Contoso`.

1. Guarde o ficheiro *TrustFrameworkExtensions.xml* e faça o upload novamente para verificação.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Adicione o fornecedor de identidade Azure AD a um fluxo de utilizador 

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Clique no fluxo de utilizador que deseja para o fornecedor de identidade Azure AD.
1. Sob os **fornecedores de identidade social,** selecione **Contoso Azure AD**.
1. Selecione **Guardar**.
1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador executar**

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que conta

Atualize o ficheiro do partido de funções (RP) que inicia a jornada do utilizador que criou.

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho, e mude o nome. Por exemplo, mude-o para *SignUpSignInContoso.xml*.
1. Abra o novo ficheiro e atualize o valor do atributo **PolicyId** para **a TrustFrameworkPolicy** com um valor único. Por exemplo, `SignUpSignInContoso`.
1. Atualize o valor da **PublicPolicyUri** com o URI para a apólice. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da jornada do utilizador que criou anteriormente. Por exemplo, *SignUpSignInContoso*.
1. Guarde as suas alterações e carrete o ficheiro.
1. De acordo com **as políticas personalizadas,** selecione a nova política da lista.
1. No drop-down da **aplicação Select,** selecione a aplicação Azure AD B2C que criou anteriormente. Por exemplo, *testapp1*.
1. Copie o **ponto final do Run e** abra-o numa janela de navegador privado, por exemplo, Modo Incógnito no Google Chrome ou uma janela InPrivate no Microsoft Edge. A abertura numa janela de navegador privada permite-lhe testar toda a viagem do utilizador não utilizando quaisquer credenciais AZure AD atualmente em cache.
1. Selecione o sinal AZure AD no botão, por exemplo, *Empregado contoso,* e, em seguida, insira as credenciais para um utilizador no seu inquilino organizacional Azure AD. É-lhe pedido que autorize o pedido e, em seguida, introduza informações para o seu perfil.

Se o sinal em processo for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

Ao trabalhar com políticas personalizadas, pode por vezes precisar de informações adicionais quando se trata de resolver problemas de uma política durante o seu desenvolvimento.

Para ajudar a diagnosticar problemas, pode colocar temporariamente a política em "modo de desenvolvimento" e recolher registos com a Azure Application Insights. Descubra como em [Azure Ative Directory B2C: Recolha de registos](troubleshoot-with-application-insights.md).

::: zone-end
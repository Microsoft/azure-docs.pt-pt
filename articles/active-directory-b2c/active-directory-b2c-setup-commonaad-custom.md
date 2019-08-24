---
title: Configurar a entrada para um provedor de identidade multilocatário do Azure AD usando políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: Adicione um provedor de identidade multilocatário do Azure AD usando políticas personalizadas-Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8524eb8f9a8d220964e5dd1f6f8dc6d1aaf94a6d
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980724"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada para Azure Active Directory multilocatário usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários usando o ponto de extremidade multilocatário para Azure Active Directory (AD do Azure) usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure ad B2C. Isso permite que os usuários de vários locatários do Azure AD entrem no Azure AD B2C sem configurar um provedor técnico para cada locatário. No entanto, os membros convidados em qualquer um desses locatários **não** poderão entrar. Para isso, você precisa [Configurar individualmente cada locatário](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
>`Contoso.com`é usado para o locatário organizacional do Azure ad `fabrikamb2c.onmicrosoft.com` e é usado como o locatário Azure ad B2C nas instruções a seguir.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em introdução [às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registar uma aplicação

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário organizacional do AD do Azure (contoso.com) clicando no **filtro diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **registros de aplicativo**.
4. Selecione **Novo registo de aplicação**.
5. Introduza um nome para a aplicação. Por exemplo, `Azure AD B2C App`.
6. Para o **tipo de aplicativo**, `Web app / API`selecione.
7. Para a **URL de logon**, insira a URL a seguir em todas as letras minúsculas, `your-tenant` onde é substituído pelo nome do seu locatário de Azure ad B2C (fabrikamb2c.onmicrosoft.com):

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

8. Clique em **Criar**. Copie a **ID do aplicativo** a ser usada posteriormente.
9. Selecione o aplicativo e, em seguida, selecione **configurações**.
10. Selecione **chaves**, insira a descrição da chave, selecione uma duração e, em seguida, clique em **salvar**. Copie o valor da chave que é exibida para ser usada posteriormente.
11. Em **configurações**, selecione **Propriedades**, defina **multilocatário** para `Yes`e, em seguida, clique em **salvar**

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar a chave do aplicativo que você criou em seu locatário Azure AD B2C.

1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
2. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
3. Na página Visão geral, selecione **Identity Experience Framework**.
4. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
5. Para **Opções**, escolha `Manual`.
6. Insira um **nome** para a chave de política. Por exemplo, `ContosoAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
7. Em **segredo**, insira a chave do aplicativo que você registrou anteriormente.
8. Para **uso de chave**, `Signature`selecione.
9. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se desejar que os usuários entrem usando o Azure AD, você precisará definir o Azure AD como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure ad como um provedor de declarações adicionando o Azure ad ao elemento Claims no arquivo de extensão da sua política.

1. Abra o *TrustFrameworkExtensions. xml*.
2. Localize o elemento **ClaimsProviders** . Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo Claims da seguinte maneira:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>

            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="unique_name" />
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

4. No elemento Claims, atualize o valor de **Domain** para um valor exclusivo que pode ser usado para distingui-lo de outros provedores de identidade.
5. No elemento **TechnicalProfile** , atualize o valor para **DisplayName**. Esse valor é exibido no botão de entrada na tela de entrada.
6. Defina **client_id** como a ID do aplicativo do registro do aplicativo Mulity do Azure AD.

### <a name="restrict-access"></a>Acesso restrito

> [!NOTE]
> Usar `https://sts.windows.net` como o valor para **ValidTokenIssuerPrefixes** permite que todos os usuários do Azure ad entrem em seu aplicativo.

Você precisa atualizar a lista de emissores de token válidos e restringir o acesso a uma lista específica de usuários de locatário do Azure AD que podem entrar. Para obter os valores, você precisa examinar os metadados de cada locatário do Azure AD específico do qual você deseja que os usuários entrem. O formato dos dados é semelhante ao seguinte: `https://login.windows.net/your-tenant/.well-known/openid-configuration`, onde `your-tenant` é o nome do locatário do Azure AD (contoso.com ou qualquer outro locatário do Azure AD).

1. Abra o navegador e vá para a URL de **metadados** e procure o objeto **emissor** e copie seu valor. Ele deve ser semelhante ao seguinte: `https://sts.windows.net/tenant-id/`.
2. Copie e cole o valor da chave **ValidTokenIssuerPrefixes** . Você pode adicionar vários separando-os usando uma vírgula. Um exemplo disso é comentado no exemplo de XML acima.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora, você configurou sua política para que Azure AD B2C saiba como se comunicar com seu diretório do Azure AD. Tente carregar o arquivo de extensão da política apenas para confirmar que ele não tem nenhum problema até o momento.

1. Na página **políticas personalizadas** no locatário do Azure ad B2C, selecione **carregar política**.
2. Habilite **substituir a política se ela existir**e, em seguida, navegue até e selecione o arquivo *TrustFrameworkExtensions. xml* .
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade foi configurado, mas ele não está disponível em nenhuma das telas de inscrição/entrada. Para disponibilizá-lo, crie uma duplicata de um percurso de usuário de modelo existente e, em seguida, modifique-o para que ele também tenha o provedor de identidade do Azure AD.

1. Abra o arquivo *TrustFrameworkBase. xml* do pacote inicial.
2. Localize e copie todo o conteúdo do elemento **userjornada** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions. xml* e localize o elemento userjornadas. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **userjornada** que você copiou como um filho do elemento userjornadas.
5. Renomeie a ID da jornada do usuário. Por exemplo, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de provedor de identidade em uma tela de inscrição/entrada. Se você adicionar um elemento **ClaimsProviderSelection** para o Azure AD, um novo botão será exibido quando um usuário chegar à página.

1. Localize o elemento **OrchestrationStep** que inclui `Order="1"` no percurso do usuário que você criou.
2. Em **ClaimsProviderSelects**, adicione o seguinte elemento. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que você tem um botão em vigor, é necessário vinculá-lo a uma ação. Nesse caso, a ação é para Azure AD B2C se comunicar com o Azure AD para receber um token. Vincule o botão a uma ação vinculando o perfil técnico para seu provedor de declarações do Azure AD.

1. Localize o **OrchestrationStep** que inclui `Order="2"` na jornada do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** , certificando-se de usar o mesmo valor para **ID** que você usou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para a **ID** do perfil técnico criado anteriormente. Por exemplo, `Common-AAD`.

3. Salve o arquivo *TrustFrameworkExtensions. xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com Azure AD B2C ocorre por meio de um aplicativo que você cria em seu locatário. Esta seção lista as etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo, por exemplo, *testapp1*.
6. Para **aplicativo Web/API Web**, selecione `Yes`e, em seguida `https://jwt.ms` , insira para a **URL de resposta**.
7. Clique em **Criar**.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo RP (terceira parte confiável) que inicia o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn. xml* em seu diretório de trabalho e renomeie-o. Por exemplo, renomeie-o como *SignUpSignContoso. xml*.
2. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInContoso`.
3. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_contoso`
4. Atualize o valor do atributo **referenceid** em **DefaultUserJourney** para corresponder à ID do novo percurso do usuário que você criou (SignUpSignContoso).
5. Salve as alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
6. Verifique se Azure AD B2C aplicativo que você criou está selecionado no campo **Selecionar aplicativo** e, em seguida, teste-o clicando em **executar agora**.

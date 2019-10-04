---
title: Configurar a entrada para um provedor de identidade multilocatário do Azure AD usando políticas personalizadas no Azure Active Directory B2C
description: Adicione um provedor de identidade multilocatário do Azure AD usando políticas personalizadas-Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a0b9166d24bea28bb3271d719e8ffe0b24d71381
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71826934"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada para Azure Active Directory multilocatário usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários usando o ponto de extremidade multilocatário para Azure Active Directory (AD do Azure) usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure ad B2C. Isso permite que os usuários de vários locatários do Azure AD entrem usando Azure AD B2C, sem a necessidade de configurar um provedor de identidade para cada locatário. No entanto, os membros convidados em qualquer um desses locatários **não** poderão entrar. Para isso, você precisa [Configurar individualmente cada locatário](active-directory-b2c-setup-aad-custom.md).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em introdução [às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registar uma aplicação

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário organizacional do Azure AD (por exemplo, contoso.com). Selecione o **diretório + filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário.
1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **registros de aplicativo**.
1. Selecione **novo registro**.
1. Insira um **nome** para seu aplicativo. Por exemplo, `Azure AD B2C App`.
1. Selecione **contas em qualquer diretório organizacional** para este aplicativo.
1. Para o **URI de redirecionamento**, aceite o valor de **Web**e insira a URL a seguir em todas as letras `your-B2C-tenant-name` minúsculas, onde é substituído pelo nome do seu locatário de Azure ad B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Selecione **Registar**. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.
1. Selecione **certificados & segredos**e, em seguida, selecione **novo segredo do cliente**.
1. Insira uma **Descrição** para o segredo, selecione uma expiração e, em seguida, selecione **Adicionar**. Registre o **valor** do segredo para uso em uma etapa posterior.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar a chave do aplicativo que você criou em seu locatário Azure AD B2C.

1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório + filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário de Azure ad B2C.
1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
1. Em **políticas**, selecione **estrutura de experiência de identidade**.
1. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **nome** para a chave de política. Por exemplo, `AADAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave quando ele é criado, portanto, sua referência no XML na seção a seguir é *B2C_1A_AADAppSecret*.
1. Em **segredo**, insira o segredo do cliente que você registrou anteriormente.
1. Para **uso de chave**, `Signature`selecione.
1. Selecione **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se desejar que os usuários entrem usando o Azure AD, você precisará definir o Azure AD como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure ad como um provedor de declarações adicionando o Azure AD ao elemento **Claims** no arquivo de extensão da sua política.

1. Abra o arquivo *TrustFrameworkExtensions. xml* .
1. Localize o elemento **ClaimsProviders** . Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Claims** da seguinte maneira:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/common/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
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

1. No elemento **Claims** , atualize o valor de **Domain** para um valor exclusivo que pode ser usado para distingui-lo de outros provedores de identidade.
1. No elemento **TechnicalProfile** , atualize o valor de **DisplayName**, por exemplo, `Contoso Employee`. Esse valor é exibido no botão de entrada na página de entrada.
1. Defina **client_id** como a ID do aplicativo do aplicativo multilocatário do Azure AD que você registrou anteriormente.
1. Em **CryptographicKeys**, atualize o valor de **StorageReferenceId** para o nome da chave de política criada anteriormente. Por exemplo, `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Acesso restrito

> [!NOTE]
> Usar `https://sts.windows.net` como o valor para **ValidTokenIssuerPrefixes** permite que todos os usuários do Azure ad entrem em seu aplicativo.

Você precisa atualizar a lista de emissores de token válidos e restringir o acesso a uma lista específica de usuários de locatário do Azure AD que podem entrar.

Para obter os valores, examine os metadados de descoberta do OpenID Connect para cada um dos locatários do Azure AD dos quais você deseja que os usuários se conectem. O formato da URL de metadados é semelhante a `https://login.windows.net/your-tenant/.well-known/openid-configuration`, em `your-tenant` que é o nome do locatário do Azure AD. Por exemplo:

`https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`

Execute estas etapas para cada locatário do Azure AD que deve ser usado para entrar:

1. Abra o navegador e vá para a URL de metadados do OpenID Connect para o locatário. Localize o objeto **emissor** e Registre seu valor. Ele deve ser semelhante a `https://sts.windows.net/00000000-0000-0000-0000-000000000000/`.
1. Copie e cole o valor na chave **ValidTokenIssuerPrefixes** . Separe vários emissores com uma vírgula. Um exemplo com dois emissores aparece no exemplo `ClaimsProvider` de XML anterior.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora, você configurou sua política para que Azure AD B2C saiba como se comunicar com seus diretórios do Azure AD. Tente carregar o arquivo de extensão da política apenas para confirmar que ele não tem nenhum problema até o momento.

1. Na página **políticas personalizadas** no locatário do Azure ad B2C, selecione **carregar política**.
2. Habilite **substituir a política se ela existir**e, em seguida, navegue até e selecione o arquivo *TrustFrameworkExtensions. xml* .
3. Selecione **Upload**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade foi configurado, mas ele não está disponível em nenhuma das telas de inscrição/entrada. Para disponibilizá-lo, crie uma duplicata de um percurso de usuário de modelo existente e, em seguida, modifique-o para que ele também tenha o provedor de identidade do Azure AD.

1. Abra o arquivo *TrustFrameworkBase. xml* do pacote inicial.
2. Localize e copie todo o conteúdo do elemento **userjornada** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions. xml* e localize o elemento **userjornadas** . Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **userjornada** que você copiou como um filho do elemento **userjornadas** .
5. Renomeie a ID da jornada do usuário. Por exemplo, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de provedor de identidade em uma tela de inscrição/entrada. Se você adicionar um elemento **ClaimsProviderSelection** para o Azure AD, um novo botão será exibido quando um usuário chegar à página.

1. Localize o elemento **OrchestrationStep** que inclui `Order="1"` no percurso do usuário que você criou em *TrustFrameworkExtensions. xml*.
1. Em **ClaimsProviderSelects**, adicione o seguinte elemento. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `AzureADExchange`:

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

A comunicação com Azure AD B2C ocorre por meio de um aplicativo que você registra em seu locatário B2C. Esta seção lista as etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo RP (terceira parte confiável) que inicia o percurso do usuário que você criou:

1. Faça uma cópia do *SignUpOrSignIn. xml* em seu diretório de trabalho e renomeie-o. Por exemplo, renomeie-o como *SignUpSignContoso. xml*.
1. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInContoso`.
1. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Atualize o valor do atributo **referenceid** em **DefaultUserJourney** para corresponder à ID da jornada do usuário que você criou anteriormente. Por exemplo, *SignUpSignInContoso*.
1. Salve as alterações e carregue o arquivo.
1. Em **políticas personalizadas**, selecione a nova política na lista.
1. Na lista suspensa **Selecionar aplicativo** , selecione o aplicativo Azure ad B2C que você criou anteriormente. Por exemplo, *testapp1*.
1. Copie o **ponto de extremidade executar agora** e abra-o em uma janela de navegador privada, por exemplo, o modo Incognito no Google Chrome ou uma janela InPrivate no Microsoft Edge. Abrir em uma janela privada do navegador permite que você teste o percurso completo do usuário não usando nenhuma credencial do Azure AD atualmente armazenada em cache.
1. Selecione o botão entrar do Azure AD, por exemplo, *funcionário contoso*e, em seguida, insira as credenciais para um usuário em um dos seus locatários organizacionais do Azure AD. Você será solicitado a autorizar o aplicativo e, em seguida, a inserir informações para seu perfil.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms`, que exibe o conteúdo do token retornado por Azure ad B2C.

Para testar a funcionalidade de entrada multilocatário, execute as duas últimas etapas usando as credenciais para um usuário que existe outro locatário do Azure AD.

## <a name="next-steps"></a>Passos seguintes

Ao trabalhar com políticas personalizadas, às vezes você pode precisar de informações adicionais ao solucionar problemas de uma política durante seu desenvolvimento.

Para ajudar a diagnosticar problemas, você pode colocar temporariamente a política em "modo de desenvolvedor" e coletar logs com o Aplicativo Azure insights. Descubra como [Azure Active Directory B2C: Coletando](active-directory-b2c-troubleshoot-custom.md)logs.

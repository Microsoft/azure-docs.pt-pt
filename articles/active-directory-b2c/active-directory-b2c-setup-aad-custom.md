---
title: Configurar a entrada com uma conta de Azure Active Directory no Azure Active Directory B2C usando políticas personalizadas
description: Configure a entrada com uma conta de Azure Active Directory no Azure Active Directory B2C usando políticas personalizadas.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3c8efc4b7bbee6b3209160b570e3e70bfccfc72e
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70964512"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada com uma conta de Azure Active Directory usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários de uma organização Azure Active Directory (Azure AD) usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em introdução [às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registar uma aplicação

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário organizacional do Azure AD (por exemplo, contoso.com). Selecione o **diretório + filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário do Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **registros de aplicativo**.
1. Selecione **novo registro**.
1. Insira um **nome** para seu aplicativo. Por exemplo, `Azure AD B2C App`.
1. Aceite a seleção padrão de **contas neste diretório organizacional somente** para este aplicativo.
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
1. Insira um **nome** para a chave de política. Por exemplo, `ContosoAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave quando ele é criado, portanto, sua referência no XML na seção a seguir é *B2C_1A_ContosoAppSecret*.
1. Em **segredo**, insira o segredo do cliente que você registrou anteriormente.
1. Para **uso de chave**, `Signature`selecione.
1. Selecione **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se desejar que os usuários entrem usando o Azure AD, você precisará definir o Azure AD como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure ad como um provedor de declarações adicionando o Azure AD ao elemento **Claims** no arquivo de extensão da sua política.

1. Abra o arquivo *TrustFrameworkExtensions. xml* .
2. Localize o elemento **ClaimsProviders** . Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **Claims** da seguinte maneira:

    ```XML
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration</Item>
            <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
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

4. No elemento **Claims** , atualize o valor de **Domain** para um valor exclusivo que pode ser usado para distingui-lo de outros provedores de identidade. Por exemplo, `Contoso`. Você não coloca um `.com` no final desta configuração de domínio.
5. No elemento **Claims** , atualize o valor de **DisplayName** para um nome amigável para o provedor de declarações. Esse valor não é usado no momento.

### <a name="update-the-technical-profile"></a>Atualizar o perfil técnico

Para obter um token do ponto de extremidade do Azure AD, você precisa definir os protocolos que Azure AD B2C deve usar para se comunicar com o Azure AD. Isso é feito dentro do elemento **TechnicalProfile** de **claimprovider**.

1. Atualize a ID do elemento **TechnicalProfile** . Essa ID é usada para fazer referência a este perfil técnico de outras partes da política.
1. Atualize o valor para **DisplayName**. Esse valor será exibido no botão de entrada na tela de entrada.
1. Atualize o valor de **Descrição**.
1. O Azure AD usa o protocolo OpenID Connect, portanto, verifique se o valor de protocolo `OpenIdConnect`é.
1. Defina o valor dos **metadados** como `https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration`, onde `your-AD-tenant-name` é o nome do locatário do Azure AD. Por exemplo, `https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`
1. Abra o navegador e vá para a URL de **metadados** que você acabou de atualizar, procure o objeto **emissor** e, em seguida, copie e cole o valor no valor de **ProviderName** no arquivo XML.
1. Defina **client_id** como a ID do aplicativo do registro do aplicativo.
1. Em **CryptographicKeys**, atualize o valor de **StorageReferenceId** para o nome da chave de política que você criou anteriormente. Por exemplo, `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora, você configurou sua política para que Azure AD B2C saiba como se comunicar com seu diretório do Azure AD. Tente carregar o arquivo de extensão da política apenas para confirmar que ele não tem nenhum problema até o momento.

1. Na página **políticas personalizadas** no locatário do Azure ad B2C, selecione **carregar política**.
1. Habilite **substituir a política se ela existir**e, em seguida, navegue até e selecione o arquivo *TrustFrameworkExtensions. xml* .
1. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade foi configurado, mas ele ainda não está disponível em nenhuma das páginas de inscrição/entrada. Para disponibilizá-lo, crie uma duplicata de um percurso de usuário de modelo existente e, em seguida, modifique-o para que ele também tenha o provedor de identidade do Azure AD:

1. Abra o arquivo *TrustFrameworkBase. xml* do pacote inicial.
1. Localize e copie todo o conteúdo do elemento **userjornada** que inclui `Id="SignUpOrSignIn"`.
1. Abra o *TrustFrameworkExtensions. xml* e localize o elemento **userjornadas** . Se o elemento não existir, adicione um.
1. Cole todo o conteúdo do elemento **userjornada** que você copiou como um filho do elemento **userjornadas** .
1. Renomeie a ID da jornada do usuário. Por exemplo, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão de provedor de identidade em uma página de inscrição/entrada. Se você adicionar um elemento **ClaimsProviderSelection** para o Azure AD, um novo botão será exibido quando um usuário chegar à página.

1. Localize o elemento **OrchestrationStep** que inclui `Order="1"` no percurso do usuário que você criou em *TrustFrameworkExtensions. xml*.
1. Em **ClaimsProviderSelections**, adicione o seguinte elemento. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que você tem um botão em vigor, é necessário vinculá-lo a uma ação. Nesse caso, a ação é para Azure AD B2C se comunicar com o Azure AD para receber um token. Vincule o botão a uma ação vinculando o perfil técnico para seu provedor de declarações do Azure AD:

1. Localize o **OrchestrationStep** que inclui `Order="2"` na jornada do usuário.
1. Adicione o seguinte elemento **ClaimsExchange** , certificando-se de usar o mesmo valor para **ID** que você usou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para a **ID** do perfil técnico criado anteriormente. Por exemplo, `ContosoProfile`.

1. Salve o arquivo *TrustFrameworkExtensions. xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com Azure AD B2C ocorre por meio de um aplicativo que você registra em seu locatário B2C. Esta seção lista as etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório + filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário de Azure ad B2C.
1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
1. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo, por exemplo, *testapp1*.
1. Para **aplicativo Web/API Web**, selecione `Yes`e, em seguida `https://jwt.ms` , insira para a **URL de resposta**.
1. Selecione **Criar**.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo RP (terceira parte confiável) que inicia o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn. xml* em seu diretório de trabalho e renomeie-o. Por exemplo, renomeie-o como *SignUpSignInContoso. xml*.
1. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInContoso`.
1. Atualize o valor de **PublicPolicyUri** com o URI para a política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Atualize o valor do atributo **referenceid** em **DefaultUserJourney** para corresponder à ID da jornada do usuário que você criou anteriormente. Por exemplo, *SignUpSignInContoso*.
1. Salve as alterações e carregue o arquivo.
1. Em **políticas personalizadas**, selecione a nova política na lista.
1. Na lista suspensa **Selecionar aplicativo** , selecione o aplicativo Azure ad B2C que você criou anteriormente. Por exemplo, *testapp1*.
1. Copie o **ponto de extremidade executar agora** e abra-o em uma janela de navegador privada, por exemplo, o modo Incognito no Google Chrome ou uma janela InPrivate no Microsoft Edge. Abrir em uma janela privada do navegador permite que você teste o percurso completo do usuário não usando nenhuma credencial do Azure AD atualmente armazenada em cache.
1. Selecione o botão entrar do Azure AD, por exemplo, *funcionário contoso*e, em seguida, insira as credenciais para um usuário em seu locatário organizacional do Azure AD. Você será solicitado a autorizar o aplicativo e, em seguida, a inserir informações para seu perfil.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms`, que exibe o conteúdo do token retornado por Azure ad B2C.

## <a name="next-steps"></a>Passos Seguintes

Ao trabalhar com políticas personalizadas, às vezes você pode precisar de informações adicionais ao solucionar problemas de uma política durante seu desenvolvimento.

Para ajudar a diagnosticar problemas, você pode colocar temporariamente a política em "modo de desenvolvedor" e coletar logs com o Aplicativo Azure insights. Descubra como [Azure Active Directory B2C: Coletando](active-directory-b2c-troubleshoot-custom.md)logs.

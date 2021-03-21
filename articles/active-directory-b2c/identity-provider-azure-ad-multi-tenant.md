---
title: Configurar o sedutar para multi-inquilino Azure AD por políticas personalizadas
titleSuffix: Azure AD B2C
description: Adicione um fornecedor de identidade Azure AD multi-inquilino usando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5089a289e617aa8c2ec153320763647fc8afac9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489041"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o sº de inscrição para o Diretório Ativo Azure multi-inquilino utilizando políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Este artigo mostra-lhe como permitir a inscrição para utilizadores que usam o ponto final de multi-inquilino para O Diretório Ativo Azure (Azure AD). Permitindo que os utilizadores de vários inquilinos da AZure AD assinem usando o Azure AD B2C, sem que tenha de configurar um fornecedor de identidade para cada inquilino. No entanto, os membros convidados de qualquer um destes inquilinos **não** poderão assinar. Para isso, é necessário [configurar individualmente cada inquilino.](identity-provider-azure-ad-single-tenant.md)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-application"></a>Registar uma aplicação

Para permitir o acesso aos utilizadores com uma conta Azure AD no Azure Ative Directory B2C (Azure AD B2C), é necessário criar uma aplicação no [portal Azure](https://portal.azure.com). Para obter mais informações, consulte [Registar uma aplicação com a plataforma de identidade da Microsoft.](../active-directory/develop/quickstart-register-app.md)


1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino organizacional Azure AD (por exemplo, contoso.com). Selecione o **filtro de subscrição Diretório +** no menu superior e, em seguida, escolha o diretório que contém o seu inquilino.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
1. Selecione **Novo registo**.
1. Insira um **Nome** para a sua inscrição. Por exemplo, `Azure AD B2C App`.
1. Selecione **Contas em qualquer diretório organizacional (Qualquer diretório AD Azure – Multitenant)** para esta aplicação.
1. Para o **Redirect URI,** aceite o valor da **Web,** e introduza o seguinte URL em todas as letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do seu inquilino Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

    Se utilizar um [domínio personalizado,](custom-domain.md)insira `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-domain-name`Substitua-o pelo seu domínio personalizado e `your-tenant-name` pelo nome do seu inquilino.

1. Selecione **Registar**. Grave o **ID da Aplicação (cliente)** para utilização num passo posterior.
1. Selecione **Certificados & segredos** e, em seguida, selecione **Novo segredo de cliente**.
1. Introduza uma **Descrição** para o segredo, selecione uma expiração e, em seguida, **selecione Adicionar**. Grave o **valor** do segredo para utilização num passo posterior.

## <a name="configuring-optional-claims"></a>Configurar pedidos opcionais

Se quiser obter o `family_name` , e `given_name` reclamações da Azure AD, pode configurar reclamações opcionais para a sua aplicação no portal Azure UI ou manifesto de aplicação. Para mais informações, consulte [Como fornecer reclamações opcionais à sua aplicação AD AZure.](../active-directory/develop/active-directory-optional-claims.md)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Procure e selecione **Azure Active Directory**.
1. A partir da secção **Gerir,** selecione **registos de Aplicações.**
1. Selecione a aplicação para a inscrição que pretende configurar pedidos opcionais na lista.
1. A partir da secção **Gerir,** selecione **a configuração Token**.
1. **Selecione Adicionar reclamação opcional**.
1. Para o **tipo Token**, selecione **ID**.
1. Selecione as alegações opcionais a `family_name` adicionar, e `given_name` .
1. Clique em **Adicionar**.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar a chave de aplicação que você criou no seu inquilino Azure AD B2C.

1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o **filtro de subscrição Diretório +** no menu superior e, em seguida, escolha o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. No âmbito **de Políticas**, selecione Identity **Experience Framework**.
1. Selecione **as teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha `Manual` .
1. Insira um **Nome** para a chave de política. Por exemplo, `AADAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave quando é criado, pelo que a sua referência no XML na secção seguinte é *B2C_1A_AADAppSecret*.
1. Em **Segredo,** insira o segredo do seu cliente que gravou anteriormente.
1. Para **a utilização da chave**, selecione `Signature` .
1. Selecione **Criar**.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configure Azure AD como fornecedor de identidade

Para permitir que os utilizadores assinem através de uma conta Azure AD, é necessário definir a AZure AD como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir a Azure AD como fornecedor de sinistros adicionando Azure AD ao elemento **ClaimsProvider** no ficheiro de extensão da sua política.

1. Abra o ficheiro *TrustFrameworkExtensions.xml.*
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Provider de Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
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

1. Sob o elemento **ClaimsProvider,** atualize o valor **do Domínio** para um valor único que possa ser usado para distingui-lo de outros fornecedores de identidade.
1. Sob o elemento **TécnicoProfile,** atualize o valor **do DisplayName,** por exemplo, `Contoso Employee` . Este valor é apresentado no botão de início de sposição na sua página de início de sposição.
1. Defina **client_id** para o ID de aplicação da aplicação Azure AD multi-inquilino que registou anteriormente.
1. Em **CryptographicKeys,** atualize o valor do **StorageReferenceId** para o nome da chave de política criada anteriormente. Por exemplo, `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Restringir o acesso

A utilização `https://login.microsoftonline.com/` como valor para **ValidTokenIssuerPrefixes** permite que todos os utilizadores AZure AD entrem na sua aplicação. Atualize a lista de emitentes de token válidos e restrinja o acesso a uma lista específica de utilizadores inquilinos da Azure AD que podem iniciar sação.

Para obter os valores, veja os metadados de descoberta OpenID Connect para cada um dos inquilinos AD Azure de que gostaria que os utilizadores iniciassem súpido. O formato do URL de metadados é semelhante a `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` , onde está o seu nome de inquilino `your-tenant` AZURE AD. Por exemplo:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Execute estes passos para cada inquilino AD Azure que deve ser usado para iniciar sedura:

1. Abra o seu navegador e vá ao URL de metadados OpenID Connect para o inquilino. Encontre o objeto **emitente** e registem o seu valor. Deve parecer-se `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` com.
1. Copie e cole o valor na tecla **ValidTokenIssuerPrefixes.** Separe vários emitentes com uma vírgula. Um exemplo com dois emitentes aparece na `ClaimsProvider` amostra XML anterior.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Teste a sua política personalizada

1. Selecione a sua política partidária de dependência, por exemplo `B2C_1A_signup_signin` .
1. Para **Aplicação**, selecione uma aplicação web que já [se registou anteriormente.](troubleshoot-custom-policies.md#troubleshoot-the-runtime) A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **Executar agora.**
1. A partir da página de inscrição ou de inscrição, selecione **Common AAD** para iniciar sação na conta Azure AD.

Para testar a capacidade de inscrição de vários inquilinos, execute os dois últimos passos usando as credenciais para um utilizador que existe outro inquilino AD AZure. Copie o **ponto final do Run e** abra-o numa janela de navegador privado, por exemplo, Modo Incógnito no Google Chrome ou uma janela InPrivate no Microsoft Edge. A abertura numa janela de navegador privada permite-lhe testar toda a viagem do utilizador não utilizando quaisquer credenciais AZure AD atualmente em cache.

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

Ao trabalhar com políticas personalizadas, pode por vezes precisar de informações adicionais quando se trata de resolver problemas de uma política durante o seu desenvolvimento.

Para ajudar a diagnosticar problemas, pode colocar temporariamente a política em "modo de desenvolvimento" e recolher registos com a Azure Application Insights. Descubra como em [Azure Ative Directory B2C: Recolha de registos](troubleshoot-with-application-insights.md).

::: zone-end

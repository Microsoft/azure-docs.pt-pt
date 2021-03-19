---
title: Configurar inscrição e inscrição com uma conta Salesforce
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição a clientes com contas Salesforce nas suas aplicações utilizando o Azure Ative Directory B2C.
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
ms.openlocfilehash: f87ef03a3af110ec17b7d907d4c2eac1bc1ca58b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579931"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta Salesforce utilizando o Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Criar uma aplicação Salesforce

Para ativar o sessão para utilizadores com uma conta Salesforce no Azure Ative Directory B2C (Azure AD B2C), é necessário criar uma aplicação no seu [Gestor de Aplicações](https://login.salesforce.com/)Salesforce. Para obter mais informações, consulte [configurar configurações básicas de aplicações conectadas](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)e [ativar definições de OAuth para integração de API](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [Inscreva-se na Salesforce.](https://login.salesforce.com/)
1. A partir do menu, selecione **Configuração**.
1.  Expandir **apps** e, em seguida, selecionar **App Manager**.
1. Selecione **nova aplicação conectada**.
1. Nos termos das **Informações Básicas,** insira:
    1. **Connected App Name** - O nome da aplicação conectada é apresentado no Gestor de Aplicações e no seu azulejo de Lançamento de Aplicações. O nome deve ser único dentro da sua org. 
    1. **Nome API** 
    1. **E-mail de contato** - O e-mail de contacto para a Salesforce
1. Em **API (Ativar definições de OAuth)**, selecione **Ative OAuth Settings**
    1. Para o **URL callback,** insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Se utilizar um [domínio personalizado,](custom-domain.md)insira `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-tenant-name`Substitua-o pelo nome do seu inquilino e `your-domain-name` pelo seu domínio personalizado. Você precisa usar todas as letras minúsculas ao introduzir o seu nome de inquilino, mesmo que o inquilino seja definido com letras maiúsculas em Azure AD B2C.
    1. Nos **OAuth Scopes Selecionados,** selecione **Aceda as suas informações básicas (id, perfil, e-mail, endereço, telefone)** e **permita o acesso ao seu identificador único (openid)**.
    1. **Selecione Requer secret for Web Server Flow**.
1. **Selecione Configurar ID Token** 
    1. Desacorda o **Token Valid durante** 5 minutos.
    1. **Selecione Inclua reclamações padrão**.
1. Clique em **Guardar**.
1. Copiar os valores da Chave do **Consumidor** e **do Segredo do Consumidor.** Você precisará de ambos para configurar a Salesforce como um fornecedor de identidade no seu inquilino. **O segredo do cliente** é uma importante credencial de segurança.

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>Configure a Salesforce como fornecedor de identidade

1. Certifique-se de que está a usar o diretório que contém o inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade** e, em seguida, selecione novo fornecedor **OpenID Connect**.
1. Insira um **nome**. Por exemplo, insira *a Salesforce*.
1. Para **o url de metadados,** introduza o URL do documento de [configuração de ligação OpenID da Salesforce](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Para uma caixa de areia, login.salesforce.com é substituído por test.salesforce.com. Para uma comunidade, login.salesforce.com é substituído pela URL da comunidade, como username.force.com/.well-known/openid-configuration. O URL deve ser HTTPS.

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. Para **identificação do cliente,** introduza o ID da aplicação que gravou anteriormente.
1. Para **segredo do Cliente,** insira o segredo do cliente que gravou anteriormente.
1. Para o **Âmbito,** insira o `openid id profile email` .
1. Deixe os valores predefinidos para o **tipo de resposta** e modo **resposta**.
1. (Opcional) Para a **sugestão de Domínio,** insira `contoso.com` . Para obter mais informações, consulte [Configurar o insusimento direto utilizando o Azure Ative Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. No âmbito **do mapeamento de reclamações do fornecedor de identidade,** selecione as seguintes reclamações:

    - **ID do utilizador**: *sub*
    - **Nome do visor**: *nome*
    - **Nome próprio**: *given_name*
    - **Apelido**: *family_name*
    - **E-mail**: *e-mail*

1. Selecione **Guardar**.

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Adicione o fornecedor de identidade Salesforce a um fluxo de utilizador 

Neste momento, o fornecedor de identidade Salesforce foi criado, mas ainda não está disponível em nenhuma das páginas de inscrição. Para adicionar o fornecedor de identidade Salesforce a um fluxo de utilizador:

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Clique no fluxo de utilizador que pretende adicionar ao fornecedor de identidade Salesforce.
1. Sob os **fornecedores de identidade social,** selecione **Salesforce**.
1. Selecione **Guardar**.
1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **de fluxo do utilizador Executar.**
1. A partir da página de inscrição ou de inscrição, selecione **Salesforce** para iniciar scontabilidade da Salesforce.

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do cliente que você já gravou no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Na página 'Visão Geral', selecione **Identity Experience Framework**.
5. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções,** escolha `Manual` .
7. Insira um **Nome** para a chave de política. Por exemplo, `SalesforceSecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Em **Segredo,** insira o segredo do seu cliente que gravou anteriormente.
9. Para **a utilização da chave**, selecione `Signature` .
10. Clique em **Criar**.

## <a name="configure-salesforce-as-an-identity-provider"></a>Configure a Salesforce como fornecedor de identidade

Para permitir que os utilizadores assinem através de uma conta Salesforce, é necessário definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir uma conta Salesforce como fornecedor de sinistros adicionando-a ao elemento **ClaimsProviders** no ficheiro de extensão da sua política.

1. Abra a *TrustFrameworkExtensions.xml.*
2. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **Provider de Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. O **METADATA** está definido para o URL do documento de [configuração de ligação OpenID da Salesforce.](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm) Para uma caixa de areia, login.salesforce.com é substituído por test.salesforce.com. Para uma comunidade, login.salesforce.com é substituído pela URL da comunidade, como username.force.com/.well-known/openid-configuration. O URL deve ser HTTPS.
5. **Desa** esta client_id ao ID do pedido a partir do registo do pedido.
6. Guarde o ficheiro.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Teste a sua política personalizada

1. Selecione a sua política partidária de dependência, por exemplo `B2C_1A_signup_signin` .
1. Para **Aplicação**, selecione uma aplicação web que já [se registou anteriormente.](troubleshoot-custom-policies.md#troubleshoot-the-runtime) A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **Executar agora.**
1. A partir da página de inscrição ou de inscrição, selecione **Salesforce** para iniciar scontabilidade da Salesforce.

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.


::: zone-end

## <a name="next-steps"></a>Passos seguintes

Saiba como passar o [token Salesforce para a sua aplicação.](idp-pass-through-user-flow.md)

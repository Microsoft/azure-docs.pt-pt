---
title: Configurar o início de saúde com um fornecedor de SAML salesforce utilizando o protocolo SAML
titleSuffix: Azure AD B2C
description: Instale o início de saúde com um fornecedor de SAML salesforce utilizando o protocolo SAML em Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 63288bca124959463dc6ea16cb9d681c68ad00da
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448205"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Configurar o início de saúde com um fornecedor de SAML salesforce utilizando o protocolo SAML em Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra-lhe como ativar o início de saúde dos utilizadores de uma organização salesforce utilizando [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C). Ativa o sismo adicionando um fornecedor de [identidade SAML](identity-provider-generic-saml.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- Se ainda não o fez, inscreva-se numa [conta gratuita da Developer Edition.](https://developer.salesforce.com/signup) Este artigo utiliza a [Experiência Relâmpago Salesforce.](https://developer.salesforce.com/page/Lightning_Experience_FAQ)
- [Crie um My Domain](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) para a sua organização Salesforce.

## <a name="set-up-salesforce-as-an-identity-provider"></a>Criar a Salesforce como fornecedor de identidade

1. [Inscreva-se na Salesforce.](https://login.salesforce.com/)
2. No menu esquerdo, em **Definições,** expandir **identidade** e, em seguida, selecionar **Fornecedor de Identidade**.
3. Selecione **Enable Identity Provider**.
4. Em **Seleção do certificado**, selecione o certificado que pretende que a Salesforce utilize para comunicar com a Azure AD B2C. Pode utilizar o certificado predefinido.
5. Clique em **Guardar**.

### <a name="create-a-connected-app-in-salesforce"></a>Criar uma aplicação conectada no Salesforce

1. Na página **Fornecedor de Identidade,** os **Fornecedores de Serviços selecionados são agora criados através de Aplicações Conectadas.**
2. Em **Informação Básica,** insira os valores necessários para a sua aplicação conectada.
3. Nas **Definições de Aplicações Web,** verifique a caixa **Ativar SAML.**
4. No campo ID da **Entidade,** insira o seguinte URL. Certifique-se de que substitui o valor pelo `your-tenant` nome do seu inquilino Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. No campo **URL ACS,** insira o seguinte URL. Certifique-se de que substitui o valor pelo `your-tenant` nome do seu inquilino Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Percorra para a parte inferior da lista e, em seguida, clique em **Guardar**.

### <a name="get-the-metadata-url"></a>Obtenha o URL de metadados

1. Na página geral da sua aplicação conectada, clique em **Gerir**.
2. Copie o valor para **Metadata Discovery Endpoint** e guarde-o. Vai usá-lo mais tarde neste artigo.

### <a name="set-up-salesforce-users-to-federate"></a>Criar utilizadores da Salesforce para federar

1. Na página **'Gerir'** da sua aplicação conectada, clique em **Gerir Perfis.**
2. Selecione os perfis (ou grupos de utilizadores) que pretende federar com Azure AD B2C. Como administrador de sistema, selecione a caixa de verificação **do Administrador do Sistema,** para que possa federar utilizando a sua conta Salesforce.

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o certificado que você criou no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Na página 'Visão Geral', selecione **Identity Experience Framework**.
5. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções,** escolha `Upload` .
7. Introduza um **Nome** para a política. Por exemplo, SAMLSigningCert. O prefixo `B2C_1A_` é automaticamente adicionado ao nome da sua chave.
8. Navegue e selecione o certificado B2CSigningCert.pfx que criou.
9. Introduza a **Palavra-passe** para o certificado.
3. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicione um fornecedor de sinistros

Se pretender que os utilizadores assinem através de uma conta Salesforce, tem de definir a conta como um fornecedor de sinistros com o qual o Azure AD B2C pode comunicar através de um ponto final. O ponto final fornece um conjunto de reclamações que são usadas pelo Azure AD B2C para verificar se um utilizador específico foi autenticado.

Pode definir uma conta Salesforce como fornecedor de sinistros adicionando-a ao elemento **ClaimsProviders** no ficheiro de extensão da sua política. Para obter mais informações, consulte [um fornecedor de identidade SAML.](identity-provider-generic-saml.md)

1. Abra a *TrustFrameworkExtensions.xml.*
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Provider de Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Atualize o valor da **Entidade Parceira** com o URL de metadados salesforce que copiou anteriormente.
1. Atualize o valor de ambas as instâncias de **StorageReferenceId** para o nome da chave do seu certificado de assinatura. Por exemplo, B2C_1A_SAMLSigningCert.
1. Localize a `<ClaimsProviders>` secção e adicione o seguinte corte XML. Se a sua apólice já contiver o `SM-Saml-idp` perfil técnico, salte para o passo seguinte. Para obter mais informações, consulte [a gestão de sessão de sessão de inscrição única.](custom-policy-reference-sso.md)

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Guarde o ficheiro.

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
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
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
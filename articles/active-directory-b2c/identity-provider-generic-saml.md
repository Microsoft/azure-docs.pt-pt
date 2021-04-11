---
title: Configurar inscrição e inscrição com o fornecedor de identidade SAML
titleSuffix: Azure Active Directory B2C
description: Confiúva de inscrição e inscrição com qualquer fornecedor de identidade SAML (IdP) em Azure Ative Directory B2C.
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
ms.openlocfilehash: 944eff6e76f4e5759f70105fe9d09aa61093917f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028303"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com o fornecedor de identidade SAML utilizando o Azure Ative Directory B2C

O Azure Ative Directory B2C (Azure AD B2C) apoia a federação com os fornecedores de identidade SAML 2.0. Este artigo mostra-lhe como permitir o início de saúde com uma conta de utilizador do fornecedor de identidade SAML, permitindo que os utilizadores assinem com as suas identidades sociais ou empresariais existentes, tais como [ADFS](./identity-provider-adfs.md) e [Salesforce.](identity-provider-salesforce-saml.md)

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>Scenario overview (Descrição geral do cenário)

Pode configurar o Azure AD B2C para permitir que os utilizadores inscrevam-se na sua aplicação com credenciais de fornecedores de identidade SAML (IdP) externos ou empresariais. Quando a Azure AD B2C federa com um fornecedor de identidade SAML, atua como prestador de **serviços** que inicia um pedido de SAML ao **fornecedor de identidade** SAML, e aguarda uma resposta DA SAML. No seguinte diagrama:

1. O pedido inicia um pedido de autorização à Azure AD B2C. A aplicação pode ser uma aplicação [OAuth 2.0](protocols-overview.md) ou [OpenId Connect,](openid-connect.md) ou um [prestador de serviços SAML.](saml-service-provider.md) 
1. Na página de sismo Azure AD B2C, o utilizador opta por iniciar seduca com uma conta de fornecedor de identidade SAML (por exemplo, *Contoso).* A Azure AD B2C inicia um pedido de autorização SAML e leva o utilizador ao fornecedor de identidade SAML para completar a inscrição.
1. O fornecedor de identidade SAML devolve uma resposta SAML.
1. O Azure AD B2C valida o token SAML, extrai reclamações, emite o seu próprio token e leva o utilizador de volta à aplicação.  

![Inscreva-se com o fluxo de fornecedor de identidade SAML](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>Componentes da solução

Os seguintes componentes necessários são para este cenário:

* Um **fornecedor de identidade** SAML com a capacidade de receber, descodificar e responder aos pedidos da SAML da Azure AD B2C.
* Um ponto final de **metadados** SAML disponível ao público para o seu fornecedor de identidade.
* Um [inquilino Azure AD B2C.](tutorial-create-tenant.md)
 

## <a name="create-a-policy-key"></a>Criar uma chave de política

Para estabelecer confiança entre a Azure AD B2C e o seu fornecedor de identidade SAML, tem de fornecer um certificado X509 válido com a chave privada. AZure AD B2C assina os pedidos DA SAML, utilizando a chave privada do certificado. O fornecedor de identidade valida o pedido utilizando a chave pública do certificado. A chave pública é acessível através de metadados de perfil técnico. Em alternativa, pode fazer o upload manual do ficheiro .cer para o seu fornecedor de identidade SAML.

Um certificado auto-assinado é aceitável para a maioria dos cenários. Para ambientes de produção, recomenda-se a utilização de um certificado X509 emitido por uma autoridade de certificados. Além disso, como descrito mais tarde neste documento, para um ambiente de não produção pode desativar a assinatura SAML de ambos os lados.

### <a name="obtain-a-certificate"></a>Obter um certificado

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Carregar o certificado

Você precisa armazenar seu certificado no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Na página 'Visão Geral', selecione **Identity Experience Framework**.
1. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha `Upload` .
1. Insira um **Nome** para a chave de política. Por exemplo, `SAMLSigningCert`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
1. Navegue e selecione o seu ficheiro certificado .pfx com a chave privada.
1. Clique em **Criar**.

## <a name="configure-the-saml-technical-profile"></a>Configure o perfil técnico DAL

Defina o fornecedor de identidade SAML adicionando-o ao elemento **ClaimsProviders** no ficheiro de extensão da sua política. Os fornecedores de sinistros contêm um perfil técnico DAL que determina os pontos finais e os protocolos necessários para comunicar com o fornecedor de identidade SAML. Para adicionar um fornecedor de sinistros com um perfil técnico SAML:

1. Abra a *TrustFrameworkExtensions.xml.*
1. Encontre o elemento **ClaimsProviders.** Se não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **Provider de Reclamações** da seguinte forma:

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

Atualizar os seguintes elementos XML com o valor relevante:

|Elemento XML  |Valor  |
|---------|---------|
|ReclamaçõesProvider\Domínio  | O nome de domínio que é utilizado para [o acesso direto](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Introduza o nome de domínio que pretende utilizar no sinduse direto. Por exemplo, *Contoso.com.* |
|TécnicaProfile\DisplayName|Este valor será apresentado no botão de início de sposição no seu ecrã de início de sposição. Por exemplo, *Contoso.* |
|Metadados\Entidade parceira|URL dos metadados do fornecedor de identidade SAML. Ou pode copiar os metadados do fornecedor de identidade e adicioná-lo dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>` .|

## <a name="map-the-claims"></a>Mapear as reivindicações

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pelo fornecedor de identidade SAML. Mapeie o nome da reclamação definida na sua política para o nome de afirmação definido no fornecedor de identidade. Verifique a lista de reclamações (afirmações) do seu fornecedor de identidade. Para obter mais informações, consulte [o mapeamento de reclamações.](identity-provider-generic-saml-options.md#claims-mapping)

No exemplo acima, *a Contoso-SAML2* inclui as reclamações devolvidas por um fornecedor de identidade SAML:

* A **alegação do emitenteUserId** está mapeada para a **afirmaçãoSubjectName** claim.
* A **first_name** alegação está mapeada para a **reclamação dada pelo Nome.**
* A **last_name** alegação está mapeada para a alegação do **sobrenome.**
* A **alegação do displayName** está mapeada para a `http://schemas.microsoft.com/identity/claims/displayname` reclamação.
* A reclamação **de e-mail** sem mapeamento de nome.

O perfil técnico também devolve alegações que não são devolvidas pelo fornecedor de identidade:

* A **identidadeProvider** afirma que contém o nome do fornecedor de identidade.
* A **autenticaçãoProvação com** um valor predefinido de **SocialIdpAuthentication**.
 
### <a name="add-the-saml-session-technical-profile"></a>Adicione o perfil técnico da sessão SAML

Se ainda não tiver o perfil técnico da `SM-Saml-idp` sessão SAML, adicione um à sua política de extensão. Localize a `<ClaimsProviders>` secção e adicione o seguinte corte XML. Se a sua apólice já contiver o `SM-Saml-idp` perfil técnico, salte para o passo seguinte. Para obter mais informações, consulte [a gestão de sessão de sessão de inscrição única.](custom-policy-reference-sso.md)

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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>Configure o seu fornecedor de identidade SAML

Depois de configurar a sua política, tem de configurar o seu fornecedor de identidade SAML com os metadados Azure AD B2C SAML. Os metadados SAML são informações utilizadas no protocolo SAML para expor a configuração da sua política, o **prestador de serviços.** Define a localização dos serviços, tais como a inscrição e a assinatura, certificados, método de inscrição e muito mais.

Cada fornecedor de identidade SAML tem diferentes passos para a criação de um prestador de serviços. Alguns fornecedores de identidade SAML pedem os metadados Azure AD B2C, enquanto outros exigem que você passe pelo ficheiro de metadados manualmente e forneça a informação. Consulte a documentação do seu fornecedor de identidade para obter orientação.

O exemplo a seguir mostra um endereço URL para os metadados SAML de um perfil técnico AZure AD B2C:

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Ao utilizar um [domínio personalizado,](custom-domain.md)utilize o seguinte formato:

```
https://your-domain-name/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Substitua os seguintes valores:

- **seu nome de inquilino** com o seu nome de inquilino, como your-tenant.onmicrosoft.com.
- **o seu nome de domínio** com o seu nome de domínio personalizado, como login.contoso.com.
- **sua política** com o seu nome de política. Por exemplo, B2C_1A_signup_signin_adfs.
- **seu perfil técnico** com o nome do seu perfil técnico do fornecedor de identidade SAML. Por exemplo, Contoso-SAML2.

Abra um navegador e navegue para o URL. Certifique-se de que escreve o URL correto e que tem acesso ao ficheiro de metadados XML.

## <a name="test-your-custom-policy"></a>Teste a sua política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. No âmbito **de políticas**, selecione Identity **Experience Framework**
1. Selecione a sua política partidária de dependência, por exemplo `B2C_1A_signup_signin` .
1. Para **Aplicação**, selecione uma aplicação web que já [se registou anteriormente.](tutorial-register-applications.md) A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **Executar agora.**
1. A partir da página de inscrição ou inscrição, **selecione Contoso** para iniciar scontabilidade de Contoso.

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

- [Configure opções de fornecedor de identidade SAML com Azure Ative Directory B2C](identity-provider-generic-saml-options.md)

::: zone-end
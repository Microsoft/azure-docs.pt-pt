---
title: Configure Azure AD B2C como IdP SAML às suas aplicações
title-suffix: Azure AD B2C
description: Como configurar o Azure AD B2C para fornecer afirmações de protocolo SAML às suas aplicações (prestadores de serviços). O Azure AD B2C funcionará como o único fornecedor de identidade (IdP) para a sua aplicação SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: b9ea9e756587af124ca94518d9f15271310ddee3
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85389383"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registar um pedido DE SAML em Azure AD B2C

Neste artigo, aprende a configurar o Azure Ative Directory B2C (Azure AD B2C) para atuar como um fornecedor de identidade de Marcação de Afirmação de Segurança (SAML) às suas aplicações.

## <a name="scenario-overview"></a>Descrição geral do cenário

As organizações que utilizam o Azure AD B2C como a sua solução de gestão de identidade e acesso podem exigir interação com fornecedores de identidade ou aplicações configuradas para autenticar usando o protocolo SAML.

Azure AD B2C alcança a interoperabilidade da SAML de uma de duas maneiras:

* Atuando como *um fornecedor* de identidade (IdP) e alcançando um único sign-on (SSO) com prestadores de serviços baseados em SAML (suas aplicações)
* Atuando como *prestador de serviços* (SP) e interagindo com fornecedores de identidade baseados em SAML, como a Salesforce e a ADFS

![Diagrama com B2C como fornecedor de identidade à esquerda e B2C como prestador de serviços à direita](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Resumindo os dois cenários centrais não exclusivos com a SAML:

| Cenário | Papel de Azure AD B2C | Procedimentos |
| -------- | ----------------- | ------- |
| A minha candidatura espera que uma afirmação da SAML complete uma autenticação. | **AZure AD B2C atua como fornecedor de identidade (IdP)**<br />A Azure AD B2C atua como um IdP SAML para as aplicações. | Este artigo. |
| Os meus utilizadores precisam de um único sinal com um fornecedor de identidade compatível com SAML, como a ADFS, a Salesforce ou a Shibboleth.  | **A Azure AD B2C atua como prestador de serviços (SP)**<br />O Azure AD B2C atua como prestador de serviços ao ligar-se ao fornecedor de identidade SAML. É um representante da federação entre o seu pedido e o fornecedor de identidade SAML.  | <ul><li>[Configurar o súps com a ADFS como um IdP SAML utilizando políticas personalizadas](identity-provider-adfs2016-custom.md)</li><li>[Configurar o sôm-in com um fornecedor de SAML salesforce usando políticas personalizadas](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Pré-requisitos

* Complete os passos em [Começar com políticas personalizadas em Azure AD B2C.](custom-policy-get-started.md) Você precisa da política personalizada *SocialAndLocalAccounts* do pacote de iniciação de políticas personalizadas discutido no artigo.
* Compreensão básica do protocolo de Marcação de Afirmação de Segurança (SAML).
* Uma aplicação web configurada como prestador de serviços SAML (SP). Para este tutorial, você pode usar uma [aplicação de teste SAML][samltest] que nós fornecemos.

## <a name="components-of-the-solution"></a>Componentes da solução

São necessários três componentes principais para este cenário:

* Prestador **de serviços** SAML com a capacidade de enviar pedidos SAML, e receber, descodificar e responder às afirmações da SAML da Azure AD B2C. Isto também é conhecido como o partido da dependência.
* Ponto **final de metadados** SAML disponível publicamente para o seu prestador de serviços.
* [Inquilino Azure AD B2C](tutorial-create-tenant.md)

Se ainda não tiver um prestador de serviços SAML e um ponto final de metadados associado, pode utilizar esta aplicação SAML de amostra que disponibilizamos para testes:

[Aplicação de teste SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Configurar certificados

Para construir uma relação de confiança entre o seu fornecedor de serviços e o Azure AD B2C, precisa de fornecer os certificados X509 da aplicação web.

* **Certificados de prestador de serviços**
  * Certificado com uma chave privada armazenada na sua Web App. Este certificado é utilizado pelo seu prestador de serviços para assinar o pedido SAML enviado à Azure AD B2C. A Azure AD B2C lê a chave pública dos metadados do prestador de serviços para validar a assinatura.
  * (Opcional) Certificado com uma chave privada armazenada na sua Web App. Azure AD B2C lê a chave pública dos metadados do prestador de serviços para encriptar a afirmação do SAML. O prestador de serviços utiliza então a chave privada para desencriptar a afirmação.
* **Certificados Azure AD B2C**
  * Certificado com chave privada em Azure AD B2C. Este certificado é utilizado pela Azure AD B2C para assinar a resposta SAML enviada ao seu prestador de serviços. O seu prestador de serviços lê a chave pública de metadados Azure AD B2C para validar a assinatura da resposta SAML.

Pode utilizar um certificado emitido por uma autoridade de certificados públicos ou, para este tutorial, um certificado auto-assinado.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Preparar um certificado auto-assinado

Se ainda não tiver um certificado, pode usar um certificado auto-assinado para este tutorial. No Windows, pode utilizar o cmdlet [New-SelfSignedCertificate da PowerShell](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) para gerar um certificado.

1. Execute este comando PowerShell para gerar um certificado auto-assinado. Modifique o `-Subject` argumento conforme apropriado para a sua aplicação e nome de inquilino Azure AD B2C. Também pode ajustar a `-NotAfter` data para especificar uma validade diferente para o certificado.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Abra **os certificados de utilizador**De  >  **gestão corrente**  >  **Personal**  >  **yourappname.yourtenant.onmicrosoft.com**  >  *yourappname.yourtenant.onmicrosoft.com*
1. Selecione o certificado > **Action**  >  **All Tasks**  >  **Export**
1. Selecione **Yes**  >  **Next**  >  **Yes, exporte a chave privada**  >  **Next**
1. Aceite os predefinições para **o formato de ficheiro de exportação**
1. Fornecer uma senha para o certificado

### <a name="12-upload-the-certificate"></a>1.2 Carregar o certificado

Em seguida, faça o upload do certificado de assinatura de afirmação e resposta DA SAML para Azure AD B2C.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue pelo seu inquilino Azure AD B2C.
1. No âmbito **de Políticas**, selecione Quadro de Experiência **de Identidade** e, em seguida, **As teclas de política**.
1. **Selecione Adicionar**e, em seguida, selecione **Opções**  >  **Upload**.
1. Introduza um **Nome,** por exemplo *SamlIdpCert*. O prefixo *B2C_1A_* é automaticamente adicionado ao nome da sua chave.
1. Faça o upload do seu certificado utilizando o controlo de ficheiros de upload.
1. Insira a senha do certificado.
1. Selecione **Criar**.
1. Verifique se a chave aparece como esperado. Por exemplo, *B2C_1A_SamlIdpCert.*

## <a name="2-prepare-your-policy"></a>2. Prepare a sua política

### <a name="21-create-the-saml-token-issuer"></a>2.1 Criar o emitente de ficha SAML

Agora, adicione a capacidade para o seu inquilino emitir fichas SAML, usando os perfis técnicos [do emitente de token SAML](saml-issuer-technical-profile.md) e [do fornecedor de sessão SAML.](custom-policy-reference-sso.md#samlssosessionprovider)

Abra `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** no pacote de arranque de política personalizado.

Localize a `<ClaimsProviders>` secção e adicione o seguinte corte XML.

Pode alterar o valor dos `IssuerUri` metadados. Este é o emitente URI que é devolvido na resposta SAML da Azure AD B2C. A sua aplicação de partido de gestão deve ser configurada para aceitar um emitente URI durante a validação de afirmação DAL.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. Adicione a política do partido que confia no SAML

Agora que o seu inquilino pode emitir afirmações SAML, precisa de criar a política do partido que conta com a SAML e modificar a viagem de utilizador para que emita uma afirmação SAML em vez de um JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Criar política de inscrição ou de inscrição

1. Crie uma cópia do ficheiro *SignUpOrSignin.xml* no seu diretório de trabalho do pack inicial e guarde-o com um novo nome. Por exemplo, *SignUpOrSigninSAML.xml. * Este é o seu ficheiro de política do partido.

1. Abra o ficheiro *SignUpOrSigninSAML.xml* no seu editor preferido.

1. Mude a `PolicyId` `PublicPolicyUri` política para _B2C_1A_signup_signin_saml_ e como se `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` vê abaixo.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Adicione o corte XML logo antes do `<RelyingParty>` elemento. Este XML substitui o passo de orquestração número 7 da jornada do utilizador _Do SignUpOrSignIn._ Se tiver começado a partir de uma pasta diferente no pacote de arranque, ou personalizado a sua viagem de utilizador adicionando ou removendo etapas de orquestração, certifique-se de que o número (no `order` elemento) está alinhado com o especificado na viagem do utilizador para o passo do emissor de token (por exemplo, nas outras pastas de arranque é o passo número 4 para `LocalAccounts` , 6 para `SocialAccounts` e 9 para `SocialAndLocalAccountsWithMfa` ).

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Substitua todo o `<TechnicalProfile>` elemento no elemento pelo seguinte perfil técnico `<RelyingParty>` XML.

    ```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. Atualize `tenant-name` com o nome do seu inquilino Azure AD B2C.

O seu ficheiro final de política do partido deve parecer o seguinte:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Carre faça upload e teste os seus metadados de política

Guarde as suas alterações e carrete o novo ficheiro de política. Depois de ter carregado ambas as políticas (a extensão e os ficheiros do partido em funções), abra um navegador web e navegue para os metadados da política.

Metadados IDP de política Azure AD B2C são informações utilizadas no protocolo SAML para expor a configuração de um fornecedor de identidade SAML. Os metadados definem a localização dos serviços, tais como a inscrição e a assinatura, certificados, método de inscrição e muito mais. Os metadados de política Azure AD B2C estão disponíveis no seguinte URL. Substitua `tenant-name` pelo nome do seu inquilino Azure AD B2C, e pelo nome `policy-name` (ID) da apólice:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Sua política personalizada e inquilino Azure AD B2C estão agora prontos. Em seguida, crie um registo de inscrição em Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Aplicação de configuração no Diretório Azure AD B2C

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4.1 Registe a sua candidatura no Azure AD B2C

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App**e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *SAMLApp1*.
1. Nos **tipos de conta suportada,** selecione Contas neste **diretório organizacional apenas**
1. Em **Redirecionar URI**, selecione **Web**e, em seguida, insira `https://localhost` . Modifica este valor mais tarde no manifesto do registo de candidatura.
1. Selecione **Registar**.

### <a name="42-update-the-app-manifest"></a>4.2 Atualizar o manifesto da aplicação

Para aplicações SAML, existem várias propriedades que precisa de configurar no manifesto do registo de candidatura.

1. No [portal Azure,](https://portal.azure.com)navegue para o registo de candidatura que criou na secção anterior.
1. Under **Manage**, selecione **Manifesto** para abrir o editor manifesto. Modifica várias propriedades nas seguintes secções.

#### <a name="identifieruris"></a>identifierUris

`identifierUris`Trata-se de uma coleção de cordas que contém URI(s) definida pelo utilizador que identifica unicamente uma aplicação Web dentro do seu inquilino Azure AD B2C. O seu prestador de serviços deve definir este valor no `Issuer` elemento de um pedido DEL.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Esta propriedade representa o URL de metadados disponíveis ao público do prestador de serviços. O URL de metadados pode apontar para um ficheiro de metadados enviado para qualquer ponto final de acesso anónimo, por exemplo, armazenamento de bolhas.

Os metadados são informações utilizadas no protocolo SAML para expor a configuração de uma parte SAML, como um prestador de serviços. Os metadados definem a localização dos serviços como a inscrição e a assinatura, certificados, método de inscrição e muito mais. A Azure AD B2C lê os metadados do prestador de serviços e age em conformidade. Os metadados não são necessários. Também pode especificar alguns dos atributos, como o URI de resposta e o logout URI diretamente no manifesto da aplicação.

Se existirem propriedades especificadas tanto no URL de metadados SAML *como* no manifesto do registo de aplicação, são **fundidas.** As propriedades especificadas no URL dos metadados são processadas primeiro e têm precedência.

Para este tutorial, que utiliza a aplicação de teste SAML, utilize o seguinte valor `samlMetadataUrl` para:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>respostaUrlsWithType (Opcional)

Se não fornecer um URI de metadados, pode especificar explicitamente o URL de resposta. Esta propriedade opcional representa o `AssertionConsumerServiceUrl` `SingleSignOnService` URL nos metadados do prestador de serviços e `BindingType` o é assumido como `HTTP POST` .

Se optar por configurar o URL de resposta e url de logout no manifesto de aplicação sem utilizar os metadados do prestador de serviços, o Azure AD B2C não validará a assinatura do pedido SAML nem encriptará a resposta SAML.

Para este tutorial, no qual utiliza a aplicação de teste SAML, desapedaque `url` a propriedade do valor indicado no seguinte corte `replyUrlsWithType` JSON.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (Opcional)

Esta propriedade opcional representa o `Logout` URL `SingleLogoutService` (URL nos metadados do partido em gestão), e o `BindingType` para isso é assumido como `Http-Redirect` .

Para este tutorial, que utiliza a aplicação de teste SAML, deixe `logoutUrl` definido `https://samltestapp2.azurewebsites.net/logout` para:

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Atualize o seu código de aplicação

O último passo é ativar o Azure AD B2C como um IdP SAML na sua aplicação de partido de gestão SAML. Cada aplicação é diferente e os passos para o fazer variam. Consulte a documentação da sua aplicação para obter mais detalhes.

Alguns ou todos os seguintes são normalmente necessários:

* **Metadados:**`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Emitente**: Utilize a entidadeID no ficheiro de metadados
* **Url de início de sessão/URL SAML / URL SAML**: Verifique o valor no ficheiro de metadados
* **Certificado**: Este é *B2C_1A_SamlIdpCert,* mas sem a chave privada. Para obter a chave pública do certificado:

    1. Aceda ao URL de metadados especificado acima.
    1. Copie o valor do `<X509Certificate>` elemento.
    1. Cole-o num ficheiro de texto.
    1. Guarde o ficheiro de texto como ficheiro *.cer.*

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Teste com a App de Teste SAML (opcional)

Para completar este tutorial utilizando a nossa [Aplicação de Teste SAML:][samltest]

* Atualize o nome do inquilino
* Atualizar o nome da política, por *exemplo, B2C_1A_signup_signin_saml*
* Especifique este emitente URI:`https://contoso.onmicrosoft.com/app-name`

Selecione **Login** e deverá ser apresentado com um ecrã de entrada de utilizador. Após a sua inscrição, uma afirmação SAML é emitida de volta ao pedido de amostra.

## <a name="sample-policy"></a>Política de exemplo

Fornecemos uma política completa de amostras que pode utilizar para testar com a App de Teste SAML.

1. Descarregue a [política de amostra de login iniciada pela SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Atualização `TenantId` para combinar com o nome do seu inquilino, por *exemplo, contoso.b2clogin.com*
1. Mantenha o nome da apólice de *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Modalidades SAML apoiadas e não apoiadas

Os seguintes cenários de partidos de suporte SAML (RP) são suportados através do seu próprio ponto final de metadados:

* URLs múltiplos de logout ou encadernação POST para URL logout em objeto principal de aplicação/serviço.
* Especifique a chave de assinatura para verificar os pedidos de RP no objeto principal de aplicação/serviço.
* Especifique a chave de encriptação simbólica no objeto principal de aplicação/serviço.

## <a name="next-steps"></a>Passos seguintes

- Pode encontrar mais informações sobre o [protocolo SAML no site da OASIS.](https://www.oasis-open.org/)
- Obtenha a aplicação web de teste SAML da [Azure AD B2C GitHub community repo](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

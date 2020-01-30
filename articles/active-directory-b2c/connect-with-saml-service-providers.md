---
title: Configurar Azure AD B2C como um IdP do SAML para seus aplicativos
title-suffix: Azure AD B2C
description: Como configurar Azure AD B2C para fornecer asserções de protocolo SAML para seus aplicativos (provedores de serviço). Azure AD B2C atuará como o IdP (provedor de identidade única) para seu aplicativo SAML.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 09c704237e3c1fde8a7591d610d1b801dd016c46
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836665"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrar um aplicativo SAML no Azure AD B2C

Neste artigo, você aprenderá a configurar o Azure Active Directory B2C (Azure AD B2C) para atuar como um provedor de identidade (IdP) Security Assertion Markup Language (SAML) para seus aplicativos.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Descrição geral do cenário

As organizações que usam Azure AD B2C como sua solução de gerenciamento de acesso e identidade do cliente podem exigir interação com provedores de identidade ou aplicativos configurados para autenticar usando o protocolo SAML.

Azure AD B2C Obtém a interoperabilidade SAML de uma das duas maneiras:

* Agindo como um IDP ( *provedor de identidade* ) e obter SSO (logon único) com provedores de serviços baseados em SAML (seus aplicativos)
* Agindo como um *provedor de serviços* (SP) e interagir com provedores de identidade baseados em SAML, como SALESFORCE e ADFS

![Diagrama com B2C como provedor de identidade à esquerda e B2C como provedor de serviços à direita](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Resumindo os dois cenários de núcleo não exclusivos com SAML:

| Cenário | Azure AD B2C função | Procedimento |
| -------- | ----------------- | ------- |
| Meu aplicativo espera que uma Asserção SAML conclua uma autenticação. | **Azure AD B2C atua como o IdP (provedor de identidade)**<br />Azure AD B2C atua como um IdP do SAML para os aplicativos. | Este artigo. |
| Meus usuários precisam de logon único com um provedor de identidade compatível com SAML, como ADFS, Salesforce ou Shibboleth.  | **Azure AD B2C atua como o provedor de serviços (SP)**<br />Azure AD B2C atua como um provedor de serviços ao se conectar ao provedor de identidade SAML. É um proxy de federação entre seu aplicativo e o provedor de identidade SAML.  | <ul><li>[Configurar a entrada com o ADFS como um IdP do SAML usando políticas personalizadas](identity-provider-adfs2016-custom.md)</li><li>[Configurar a entrada com um provedor SAML do Salesforce usando políticas personalizadas](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Pré-requisitos

* Conclua as etapas em introdução [às políticas personalizadas no Azure ad B2C](custom-policy-get-started.md). Você precisa da política personalizada *SocialAndLocalAccounts* do pacote de início de política personalizada abordado no artigo.
* Noções básicas sobre o protocolo SAML (Security Assertion Markup Language).
* Um aplicativo Web configurado como um provedor de serviços SAML (SP). Para este tutorial, você pode usar um [aplicativo de teste do SAML][samltest] que fornecemos.

## <a name="components-of-the-solution"></a>Componentes da solução

Há três componentes principais necessários para este cenário:

* O **provedor de serviços** SAML com a capacidade de enviar solicitações SAML e receber, decodificar e responder a asserções saml de Azure ad B2C. Isso também é conhecido como a terceira parte confiável.
* **Ponto de extremidade de metadados** SAML disponível publicamente para seu provedor de serviços.
* [Azure AD B2C locatário](tutorial-create-tenant.md)

Se você ainda não tiver um provedor de serviços SAML e um ponto de extremidade de metadados associado, poderá usar este aplicativo SAML de exemplo que disponibilizamos para teste:

[Aplicativo de teste do SAML][samltest]

## <a name="1-set-up-certificates"></a>1. configurar certificados

Para criar uma relação de confiança entre seu provedor de serviços e Azure AD B2C, você precisa fornecer certificados X509 e suas chaves privadas.

* **Certificados do provedor de serviços**
  * Certificado com uma chave privada armazenada em seu aplicativo Web. Esse certificado é usado pelo seu provedor de serviços para assinar a solicitação SAML enviada para Azure AD B2C. Azure AD B2C lê a chave pública dos metadados do provedor de serviços para validar a assinatura.
  * Adicional Certificado com uma chave privada armazenada em seu aplicativo Web. Azure AD B2C lê a chave pública dos metadados do provedor de serviços para criptografar a Asserção SAML. Em seguida, o provedor de serviço usa a chave privada para descriptografar a asserção.
* **Azure AD B2C certificados**
  * Certificado com uma chave privada em Azure AD B2C. Esse certificado é usado pelo Azure AD B2C para assinar a resposta SAML enviada ao seu provedor de serviços. Seu provedor de serviços lê a chave pública de metadados Azure AD B2C para validar a assinatura da resposta SAML.

Você pode usar um certificado emitido por uma autoridade de certificação pública ou, para este tutorial, um certificado autoassinado.

### <a name="11-prepare-a-self-signed-certificate"></a>1,1 preparar um certificado autoassinado

Se você ainda não tiver um certificado, poderá usar um certificado autoassinado para este tutorial. No Windows, você pode usar o cmdlet [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) do PowerShell para gerar um certificado.

1. Execute este comando do PowerShell para gerar um certificado autoassinado. Modifique o argumento `-Subject` conforme apropriado para seu aplicativo e Azure AD B2C nome do locatário. Você também pode ajustar a data de `-NotAfter` para especificar uma expiração diferente para o certificado.

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

1. Abrir **gerenciar certificados de usuário** > **usuário atual** > **certificados** de > **pessoais** > *yourappname.yourtenant.onmicrosoft.com*
1. Selecione a **ação** de > de certificado > **todas as tarefas** > **Exportar**
1. Selecione **sim** > **próximo** > **Sim, exportar a chave privada** > **Avançar**
1. Aceitar os padrões para o **formato de arquivo de exportação**
1. Forneça uma senha para o certificado

### <a name="12-upload-the-certificate"></a>1,2 carregar o certificado

Em seguida, carregue a declaração SAML e o certificado de assinatura de resposta para Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o locatário do Azure ad B2C.
1. Em **políticas**, selecione **estrutura de experiência de identidade** e **chaves de política**.
1. Selecione **Adicionar**e, em seguida, selecione **Opções** > **carregar**.
1. Insira um **nome**, por exemplo *SamlIdpCert*. O prefixo *B2C_1A_* é automaticamente adicionado ao nome da sua chave.
1. Carregue seu certificado usando o controle carregar arquivo.
1. Insira a senha do certificado.
1. Selecione **Criar**.
1. Verifique se a chave aparece como esperado. Por exemplo, *B2C_1A_SamlIdpCert.*

## <a name="2-prepare-your-policy"></a>2. Prepare sua política

### <a name="21-create-the-saml-token-issuer"></a>2,1 criar o emissor do token SAML

Agora, adicione a capacidade do seu locatário para emitir tokens SAML.

Abra `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** no pacote de início de política personalizada.

Localize a seção `<ClaimsProviders>` e adicione o trecho de código XML a seguir.

Você pode alterar o valor dos metadados de `IssuerUri`. Esse é o URI do emissor que é retornado na resposta SAML de Azure AD B2C. Seu aplicativo de terceira parte confiável deve ser configurado para aceitar um URI de emissor durante a validação de Asserção SAML.

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
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
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. adicionar a política de terceira parte confiável SAML

Agora que seu locatário pode emitir asserções SAML, você precisa criar a política de terceira parte confiável SAML e modificar o percurso do usuário para que ele emita uma Asserção SAML em vez de um JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3,1 criar política de inscrição ou entrada

1. Crie uma cópia do arquivo *SignUpOrSignin. xml* no diretório de trabalho do pacote inicial e salve-o com um novo nome. Por exemplo, *SignUpOrSigninSAML. xml*. Este é o arquivo de política de terceira parte confiável.

1. Abra o arquivo *SignUpOrSigninSAML. xml* em seu editor preferido.

1. Mude a `PolicyId` e `PublicPolicyUri` da política para _B2C_1A_signup_signin_saml_ e `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` como se vê abaixo.

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Adicione o seguinte trecho XML logo antes do elemento `<RelyingParty>`. Esse XML substitui o número 7 da etapa de orquestração da jornada do usuário _SignUpOrSignIn_ . Se você iniciou a partir de uma pasta diferente no pacote de início, ou personalizado o percurso do usuário adicionando ou removendo etapas de orquestração, verifique se o número (no elemento `order`) está alinhado com aquele especificado no percurso do usuário para a etapa do emissor do token (por exemplo, nas outras pastas do pacote de início, é a etapa 4 da `LocalAccounts`, 6 para `SocialAccounts` e 9 para `SocialAndLocalAccountsWithMfa`).

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Substitua todo o elemento `<TechnicalProfile>` no elemento `<RelyingParty>` pelo seguinte XML de perfil técnico.

    ```XML
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

1. Atualize `tenant-name` com o nome do seu locatário Azure AD B2C.

O arquivo de política de terceira parte confiável final deve ser semelhante ao seguinte:

```XML
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

### <a name="32-upload-and-test-your-policy-metadata"></a>3,2 carregar e testar seus metadados de política

Salve as alterações e carregue o novo arquivo de política. Depois de carregar ambas as políticas (a extensão e os arquivos de terceira parte confiável), abra um navegador da Web e navegue até os metadados da política.

Os metadados da política de Azure AD B2C estão disponíveis na URL a seguir. Substitua `tenant-name` pelo nome do seu locatário do Azure AD B2C e `policy-name` pelo nome (ID) da política:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Sua política personalizada e o locatário de Azure AD B2C agora estão prontos. Em seguida, crie um registro de aplicativo no Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. aplicativo de instalação no diretório Azure AD B2C

### <a name="41-register-your-application-in-azure-active-directory"></a>4,1 registrar seu aplicativo no Azure Active Directory

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione **novo registro**.
1. Insira um **nome** para o aplicativo. Por exemplo, *SAMLApp1*.
1. Em **tipos de conta com suporte**, selecione **contas neste diretório organizacional somente**
1. Em **URI de redirecionamento**, selecione **Web**e, em seguida, insira `https://localhost`. Você modificará esse valor posteriormente no manifesto do registro do aplicativo.
1. Selecione **o consentimento do administrador grant para permissões abertas e offline_access**.
1. Selecione **Registar**.

### <a name="42-update-the-app-manifest"></a>4,2 atualizar o manifesto do aplicativo

Para aplicativos SAML, há várias propriedades que você precisa configurar no manifesto do registro do aplicativo.

1. No [portal do Azure](https://portal.azure.com), navegue até o registro do aplicativo que você criou na seção anterior.
1. Em **gerenciar**, selecione **manifesto** para abrir o editor de manifesto. Você modifica várias propriedades nas seções a seguir.

#### <a name="identifieruris"></a>identifierUris

O `identifierUris` é uma coleção de cadeia de caracteres que contém os URIS definidos pelo usuário que identificam exclusivamente um aplicativo Web dentro de seu locatário Azure AD B2C. Seu provedor de serviços deve definir esse valor no elemento `Issuer` de uma solicitação SAML.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Essa propriedade representa a URL de metadados publicamente disponível do provedor de serviços. A URL de metadados pode apontar para um arquivo de metadados carregado para qualquer ponto de extremidade acessível anonimamente, por exemplo, armazenamento de BLOBs.

Os metadados são informações usadas no protocolo SAML para expor a configuração de uma entidade SAML, como um provedor de serviços. Os metadados definem o local dos serviços, como entrada e saída, certificados, método de entrada e muito mais. Azure AD B2C lê os metadados do provedor de serviços e age de acordo. Os metadados não são necessários. Você também pode especificar alguns atributos, como o URI de resposta e o URI de logout diretamente no manifesto do aplicativo.

Se houver propriedades *especificadas na URL* de metadados SAML e no manifesto do registro do aplicativo, elas serão **mescladas**. As propriedades especificadas na URL de metadados são processadas primeiro e têm precedência.

Para este tutorial que usa o aplicativo de teste do SAML, use o seguinte valor para `samlMetadataUrl`:

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (opcional)

Se você não fornecer um URI de metadados, poderá especificar explicitamente a URL de resposta. Essa propriedade opcional representa o `AssertionConsumerServiceUrl` (`SingleSignOnService` URL nos metadados do provedor de serviços) e o `BindingType` é considerado `HTTP POST`.

Se você optar por configurar a URL de resposta e a URL de logout no manifesto do aplicativo sem usar os metadados do provedor de serviço, Azure AD B2C não validará a assinatura de solicitação SAML nem criptografará a resposta SAML.

Para este tutorial, no qual você usa o aplicativo de teste do SAML, defina a propriedade `url` de `replyUrlsWithType` como o valor mostrado no trecho de JSON a seguir.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (opcional)

Essa propriedade opcional representa a URL de `Logout` (`SingleLogoutService` URL nos metadados de terceira parte confiável) e o `BindingType` para isso é considerado `Http-Redirect`.

Para este tutorial que usa o aplicativo de teste do SAML, deixe `logoutUrl` definido como `https://samltestapp2.azurewebsites.net/logout`:

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. atualizar o código do aplicativo

A última etapa é habilitar Azure AD B2C como um IdP SAML em seu aplicativo de terceira parte confiável SAML. Cada aplicativo é diferente e as etapas para fazer isso variam. Consulte a documentação do aplicativo para obter detalhes.

Algumas ou todas as seguintes são normalmente necessárias:

* **Metadados**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Emissor**: `https://tenant-name.onmicrosoft.com/policy-name`
* **URL de logon/ponto de extremidade SAML/URL SAML**: Verifique o valor no arquivo de metadados
* **Certificado**: Este é *B2C_1A_SamlIdpCert,* mas sem a chave privada. Para obter a chave pública do certificado:

    1. Vá para a URL de metadados especificada acima.
    1. Copie o valor no elemento `<X509Certificate>`.
    1. Cole-o em um arquivo de texto.
    1. Salve o arquivo de texto como um arquivo *. cer* .

### <a name="51-test-with-the-saml-test-app-optional"></a>5,1 teste com o aplicativo de teste do SAML (opcional)

Para concluir este tutorial usando nosso [aplicativo de teste do SAML][samltest]:

* Atualizar o nome do locatário
* Atualizar o nome da política, por *exemplo, B2C_1A_signup_signin_saml*
* Especifique este URI do emissor: `https://contoso.onmicrosoft.com/app-name`

Selecione **logon** e você deverá receber uma tela de entrada do usuário final. Após a entrada, uma Asserção SAML é emitida de volta para o aplicativo de exemplo.

## <a name="sample-policy"></a>Política de exemplo

Fornecemos uma política de exemplo completa que você pode usar para testar com o aplicativo de teste do SAML.

1. Baixar a [política de exemplo de logon iniciada pelo SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Atualize `TenantId` para corresponder ao nome do locatário, por exemplo *contoso.b2clogin.com*
1. Mantenha o nome da política de *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Modalidades SAML com e sem suporte

Os seguintes cenários de RP (terceira parte confiável) do SAML têm suporte por meio de seu próprio ponto de extremidade de metadados:

* Várias URLs de logout ou pós-Associação para URL de logout no objeto de aplicativo/entidade de serviço.
* Especifique a chave de assinatura para verificar as solicitações de RP no objeto de aplicativo/entidade de serviço.
* Especifique a chave de criptografia de token no objeto de aplicativo/entidade de serviço.
* Atualmente, não há suporte para logons iniciados pelo provedor de identidade na versão de visualização.

## <a name="next-steps"></a>Passos seguintes

Você pode encontrar mais informações sobre o [protocolo SAML no site da Oasis](https://www.oasis-open.org/).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

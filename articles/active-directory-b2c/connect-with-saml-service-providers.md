---
title: Configure Azure AD B2C como um IdP SAML para as suas aplicações
title-suffix: Azure AD B2C
description: Como configurar o Azure AD B2C para fornecer afirmações protocolares SAML às suas aplicações (prestadores de serviços). O Azure AD B2C funcionará como o fornecedor de identidade única (IdP) à sua aplicação SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 1c362cd2924de73b2e40e634fe554ff1526e09d8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189655"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registe uma aplicação SAML no Azure AD B2C

Neste artigo, aprende a configurar o Azure Ative Directory B2C (Azure AD B2C) para atuar como fornecedor de identidade de Marcação de Afirmação de Segurança (SAML) nas suas aplicações.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Descrição geral do cenário

As organizações que utilizam o Azure AD B2C como a sua identidade de cliente e solução de gestão de acesso podem exigir interação com fornecedores de identidade ou aplicações configuradas para autenticar usando o protocolo SAML.

Azure AD B2C obtém interoperabilidade SAML de uma de duas formas:

* Atuando como fornecedor de *identidade* (IdP) e alcançando um único sign-on (SSO) com prestadores de serviços baseados em SAML (as suas aplicações)
* Atuando como prestador de *serviços* (SP) e interagindo com fornecedores de identidade baseados na SAML, como a Salesforce e a ADFS

![Diagrama com B2C como fornecedor de identidade à esquerda e B2C como prestador de serviços à direita](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Resumindo os dois cenários centrais não exclusivos com a SAML:

| Cenário | Função Azure AD B2C | Procedimentos |
| -------- | ----------------- | ------- |
| A minha aplicação espera que uma afirmação da SAML complete uma autenticação. | **Azure AD B2C atua como fornecedor de identidade (IdP)**<br />O Azure AD B2C atua como um IdP SAML para as aplicações. | Este artigo. |
| Os meus utilizadores precisam de um único sinal com um fornecedor de identidade compatível com a SAML, como a ADFS, a Salesforce ou a Shibboleth.  | **O Azure AD B2C atua como prestador de serviços (SP)**<br />O Azure AD B2C atua como prestador de serviços ao ligar-se ao fornecedor de identidade SAML. É um representante da federação entre a sua candidatura e o fornecedor de identidade SAML.  | <ul><li>[Configurar o início de sessão com a ADFS como um IdP SAML utilizando políticas personalizadas](identity-provider-adfs2016-custom.md)</li><li>[Configurar o início de sessão com um fornecedor SAML Salesforce utilizando políticas personalizadas](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Pré-requisitos

* Complete os passos em [Get started com políticas personalizadas em Azure AD B2C](custom-policy-get-started.md). Você precisa da política personalizada *SocialAndLocalAccounts* do pacote de iniciação de política personalizada discutido no artigo.
* Compreensão básica do protocolo de linguagem de marcação de afirmação de segurança (SAML).
* Uma aplicação web configurada como prestador de serviços SAML (SP). Para este tutorial, pode utilizar uma aplicação de [teste SAML][samltest] que fornecemos.

## <a name="components-of-the-solution"></a>Componentes da solução

São necessários três componentes principais para este cenário:

* Prestador de **serviços** SAML com a capacidade de enviar pedidos SAML, receber, descodificar e responder às afirmações da SAML do Azure AD B2C. Isto também é conhecido como o partido que confia.
* Ponto final de **metadados** SAML disponível publicamente para o seu prestador de serviços.
* [Inquilino Azure AD B2C](tutorial-create-tenant.md)

Se ainda não tiver um prestador de serviços SAML e um ponto final de metadados associados, pode utilizar esta aplicação SAML da amostra que disponibilizámos para testes:

[Aplicação de teste SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Configurar certificados

Para construir uma relação de confiança entre o seu prestador de serviços e o Azure AD B2C, precisa de fornecer os certificados X509 da aplicação web.

* **Certificados de prestador de serviços**
  * Certificado com chave privada armazenada na sua Web App. Este certificado é utilizado pelo seu prestador de serviços para assinar o pedido DaSL enviado ao Azure AD B2C. O Azure AD B2C lê a chave pública dos metadados do prestador de serviços para validar a assinatura.
  * (Opcional) Certificado com chave privada armazenada na sua Web App. O Azure AD B2C lê a chave pública dos metadados do prestador de serviços para encriptar a afirmação do SAML. O prestador de serviços utiliza então a chave privada para desencriptar a afirmação.
* **Certificados Azure AD B2C**
  * Certificado com chave privada em Azure AD B2C. Este certificado é utilizado pelo Azure AD B2C para assinar a resposta SAML enviada ao seu prestador de serviços. O seu prestador de serviços lê a chave pública de metadados Azure AD B2C para validar a assinatura da resposta SAML.

Pode utilizar um certificado emitido por uma autoridade de certificados públicos ou, para este tutorial, um certificado auto-assinado.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Preparar um certificado auto-assinado

Se ainda não tiver um certificado, pode usar um certificado auto-assinado para este tutorial. No Windows, pode utilizar o cmdlet [New SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) da PowerShell para gerar um certificado.

1. Execute este comando PowerShell para gerar um certificado auto-assinado. Modifique o argumento `-Subject` conforme apropriado para a sua candidatura e nome de inquilino Azure AD B2C. Também pode ajustar a data `-NotAfter` para especificar uma expiração diferente para o certificado.

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

1. **Open Manage certificados** de utilizador > **Atual Utilizador** > **Certificados** de > **Pessoais** > *yourappname.yourtenant.onmicrosoft.com*
1. Selecione o certificado > **Action** > **todas as tarefas** > **Exportação**
1. **Selecione Yes** > **Next** > **Yes, exporte a chave privada** > **Seguinte**
1. Aceitar os incumprimentos do formato de ficheiro de **exportação**
1. Fornecer uma senha para o certificado

### <a name="12-upload-the-certificate"></a>1.2 Carregar o certificado

Em seguida, faça o upload do certificado de assinatura de afirmação e resposta SAML para O Azure AD B2C.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue ao seu inquilino Azure AD B2C.
1. No âmbito **das Políticas,** selecione Quadro de **Experiência de Identidade** e, em **seguida, teclas políticas**.
1. **Selecione Adicionar**, e, em seguida, selecione **Opções** > **carregar**.
1. Introduza um **nome**, por *exemplo, SamlIdpCert*. O prefixo *B2C_1A_* é automaticamente adicionado ao nome da sua chave.
1. Faça upload do seu certificado utilizando o controlo de ficheiros de upload.
1. Insira a senha do certificado.
1. Selecione **Criar**.
1. Verifique se a chave aparece como esperado. Por exemplo, *B2C_1A_SamlIdpCert.*

## <a name="2-prepare-your-policy"></a>2. Prepare a sua política

### <a name="21-create-the-saml-token-issuer"></a>2.1 Criar o emitente de token SAML

Agora, adicione a capacidade para o seu inquilino emitir fichas SAML.

Abra `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** no pacote de iniciação de política personalizada.

Localize a secção `<ClaimsProviders>` e adicione o seguinte corte XML.

Pode alterar o valor dos metadados `IssuerUri`. Este é o emitente URI que é devolvido na resposta SAML do Azure AD B2C. A sua aplicação de parte de base deve ser configurada para aceitar um emitente URI durante a validação da afirmação do SAML.

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
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-sp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. Adicione a política partidária de base saml

Agora que o seu inquilino pode emitir afirmações saml, você precisa criar a política do partido de base SAML, e modificar a jornada do utilizador para que emita uma afirmação SAML em vez de um JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Criar política de inscrição ou inscrição

1. Crie uma cópia do ficheiro *SignUpOrSignin.xml* no seu diretório de trabalho de pack inicial e guarde-o com um novo nome. Por exemplo, *SignUpOrSigninSAML.xml*. Este é o seu arquivo político do partido.

1. Abra o ficheiro *SignUpOrSigninSAML.xml* no seu editor preferido.

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

1. Adicione o seguinte corte XML pouco antes do elemento `<RelyingParty>`. Este XML substitui a etapa de orquestração número 7 da jornada de utilizador _SignUpOrSignIn._ Se tiver começado a partir de uma pasta diferente no pacote de arranque, ou personalizar a sua jornada de utilizador adicionando ou removendo passos de orquestração, certifique-se de que o número (no elemento `order`) está alinhado com o especificado na viagem do utilizador para o passo do emitente do token (por exemplo, nas outras pastas de arranque é passo 4 para `LocalAccounts`, 6 para `SocialAccounts` e 9 para `SocialAndLocalAccountsWithMfa`).

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Substitua todo o elemento `<TechnicalProfile>` no elemento `<RelyingParty>` pelo perfil técnico XML seguinte.

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

1. Atualização `tenant-name` com o nome do seu inquilino Azure AD B2C.

O seu ficheiro político do partido de base final deve parecer o seguinte:

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

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Carregar e testar os metadados da sua política

Guarde as suas alterações e carregue o novo ficheiro de política. Depois de ter carregado ambas as políticas (a extensão e os ficheiros do partido que dependem), abra um navegador web e navegue para os metadados da política.

Os metadados IDP da política Azure AD B2C são informações utilizadas no protocolo SAML para expor a configuração de um fornecedor de identidade SAML. Os metadados definem a localização dos serviços, tais como inscrição e inscrição, certificados, método de inscrição e muito mais. Os metadados da política Azure AD B2C estão disponíveis no seguinte URL. Substitua `tenant-name` pelo nome do seu inquilino Azure AD B2C e `policy-name` com o nome (ID) da apólice:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Sua política personalizada e inquilino Azure AD B2C estão agora prontos. Em seguida, crie um registo de candidatura no Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Aplicação de configuração no Diretório Azure AD B2C

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 Registe a sua candidatura no Diretório Ativo do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **registos de aplicativos (Pré-visualização)** e, em seguida, selecione **Nova inscrição**.
1. Insira um **Nome** para a aplicação. Por exemplo, *SAMLApp1*.
1. Sob os tipos de **conta suportada,** selecione **Contas apenas neste diretório organizacional**
1. Em **Redirecione o URI,** selecione **Web**e, em seguida, introduza `https://localhost`. Modifica este valor mais tarde no manifesto do registo de candidaturas.
1. Selecione **Registar**.

### <a name="42-update-the-app-manifest"></a>4.2 Atualizar o manifesto da aplicação

Para aplicações SAML, existem várias propriedades que precisa de configurar no manifesto do registo de aplicações.

1. No [portal Azure,](https://portal.azure.com)navegue para o registo de candidatura que criou na secção anterior.
1. Under **Manage**, selecione **Manifesto** para abrir o editor manifesto. Modifica várias propriedades nas seguintes secções.

#### <a name="identifieruris"></a>identificadorUris

O `identifierUris` é uma coleção de cordas que contém URI(s) definidos pelo utilizador que identifica uma aplicação Web exclusivamente dentro do seu inquilino Azure AD B2C. O seu prestador de serviços deve definir este valor no elemento `Issuer` de um pedido SAML.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Esta propriedade representa o URL de metadados disponível publicamente pelo fornecedor de serviços. O URL de metadados pode apontar para um ficheiro de metadados enviado para qualquer ponto final acessível anonimamente, por exemplo, armazenamento de blob.

Os metadados são informações utilizadas no protocolo SAML para expor a configuração de uma parte SAML, como um prestador de serviços. Os metadados definem a localização dos serviços como inscrição e inscrição, certificados, método de inscrição e muito mais. O Azure AD B2C lê os metadados do prestador de serviços e atua em conformidade. Os metadados não são necessários. Também pode especificar alguns atributos como a resposta URI e logout URI diretamente no manifesto da aplicação.

Se existirem propriedades especificadas *tanto* no URL de metadados SAML como no manifesto do registo de aplicação, são **fundidas**. As propriedades especificadas no URL dos metadados são processadas primeiro e têm precedência.

Para este tutorial que utiliza a aplicação de teste SAML, utilize o seguinte valor para `samlMetadataUrl`:

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>respostaUrlsWithType (Opcional)

Se não fornecer um URI de metadados, pode especificar explicitamente o URL de resposta. Esta propriedade opcional representa o `AssertionConsumerServiceUrl` (URL`SingleSignOnService` nos metadados do prestador de serviços) e o `BindingType` é assumido como `HTTP POST`.

Se optar por configurar o URL de resposta e iniciar sessão no manifesto de aplicação sem utilizar os metadados do prestador de serviços, o Azure AD B2C não validará a assinatura de pedido saml nem encriptará a resposta SAML.

Para este tutorial, no qual utiliza a aplicação de teste SAML, delineie a propriedade `url` de `replyUrlsWithType` ao valor mostrado no seguinte corte JSON.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (Opcional)

Esta propriedade opcional representa o URL `Logout` ( URL`SingleLogoutService` nos metadados partidários), e o `BindingType` para isso é assumido como `Http-Redirect`.

Para este tutorial que utiliza a aplicação de teste SAML, deixe `logoutUrl` definido para `https://samltestapp2.azurewebsites.net/logout`:

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Atualizar o seu código de candidatura

O último passo é ativar o Azure AD B2C como um IdP SAML na sua aplicação de partido fiada saml. Cada aplicação é diferente e os passos para fazê-lo variam. Consulte a documentação da sua aplicação para obter mais detalhes.

Alguns ou todos os seguintes são normalmente necessários:

* **Metadados**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Emitente**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name`
* **Url de login/saml endpoint/Url SAML**: Verifique o valor no ficheiro de metadados
* **Certificado**: Este é *B2C_1A_SamlIdpCert,* mas sem a chave privada. Para obter a chave pública do certificado:

    1. Vá ao URL dos metadados acima especificado.
    1. Copie o valor no elemento `<X509Certificate>`.
    1. Cola-o num ficheiro de texto.
    1. Guarde o ficheiro de texto como ficheiro *.cer.*

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Teste com a aplicação de teste SAML (opcional)

Para completar este tutorial utilizando a nossa [Aplicação de Teste SAML:][samltest]

* Atualize o nome do inquilino
* Atualizar o nome da política, por *exemplo, B2C_1A_signup_signin_saml*
* Especifique este emitente URI: `https://contoso.onmicrosoft.com/app-name`

Selecione **Login** e deverá ser apresentado com um ecrã de início de sessão do utilizador final. Após o inessão, uma afirmação SAML é emitida de volta para o pedido de amostra.

## <a name="sample-policy"></a>Política de exemplo

Fornecemos uma política completa de amostras que pode utilizar para testar com a App de Teste SAML.

1. Descarregue a [política de amostrade login iniciada pela SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Atualize `TenantId` para combinar com o nome do seu inquilino, por exemplo *contoso.b2clogin.com*
1. Mantenha o nome da política de *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Modalidades SAML apoiadas e não apoiadas

Os seguintes cenários de fiação SAML (RP) são suportados através do seu próprio ponto final de metadados:

* UrLs de logout múltiplos ou encadernação POST para URL de logout no objeto principal de aplicação/serviço.
* Especifique a chave de assinatura para verificar os pedidos de RP no objeto principal de aplicação/serviço.
* Especifique a chave de encriptação token no objeto principal de aplicação/serviço.
* Os logins iniciados pelo fornecedor de identidade não são atualmente suportados no lançamento de pré-visualização.

## <a name="next-steps"></a>Passos seguintes

Pode encontrar mais informações sobre o [protocolo SAML no site da OASIS.](https://www.oasis-open.org/)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

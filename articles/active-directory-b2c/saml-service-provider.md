---
title: Configure Azure Ative Directory B2C como um IdP SAML às suas aplicações
title-suffix: Azure Active Directory B2C
description: Como configurar o Azure Ative Directory B2C para fornecer afirmações de protocolo SAML às suas aplicações (prestadores de serviços). O Azure AD B2C funcionará como o único fornecedor de identidade (IdP) para a sua aplicação SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1035f43642f3884e7cc0f6ab47e9c9afd1f29170
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107769"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registar um pedido DE SAML em Azure AD B2C

Neste artigo, saiba como ligar as suas aplicações de Markup Language (SAML) (prestadores de serviços) ao Azure Ative Directory B2C (Azure AD B2C) para autenticação.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Descrição Geral

As organizações que utilizam o Azure AD B2C como a sua solução de gestão de identidade e acesso podem exigir integração com aplicações que autenticam usando o protocolo SAML. O diagrama seguinte mostra como o Azure AD B2C serve como *um fornecedor* de identidade (IdP) para alcançar um único sign-on (SSO) com aplicações baseadas em SAML.

![Diagrama com B2C como fornecedor de identidade à esquerda e B2C como prestador de serviços à direita.](media/saml-service-provider/saml-service-provider-integration.png)

1. A aplicação cria um Pedido SAML AuthN que é enviado para o ponto final de login SAML da Azure AD B2C.
2. O utilizador pode utilizar uma conta local Azure AD B2C ou qualquer outro fornecedor de identidade federado (se configurado) para autenticar.
3. Se o utilizador assinar a utilização de um fornecedor de identidade federado, é enviada uma resposta simbólica para o Azure AD B2C.
4. Azure AD B2C gera uma afirmação SAML e envia-a para a aplicação.

## <a name="prerequisites"></a>Pré-requisitos

* Complete os passos em [Começar com políticas personalizadas em Azure AD B2C.](custom-policy-get-started.md) Você precisa da política personalizada *SocialAndLocalAccounts* do pacote de iniciação de políticas personalizadas discutido no artigo.
* Compreensão básica do protocolo SAML e familiaridade com a implementação da SAML da aplicação.
* Uma aplicação web configurada como uma aplicação SAML. Para este tutorial, você pode usar uma [aplicação de teste SAML][samltest] que nós fornecemos.

## <a name="components"></a>Componentes

São necessários três componentes principais para este cenário:

* Uma **aplicação** SAML com a capacidade de enviar pedidos da SAML AuthN e receber, descodificar e verificar respostas SAML da Azure AD B2C. A aplicação SAML também é conhecida como a aplicação ou prestador de serviços da parte dependente.
* O ponto final de **metadados** SAML disponível publicamente ou documento XML da aplicação SAML.
* Um [inquilino Azure AD B2C](tutorial-create-tenant.md)

Se ainda não tiver uma aplicação SAML e um ponto final de metadados associado, pode utilizar esta aplicação SAML de amostra que disponibilizamos para testes:

[Aplicação de teste SAML][samltest]

## <a name="set-up-certificates"></a>Configurar certificados

Para construir uma relação de confiança entre a sua aplicação e o Azure AD B2C, ambos os serviços devem ser capazes de criar e validar as assinaturas uns dos outros. Configurar um certificado X509 configurado em Azure AD B2C e a sua aplicação.

**Certificados de inscrição**

| Utilização | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Assinatura de pedido saml  | Não | Um certificado com uma chave privada armazenada na sua aplicação web, utilizado pela sua aplicação para assinar pedidos SAML enviados para Azure AD B2C. A aplicação web deve expor a chave pública através do seu ponto final de metadados SAML. O Azure AD B2C valida a assinatura do pedido SAML utilizando a chave pública a partir dos metadados da aplicação.|
| Encriptação de afirmação SAML  | Não | Um certificado com uma chave privada armazenada na sua aplicação web. A aplicação web deve expor a chave pública através do seu ponto final de metadados SAML. O Azure AD B2C pode encriptar afirmações à sua aplicação utilizando a chave pública. A aplicação utiliza a chave privada para desencriptar a afirmação.|

**Certificados Azure AD B2C**

| Utilização | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Assinatura de resposta SAML | Sim | Um certificado com uma chave privada armazenada em Azure AD B2C. Este certificado é utilizado pela Azure AD B2C para assinar a resposta SAML enviada à sua candidatura. A sua aplicação lê a chave pública de metadados Azure AD B2C para validar a assinatura da resposta SAML. |

Num ambiente de produção, recomendamos a utilização de certificados emitidos por uma autoridade de certificados públicos. No entanto, também pode completar este procedimento com certificados auto-assinados.

### <a name="prepare-a-self-signed-certificate-for-saml-response-signing"></a>Preparar um certificado auto-assinado para a assinatura de resposta SAML

Tem de criar um certificado de assinatura de resposta SAML para que o seu pedido possa confiar na afirmação da Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>Habilitar a sua política a ligar-se a uma aplicação SAML

Para se ligar à sua aplicação SAML, o Azure AD B2C deve ser capaz de criar respostas SAML.

Abra `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** no pacote de arranque de política personalizado.

Localize a `<ClaimsProviders>` secção e adicione o seguinte corte XML para implementar o seu gerador de resposta SAML.

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
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
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

#### <a name="configure-the-issueruri-of-the-saml-response"></a>Configure o EmitenteUri da resposta SAML

Pode alterar o valor do `IssuerUri` item dos metadados no perfil técnico do emitente de token SAML. Esta alteração refletir-se-á no `issuerUri` atributo devolvido na resposta SAML da Azure AD B2C. A sua aplicação deve ser configurada para aceitar o mesmo durante a `issuerUri` validação da resposta DAL.

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
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-metadata-optional"></a>Assine os metadados Azure AD B2C IdP SAML (opcional)

Pode instruir o Azure AD B2C a assinar o seu documento de metadados SAML IdP, se necessário pela aplicação. Para tal, gere e carrede uma chave de assinatura de metadados SAML IdP, tal como mostrado na [Preparação de um certificado auto-assinado para a assinatura de resposta SAML](#prepare-a-self-signed-certificate-for-saml-response-signing). Em seguida, configuure o `MetadataSigning` item dos metadados no perfil técnico do emitente de token SAML. Deve `StorageReferenceId` referenciar o nome-chave da política.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-response-element-optional"></a>Assine o elemento de resposta Azure AD B2C IdP SAML (opcional)

Pode especificar um certificado a ser utilizado para assinar as mensagens SAML. A mensagem é o `<samlp:Response>` elemento dentro da resposta SAML enviada para a aplicação.

Para especificar um certificado, gere e carrefique uma chave de política como mostrado na [Preparação de um certificado auto-assinado para a assinatura de resposta SAML](#prepare-a-self-signed-certificate-for-saml-response-signing). Em seguida, configuure o `SamlMessageSigning` item metadados no perfil técnico do emitente do token SAML. O `StorageReferenceId` nome deve fazer referência ao nome chave de política.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```
## <a name="configure-your-policy-to-issue-a-saml-response"></a>Configure a sua política para emitir uma resposta SAML

Agora que a sua política pode criar respostas SAML, tem de configurar a política para emitir uma resposta SAML em vez da resposta padrão do JWT à sua aplicação.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>Criar uma política de inscrição ou de inscrição configurada para o SAML

1. Crie uma cópia do ficheiro *SignUpOrSignin.xml* no seu diretório de trabalho do pack inicial e guarde-o com um novo nome. Por exemplo, *SignUpOrSigninSAML.xml.* Este ficheiro é o seu ficheiro de política do partido, e está configurado para emitir uma resposta JWT por padrão.

1. Abra o ficheiro *SignUpOrSigninSAML.xml* no seu editor preferido.

1. Mude a `PolicyId` `PublicPolicyUri` política para _B2C_1A_signup_signin_saml_ e como se `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml` vê abaixo.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. No final da Jornada do Utilizador, o Azure AD B2C contém um `SendClaims` passo. Este passo refere-se ao Perfil Técnico do Emitente Token. Para emitir uma resposta SAML em vez da resposta JWT padrão, modifique o `SendClaims` passo para fazer referência ao novo perfil técnico do emitente SAML Token, `Saml2AssertionIssuer` .

Adicione o seguinte corte XML pouco antes do `<RelyingParty>` elemento. Este XML substitui o passo de orquestração número 7 na jornada do utilizador _Do SignUpOrSignIn._ Se tiver começado a partir de uma pasta diferente no pacote de arranque ou personalizar a viagem do utilizador adicionando ou removendo etapas de orquestração, certifique-se de que o número do `order` elemento corresponde ao número especificado na viagem do utilizador para o passo do emitente simbólico. Por exemplo, nas outras pastas de arranque, o número de passo correspondente é de 4 para `LocalAccounts` , 6 para `SocialAccounts` e 9 para `SocialAndLocalAccountsWithMfa` ).

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

O elemento do partido que conta determina qual o protocolo que a sua aplicação utiliza. A predefinição é `OpenId`. O `Protocol` elemento deve ser alterado para `SAML` . As Alegações de Saída criarão o mapeamento de reclamações para a afirmação SAML.

Substitua todo o `<TechnicalProfile>` elemento no elemento pelo seguinte perfil técnico `<RelyingParty>` XML. Atualize `tenant-name` com o nome do seu inquilino Azure AD B2C.

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

O seu ficheiro de política final do partido deve parecer o seguinte código XML:

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

> [!NOTE]
> Pode seguir este mesmo processo para implementar outros tipos de fluxos de utilizador (por exemplo, iniciar sessão, reset de palavra-passe ou fluxos de edição de perfis).

### <a name="upload-your-policy"></a>Faça upload da sua política

Guarde as suas alterações e faça o upload dos novos ficheiros de política **TrustFrameworkExtensions.xml** e **SignUpOrSigninSAML.xml** para o portal Azure.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Teste os metadados Azure AD B2C IdP SAML

Após o upload dos ficheiros de política, o Azure AD B2C utiliza as informações de configuração para gerar o documento de metadados SAML do fornecedor de identidade a ser utilizado pela aplicação. O documento de metadados SAML contém a localização de serviços, tais como métodos de início de súmu, certificados, e assim por diante.

Os metadados de política Azure AD B2C estão disponíveis no seguinte URL:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

`<tenant-name>`Substitua-o pelo nome do seu inquilino Azure AD B2C e `<policy-name>` pelo nome (ID) da apólice, por exemplo:

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Registe a sua aplicação SAML em Azure AD B2C

Para que o Azure AD B2C confie na sua aplicação, cria um registo de aplicação AD B2C Azure, que contém informações de configuração como o ponto final dos metadados da aplicação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App** e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *SAMLApp1*.
1. Nos **tipos de conta suportada,** selecione Contas **apenas neste diretório organizacional**.
1. Em **Redirecionar URI**, selecione **Web** e, em seguida, insira `https://localhost` . Irá modificar este valor mais tarde no manifesto do registo de candidatura.
1. Selecione **Registar**.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Configure a sua aplicação em Azure AD B2C

Para aplicações SAML, você precisará configurar várias propriedades no manifesto do registo de aplicação.

1. No [portal Azure,](https://portal.azure.com)navegue para o registo de candidatura que criou na secção anterior.
1. Em **Manage**, selecione **Manifesto** para abrir o editor manifesto e, em seguida, modifique as propriedades descritas nas seguintes secções.

#### <a name="add-the-identifier"></a>Adicione o identificador

Quando a sua aplicação SAML faz um pedido ao Azure AD B2C, o pedido da SAML AuthN inclui um `Issuer` atributo, que é tipicamente o mesmo valor que os metadados da aplicação. `entityID` A Azure AD B2C utiliza este valor para procurar o registo de pedidos no diretório e ler a configuração. Para que esta procura tenha sucesso, o `identifierUri` registo no pedido deve ser preenchido com um valor que corresponda ao `Issuer` atributo.

No manifesto de registo, localize o `identifierURIs` parâmetro e adicione o valor adequado. Este valor será o mesmo valor configurado nos pedidos da SAML AuthN para EntityId na aplicação, e o `entityID` valor nos metadados da aplicação.

O exemplo a seguir mostra `entityID` os metadados SAML:

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

A `identifierUris` propriedade só aceitará URLs no `tenant-name.onmicrosoft.com` domínio.

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Partilhe os metadados da aplicação com o Azure AD B2C

Depois de o registo de pedido ter sido carregado pelo `identifierUri` seu, o Azure AD B2C utiliza os metadados da aplicação para validar o pedido da SAML AuthN e determinar como responder.

Recomenda-se que a sua aplicação exponha um ponto final de metadados acessíveis ao público.

Se existirem propriedades especificadas tanto no URL de metadados SAML *como* no manifesto do registo de aplicação, são *fundidas.* As propriedades especificadas no URL dos metadados são processadas primeiro e têm precedência.

Utilizando a aplicação de teste SAML como exemplo, utilizaria o seguinte valor para `samlMetadataUrl` o manifesto de aplicação:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>Sobrepor ou definir o URL do consumidor de afirmação (opcional)

Pode configurar o URL de resposta ao qual a Azure AD B2C envia respostas SAML. Os URLs de resposta podem ser configurados dentro do manifesto de pedido. Esta configuração é útil quando a sua aplicação não expõe um ponto final de metadados acessíveis ao público.

O URL de resposta para um pedido SAML é o ponto final no qual o pedido espera receber respostas SAML. A aplicação geralmente fornece este URL no documento de metadados no `AssertionConsumerServiceUrl` atributo, como mostrado abaixo:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Se pretender sobrepor os metadados fornecidos no `AssertionConsumerServiceUrl` atributo ou se o URL não estiver presente no documento de metadados, pode configurar o URL no manifesto sob a `replyUrlsWithType` propriedade. O `BindingType` será definido para `HTTP POST` .

Utilizando a aplicação de teste SAML como exemplo, definiria a `url` propriedade do valor indicado no seguinte corte `replyUrlsWithType` JSON.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>Sobrepôr ou definir o URL logout (opcional)

Pode configurar o URL logout para o qual a Azure AD B2C enviará ao utilizador após um pedido de logout. Os URLs de resposta podem ser configurados dentro do Manifesto de Aplicação.

Se pretender sobrepor os metadados fornecidos no `SingleLogoutService` atributo ou se o URL não estiver presente no documento de metadados, pode configugá-lo no manifesto sob a `Logout` propriedade. O `BindingType` será definido para `Http-Redirect` .

A aplicação geralmente fornece este URL no documento de metadados no `AssertionConsumerServiceUrl` atributo, como mostrado abaixo:

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

Utilizando a aplicação de teste SAML como exemplo, deixaria `logoutUrl` definido `https://samltestapp2.azurewebsites.net/logout` para:

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Se optar por configurar o URL de resposta e URL de logout no manifesto da aplicação sem povoar o ponto final dos metadados da aplicação através da propriedade, o `samlMetadataUrl` Azure AD B2C não validará a assinatura de pedido SAML, nem encriptará a resposta SAML.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>Configure Azure AD B2C como um IdP SAML na sua aplicação SAML

O último passo é ativar o Azure AD B2C como um IdP SAML na sua aplicação SAML. Cada aplicação é diferente e os passos variam. Consulte a documentação da sua aplicação para obter mais detalhes.

Os metadados podem ser configurados na sua aplicação como *metadados estáticos* ou *metadados dinâmicos*. Em modo estático, copie a total ou parte dos metadados dos metadados da política Azure AD B2C. Em modo dinâmico, forneça o URL aos metadados e permita que a sua aplicação leia os metadados de forma dinâmica.

Alguns ou todos os seguintes são normalmente necessários:

* **Metadados**: Utilize o formato `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` .
* **Emitente**: O valor de pedido SAML `issuer` deve corresponder a um dos URIs configurados no elemento do manifesto de registo de `identifierUris` pedido. Se o nome de pedido SAML `issuer` não existir no `identifierUris` elemento, [adicione-o ao manifesto de registo de inscrição.](#add-the-identifier) Por exemplo, `https://contoso.onmicrosoft.com/app-name`. 
* **Url de início de sessão/URL SAML**: Verifique o valor no ficheiro de metadados de política Azure AD B2C SAML para o `<SingleSignOnService>` elemento XML.
* **Certificado**: Este certificado é *B2C_1A_SamlIdpCert,* mas sem a chave privada. Para obter a chave pública do certificado:

    1. Aceda ao URL de metadados especificado acima.
    1. Copie o valor do `<X509Certificate>` elemento.
    1. Cole-o num ficheiro de texto.
    1. Guarde o ficheiro de texto como um ficheiro *.cer.*

### <a name="test-with-the-saml-test-app"></a>Teste com a aplicação de teste SAML

Pode utilizar a nossa [Aplicação de Teste SAML][samltest] para testar a sua configuração:

* Atualize o nome do inquilino.
* Atualize o nome da apólice, por *exemplo, B2C_1A_signup_signin_saml*.
* Especifique este emitente URI. Utilize um dos URIs encontrados `identifierUris` no elemento no manifesto de registo de pedidos, por `https://contoso.onmicrosoft.com/app-name` exemplo.

Selecione **Login** e deverá ser apresentado com um ecrã de entrada de utilizador. Após a sua inscrição, é emitida uma resposta SAML ao pedido de amostra.

## <a name="supported-and-unsupported-saml-modalities"></a>Modalidades SAML apoiadas e não apoiadas

Os seguintes cenários de aplicação SAML são suportados através do seu próprio ponto final de metadados:

* URLs múltiplos de logout ou encadernação POST para URL logout no objeto principal de aplicação/serviço.
* Especifique uma chave de assinatura para verificar os pedidos de parte de funções (RP) no objeto principal de aplicação/serviço.
* Especifique uma chave de encriptação simbólica no objeto principal de aplicação/serviço.
* Início de inscrição iniciado pelo fornecedor de identidade, onde o fornecedor de identidade é Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

- Obtenha a aplicação web de teste SAML da [Azure AD B2C GitHub community repo](https://github.com/azure-ad-b2c/saml-sp-tester).
- Consulte as [opções de registo de uma aplicação SAML em Azure AD B2C](saml-service-provider-options.md)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end

---
title: Configure as opções de prestador de serviços SAML
title-suffix: Azure Active Directory B2C
description: Como configurar opções de prestador de serviços Azure Ative Directory B2C SAML
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
ms.openlocfilehash: b9a491b639cd1b960ffe3b7164a0940770792148
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107777"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Opções para registo de uma aplicação SAML em Azure AD B2C

Este artigo descreve as opções de configuração que estão disponíveis ao ligar o Azure Ative Directory (Azure AD B2C) à sua aplicação SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="encrypted-saml-assertions"></a>Afirmações SAML encriptadas

Quando a sua aplicação espera que as afirmações da SAML estejam num formato encriptado, precisa de se certificar de que a encriptação está ativada na política AZure AD B2C.

O Azure AD B2C utiliza o certificado de chave pública do prestador de serviços para encriptar a afirmação do SAML. A chave pública deve existir no ponto final de metadados da aplicação SAML com o conjunto 'Utilização' do KeyDescriptor para 'Encriptação', como mostra o seguinte exemplo:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Para permitir que o Azure AD B2C envie afirmações encriptadas, desafie o item de metadados **de Setadar DeEncriptado** para `true` o perfil técnico do partido em [suporte](relyingparty.md#technicalprofile). Também pode configurar o algoritmo usado para encriptar a afirmação DOL.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="identity-provider-initiated-flow"></a>Fluxo iniciado pelo fornecedor de identidade

Quando a sua aplicação espera receber uma afirmação SAML sem primeiro enviar um pedido DA SAML AuthN ao fornecedor de identidade, deve configurar a Azure AD B2C para fluxo iniciado pelo fornecedor de identidade.

No fluxo iniciado pelo fornecedor de identidade, o processo de inscrição é iniciado pelo fornecedor de identidade (Azure AD B2C), que envia uma resposta SAML não solicitada ao prestador de serviços (a sua aplicação de partido em gestão).

Atualmente, não apoiamos cenários em que o fornecedor de identidade iniciante seja um fornecedor de identidade externo federado com Azure AD B2C, por exemplo [AD-FS,](identity-provider-adfs.md)ou [Salesforce.](identity-provider-salesforce-saml.md) É suportado apenas para autenticação de conta local Azure AD B2C.

Para permitir o fluxo iniciado pelo fornecedor de identidade, defina o item de metadados **IdpInitiatedProfileEnabled** `true` no perfil técnico da parte de [suporte](relyingparty.md#technicalprofile).

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Para iniciar sedução ou inscrição de um utilizador através do fluxo iniciado pelo fornecedor de identidade, utilize o seguinte URL:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

Substitua os seguintes valores:

* **nome inquilino** com o seu nome de inquilino
* **nome de política** com o seu nome de política do partido de sing saml
* **app-identifier-uri** com o `identifierUris` no ficheiro de metadados, tais como `https://contoso.onmicrosoft.com/app-name`

### <a name="sample-policy"></a>Política de exemplo

Fornecemos uma política completa de amostra que pode usar para testar com a aplicação de teste SAML.

1. Descarregue a [política de amostra de login iniciada pela SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated).
1. Atualização `TenantId` para combinar com o nome do seu inquilino, por *exemplo, contoso.b2clogin.com*.
1. Mantenha o nome da apólice *B2C_1A_signup_signin_saml*.

## <a name="saml-response-signature-algorithm"></a>Algoritmo de assinatura de resposta SAML

Pode configurar o algoritmo de assinatura usado para assinar a afirmação SAML. Os valores possíveis `Sha256` `Sha384` `Sha512` são, , ou `Sha1` . Certifique-se de que o perfil técnico e a aplicação utilizam o mesmo algoritmo de assinatura. Use apenas o algoritmo que o seu certificado suporta.

Configure o algoritmo de assinatura utilizando a `XmlSignatureAlgorithm` chave de metadados dentro do nó de metadados DaPartid.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="saml-response-lifetime"></a>Vida útil da resposta SAML

Pode configurar o tempo de duração da resposta SAML. Desacorda a vida útil utilizando o `TokenLifeTimeInSeconds` item metadados dentro do perfil técnico do emitente do token SAML. Este valor é o número de segundos que pode decorrer a partir do `NotBefore` relógio calculado no tempo de emissão de ficha. Automaticamente, o tempo escolhido para isto é a sua hora atual. O tempo de vida predefinido é de 300 segundos (5 minutos).

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="saml-response-valid-from-skew"></a>Resposta SAML válida de distorção

Pode configurar o desvio de tempo aplicado ao tempo de tempo de resposta `NotBefore` SAML. Esta configuração garante que se os tempos entre duas plataformas não estiverem sincronizados, a afirmação da SAML ainda será considerada válida quando dentro deste tempo distorcer.

Desaperte o desvio de tempo utilizando o `TokenNotBeforeSkewInSeconds` item metadados dentro do perfil técnico do emitente do token SAML. O valor distorcido é dado em segundos, com um valor padrão de 0. O valor máximo é de 3600 (uma hora).

Por exemplo, quando o `TokenNotBeforeSkewInSeconds` é definido para `120` segundos:

- O token é emitido às 13:05:10 UTC
- O token é válido a partir das 13:03:10 UTC

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="azure-ad-b2c-issuer-id"></a>ID do emitente Azure AD B2C

Se tiver várias aplicações SAML que dependem de `entityID` valores diferentes, pode sobrepor-se ao `issueruri` valor no seu ficheiro de festa de confiança. Para anular o emitente URI, copie o perfil técnico com o ID "Saml2AssertionIssuer" do ficheiro base e sobreponha o `issueruri` valor.

> [!TIP]
> Copie a `<ClaimsProviders>` secção da base e preserve estes elementos dentro do fornecedor de sinistros: , e `<DisplayName>Token Issuer</DisplayName>` `<TechnicalProfile Id="Saml2AssertionIssuer">` `<DisplayName>Token Issuer</DisplayName>` .
 
Exemplo:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="session-management"></a>Gestão de sessões

Pode gerir a sessão entre o Azure AD B2C e a aplicação do partido que conta com a SAML utilizando o `UseTechnicalProfileForSessionManagement` elemento e o [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="debug-the-saml-protocol"></a>Depurar o protocolo SAML

Para ajudar a configurar e depurar a integração com o seu fornecedor de serviços, pode utilizar uma extensão de navegador para o protocolo SAML, por exemplo, [extensão SAML DevTools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) para ferramentas Chrome, [SAML-tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) para FireFox ou [Edge ou IE Developer](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Utilizando estas ferramentas, pode verificar a integração entre a sua aplicação e o Azure AD B2C. Por exemplo:

* Verifique se o pedido DE SAML contém uma assinatura e determine que algoritmo é usado para assinar o pedido de autorização.
* Verifique se o Azure AD B2C devolve uma mensagem de erro.
* Verifique se a secção de afirmação está encriptada.

## <a name="next-steps"></a>Passos seguintes

- Mais informações sobre o [protocolo SAML no site da OASIS.](https://www.oasis-open.org/)
- Obtenha a aplicação web de teste SAML a partir do [Azure AD B2C GitHub comunidade repo](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end

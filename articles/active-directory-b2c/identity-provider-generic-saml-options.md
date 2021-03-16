---
title: Descreva o sismo com as opções de fornecedor de identidade SAML
titleSuffix: Azure Active Directory B2C
description: Configure as opções de fornecedor de identidade SAML (IdP) de assinatura em Azure Ative Directory B2C.
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
ms.openlocfilehash: 43c57950d317de42df666ddd25cbcb2e9a4c9611
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488878"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Configure opções de fornecedor de identidade SAML com Azure Ative Directory B2C

O Azure Ative Directory B2C (Azure AD B2C) apoia a federação com os fornecedores de identidade SAML 2.0. Este artigo descreve as opções de configuração disponíveis ao permitir o início de saúde com um fornecedor de identidade SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>Mapeamento de reclamações

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pelo fornecedor de identidade SAML. Tem de mapear o nome da reclamação definida na sua política para o nome definido no fornecedor de identidade. Verifique a lista de reclamações (afirmações) do seu fornecedor de identidade. Também pode verificar o conteúdo da resposta SAML que o seu fornecedor de identidade devolve. Para mais informações, consulte [depurar as mensagens SAML](#debug-saml-protocol). Para adicionar uma reclamação, primeiro [defina uma reclamação,](claimsschema.md)em seguida, adicione o pedido à cobrança de reclamações de saída.

Também pode incluir reclamações que não são devolvidas pelo fornecedor de identidade, desde que desagressem o `DefaultValue` atributo. O valor predefinido pode ser estático ou dinâmico, utilizando [alegações de contexto](#enable-use-of-context-claims).

O elemento de reclamação de saída contém os seguintes atributos:

- **ClaimTypeReferenceId** é a referência a um tipo de reclamação. 
- **PartnerClaimType** é o nome da propriedade que aparece afirmação SAML. 
- **DefaultValue** é um valor predefinido por defeito. Se a reclamação estiver vazia, o valor predefinido será utilizado. Também pode utilizar uma [reclamação](claim-resolver-overview.md) com um valor contextual, como o ID de correlação ou o endereço IP do utilizador.

### <a name="subject-name"></a>Nome do requerente

Para ler a afirmação SAML **NameId** no **Sujeito** como uma reclamação normalizada, desajuste o Parceiro de **ReclamaçãoClaimType** ao valor do `SPNameQualifier` atributo. Se o `SPNameQualifier` atributo não for apresentado, desavenda o Parceiro de ReclamaçãoClaimType ao valor do  `NameQualifier` atributo.

Afirmação SAML:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Reclamação de saída:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Se ambos `SPNameQualifier` ou `NameQualifier` atributos não forem apresentados na afirmação SAML, desa um conjunto de parceiros de reclamaçãoClaimType para  `assertionSubjectName` . Certifique-se de que o **NameId** é o primeiro valor em afirmação XML. Quando define mais do que uma afirmação, a Azure AD B2C escolhe o valor do sujeito a partir da última afirmação.


## <a name="configure-saml-protocol-bindings"></a>Vinculação do protocolo SAML

Os pedidos SAML são enviados ao fornecedor de identidade conforme especificado no elemento metadados do fornecedor de `SingleSignOnService` identidade. A maioria dos pedidos de autorização dos fornecedores de identidade são realizados diretamente na cadeia de consulta DE URL de um pedido HTTP GET (uma vez que as mensagens são relativamente curtas). Consulte a documentação do seu fornecedor de identidade sobre como configurar as ligações para ambos os pedidos DE SAML.

Segue-se um exemplo de um serviço único de inscrição de metadados AD AZure com duas encadernações. A `HTTP-Redirect` precedência tem precedência sobre o `HTTP-POST` porque aparece em primeiro lugar nos metadados do fornecedor de identidade SAML.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

As respostas SAML são transmitidas ao Azure AD B2C através da ligação HTTP POST. Os metadados de política Azure AD B2C `AssertionConsumerService` definem a ligação a `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST` .

Segue-se um exemplo de um elemento de metadados de afirmação de metadados de política Azure AD B2C.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>Configure a assinatura do pedido DA SAML

O Azure AD B2C assina todos os pedidos de autenticação de saída utilizando a chave criptográfica **SamlMessageSigning.** Para desativar a assinatura de pedido SAML, desative os Pedidos de **Pedidos de Designação** para `false` . Se os metadados estiverem definidos para `false` , os parâmetros **SigAlg** e **Signature** (linha de consulta ou parâmetro pós)são omitidos do pedido.

Estes metadados também controlam o atributo **AuthnRequestsSigned,** que está incluído nos metadados do perfil técnico Azure AD B2C que é partilhado com o fornecedor de identidade. O Azure AD B2C não assina o pedido se o valor dos **QuerSignedRequests** nos metadados de perfil técnico for definido `false` e os metadados do fornecedor de identidade **WantAuthnRequestsSigned** forem definidos ou `false` não.

O exemplo a seguir remove a assinatura do pedido DAL.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>Algoritmo de assinatura

A Azure AD B2C utiliza `Sha1` para assinar o pedido DAL. Utilize os metadados **XmlSignatureAlgorithm** para configurar o algoritmo a utilizar. Os valores possíveis são `Sha256` `Sha384` , , ou `Sha512` `Sha1` (predefinição). Estes metadados controlam o valor do parâmetro  **SigAlg** (cadeia de consulta ou parâmetro de pós) no pedido DEL. Certifique-se de configurar o algoritmo de assinatura em ambos os lados com o mesmo valor. Use apenas o algoritmo que o seu certificado suporta.

### <a name="include-key-info"></a>Incluir informações chave

Quando o fornecedor de identidade indica que a ligação Azure AD B2C está definida para `HTTP-POST` , Azure AD B2C inclui a assinatura e o algoritmo no corpo do pedido SAML. Também pode configurar a Azure AD para incluir a chave pública do certificado quando a ligação estiver definida para `HTTP-POST` . Utilize os metadados **IncludeKeyInfo** para `true` , ou `false` . No exemplo seguinte, a Azure AD não incluirá a chave pública do certificado.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>Configurar identificação do nome de pedido SAML

O elemento de pedido de autorização SAML `<NameID>` indica o formato identificador de nome SAML. Esta secção descreve a configuração padrão e como personalizar o elemento ID do nome.

## <a name="preferred-username"></a>Nome de utilizador preferido

Durante uma viagem de utilizador de entrada, uma aplicação de parte de gestão pode visar um utilizador específico. O Azure AD B2C permite-lhe enviar um nome de utilizador preferido ao fornecedor de identidade SAML. O elemento **InputClaims** é utilizado para enviar um **NameId** no âmbito do **pedido** de autorização SAML.

Para incluir o ID do nome do sujeito no pedido de autorização, adicione o seguinte `<InputClaims>` elemento imediatamente após o `<CryptographicKeys>` . O **PartnerClaimType** deve ser programado para `subject` .

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

Neste exemplo, a Azure AD B2C envia o valor da reclamação **signInName** com **a NameId** no **âmbito** do pedido de autorização DA SAML.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

Pode utilizar [reclamações de contexto,](claim-resolver-overview.md)tais como `{OIDC:LoginHint}` para preencher o valor da reclamação.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>Formato de política de identificação de nome

Por defeito, o pedido de autorização SAML especifica a `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` apólice. Isto indica que qualquer tipo de identificador apoiado pelo fornecedor de identidade para o sujeito solicitado pode ser utilizado.

Para alterar este comportamento, consulte a documentação do seu fornecedor de identidade para obter orientações sobre quais as políticas de identificação de nomes que são apoiadas. Em seguida, adicione os `NameIdPolicyFormat` metadados no formato de política correspondente. Por exemplo:

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

O seguinte pedido de autorização DA SAML contém a política de identificação do nome.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>Permitir a criação de novas contas

Se especificar o [formato de política de identificação do nome,](#name-id-policy-format)também pode especificar a propriedade da `AllowCreate` **NameIDPolicy** para indicar se o fornecedor de identidade está autorizado a criar uma nova conta durante o fluxo de entrada. Consulte a documentação do seu fornecedor de identidade para obter orientação.

Azure AD B2C omite a `AllowCreate` propriedade por padrão. Pode alterar este comportamento utilizando os `NameIdPolicyAllowCreate` metadados. O valor deste metadados é `true` `false` ou.

O exemplo a seguir demonstra como definir `AllowCreate` a propriedade de `NameIDPolicy` `true` .

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


O exemplo a seguir demonstra um pedido de autorização com **AllowCreate** do elemento **NameIDPolicy** no pedido de autorização.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>Incluir referências de classe de contexto de autenticação

Um pedido de autorização SAML pode conter um elemento **AuthnContext,** que especifica o contexto de um pedido de autorização. O elemento pode conter uma referência de classe de contexto de autenticação, que diz ao fornecedor de identidade SAML qual o mecanismo de autenticação a apresentar ao utilizador.

Para configurar as referências de classe de contexto de autenticação, adicione **Metadados de Classe IncluirAuthnContextClassReferences.** No valor, especifique uma ou mais referências URI identificando classes de contexto de autenticação. Especifique vários URIs como uma lista delimitada por vírgula. Consulte a documentação do seu fornecedor de identidade para obter orientações sobre os URIs **AuthnContextClassRef** que são suportados.

O exemplo a seguir permite que os utilizadores entrem com o nome de utilizador e senha, e insinuem-se com o nome de utilizador e a palavra-passe durante uma sessão protegida (SSL/TLS).

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

O seguinte pedido de autorização SAML contém as referências de classe de contexto de autenticação.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>Incluir dados personalizados no pedido de autorização

Pode opcionalmente incluir elementos de extensão de mensagens protocolares que são acordados tanto pelo Azure AD BC como pelo seu fornecedor de identidade. A extensão é apresentada em formato XML. Inclui elementos de extensão adicionando dados XML dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>` . Consulte a documentação do seu fornecedor de identidade para ver se o elemento de extensão está suportado.

O exemplo a seguir ilustra a utilização de dados de extensão:

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

Ao utilizar a extensão da mensagem de protocolo SAML, a resposta SAML será o seguinte exemplo:

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>Requerem respostas SAML assinadas

O Azure AD B2C exige que todas as afirmações recebidas sejam assinadas. Pode remover este requisito definindo as **Sesignrtions** para `false` . O fornecedor de identidade não deve assinar as afirmações neste caso, mas mesmo que isso faça, a Azure AD B2C não validará a assinatura.

Os metadados **WantSignedAssertions** controlam a bandeira de metadados SAML **WantAssertionsSigned**, que está incluído nos metadados do perfil técnico Azure AD B2C que é partilhado com o fornecedor de identidade.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

Se desativar a validação das afirmações, também poderá querer desativar a validação da assinatura da mensagem de resposta. Desa estaveriu os metadados **de respostas** para `false` . O fornecedor de identidade não deve assinar a mensagem de resposta SAML neste caso, mas mesmo que o faça, a Azure AD B2C não validará a assinatura.

O exemplo a seguir remove tanto a mensagem como a assinatura de afirmação:

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>Requerem respostas SAML encriptadas

Para exigir que todas as afirmações recebidas sejam encriptadas, desencripta os metadados **WantEncryptedAssertions.** Quando a encriptação é necessária, o fornecedor de identidade utiliza uma chave pública de um certificado de encriptação num perfil técnico Azure AD B2C. Azure AD B2C desencripta a afirmação de resposta utilizando a parte privada do certificado de encriptação.

Se ativar a encriptação das afirmações, poderá também necessitar de desativar a validação da assinatura de resposta (para obter mais informações, consulte [Exigir respostas SAML assinadas](#require-signed-saml-responses).

Quando os metadados **DoEsencriptedAssertions** estiverem definidos `true` para, os metadados do perfil técnico Azure AD B2C incluem a secção **de encriptação.** O fornecedor de identidade lê os metadados e encripta a afirmação de resposta SAML com a chave pública que é fornecida nos metadados do perfil técnico Azure AD B2C.

O exemplo a seguir mostra a secção de descritor chave dos metadados SAML utilizados para encriptação:

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

Para encriptar a afirmação de resposta SAML:

1. [Crie uma chave de política](identity-provider-generic-saml.md#create-a-policy-key) com um identificador único. Por exemplo, `B2C_1A_SAMLEncryptionCert`.
2. Na sua coleção **cryptographicKeys** de perfil técnico SAML. Desaprote o **StorageReferenceId** para o nome da chave de política que criou no primeiro passo. O `SamlAssertionDecryption` ID indica o uso da chave criptográfica para encriptar e desencriptar a afirmação da resposta SAML.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. Desafie os metadados de perfil técnico **Quer Configurações encriptadas** para `true` .
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. Atualize o seu fornecedor de identidade com os novos metadados de perfil técnico Azure AD B2C. Deverá consultar o **KeyDescriptor** com a propriedade **de utilização** definida para `encryption` conter a chave pública do seu certificado.

## <a name="enable-use-of-context-claims"></a>Permitir a utilização de reclamações de contexto

Na recolha de pedidos de entrada e saída, pode incluir reclamações que não sejam devolvidas pelo fornecedor de identidade desde que desagressem o `DefaultValue` atributo. Também pode utilizar [alegações de contexto](claim-resolver-overview.md) para serem incluídas no perfil técnico. Para utilizar uma reivindicação de contexto:

1. Adicione um tipo de reclamação ao elemento [ClaimsSchema](claimsschema.md) dentro [do BuildingBlocks](buildingblocks.md).
2. Adicione uma reivindicação de saída à entrada ou recolha de saída. No exemplo seguinte, a primeira reclamação define o valor do fornecedor de identidade. A segunda reclamação utiliza as [reclamações](claim-resolver-overview.md)do contexto do endereço IP do utilizador .
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>Desativar o logout único

Após um pedido de inscrição, a Azure AD B2C tenta assinar do seu fornecedor de identidade SAML. Para mais informações, consulte [a sessão de Sessão Azure AD B2C](session-behavior.md#sign-out).  Para desativar o comportamento de um único logout, desafie os metadados **SingleLogoutEnabled** para `false` .

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>Protocolo DEBUG SAML

Para ajudar a configurar e depurar a federação com um fornecedor de identidade SAML, pode utilizar uma extensão de navegador para o protocolo SAML, como [extensão SAML DevTools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) para ferramentas Chrome, [SAML-tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) para FireFox, ou [Edge ou IE Developer .](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)

Utilizando estas ferramentas, pode verificar a integração entre o Azure AD B2C e o seu fornecedor de identidade SAML. Por exemplo:

* Verifique se o pedido DE SAML contém uma assinatura e determine que algoritmo é usado para assinar o pedido de autorização.
* Obtenha as reclamações (afirmações) na `AttributeStatement` secção.
* Verifique se o fornecedor de identidade devolve uma mensagem de erro.
* Verifique se a secção de afirmação está encriptada.

## <a name="next-steps"></a>Passos seguintes

- Saiba como diagnosticar problemas com as suas políticas personalizadas, utilizando [o Application Insights](troubleshoot-with-application-insights.md). 

::: zone-end

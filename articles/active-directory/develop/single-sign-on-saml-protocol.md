---
title: Protocolo SAML de logon único do Azure | Microsoft Docs
description: Este artigo descreve o protocolo SAML de logon único no Azure Active Directory
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf512f802e0e4944e6ce949830719b87301adfc4
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834804"
---
# <a name="single-sign-on-saml-protocol"></a>Protocolo SAML de logon único

Este artigo aborda as solicitações e respostas de autenticação SAML 2,0 que o Azure Active Directory (Azure AD) dá suporte para logon único.

O diagrama de protocolo abaixo descreve a sequência de logon único. O serviço de nuvem (o provedor de serviços) usa uma associação de redirecionamento http para passar um `AuthnRequest` elemento (solicitação de autenticação) para o Azure AD (o provedor de identidade). Em seguida, o Azure ad usa uma associação http post `Response` para postar um elemento no serviço de nuvem.

![Fluxo de trabalho de logon único](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Para solicitar uma autenticação de usuário, os serviços de `AuthnRequest` nuvem enviam um elemento para o Azure AD. Um exemplo de SAML `AuthnRequest` 2,0 poderia ser semelhante ao exemplo a seguir:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| id | Requerido | O Azure ad usa esse atributo para popular `InResponseTo` o atributo da resposta retornada. A ID não deve começar com um número, portanto, uma estratégia comum é preceder uma cadeia de caracteres como "ID" à representação de cadeia de caracteres de um GUID. Por exemplo, `id6c1c178c166d486687be4aaf5e482730` é uma ID válida. |
| Version | Requerido | Esse parâmetro deve ser definido como **2,0**. |
| IssueInstant | Requerido | Essa é uma cadeia de caracteres DateTime com um valor UTC e um [formato de ida e volta ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). O Azure AD espera um valor DateTime desse tipo, mas não avalia ou usa o valor. |
| AssertionConsumerServiceUrl | Opcional | Se fornecido, esse parâmetro deve corresponder `RedirectUri` ao do serviço de nuvem no Azure AD. |
| ForceAuthn | Opcional | Esse é um valor booliano. Se for true, significa que o usuário será forçado a autenticar novamente, mesmo que tenha uma sessão válida com o Azure AD. |
| Ispassal | Opcional | Esse é um valor booliano que especifica se o Azure AD deve autenticar o usuário silenciosamente, sem interação do usuário, usando o cookie de sessão, se houver. Se isso for verdadeiro, o Azure AD tentará autenticar o usuário usando o cookie de sessão. |

Todos os `AuthnRequest` outros atributos, como consentimento, destino, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex e ProviderName, são **ignorados**.

O Azure ad também ignora o `Conditions` elemento em `AuthnRequest`.

### <a name="issuer"></a>Emissor

O `Issuer` elemento em um `AuthnRequest` deve corresponder exatamente a um dos **servicePrincipalName** no serviço de nuvem no Azure AD. Normalmente, isso é definido como o **URI da ID do aplicativo** que é especificado durante o registro do aplicativo.

Um trecho SAML que contém `Issuer` o elemento é semelhante ao exemplo a seguir:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Esse elemento solicita um formato de ID de nome específico na resposta e é opcional `AuthnRequest` em elementos enviados ao Azure AD.

Um `NameIdPolicy` elemento é semelhante ao exemplo a seguir:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Se `NameIDPolicy` for fornecido, você poderá incluir seu atributo `Format` opcional. O `Format` atributo pode ter apenas um dos valores a seguir; qualquer outro valor resulta em um erro.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory emite a declaração NameID como um identificador de par.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory emite a declaração NameID no formato de endereço de email.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Esse valor permite que Azure Active Directory selecione o formato de declaração. Azure Active Directory emite NameID como um identificador de par.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory emite a declaração NameID como um valor gerado aleatoriamente que é exclusivo para a operação de SSO atual. Isso significa que o valor é temporário e não pode ser usado para identificar o usuário de autenticação.

O Azure ad ignora o `AllowCreate` atributo.

### <a name="requestauthncontext"></a>RequestAuthnContext
O `RequestedAuthnContext` elemento Especifica os métodos de autenticação desejados. É opcional em `AuthnRequest` elementos enviados ao Azure AD. O Azure ad `AuthnContextClassRef` dá suporte a `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`valores como.

### <a name="scoping"></a>Cedido
O `Scoping` elemento, que inclui uma lista de provedores de identidade, é opcional `AuthnRequest` em elementos enviados ao Azure AD.

Se fornecido, não inclua o `ProxyCount` `IDPListOption` atributo ou `RequesterID` o elemento, pois eles não têm suporte.

### <a name="signature"></a>Assinatura
Não inclua um `Signature` elemento em `AuthnRequest` elementos, pois o Azure ad não oferece suporte a solicitações de autenticação assinadas.

### <a name="subject"></a>Subject
O Azure ad ignora o `Subject` elemento dos `AuthnRequest` elementos.

## <a name="response"></a>Resposta
Quando um logon solicitado é concluído com êxito, o Azure AD posta uma resposta para o serviço de nuvem. Uma resposta a uma tentativa de logon bem-sucedida é semelhante ao exemplo a seguir:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Resposta

O `Response` elemento inclui o resultado da solicitação de autorização. O Azure ad define `ID`os `Version` valores `IssueInstant` e no `Response` elemento. Ele também define os seguintes atributos:

* `Destination`: Quando o logon é concluído com êxito, isso é definido como o `RedirectUri` do provedor de serviços (serviço de nuvem).
* `InResponseTo`: Isso é definido como o `ID` atributo `AuthnRequest` do elemento que iniciou a resposta.

### <a name="issuer"></a>Emissor

O Azure ad define `Issuer` o elemento `https://login.microsoftonline.com/<TenantIDGUID>/` para \<onde TenantIDGUID > é a ID de locatário do locatário do Azure AD.

Por exemplo, uma resposta com elemento emissor poderia ser semelhante ao exemplo a seguir:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Estado

O `Status` elemento transmite o êxito ou a falha de logon. Ele inclui o `StatusCode` elemento, que contém um código ou um conjunto de códigos aninhados que representa o status da solicitação. Ele também inclui o `StatusMessage` elemento, que contém mensagens de erro personalizadas que são geradas durante o processo de logon.

<!-- TODO: Add an authentication protocol error reference -->

O exemplo a seguir é uma resposta SAML para uma tentativa de logon malsucedida.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Declaração

Além do, `IssueInstant` e `ID` `Version`o Azureaddefineosseguinteselementosno`Assertion` elemento da resposta.

#### <a name="issuer"></a>Emissor

Isso é definido como `https://sts.windows.net/<TenantIDGUID>/`onde \<TenantIDGUID > é a ID de locatário do locatário do Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Assinatura

O Azure AD assina a asserção em resposta a um logon bem-sucedido. O `Signature` elemento contém uma assinatura digital que o serviço de nuvem pode usar para autenticar a origem para verificar a integridade da asserção.

Para gerar essa assinatura digital, o Azure ad usa a chave de assinatura `IDPSSODescriptor` no elemento de seu documento de metadados.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Subject

Isso especifica a entidade que é a entidade das instruções na asserção. Ele contém um `NameID` elemento, que representa o usuário autenticado. O `NameID` valor é um identificador de destino que é direcionado apenas para o provedor de serviços que é o público-alvo do token. É persistente-ele pode ser revogado, mas nunca é reatribuído. Ele também é opaco, pois não revela nada sobre o usuário e não pode ser usado como um identificador para consultas de atributo.

O `Method` atributo `urn:oasis:names:tc:SAML:2.0:cm:bearer`do elemento é sempre definido como. `SubjectConfirmation`

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Condições

Esse elemento especifica as condições que definem o uso aceitável de asserções SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Os `NotBefore` atributos `NotOnOrAfter` e especificam o intervalo durante o qual a asserção é válida.

* O valor do `NotBefore` atributo é igual ou ligeiramente (menor que um segundo) depois do valor do `IssueInstant` atributo do `Assertion` elemento. O Azure AD não conta nenhuma diferença de tempo entre si mesmo e o serviço de nuvem (provedor de serviços) e não adiciona nenhum buffer a esse tempo.
* O valor do `NotOnOrAfter` atributo é 70 minutos depois do valor `NotBefore` do atributo.

#### <a name="audience"></a>Audiência

Ele contém um URI que identifica um público-alvo. O Azure ad define o valor desse elemento como o valor do `Issuer` elemento `AuthnRequest` do que iniciou o logon. Para avaliar o `Audience` valor, use o valor do que `App ID URI` foi especificado durante o registro do aplicativo.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Como o `Issuer` valor, o `Audience` valor deve corresponder exatamente a um dos nomes da entidade de serviço que representa o serviço de nuvem no Azure AD. No entanto, se o valor `Issuer` do elemento não for um valor de URI `Audience` , o valor na resposta será `Issuer` o valor prefixado com `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Isso contém declarações sobre o assunto ou o usuário. O trecho a seguir contém um `AttributeStatement` elemento de exemplo. A elipse indica que o elemento pode incluir vários atributos e valores de atributo.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Declaração de nome** -o valor do `Name` atributo (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) é o nome UPN do usuário autenticado, `testuser@managedtenant.com`como.
* **Declaração objectidentifier** – o valor do `ObjectIdentifier` atributo (`http://schemas.microsoft.com/identity/claims/objectidentifier`) é o `ObjectId` do objeto de diretório que representa o usuário autenticado no Azure AD. `ObjectId`é um identificador seguro, globalmente exclusivo e de reutilização do usuário autenticado.

#### <a name="authnstatement"></a>AuthnStatement

Esse elemento declara que a entidade de asserção foi autenticada por um meio específico em um determinado momento.

* O `AuthnInstant` atributo especifica a hora em que o usuário se autenticou com o Azure AD.
* O `AuthnContext` elemento Especifica o contexto de autenticação usado para autenticar o usuário.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```

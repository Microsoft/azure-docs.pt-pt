---
title: Sinal único azure no protocolo SAML / Microsoft Docs
description: Este artigo descreve o protocolo SAML de assinatura única no Diretório Ativo Azure
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
ms.openlocfilehash: cecb78a82eb2925813bdc7f6df2503fae94b6437
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375665"
---
# <a name="single-sign-on-saml-protocol"></a>Protocolo SAML de início único

Este artigo abrange os pedidos de autenticação SAML 2.0 e respostas que o Azure Ative Directory (Azure AD) suporta para o Single Sign-On.

O diagrama de protocolo abaixo descreve a sequência de inscrição única. O serviço de nuvem (o prestador de serviços) utiliza um código DE Redirecionamento HTTP para passar um elemento `AuthnRequest` (pedido de autenticação) ao Azure AD (o fornecedor de identidade). A Azure AD utiliza então um post HTTP vinculativo para colocar um elemento `Response` no serviço de nuvem.

![Sinal único no fluxo de trabalho](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Para solicitar a autenticação de um utilizador, os serviços na nuvem enviam um elemento `AuthnRequest` para a AD Azure. Uma amostra SAML 2.0 `AuthnRequest` pode parecer o seguinte exemplo:

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
| ID | Necessário | A Azure AD usa este atributo para povoar o atributo `InResponseTo` da resposta devolvida. O ID não deve começar com um número, por isso uma estratégia comum é preparar uma corda como "id" para a representação de cordas de um GUID. Por exemplo, `id6c1c178c166d486687be4aaf5e482730` é uma identificação válida. |
| Versão | Necessário | Este parâmetro deve ser definido para **2.0**. |
| EmissãoInstant | Necessário | Esta é uma cadeia DateTime com um valor UTC e [formato de ida e volta ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). A Azure AD espera um valor DateTime deste tipo, mas não avalia nem utiliza o valor. |
| AssertionConsumerServiceUrl | Opcional | Se fornecido, este parâmetro deve coincidir com o `RedirectUri` do serviço de nuvem em Azure AD. |
| ForceAuthn | Opcional | Este é um valor booleano. Se for verdade, significa que o utilizador será obrigado a reautenticar, mesmo que tenha uma sessão válida com a AD Azure. |
| IsPassive | Opcional | Este é um valor booleano que especifica se o Azure AD deve autenticar o utilizador em silêncio, sem interação do utilizador, utilizando o cookie de sessão se existir. Se isso for verdade, a Azure AD tentará autenticar o utilizador utilizando o cookie de sessão. |

Todos os outros atributos `AuthnRequest`, tais como Consentimento, Destino, AfirmaçãoConsumerServiceIndex, AttributeConsumerServiceIndex e ProviderName são **ignorados**.

A AD Azure também ignora o elemento `Conditions` em `AuthnRequest`.

### <a name="issuer"></a>Emissor

O elemento `Issuer` numa `AuthnRequest` deve corresponder exatamente a um dos **ServicePrincipaNames** no serviço de cloud em Azure AD. Normalmente, isto é definido para o **ID da aplicação URI** que é especificado durante o registo da aplicação.

Um excerto saml contendo o elemento `Issuer` se parece com a seguinte amostra:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Este elemento solicita um formato de ID de nome específico na resposta e é opcional em `AuthnRequest` elementos enviados para a AD Azure.

Um elemento `NameIdPolicy` se parece com a seguinte amostra:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Se for fornecida `NameIDPolicy`, pode incluir o seu atributo opcional `Format`. O atributo `Format` só pode ter um dos seguintes valores; qualquer outro valor resulta num erro.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: O Diretório Ativo azure emite a alegação NameID como um identificador em pares.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: O Diretório Ativo do Azure emite a reclamação do NameID no formato de endereço de e-mail.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Este valor permite ao Azure Ative Directory selecionar o formato de reclamação. O Diretório Ativo Azure emite o NameID como um identificador em pares.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: O Diretório Ativo azure emite a alegação NameID como um valor gerado aleatoriamente que é exclusivo da operação SSO atual. Isto significa que o valor é temporário e não pode ser utilizado para identificar o utilizador autenticador.

A AD Azure ignora o atributo `AllowCreate`.

### <a name="requestauthncontext"></a>RequestAuthnContext
O elemento `RequestedAuthnContext` especifica os métodos de autenticação desejados. É opcional em `AuthnRequest` elementos enviados para a AD Azure. A Azure AD suporta `AuthnContextClassRef` valores como `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Scoping
O elemento `Scoping`, que inclui uma lista de fornecedores de identidade, é opcional em elementos `AuthnRequest` enviados para a AD Azure.

Se fornecido, não inclua o `ProxyCount` elemento atributo, `IDPListOption` ou `RequesterID`, uma vez que não são suportados.

### <a name="signature"></a>Assinatura
Não inclua um elemento `Signature` em elementos `AuthnRequest`, uma vez que a Azure AD não suporta pedidos de autenticação assinados.

### <a name="subject"></a>Requerente
A AD Azure ignora o elemento `Subject` de elementos `AuthnRequest`.

## <a name="response"></a>Resposta
Quando um sinal solicitado completa com sucesso, a Azure AD publica uma resposta ao serviço de nuvem. Uma resposta a uma tentativa de inscrição bem sucedida parece a seguinte amostra:

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

O elemento `Response` inclui o resultado do pedido de autorização. A AD Azure define os valores `ID`, `Version` e `IssueInstant` no elemento `Response`. Também define os seguintes atributos:

* `Destination`: Quando o início de sessão for concluído com sucesso, este é definido para o `RedirectUri` do prestador de serviços (serviço na nuvem).
* `InResponseTo`: Isto é definido para o atributo `ID` do elemento `AuthnRequest` que iniciou a resposta.

### <a name="issuer"></a>Emissor

A Azure AD define o elemento `Issuer` para `https://login.microsoftonline.com/<TenantIDGUID>/` onde \<TenantIDGUID> é a identificação do inquilino do inquilino azure AD.

Por exemplo, uma resposta com elemento emitente pode parecer a seguinte amostra:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Estado

O elemento `Status` transmite o sucesso ou falha do sign-on. Inclui o elemento `StatusCode`, que contém um código ou um conjunto de códigos aninhados que representam o estado do pedido. Também inclui o elemento `StatusMessage`, que contém mensagens de erro personalizadas que são geradas durante o processo de iniciar sessão.

<!-- TODO: Add an authentication protocol error reference -->

A amostra que se segue é uma resposta SAML a uma tentativa de inscrição fracassada.

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

### <a name="assertion"></a>Afirmação

Além da `ID`, `IssueInstant` e `Version`, a AD Azure define os seguintes elementos no elemento `Assertion` da resposta.

#### <a name="issuer"></a>Emissor

Isto está programado para `https://sts.windows.net/<TenantIDGUID>/`onde \<TenantIDGUID> é o Id inquilino do inquilino azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Assinatura

A Azure AD assina a afirmação em resposta a uma inscrição bem sucedida. O elemento `Signature` contém uma assinatura digital que o serviço de nuvem pode usar para autenticar a fonte para verificar a integridade da afirmação.

Para gerar esta assinatura digital, a Azure AD utiliza a chave de assinatura no elemento `IDPSSODescriptor` do seu documento de metadados.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Requerente

Isto especifica o principal que é objeto das declarações na afirmação. Contém um elemento `NameID`, que representa o utilizador autenticado. O valor `NameID` é um identificador direcionado que é direcionado apenas para o prestador de serviços que é o público para o token. É persistente - pode ser revogado, mas nunca é reatribuído. É também opaco, na medida em que não revela nada sobre o utilizador e não pode ser usado como um identificador para consultas de atributos.

O atributo `Method` do elemento `SubjectConfirmation` está sempre definido para `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Condições

Este elemento especifica condições que definem a utilização aceitável de afirmações SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Os atributos `NotBefore` e `NotOnOrAfter` especificam o intervalo durante o qual a afirmação é válida.

* O valor do atributo `NotBefore` é igual ou ligeiramente (inferior a um segundo) mais tarde do que o valor do atributo `IssueInstant` do elemento `Assertion`. A Azure AD não explica qualquer diferença de tempo entre si e o serviço de nuvem (prestador de serviços), e não adiciona nenhum tampão a este tempo.
* O valor do atributo `NotOnOrAfter` é 70 minutos mais tarde do que o valor do atributo `NotBefore`.

#### <a name="audience"></a>Audiência

Isto contém um URI que identifica um público pretendido. A Azure AD define o valor deste elemento no valor de `Issuer` elemento da `AuthnRequest` que iniciou o signo. Para avaliar o valor `Audience`, utilize o valor do `App ID URI` especificado durante o registo da candidatura.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Tal como o valor `Issuer`, o valor `Audience` deve corresponder exatamente a um dos principais nomes de serviço que representa o serviço de nuvem em Azure AD. No entanto, se o valor do elemento `Issuer` não for um valor URI, o valor `Audience` na resposta é o valor `Issuer` prefixado com `spn:`.

#### <a name="attributestatement"></a>Declaração de Atributo

Isto contém reclamações sobre o assunto ou o utilizador. O seguinte excerto contém uma amostra `AttributeStatement` elemento. A elipse indica que o elemento pode incluir múltiplos atributos e valores de atributo.

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

* **Reivindicação** de nome - O valor do atributo `Name` (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) é o nome principal do utilizador autenticado, como `testuser@managedtenant.com`.
* **Reclamação ObjectIdentifier** - O valor do atributo `ObjectIdentifier` (`http://schemas.microsoft.com/identity/claims/objectidentifier`) é o `ObjectId` do objeto de diretório que representa o utilizador autenticado em Azure AD. `ObjectId` é um identificador imutável, globalmente único, e reutilizar o identificador seguro do utilizador autenticado.

#### <a name="authnstatement"></a>AuthnStatement

Este elemento afirma que o sujeito de afirmação foi autenticado por um determinado meio num determinado momento.

* O atributo `AuthnInstant` especifica o tempo em que o utilizador autenticou com a AD Azure.
* O elemento `AuthnContext` especifica o contexto de autenticação utilizado para autenticar o utilizador.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```

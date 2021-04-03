---
title: Azure único sinal no protocolo SAML
titleSuffix: Microsoft identity platform
description: Este artigo descreve o protocolo SAML de Sign-On Único (SSO) no Diretório Ativo Azure
services: active-directory
documentationcenter: .net
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 40bf202e0f14f18d817e4e918f8372ba3c0a4ad8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950674"
---
# <a name="single-sign-on-saml-protocol"></a>Protocolo SAML de Início de Sessão Único

Este artigo abrange os pedidos e respostas de autenticação SAML 2.0 que o Azure Ative Directory (Azure AD) suporta para Sign-On Individuais (SSO).

O diagrama do protocolo abaixo descreve a sequência de inscrição única. O serviço de nuvem (o prestador de serviços) utiliza uma ligação HTTP Redirect para passar um `AuthnRequest` elemento (pedido de autenticação) ao Azure AD (o fornecedor de identidade). Azure AD utiliza então uma ligação de post HTTP para colocar um `Response` elemento no serviço de nuvem.

![Fluxo de trabalho de Sign-On único (SSO)](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

> [!NOTE]
> Este artigo discute a utilização de SAML para uma única súmis. Para obter mais informações sobre outras formas de lidar com um único sinal de acesso (por exemplo, utilizando o OpenID Connect ou a Autenticação Integrada do Windows), consulte [um único sinal de acesso às aplicações no Diretório Ativo Azure](../manage-apps/what-is-single-sign-on.md).

## <a name="authnrequest"></a>AuthnRequest

Para solicitar a autenticação de um utilizador, os serviços na nuvem enviam um `AuthnRequest` elemento para a Azure AD. Uma amostra SAML 2.0 `AuthnRequest` pode parecer o seguinte exemplo:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parâmetro | Tipo | Description |
| --- | --- | --- |
| ID | Necessário | A Azure AD utiliza este atributo para preencher o `InResponseTo` atributo da resposta devolvida. O ID não deve começar com um número, por isso uma estratégia comum é preparar uma corda como "id" para a representação de cordas de um GUID. Por exemplo, `id6c1c178c166d486687be4aaf5e482730` é um ID válido. |
| Versão | Necessário | Este parâmetro deve ser definido para **2.0**. |
| EmissãoInstant | Necessário | Esta é uma cadeia DateTime com um valor UTC e [formato de ida e volta ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings). A Azure AD espera um valor DateTime deste tipo, mas não avalia ou utiliza o valor. |
| AfirmaçãoConsumerServiceURL | Opcional | Se fornecido, este parâmetro deve coincidir com `RedirectUri` o serviço de nuvem em Azure AD. |
| ForçaAuthn | Opcional | Este é um valor booleano. Se for verdade, significa que o utilizador será forçado a reautensar, mesmo que tenha uma sessão válida com Azure AD. |
| IsPassive | Opcional | Este é um valor booleano que especifica se a Azure AD deve autenticar o utilizador silenciosamente, sem interação do utilizador, utilizando o cookie de sessão se existir. Se isso for verdade, o Azure AD tentará autenticar o utilizador utilizando o cookie de sessão. |

Todos os `AuthnRequest` outros atributos, tais como Consentimento, Destino, AfirmaçãoConsumerServiceIndex, AttributeConsumerServiceIndex e ProviderName são **ignorados.**

A Azure AD também ignora o `Conditions` elemento em `AuthnRequest` .

### <a name="issuer"></a>Emissor

O `Issuer` elemento num must corresponde `AuthnRequest` exatamente a um dos **nomes ServicePrincipalNames** no serviço de nuvem em Azure AD. Normalmente, isto é definido para o **ID URI da app** que é especificado durante o registo da aplicação.

Um excerto DE SAML contendo o `Issuer` elemento parece a seguinte amostra:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Este elemento solicita um formato de identificação de nome particular na resposta e é opcional em `AuthnRequest` elementos enviados para Azure AD.

Um `NameIdPolicy` elemento parece a seguinte amostra:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Se `NameIDPolicy` for fornecido, pode incluir o seu `Format` atributo opcional. O `Format` atributo pode ter apenas um dos seguintes valores; qualquer outro valor resulta num erro.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: O Azure Ative Directory emite a alegação NameID como identificador de pares.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Ative Directory emite a reclamação NameID no formato de endereço de e-mail.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Este valor permite ao Azure Ative Directory selecionar o formato de reclamação. O Azure Ative Directory emite o NameID como um identificador de pares.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: O Azure Ative Directory emite a reivindicação NameID como um valor gerado aleatoriamente que é exclusivo da operação SSO atual. Isto significa que o valor é temporário e não pode ser usado para identificar o utilizador autenticante.

Se `SPNameQualifier` for especificado, a Azure AD incluirá o mesmo `SPNameQualifier` na resposta.

AZure AD ignora o `AllowCreate` atributo.

### <a name="requestauthncontext"></a>PedidoAuthnContext
O `RequestedAuthnContext` elemento especifica os métodos de autenticação pretendidos. É opcional em `AuthnRequest` elementos enviados para a Azure AD. A Azure AD suporta `AuthnContextClassRef` valores como `urn:oasis:names:tc:SAML:2.0:ac:classes:Password` .

### <a name="scoping"></a>Escoar
O `Scoping` elemento, que inclui uma lista de fornecedores de identidade, é opcional em `AuthnRequest` elementos enviados à Azure AD.

Se fornecido, não inclua o `ProxyCount` atributo, `IDPListOption` ou `RequesterID` elemento, uma vez que não são suportados.

### <a name="signature"></a>Assinatura
Um `Signature` elemento em `AuthnRequest` elementos é opcional. A Azure AD não valida pedidos de autenticação assinados se estiver presente uma assinatura. A verificação do solicitador é prevista apenas respondendo aos URLs de Serviço de Consumo de Afirmação registados.

### <a name="subject"></a>Assunto
Não inclua um `Subject` elemento. A Azure AD não suporta especificar um assunto para um pedido e devolverá um erro se for fornecido.

## <a name="response"></a>Resposta
Quando um sinal de inscrição solicitado termina com sucesso, o Azure AD publica uma resposta ao serviço de nuvem. Uma resposta a uma tentativa de inscrição bem sucedida parece a seguinte amostra:

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

O `Response` elemento inclui o resultado do pedido de autorização. Azure AD define o `ID` , `Version` e `IssueInstant` valores no `Response` elemento. Também define os seguintes atributos:

* `Destination`: Quando a inscrição termina com sucesso, esta é definida para `RedirectUri` o prestador de serviços (serviço na nuvem).
* `InResponseTo`: Isto é definido para o `ID` atributo do elemento que `AuthnRequest` iniciou a resposta.

### <a name="issuer"></a>Emissor

A Azure AD define o `Issuer` elemento para onde está a `https://sts.windows.net/<TenantIDGUID>/` \<TenantIDGUID> identificação do inquilino do inquilino Azure AD.

Por exemplo, uma resposta com o elemento Emitente pode parecer a seguinte amostra:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Estado

O `Status` elemento transmite o sucesso ou falha da assinatura. Inclui o `StatusCode` elemento, que contém um código ou um conjunto de códigos aninhados que representam o estado do pedido. Também inclui o `StatusMessage` elemento, que contém mensagens de erro personalizadas que são geradas durante o processo de inscrição.

<!-- TODO: Add an authentication protocol error reference -->

A amostra a seguir é uma resposta SAML a uma tentativa de sinal-on fracassada.

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

Além do `ID` , e , `IssueInstant` `Version` AD AD define os seguintes elementos no `Assertion` elemento da resposta.

#### <a name="issuer"></a>Emissor

Este é definido para `https://sts.windows.net/<TenantIDGUID>/` onde \<TenantIDGUID> está a identificação do inquilino do inquilino Azure AD.

```
<Issuer>https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Assinatura

A Azure AD assina a afirmação em resposta a um sucesso de acesso. O `Signature` elemento contém uma assinatura digital que o serviço de nuvem pode usar para autenticar a fonte para verificar a integridade da afirmação.

Para gerar esta assinatura digital, a Azure AD utiliza a chave de assinatura no `IDPSSODescriptor` elemento do seu documento de metadados.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Assunto

Isto especifica o principal que é objeto das declarações na afirmação. Contém um `NameID` elemento que representa o utilizador autenticado. O `NameID` valor é um identificador direcionado que é dirigido apenas ao prestador de serviços que é o público para o token. É persistente - pode ser revogado, mas nunca é reatribuído. É também opaco, na medida em que não revela nada sobre o utilizador e não pode ser usado como identificador para consultas de atributos.

O `Method` atributo do elemento é sempre definido para `SubjectConfirmation` `urn:oasis:names:tc:SAML:2.0:cm:bearer` .

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Condições

Este elemento especifica as condições que definem a utilização aceitável de afirmações SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Os `NotBefore` `NotOnOrAfter` atributos e atributos especificam o intervalo durante o qual a afirmação é válida.

* O valor do `NotBefore` atributo é igual ou ligeiramente (menos de um segundo) mais tarde do que o valor do atributo do `IssueInstant` `Assertion` elemento. O Azure AD não explica qualquer diferença de tempo entre si e o serviço de cloud (prestador de serviços), e não adiciona nenhum tampão a este momento.
* O valor do `NotOnOrAfter` atributo é 70 minutos mais tarde do que o valor do `NotBefore` atributo.

#### <a name="audience"></a>Audiência

Isto contém um URI que identifica um público pretendido. A AD Azure define o valor deste elemento ao valor do `Issuer` elemento do `AuthnRequest` que iniciou a inscrição. Para avaliar o `Audience` valor, utilize o valor do que foi especificado durante o registo da `App ID URI` aplicação.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Tal como o `Issuer` valor, o `Audience` valor deve corresponder exatamente a um dos nomes principais do serviço que representa o serviço de nuvem em Azure AD. No entanto, se o valor do `Issuer` elemento não for um valor URI, o valor na resposta é o valor `Audience` `Issuer` pré-fixado com `spn:` .

#### <a name="attributestatement"></a>AtributoStatement

Isto contém reclamações sobre o assunto ou o utilizador. O seguinte excerto contém um elemento de `AttributeStatement` amostra. A elipse indica que o elemento pode incluir múltiplos atributos e valores de atributos.

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

* **Denominação -** O valor do `Name` atributo é o nome principal do utilizador do utilizador `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` autenticado, tal como `testuser@managedtenant.com` .
* **Reclamação do ObjectIdentifier** - O valor do `ObjectIdentifier` atributo é o do objeto de `http://schemas.microsoft.com/identity/claims/objectidentifier` `ObjectId` diretório que representa o utilizador autenticado em Azure AD. `ObjectId` é um identificador imutável, globalmente único e reutilizador seguro do utilizador autenticado.

#### <a name="authnstatement"></a>Declaração de Authn

Este elemento afirma que o sujeito de afirmação foi autenticado por um determinado meio num determinado momento.

* O `AuthnInstant` atributo especifica o momento em que o utilizador autenticado com Azure AD.
* O `AuthnContext` elemento especifica o contexto de autenticação utilizado para autenticar o utilizador.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```

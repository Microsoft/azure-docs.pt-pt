---
title: Início de sessão único do Azure no protocolo SAML | Documentos da Microsoft
description: Este artigo descreve o protocolo de início de sessão único no SAML no Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: d976a43173ce4f9deee0a723a895b40678e173b3
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437888"
---
# <a name="single-sign-on-saml-protocol"></a>Protocolo SAML de início de sessão único

Este artigo aborda os pedidos de autenticação SAML 2.0 e respostas que suporte o Azure Active Directory (Azure AD) para início de sessão único.

O diagrama de protocolo abaixo descreve a sequência de início de sessão única. O serviço em nuvem (o provedor de serviço) usa um enlace do redirecionamento de HTTP para passar uma `AuthnRequest` elemento (pedido de autenticação) para o Azure AD (o provedor de identidade). O Azure AD, em seguida, utiliza um HTTP post de enlace para postar um `Response` elemento ao serviço cloud.

![Início de sessão único fluxo de trabalho](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Para pedir uma autenticação de utilizador, serviços em nuvem no envio um `AuthnRequest` elemento com o Azure AD. Um exemplo de SAML 2.0 `AuthnRequest` pode ter um aspeto semelhante ao seguinte exemplo:

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
| ID | Necessário | Azure AD utiliza este atributo para preencher o `InResponseTo` atributo da resposta devolvida. ID não tem de começar com um número, portanto, uma estratégia comum é preceder uma cadeia de caracteres como "id" para a representação de cadeia de caracteres de um GUID. Por exemplo, `id6c1c178c166d486687be4aaf5e482730` é um ID válido. |
| Versão | Necessário | Este parâmetro deve ser definido como **2.0**. |
| IssueInstant | Necessário | Esta é uma cadeia de DateTime com um valor de UTC e [formato de ida e volta ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). O Azure AD espera um valor de DateTime deste tipo, mas não avaliar ou utilize o valor. |
| AssertionConsumerServiceUrl | Opcional | Se for fornecido, este parâmetro tem de corresponder a `RedirectUri` do serviço cloud no Azure AD. |
| ForceAuthn | Opcional | Este é um valor booleano. Se for VERDADEIRO, significa que o utilizador será forçado a autenticar novamente, mesmo que tenham uma sessão válida com o Azure AD. |
| IsPassive | Opcional | Este é um valor booleano que especifica se o Azure AD deve autenticar o utilizador silenciosamente, sem interação do utilizador, utilizando o cookie de sessão, se existir um. Se isso for VERDADEIRO, do Azure AD irá tentar autenticar o utilizador com o cookie de sessão. |

Todos os outros `AuthnRequest` atributos, como o consentimento, destino, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex ProviderName jsou **ignorado**.

O Azure AD também ignora a `Conditions` elemento no `AuthnRequest`.

### <a name="issuer"></a>Emissor

O `Issuer` elemento numa `AuthnRequest` deve corresponder exatamente um da **ServicePrincipalNames** no serviço cloud no Azure AD. Normalmente, esta definição estiver definida o **URI de ID de aplicação** que é especificado durante o registo de aplicação.

Um trecho SAML que contenha o `Issuer` elemento se parece com o exemplo a seguir:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Este elemento solicita um formato de ID de nome específico na resposta e é opcional no `AuthnRequest` elementos enviados para o Azure AD.

A `NameIdPolicy` elemento se parece com o exemplo a seguir:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Se `NameIDPolicy` for fornecido, pode incluir sua opcional `Format` atributo. O `Format` atributo pode ter apenas um dos seguintes valores; qualquer outro resultado de valor num erro.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: O Azure Active Directory emite a afirmação NameID como um identificador emparelhado.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: O Azure Active Directory emite a afirmação NameID no formato de endereço de email.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Este valor permite ao Azure Active Directory para selecionar o formato de afirmação. O Azure Active Directory emite o NameID como um identificador emparelhado.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: O Azure Active Directory emite a afirmação NameID como um valor gerado aleatoriamente, que é exclusivo para a operação atual do SSO. Isso significa que o valor é temporário e não pode ser utilizado para identificar o utilizador de autenticação.

O Azure AD ignora o `AllowCreate` atributo.

### <a name="requestauthncontext"></a>RequestAuthnContext
O `RequestedAuthnContext` elemento Especifica os métodos de autenticação pretendido. É opcional no `AuthnRequest` elementos enviados para o Azure AD. O Azure AD suporta apenas um `AuthnContextClassRef` valor: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Controlo de âmbito
O `Scoping` elemento, que inclui uma lista de fornecedores de identidade, é opcional no `AuthnRequest` elementos enviados para o Azure AD.

Se for fornecido, não incluem o `ProxyCount` atributo, `IDPListOption` ou `RequesterID` elemento, à medida que eles não são suportados.

### <a name="signature"></a>Assinatura
Não inclua uma `Signature` elemento no `AuthnRequest` elementos, como o Azure AD suporta assinados pedidos de autenticação.

### <a name="subject"></a>Requerente
O Azure AD ignora a `Subject` elemento de `AuthnRequest` elementos.

## <a name="response"></a>Resposta
Quando um pedido início de sessão for concluída com êxito, o Azure AD posta uma resposta para o serviço em nuvem. Uma resposta a uma tentativa de início de sessão bem-sucedido é semelhante ao exemplo seguinte:

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

O `Response` elemento inclui o resultado do pedido de autorização. Os conjuntos do Azure AD a `ID`, `Version` e `IssueInstant` valores no `Response` elemento. Ela também define os seguintes atributos:

* `Destination`: Quando o início de sessão for concluída com êxito, ele é definido como o `RedirectUri` do fornecedor de serviços (serviço em nuvem).
* `InResponseTo`: Esta definição estiver definida o `ID` atributo do `AuthnRequest` elemento que iniciou a resposta.

### <a name="issuer"></a>Emissor

Os conjuntos do Azure AD a `Issuer` elemento `https://login.microsoftonline.com/<TenantIDGUID>/` onde \<TenantIDGUID > é o ID de inquilino do inquilino do Azure AD.

Por exemplo, uma resposta com o elemento de emissor pode ter um aspeto semelhantes ao exemplo seguinte:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Estado

O `Status` elemento transmite o êxito ou falha de início de sessão. Ele inclui o `StatusCode` elemento, que contém um código ou um conjunto de códigos aninhados que representa o estado do pedido. Ele também inclui o `StatusMessage` elemento, que contém mensagens de erro personalizadas que são geradas durante o processo de início de sessão.

<!-- TODO: Add an authentication protocol error reference -->

O exemplo a seguir é uma resposta SAML para uma tentativa de início de sessão sem êxito.

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

### <a name="assertion"></a>asserção

Para além da `ID`, `IssueInstant` e `Version`, do Azure AD define os seguintes elementos no `Assertion` elemento da resposta.

#### <a name="issuer"></a>Emissor

Ele é definido como `https://sts.windows.net/<TenantIDGUID>/`onde \<TenantIDGUID > é o ID de inquilino do inquilino do Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Assinatura

O Azure AD assina a asserção em resposta a um início de sessão-com êxito. O `Signature` elemento contém uma assinatura digital que o serviço em nuvem pode utilizar para autenticar a origem para verificar a integridade da asserção.

Para gerar esta assinatura digital, o Azure AD utiliza a chave de assinatura no `IDPSSODescriptor` elemento do seu documento de metadados.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Requerente

Esta ação Especifica o principal que é o assunto das instruções na asserção. Contém um `NameID` elemento, que representa o usuário autenticado. O `NameID` valor é um identificador de destino que é direcionado apenas para o fornecedor de serviço que é o público-alvo para o token. Ele é persistente - ele pode ser revogado, mas nunca é reatribuído. Também são opaca, pois ele não expõe nada sobre o utilizador e não pode ser utilizado como um identificador para consultas de atributo.

O `Method` atributo do `SubjectConfirmation` elemento está sempre definido como `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Condições

Este elemento Especifica as condições que definem o uso aceitável de asserções SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

O `NotBefore` e `NotOnOrAfter` atributos especificam o intervalo durante o qual a asserção é válida.

* O valor do `NotBefore` atributo é igual a ou um pouco (menos de um segundo) mais tarde do que o valor de `IssueInstant` atributo do `Assertion` elemento. Azure AD não leva em conta para qualquer diferença de tempo entre ele e o serviço em nuvem (fornecedor de serviços) e não adiciona qualquer buffer neste momento.
* O valor do `NotOnOrAfter` atributo é 70 minutos mais tarde do que o valor da `NotBefore` atributo.

#### <a name="audience"></a>Audiência

Contém um URI que identifica um público-alvo. Azure AD define o valor deste elemento como o valor de `Issuer` elemento do `AuthnRequest` que iniciou o início de sessão. Para avaliar o `Audience` valor, utilize o valor da `App ID URI` que foi especificado durante o registo de aplicação.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Como o `Issuer` valor, o `Audience` valor deve corresponder exatamente um dos nomes de principal de serviço representa o serviço em nuvem no Azure AD. No entanto, se o valor do `Issuer` elemento não é um valor URI, o `Audience` valor na resposta é o `Issuer` com o valor de prefixo `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Ele contém declarações sobre o assunto ou o utilizador. O resumo a seguir contém um exemplo `AttributeStatement` elemento. O botão de reticências indica que o elemento pode incluir vários atributos e valores de atributo.

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

* **Nome de afirmação** -o valor da `Name` atributo (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) é o nome do principal de utilizador do utilizador autenticado, tal como `testuser@managedtenant.com`.
* **Afirmação ObjectIdentifier** -o valor da `ObjectIdentifier` atributo (`http://schemas.microsoft.com/identity/claims/objectidentifier`) é o `ObjectId` do objeto de diretório que representa o usuário autenticado no Azure AD. `ObjectId` é um imutável, globalmente exclusivo e reutilizar o identificador de segurança do usuário autenticado.

#### <a name="authnstatement"></a>AuthnStatement

Este elemento declara que o assunto de asserção foi autenticado por um meio específico num determinado momento.

* O `AuthnInstant` atributo especifica o tempo em que o utilizador autenticado com o Azure AD.
* O `AuthnContext` elemento Especifica o contexto de autenticação utilizado para autenticar o utilizador.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```

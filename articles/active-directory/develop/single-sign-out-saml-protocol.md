---
title: Protocolo SAML de assinatura única azure
description: Este artigo descreve o Protocolo SAML de Inscrição Única no Diretório Ativo Azure
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: dbe21d020d5d01f24913b95587721403fa218cc8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881271"
---
# <a name="single-sign-out-saml-protocol"></a>Protocolo SAML de inscrição única

O Azure Ative Directory (Azure AD) suporta o perfil único de inscrição do navegador Web SAML 2.0. Para que o registo único funcione corretamente, o **LogoutURL** para a aplicação deve ser explicitamente registado com a AD Azure durante o registo da aplicação. O Azure AD utiliza o LogoutURL para redirecionar os utilizadores depois de assinado.

O diagrama seguinte mostra o fluxo de trabalho do processo de inscrição único da AD Azure.

![Fluxo de trabalho de sinal único azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>Pedido de Logout
O serviço de `LogoutRequest` nuvem envia uma mensagem à AD Azure para indicar que uma sessão foi encerrada. O seguinte excerto `LogoutRequest` mostra um elemento de amostra.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>Pedido de Logout
O `LogoutRequest` elemento enviado para a AD Azure requer os seguintes atributos:

* `ID`Isto identifica o pedido de inscrição. O valor `ID` não deve começar com um número. A prática típica é anexar a **identificação** à representação de cordas de um GUID.
* `Version`- Desajuste o valor deste elemento para **2.0**. Este valor é preciso.
* `IssueInstant`- Trata-se de uma `DateTime` cadeia com um valor de Tempo Universal (UTC) e [formato de ida e volta ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). A Azure AD espera um valor deste tipo, mas não o aplica.

### <a name="issuer"></a>Emissor
O `Issuer` elemento `LogoutRequest` num must exatamente coincidir com um dos **ServicePrincipaNames** no serviço de nuvem em Azure AD. Normalmente, isto é definido para o **ID da aplicação URI** que é especificado durante o registo da aplicação.

### <a name="nameid"></a>Id nome
O valor `NameID` do elemento deve `NameID` corresponder exatamente ao do utilizador que está a ser assinado.

## <a name="logoutresponse"></a>Resposta de logout
A AD Azure envia `LogoutResponse` `LogoutRequest` uma resposta a um elemento. O seguinte excerto `LogoutResponse`mostra uma amostra .

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>Resposta de logout
A AD Azure `ID` `Version` define `IssueInstant` os `LogoutResponse` valores e valores no elemento. Também define o `InResponseTo` elemento ao valor `ID` do atributo do `LogoutRequest` que provocou a resposta.

### <a name="issuer"></a>Emissor
A Azure AD define `https://login.microsoftonline.com/<TenantIdGUID>/` \<este valor para onde o TenantIdGUID> é o iD inquilino do inquilino Azure AD.

Para avaliar o `Issuer` valor do elemento, utilize o valor do **ID da aplicação URI** fornecido durante o registo da candidatura.

### <a name="status"></a>Estado
A AD Azure utiliza o `StatusCode` elemento no `Status` elemento para indicar o sucesso ou falha de inscrição. Quando a tentativa de inscrição `StatusCode` falha, o elemento também pode conter mensagens de erro personalizadas.

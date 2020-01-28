---
title: Azure Single Sign out SAML Protocol  Microsoft Docs
description: Este artigo descreve o Protocolo SAML de Inscrição Única no Diretório Ativo Azure
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
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
ms.openlocfilehash: 95d3deff73ce357f012b15a7fc1cfa3decdb4bda
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701370"
---
# <a name="single-sign-out-saml-protocol"></a>Protocolo SAML de inscrição única

O Azure Ative Directory (Azure AD) suporta o perfil único de inscrição do navegador Web SAML 2.0. Para que o registo único funcione corretamente, o **LogoutURL** para a aplicação deve ser explicitamente registado com a AD Azure durante o registo da aplicação. O Azure AD utiliza o LogoutURL para redirecionar os utilizadores depois de assinado.

O diagrama seguinte mostra o fluxo de trabalho do processo de inscrição único da AD Azure.

![Fluxo de trabalho de sinal único azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>Pedido de Logout
O serviço de nuvem envia uma mensagem `LogoutRequest` ao Azure AD para indicar que uma sessão foi encerrada. O seguinte excerto mostra uma amostra `LogoutRequest` elemento.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>Pedido de Logout
O elemento `LogoutRequest` enviado para a AD Azure requer os seguintes atributos:

* `ID` - Isto identifica o pedido de inscrição. O valor da `ID` não deve começar com um número. A prática típica é anexar a **identificação** à representação de cordas de um GUID.
* `Version` - Desloque o valor deste elemento para **2.0**. Este valor é preciso.
* `IssueInstant` - Esta é uma cadeia `DateTime` com um valor de Tempo Universal (UTC) e [formato de ida e volta ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). A Azure AD espera um valor deste tipo, mas não o aplica.

### <a name="issuer"></a>Emissor
O elemento `Issuer` num `LogoutRequest` deve corresponder exatamente a um dos **ServicePrincipaNames** no serviço de cloud em Azure AD. Normalmente, isto é definido para o **ID da aplicação URI** que é especificado durante o registo da aplicação.

### <a name="nameid"></a>NameID
O valor do elemento `NameID` deve corresponder exatamente à `NameID` do utilizador que está a ser assinado.

## <a name="logoutresponse"></a>Resposta de logout
A Azure AD envia uma `LogoutResponse` em resposta a um elemento `LogoutRequest`. O seguinte excerto mostra uma amostra `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>Resposta de logout
A AD Azure define os valores `ID`, `Version` e `IssueInstant` no elemento `LogoutResponse`. Também define o elemento `InResponseTo` ao valor do atributo `ID` do `LogoutRequest` que provocou a resposta.

### <a name="issuer"></a>Emissor
A Azure AD define este valor para `https://login.microsoftonline.com/<TenantIdGUID>/` onde \<TenantIdGUID> é a identificação do inquilino do inquilino azure AD.

Para avaliar o valor do elemento `Issuer`, utilize o valor do **ID da aplicação URI** fornecido durante o registo da candidatura.

### <a name="status"></a>Estado
A AD Azure utiliza o elemento `StatusCode` no elemento `Status` para indicar o sucesso ou falha de inscrição. Quando a tentativa de inscrição falha, o elemento `StatusCode` também pode conter mensagens de erro personalizadas.

---
title: Azure Single Sign out Protocolo SAML
description: Este artigo descreve o Protocolo SAML de Sign-Out Único no Diretório Ativo do Azure
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 2064ab7e759798d8934facb8d293e8ac60ec6c82
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/20/2020
ms.locfileid: "97703420"
---
# <a name="single-sign-out-saml-protocol"></a>Protocolo SAML de Sign-Out Única

O Azure Ative Directory (Azure AD) suporta o perfil de sessão de assinatura único do navegador SAML 2.0. Para que a súmis funcione corretamente, o **LogoutURL** para o pedido deve ser explicitamente registado no Azure AD durante o registo da aplicação. O Azure AD utiliza o LogoutURL para redirecionar os utilizadores depois de terem sido assinados.

A azure AD suporta a ligação de redirecionamento (HTTP GET) e não a ligação HTTP POST.

O diagrama seguinte mostra o fluxo de trabalho do processo de assinatura único Azure AD.

![Azure Ad único sinal fora fluxo de trabalho](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
O serviço de nuvem envia uma `LogoutRequest` mensagem para a Azure AD para indicar que uma sessão foi terminada. O seguinte excerto mostra um elemento de `LogoutRequest` amostra.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
O `LogoutRequest` elemento enviado para a Azure AD requer os seguintes atributos:

* `ID` - Isto identifica o pedido de inscrição. O valor de `ID` não deve começar com um número. A prática típica é anexar o **id** à representação de cordas de um GUID.
* `Version` - Desa couuser o valor deste elemento a **2.0**. Este valor é preciso.
* `IssueInstant` - Trata-se de uma `DateTime` cadeia com um valor de Tempo Universal coordenado (UTC) e formato de ida e volta [("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings). A AZure AD espera um valor deste tipo, mas não o aplica.

### <a name="issuer"></a>Emissor
O `Issuer` elemento num must corresponde `LogoutRequest` exatamente a um dos **nomes ServicePrincipalNames** no serviço de nuvem em Azure AD. Normalmente, isto é definido para o **ID URI da app** que é especificado durante o registo da aplicação.

### <a name="nameid"></a>NameID
O valor do `NameID` elemento deve corresponder exatamente ao do utilizador `NameID` que está a ser assinado.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD envia uma `LogoutResponse` resposta a um `LogoutRequest` elemento. O seguinte excerto mostra uma `LogoutResponse` amostra.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD define o `ID` , `Version` e `IssueInstant` valores no `LogoutResponse` elemento. Também define o `InResponseTo` elemento ao valor do atributo do que provocou a `ID` `LogoutRequest` resposta.

### <a name="issuer"></a>Emissor
A Azure AD define este valor `https://login.microsoftonline.com/<TenantIdGUID>/` para onde está a \<TenantIdGUID> identificação do inquilino do inquilino Azure AD.

Para avaliar o valor do `Issuer` elemento, utilize o valor da **App ID URI** fornecida durante o registo da aplicação.

### <a name="status"></a>Estado
A Azure AD utiliza o `StatusCode` elemento no elemento para indicar o sucesso ou falha da `Status` sedência. Quando a tentativa de inscrição falhar, o `StatusCode` elemento também pode conter mensagens de erro personalizadas.

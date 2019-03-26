---
title: O protocolo SAML de sessão único do Azure | Documentos da Microsoft
description: Este artigo descreve o protocolo SAML de fim de sessão único no Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
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
ms.openlocfilehash: 0d4f9c69d6ddcbef96a88a0df10c3b0974cd5b74
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437089"
---
# <a name="single-sign-out-saml-protocol"></a>Protocolo SAML de fim de sessão único

Perfil de fim de sessão único de navegador da web do Azure suporta do Active Directory (Azure AD) o SAML 2.0. Para single sign-out funcione corretamente, o **LogoutURL** para a aplicação tem de ser explicitamente registrada com o Azure AD durante o registo de aplicação. Azure AD utiliza o LogoutURL para redirecionar os utilizadores depois que terminar a sessão.

O diagrama seguinte mostra o fluxo de trabalho do processo de fim de sessão único do Azure AD.

![Fim de sessão do Azure AD único fluxo de trabalho](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
O envia de serviço de nuvem um `LogoutRequest` mensagem para o Azure AD para indicar que uma sessão foi terminada. O resumo a seguir mostra um exemplo `LogoutRequest` elemento.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
O `LogoutRequest` elemento enviado para o Azure AD requer os seguintes atributos:

* `ID` -Esta identifica a solicitação a fim de sessão. O valor de `ID` não deve começar com um número. A prática típica é anexar **id** para a representação de cadeia de caracteres de um GUID.
* `Version` -Definir o valor deste elemento para **2.0**. Este valor é preciso.
* `IssueInstant` -Essa é uma `DateTime` cadeias de caracteres com um valor de hora Universal Coordenada (UTC) e [formato de ida e volta ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). O Azure AD espera um valor deste tipo, mas não impõe-lo.

### <a name="issuer"></a>Emissor
O `Issuer` elemento numa `LogoutRequest` deve corresponder exatamente um da **ServicePrincipalNames** no serviço cloud no Azure AD. Normalmente, esta definição estiver definida o **URI de ID de aplicação** que é especificado durante o registo de aplicação.

### <a name="nameid"></a>NameID
O valor do `NameID` elemento deve corresponder exatamente a `NameID` do utilizador que está a ser terminado.

## <a name="logoutresponse"></a>LogoutResponse
O Azure AD envia um `LogoutResponse` em resposta a um `LogoutRequest` elemento. O resumo a seguir mostra um exemplo `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Os conjuntos do Azure AD a `ID`, `Version` e `IssueInstant` valores no `LogoutResponse` elemento. Também define a `InResponseTo` elemento para o valor da `ID` atributo do `LogoutRequest` elicited que a resposta.

### <a name="issuer"></a>Emissor
O Azure AD define este valor como `https://login.microsoftonline.com/<TenantIdGUID>/` onde \<TenantIdGUID > é o ID de inquilino do inquilino do Azure AD.

Para avaliar o valor dos `Issuer` elemento, utilize o valor da **URI de ID de aplicação** fornecido durante o registo de aplicação.

### <a name="status"></a>Estado
O Azure AD utiliza a `StatusCode` elemento no `Status` elemento para indicar o sucesso ou fracasso de fim de sessão. Quando a tentativa de fim de sessão falhar, o `StatusCode` elemento também pode conter mensagens de erro personalizadas.

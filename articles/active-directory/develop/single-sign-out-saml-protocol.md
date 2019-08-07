---
title: Protocolo SAML de saída única do Azure | Microsoft Docs
description: Este artigo descreve o protocolo SAML de logout único no Azure Active Directory
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66c509b1b901889241d6837611a2c373750fdb3a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834786"
---
# <a name="single-sign-out-saml-protocol"></a>Protocolo SAML de logout único

Azure Active Directory (AD do Azure) dá suporte ao perfil de saída único do navegador da Web do SAML 2,0. Para que o logout único funcione corretamente, o **LogoutURL** para o aplicativo deve ser explicitamente registrado no Azure ad durante o registro do aplicativo. O Azure AD usa o LogoutURL para redirecionar os usuários depois que eles são desconectados.

O diagrama a seguir mostra o fluxo de trabalho do processo de saída única do Azure AD.

![Fluxo de trabalho de logoff único do Azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
O serviço de nuvem envia `LogoutRequest` uma mensagem para o Azure ad para indicar que uma sessão foi encerrada. O trecho a seguir mostra um `LogoutRequest` exemplo de elemento.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
O `LogoutRequest` elemento enviado ao Azure ad requer os seguintes atributos:

* `ID`-Identifica a solicitação de saída. O valor de `ID` não deve começar com um número. A prática típica é acrescentar a **ID** à representação de cadeia de caracteres de um GUID.
* `Version`-Defina o valor desse elemento como **2,0**. Este valor é obrigatório.
* `IssueInstant`-Esta é uma `DateTime` cadeia de caracteres com um valor de UTC (tempo universal de coordenada) e o formato de viagem de ida e [volta ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). O Azure AD espera um valor desse tipo, mas não o impõe.

### <a name="issuer"></a>Emissor
O `Issuer` elemento em um `LogoutRequest` deve corresponder exatamente a um dos **servicePrincipalName** no serviço de nuvem no Azure AD. Normalmente, isso é definido como o **URI da ID do aplicativo** que é especificado durante o registro do aplicativo.

### <a name="nameid"></a>NameID
O valor do `NameID` elemento deve corresponder exatamente ao `NameID` do usuário que está sendo desconectado.

## <a name="logoutresponse"></a>LogoutResponse
O Azure ad envia `LogoutResponse` um em resposta a `LogoutRequest` um elemento. O trecho a seguir mostra um `LogoutResponse`exemplo.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
O Azure ad define `ID`os `Version` valores `IssueInstant` e no `LogoutResponse` elemento. Ele também define o `InResponseTo` elemento como o valor `ID` do atributo do `LogoutRequest` que exteveva a resposta.

### <a name="issuer"></a>Emissor
O Azure ad define esse valor `https://login.microsoftonline.com/<TenantIdGUID>/` como \<onde TenantIdGUID > é a ID de locatário do locatário do Azure AD.

Para avaliar o valor do `Issuer` elemento, use o valor do URI da **ID do aplicativo** fornecido durante o registro do aplicativo.

### <a name="status"></a>Estado
O Azure ad usa `StatusCode` o elemento `Status` no elemento para indicar o êxito ou a falha da saída. Quando a tentativa de saída falha, o `StatusCode` elemento também pode conter mensagens de erro personalizadas.

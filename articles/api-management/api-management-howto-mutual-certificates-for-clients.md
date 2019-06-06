---
title: Proteger APIs com o cliente autenticação de certificado na gestão de API - API Management do Azure | Documentos da Microsoft
description: Saiba como proteger o acesso a APIs utilizando certificados de cliente
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 5427c4050b6b70c18da7a1899d16e448c41e81c6
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427344"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Como proteger APIs com o cliente de autenticação de certificado na gestão de API

Gestão de API fornece a capacidade de proteger o acesso às APIs (ou seja, o cliente para gestão de API) usando certificados de cliente. Pode validar o certificado de entrada e verificar as propriedades do certificado em relação aos valores pretendidos utilizando expressões de política.

Para obter informações sobre como proteger o acesso ao serviço de back-end de uma API de utilização de certificados de cliente (ou seja, gestão de API para o back-end), consulte [como proteger os serviços de back-end com o cliente de autenticação de certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Para receber e verificar os certificados de cliente na camada de consumo tem primeiro de ativar "Pedido de certificado de cliente" definição no painel "Domínios personalizados", conforme mostrado abaixo.

![Pedido de certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>A verificar o emissor e o assunto

Abaixo as políticas podem ser configuradas para verificar o emissor e o assunto de um certificado de cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para desativar a verificação uso de lista de revogação de certificado `context.Request.Certificate.VerifyNoRevocation()` em vez de `context.Request.Certificate.Verify()`.
> Se o certificado de cliente autoassinado, de raiz (ou intermediária) certificado (s) de AC tem de ser [carregado](api-management-howto-ca-certificates.md) à gestão de API para `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` para trabalhar.

## <a name="checking-the-thumbprint"></a>A verificar o thumbprint

Abaixo as políticas podem ser configuradas para verificar o thumbprint de um certificado de cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para desativar a verificação uso de lista de revogação de certificado `context.Request.Certificate.VerifyNoRevocation()` em vez de `context.Request.Certificate.Verify()`.
> Se o certificado de cliente autoassinado, de raiz (ou intermediária) certificado (s) de AC tem de ser [carregado](api-management-howto-ca-certificates.md) à gestão de API para `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` para trabalhar.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>A verificação de um thumbprint contra certificados carregados para a gestão de API

O exemplo seguinte mostra como verificar o thumbprint de um certificado de cliente em relação a certificados carregados para a gestão de API:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Para desativar a verificação uso de lista de revogação de certificado `context.Request.Certificate.VerifyNoRevocation()` em vez de `context.Request.Certificate.Verify()`.
> Se o certificado de cliente autoassinado, de raiz (ou intermediária) certificado (s) de AC tem de ser [carregado](api-management-howto-ca-certificates.md) à gestão de API para `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` para trabalhar.

> [!TIP]
> Problema de deadlock de certificado de cliente descrito neste [artigo](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) pode se manifestar de várias maneiras, por exemplo, pedidos congelar, resultado de pedidos `403 Forbidden` código de estado após o tempo limite, `context.Request.Certificate` é `null`. Este problema ocorrer normalmente `POST` e `PUT` pedidos com o comprimento do conteúdo de cerca de 60 KB ou superior.
> Para evitar este problema de passar ativar definição de "Certificado de cliente Negotiate" para os nomes de anfitrião desejado no painel "Domínios personalizados", conforme mostrado abaixo. Esta funcionalidade não está disponível na camada de consumo.

![Negociação de certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Passos Seguintes

-   [Como proteger os serviços de back-end com o cliente de autenticação de certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Como carregar certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

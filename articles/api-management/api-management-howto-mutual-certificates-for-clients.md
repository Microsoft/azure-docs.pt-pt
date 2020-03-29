---
title: APIs seguros utilizando autenticação de certificado de cliente na Gestão API
titleSuffix: Azure API Management
description: Saiba como garantir o acesso às APIs usando certificados de cliente
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76713144"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Como proteger APIs com a autenticação de certificado de cliente na Gestão de API

A API Management fornece a capacidade de garantir o acesso às APIs (ou seja, cliente da API Management) utilizando certificados de cliente. Pode validar o certificado de entrada e verificar as propriedades dos certificados contra os valores desejados utilizando expressões de política.

Para obter informações sobre o acesso ao serviço back-end de uma API utilizando certificados de cliente (isto é, Gestão API para backend), consulte [Como garantir serviços back-end usando a autenticação](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) de certificado de cliente

> [!IMPORTANT]
> Para receber e verificar os certificados de cliente sobre http/2 nos níveis de Desenvolvimento, Básico, Standard ou Premium, deve ativar a definição de "Negociar o certificado de cliente" na lâmina "Domínios Personalizados", como mostrado abaixo.

![Negociar certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Para receber e verificar os certificados de cliente no nível de Consumo, deve ativar a definição de "Solicitar certificado de cliente" na lâmina "Domínios Personalizados", como mostrado abaixo.

![Solicitar certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Verificação do emitente e do sujeito

As políticas abaixo podem ser configuradas para verificar o emitente e o sujeito de um certificado de cliente:

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
> Para desativar a `context.Request.Certificate.VerifyNoRevocation()` utilização `context.Request.Certificate.Verify()`da lista de revogação do certificado em vez de .
> Se o certificado de cliente for auto-assinado, os certificados ca(ou intermédios) devem ser [enviados](api-management-howto-ca-certificates.md) para a API Management para `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` para funcionar.

## <a name="checking-the-thumbprint"></a>Verificando a impressão digital

As políticas abaixo podem ser configuradas para verificar a impressão digital de um certificado de cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para desativar a `context.Request.Certificate.VerifyNoRevocation()` utilização `context.Request.Certificate.Verify()`da lista de revogação do certificado em vez de .
> Se o certificado de cliente for auto-assinado, os certificados ca(ou intermédios) devem ser [enviados](api-management-howto-ca-certificates.md) para a API Management para `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` para funcionar.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Verificação de uma impressão digital digital com certificados enviados para a API Management

O exemplo que se segue mostra como verificar a impressão digital de um certificado de cliente em relação aos certificados enviados para a API Management:

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
> Para desativar a `context.Request.Certificate.VerifyNoRevocation()` utilização `context.Request.Certificate.Verify()`da lista de revogação do certificado em vez de .
> Se o certificado de cliente for auto-assinado, os certificados ca(ou intermédios) devem ser [enviados](api-management-howto-ca-certificates.md) para a API Management para `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` para funcionar.

> [!TIP]
> A questão do impasse do certificado de cliente descrita neste [artigo](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) pode manifestar-se `403 Forbidden` de várias `context.Request.Certificate` formas, por exemplo, pedidos de congelamento, pedidos resultam em código de estado após o tempo de saída, é `null`. Este problema geralmente afeta `POST` e `PUT` solicita com comprimento de conteúdo de aproximadamente 60KB ou maior.
> Para evitar que este problema ocorra, ligue a definição "Negociar o certificado de cliente" para os nomes de anfitriões desejados na lâmina "Domínios Personalizados", como mostrado abaixo. Esta funcionalidade não está disponível no nível de Consumo.

![Negociar certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Passos seguintes

-   [Como garantir serviços back-end usando a autenticação de certificado de cliente](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Como carregar certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

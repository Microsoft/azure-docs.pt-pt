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
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: c9d453be0aeb07bf0d78ee4c043fbb6202c225f2
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520467"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Como proteger APIs com o cliente de autenticação de certificado na gestão de API

Gestão de API fornece a capacidade de proteger o acesso às APIs (ou seja, o cliente para gestão de API) usando certificados de cliente. Atualmente, pode verificar o thumbprint de um certificado de cliente em relação a um valor pretendido. Também pode verificar o thumbprint contra certificados existentes carregado para a gestão de API.  

Para obter informações sobre como proteger o acesso ao serviço de back-end de uma API de utilização de certificados de cliente (ou seja, gestão de API para back-end), consulte [como proteger os serviços de back-end com o cliente de autenticação de certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="checking-the-expiration-date"></a>A verificar a data de expiração

Abaixo as políticas podem ser configuradas para verificar se o certificado está expirado:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>A verificar o emissor e o assunto

Abaixo as políticas podem ser configuradas para verificar o emissor e o assunto de um certificado de cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != 'trusted-issuer' || context.Request.Certificate.SubjectName != 'expected-subject-name')" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>A verificar o thumbprint

Abaixo as políticas podem ser configuradas para verificar o thumbprint de um certificado de cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != 'desired-thumbprint')" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>A verificação de um thumbprint contra certificados carregados para a gestão de API

O exemplo seguinte mostra como verificar o thumbprint de um certificado de cliente em relação a certificados carregados para a gestão de API: 

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Passo seguinte

*  [Como proteger os serviços de back-end com o cliente de autenticação de certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [Como carregar certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)


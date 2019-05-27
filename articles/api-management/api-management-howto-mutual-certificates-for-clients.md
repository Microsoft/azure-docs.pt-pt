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
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: ac9910358cf19eac3f704f1bf3e259e9a1543dcc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66141524"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Como proteger APIs com o cliente de autenticação de certificado na gestão de API

Gestão de API fornece a capacidade de proteger o acesso às APIs (ou seja, o cliente para gestão de API) usando certificados de cliente. Atualmente, pode verificar o thumbprint de um certificado de cliente em relação a um valor pretendido. Também pode verificar o thumbprint contra certificados existentes carregado para a gestão de API.  

Para obter informações sobre como proteger o acesso ao serviço de back-end de uma API de utilização de certificados de cliente (ou seja, gestão de API para back-end), consulte [como proteger os serviços de back-end com o cliente de autenticação de certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

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
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
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
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
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


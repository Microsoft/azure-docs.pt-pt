---
title: Proteger APIs usando a autenticação de certificado do cliente no gerenciamento de API – gerenciamento de API do Azure | Microsoft Docs
description: Saiba como proteger o acesso a APIs usando certificados de cliente
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 263f8495b9dbb0a1c5b3c54301b4b4deab425e31
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072366"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Como proteger APIs usando a autenticação de certificado de cliente no gerenciamento de API

O gerenciamento de API fornece a capacidade de proteger o acesso a APIs (ou seja, cliente para gerenciamento de API) usando certificados de cliente. Você pode validar o certificado de entrada e verificar as propriedades do certificado em relação aos valores desejados usando expressões de política.

Para obter informações sobre como proteger o acesso ao serviço de back-end de uma API usando certificados de cliente (ou seja, gerenciamento de API para back-end), consulte [como proteger serviços de back-end usando autenticação de certificado de cliente](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Para receber e verificar os certificados do cliente no nível de consumo, primeiro você deve ativar a configuração "solicitar certificado do cliente" na folha "domínios personalizados", conforme mostrado abaixo.

![Solicitar certificado do cliente](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Verificando o emissor e o assunto

As políticas abaixo podem ser configuradas para verificar o emissor e o assunto de um certificado de cliente:

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
> Para desabilitar a verificação do uso `context.Request.Certificate.VerifyNoRevocation()` da lista de certificados revogados em vez de. `context.Request.Certificate.Verify()`
> Se o certificado do cliente for autoassinado, os certificados de autoridade de certificação raiz (ou intermediário) deverão ser [carregados](api-management-howto-ca-certificates.md) no gerenciamento `context.Request.Certificate.Verify()` de `context.Request.Certificate.VerifyNoRevocation()` API para o e para funcionar.

## <a name="checking-the-thumbprint"></a>Verificando a impressão digital

As políticas abaixo podem ser configuradas para verificar a impressão digital de um certificado de cliente:

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
> Para desabilitar a verificação do uso `context.Request.Certificate.VerifyNoRevocation()` da lista de certificados revogados em vez de. `context.Request.Certificate.Verify()`
> Se o certificado do cliente for autoassinado, os certificados de autoridade de certificação raiz (ou intermediário) deverão ser [carregados](api-management-howto-ca-certificates.md) no gerenciamento `context.Request.Certificate.Verify()` de `context.Request.Certificate.VerifyNoRevocation()` API para o e para funcionar.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Verificando uma impressão digital em relação aos certificados carregados no gerenciamento de API

O exemplo a seguir mostra como verificar a impressão digital de um certificado de cliente em relação a certificados carregados para o gerenciamento de API:

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
> Para desabilitar a verificação do uso `context.Request.Certificate.VerifyNoRevocation()` da lista de certificados revogados em vez de. `context.Request.Certificate.Verify()`
> Se o certificado do cliente for autoassinado, os certificados de autoridade de certificação raiz (ou intermediário) deverão ser [carregados](api-management-howto-ca-certificates.md) no gerenciamento `context.Request.Certificate.Verify()` de `context.Request.Certificate.VerifyNoRevocation()` API para o e para funcionar.

> [!TIP]
> O problema de deadlock de certificado de cliente descrito neste [artigo](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) pode se manifestar de várias maneiras, por exemplo, as `403 Forbidden` solicitações Freeze, as solicitações resultam `null`em código de status `context.Request.Certificate` após o tempo limite é. Esse problema geralmente afeta `POST` e `PUT` solicita o tamanho do conteúdo de aproximadamente 60KB ou maior.
> Para evitar que esse problema ocorra, ative a configuração "negociar certificado de cliente" para os nomes de host desejados na folha "domínios personalizados", conforme mostrado abaixo. Este recurso não está disponível na camada de consumo.

![Negociar certificado do cliente](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Passos seguintes

-   [Como proteger serviços de back-end usando a autenticação de certificado do cliente](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Como carregar certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

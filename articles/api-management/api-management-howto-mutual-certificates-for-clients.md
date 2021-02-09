---
title: APIs seguros usando autenticação de certificado de cliente na Gestão da API
titleSuffix: Azure API Management
description: Saiba como garantir o acesso às APIs utilizando certificados de cliente. Pode utilizar expressões de política para validar certificados de entrada.
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
ms.openlocfilehash: 553b4527796db3e5d0f430afd6c5e614626187e5
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988889"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Como proteger APIs com a autenticação de certificado de cliente na Gestão de API

A API Management fornece a capacidade de garantir o acesso às APIs (ou seja, cliente da API Management) utilizando certificados de cliente. Pode validar o certificado de entrada e verificar as propriedades dos certificados com os valores pretendidos utilizando expressões de política.

Para obter informações sobre a garantia do acesso ao serviço back-end de uma API utilizando certificados de cliente (isto é, API Management to backend), consulte [Como garantir serviços de back-end utilizando a autenticação de certificados de cliente](./api-management-howto-mutual-certificates.md)

> [!IMPORTANT]
> Para receber e verificar os certificados de cliente sobre HTTP/2 nos níveis de Desenvolvedor, Básico, Standard ou Premium, deve ligar a definição de "Certificado de cliente negociar" na lâmina "domínios personalizados", conforme mostrado abaixo.

![Negociar certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Para receber e verificar os certificados de cliente no nível de Consumo, deve ligar a definição de "Certificado cliente pedido" na lâmina "Domínios personalizados", conforme mostrado abaixo.

![Requerer certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Verificação do emitente e do sujeito

Abaixo as políticas podem ser configuradas para verificar o emitente e o sujeito de um certificado de cliente:

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
> Para desativar a utilização da lista de revogação do certificado `context.Request.Certificate.VerifyNoRevocation()` de verificação em vez de `context.Request.Certificate.Verify()` .
> Se o certificado de cliente for auto-assinado, os certificados de CA de raiz (ou intermédios) devem ser [enviados](api-management-howto-ca-certificates.md) para a API Management para `context.Request.Certificate.Verify()` e para o `context.Request.Certificate.VerifyNoRevocation()` trabalho.

## <a name="checking-the-thumbprint"></a>Verificando a impressão digital

Abaixo as políticas podem ser configuradas para verificar a impressão digital de um certificado de cliente:

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
> Para desativar a utilização da lista de revogação do certificado `context.Request.Certificate.VerifyNoRevocation()` de verificação em vez de `context.Request.Certificate.Verify()` .
> Se o certificado de cliente for auto-assinado, os certificados de CA de raiz (ou intermédios) devem ser [enviados](api-management-howto-ca-certificates.md) para a API Management para `context.Request.Certificate.Verify()` e para o `context.Request.Certificate.VerifyNoRevocation()` trabalho.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Verificação de uma impressão digital em polegar contra certificados enviados para a API Management

O exemplo a seguir mostra como verificar a impressão digital de um certificado de cliente contra certificados enviados para a API Management:

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
> Para desativar a utilização da lista de revogação do certificado `context.Request.Certificate.VerifyNoRevocation()` de verificação em vez de `context.Request.Certificate.Verify()` .
> Se o certificado de cliente for auto-assinado, os certificados de CA de raiz (ou intermédios) devem ser [enviados](api-management-howto-ca-certificates.md) para a API Management para `context.Request.Certificate.Verify()` e para o `context.Request.Certificate.VerifyNoRevocation()` trabalho.

> [!TIP]
> A emissão de impasse do certificado do cliente descrita neste [artigo](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) pode manifestar-se de várias formas, por exemplo, os pedidos de congelamento, os pedidos resultam em `403 Forbidden` código de estado após o tempo de saída, é `context.Request.Certificate` `null` . Este problema geralmente afeta `POST` e solicita com comprimento de conteúdo de `PUT` aproximadamente 60KB ou maior.
> Para evitar que este problema ocorra, ligue a definição de "Negociar o certificado do cliente" para os nomes de anfitriões pretendidos na lâmina "Domínios personalizados", como mostra a primeira imagem deste documento. Esta funcionalidade não está disponível no nível de Consumo.

## <a name="certificate-validation-in-self-hosted-gateway"></a>Validação de certificados em gateway auto-hospedado

A imagem [de gateway auto-hospedada](self-hosted-gateway-overview.md) da API Management não suporta a validação de certificados de servidor e cliente usando [certificados de raiz ca enviados](api-management-howto-ca-certificates.md) para uma instância de Gestão da API. Os clientes que apresentem um certificado personalizado para o gateway auto-hospedado podem experimentar respostas lentas, porque a validação da lista de revogação de certificados (CRL) pode demorar muito tempo a sair no gateway. 

Como solução alternativa ao executar o gateway, pode configurar o endereço IP PKI para indicar o endereço local (127.0.0.1) em vez da instância de Gestão da API. Isto faz com que a validação crl falhe rapidamente quando o gateway tenta validar o certificado do cliente. Para configurar o gateway, adicione uma entrada DNS para a instância de Gestão da API para resolver o local no `/etc/hosts` ficheiro no recipiente. Pode adicionar esta entrada durante a implementação do gateway:
 
* Para a colocação do Docker - adicione o `--add-host <hostname>:127.0.0.1` parâmetro ao `docker run` comando. Para obter mais informações, consulte [Adicionar entradas ao ficheiro de anfitriões de contentores](https://docs.docker.com/engine/reference/commandline/run/#add-entries-to-container-hosts-file---add-host)
 
* Para a implementação de Kubernetes - Adicione uma `hostAliases` especificação ao `myGateway.yaml` ficheiro de configuração. Para obter mais informações, consulte [adicionar entradas ao Pod /etc/hosts com Aliases hospedeiros.](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)




## <a name="next-steps"></a>Passos seguintes

-   [Como garantir serviços de back-end usando a autenticação de certificado de cliente](./api-management-howto-mutual-certificates.md)
-   [Como carregar certificados](./api-management-howto-mutual-certificates.md)

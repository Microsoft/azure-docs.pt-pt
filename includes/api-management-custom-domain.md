---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: b9e601c72395b4910850714460321a83a3113e69
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649551"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Como o APIM Proxy Server responde com certificados SSL no aperto de mão TLS

### <a name="clients-calling-with-sni-header"></a>Clientes ligando com cabeçalho SNI
Se o cliente tiver um ou vários domínios personalizados configurados para Proxy, a APIM pode responder aos pedidos HTTPS do domínio(s) personalizado (por exemplo, contoso.com) bem como ao domínio predefinido (por exemplo, apim-service-name.azure-api.net). Com base nas informações no cabeçalho de indicação de nome do servidor (SNI), a APIM responde com o certificado de servidor apropriado.

### <a name="clients-calling-without-sni-header"></a>Clientes que ligam sem cabeçalho SNI
Se o cliente estiver a utilizar um cliente, que não envia o cabeçalho [SNI,](https://tools.ietf.org/html/rfc6066#section-3) a APIM cria respostas com base na seguinte lógica:

* Se o serviço tiver apenas um domínio personalizado configurado para Proxy, o Certificado Predefinido é o certificado que foi emitido para o domínio personalizado proxy.
* Se o serviço tiver configurado vários domínios personalizados para proxy (suportado no nível **De desenvolvimento** e **premium),** o cliente pode designar qual o certificado que deve ser o certificado predefinido. Para definir o certificado predefinido, a propriedade [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/createorupdate#hostnameconfiguration) deve ser definida como verdadeira ("defaultSslBinding":"true"). Se o cliente não definir a propriedade, o certificado predefinido é o certificado emitido para o domínio proxy predefinido hospedado em *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Suporte para pedido DE PUT/POST com grande carga útil

O servidor APIM Proxy suporta o pedido com grande carga útil ao utilizar certificados do lado do cliente em HTTPS (por exemplo, carga útil > 40 KB). Para evitar o congelamento do pedido do servidor, os clientes podem definir a propriedade ["negociarClientCertificate": "verdadeiro"](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) no nome de anfitrião proxy. Se o imóvel estiver definido como verdadeiro, o certificado de cliente é solicitado no tempo de ligação SSL/TLS, antes de qualquer troca de pedidos http. Uma vez que a definição se aplica ao nível **proxy Hostname,** todos os pedidos de ligação pedem o certificado de cliente. Os clientes podem configurar até 20 domínios personalizados para Proxy (apenas suportados no nível **Premium)** e trabalhar em torno desta limitação.


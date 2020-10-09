---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 3b42d5fbcfb19f08b46241dbe92e6a300bec1df6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81275510"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Como o Servidor Proxy de APIM responde com certificados SSL no handshake do TLS

### <a name="clients-calling-with-sni-header"></a>Clientes ligando com cabeçalho SNI
Se o cliente tiver um ou vários domínios personalizados configurados para Proxy, a APIM pode responder a pedidos HTTPS a partir do(s) domínio(s) personalizado (por exemplo, contoso.com) bem como domínio predefinido (por exemplo, apim-service-name.azure-api.net). Com base nas informações no cabeçalho 'Indicação de Nome do Servidor' (SNI), a APIM responde com certificado de servidor apropriado.

### <a name="clients-calling-without-sni-header"></a>Clientes ligando sem cabeçalho SNI
Se o cliente estiver a utilizar um cliente, que não envia o cabeçalho [SNI,](https://tools.ietf.org/html/rfc6066#section-3) a APIM cria respostas baseadas na seguinte lógica:

* Se o serviço tiver apenas um domínio personalizado configurado para Proxy, o Certificado Padrão é o certificado que foi emitido para o domínio personalizado Proxy.
* Se o serviço tiver configurado vários domínios personalizados para Proxy (suportado no nível **Developer** e **Premium),** o cliente pode designar qual o certificado que deve ser o certificado padrão. Para definir o certificado predefinido, a propriedade [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) deve ser definida como verdadeira ("defaultSslBinding":"true"). Se o cliente não definir o imóvel, o certificado por defeito é o certificado emitido para o domínio proxy predefinido alojado em *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Suporte para pedido PUT/POST com grande carga útil

O servidor Proxy DA APIM suporta o pedido com uma grande carga útil quando se utiliza certificados do lado do cliente em HTTPS (por exemplo, carga útil > 40 KB). Para evitar que o pedido do servidor congele, os clientes podem definir a propriedade ["negociar OClientCertificate": "verdadeiro"](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) no nome de anfitrião Proxy. Se o imóvel for definido como verdadeiro, o certificado de cliente é solicitado no tempo de ligação SSL/TLS, antes de qualquer troca de pedidos HTTP. Uma vez que a definição se aplica ao nível do **Nome anfitrião proxy,** todos os pedidos de ligação solicitam o certificado do cliente. Os clientes podem configurar até 20 domínios personalizados para Proxy (apenas suportado no nível **Premium)** e trabalhar em torno desta limitação.


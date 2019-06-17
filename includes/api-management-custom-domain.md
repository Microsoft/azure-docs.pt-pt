---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: dff01f8bc4a4cf58d1ed503b69a29dadc367fecb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66249099"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Como o servidor de Proxy de APIM responde com certificados SSL no handshake TLS

### <a name="clients-calling-with-sni-header"></a>Clientes a chamar com cabeçalho SNI
Se o cliente tem um ou vários domínios personalizados configurados para Proxy, APIM pode responder a pedidos HTTPS dos domínios personalizados (por exemplo, contoso.com), bem como o domínio predefinido (por exemplo, apim-service-name.azure-api.net). Com base nas informações no cabeçalho de indicação de nome de servidor (SNI), a APIM responde com o certificado de servidor adequado.

### <a name="clients-calling-without-sni-header"></a>Clientes chamando sem cabeçalho SNI
Se o cliente está a utilizar um cliente, que não envia a [SNI](https://tools.ietf.org/html/rfc6066#section-3) cabeçalho, APIM cria respostas com base na seguinte lógica:

* Se o serviço tiver apenas um domínio personalizado configurado para o Proxy, o certificado predefinido é o certificado que foi emitido para o domínio personalizado de Proxy.
* Se o serviço tem configurado vários domínios personalizados para o Proxy (suportado apenas no **Premium** camada), o cliente pode designar que certificado deve ser o certificado predefinido. Para definir o certificado do predefinido, o [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/createorupdate#hostnameconfiguration) propriedade deve ser definida como true ("defaultSslBinding": "true"). Se o cliente não defina a propriedade, o certificado predefinido é o certificado emitido para o domínio de Proxy predefinido hospedado em *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Suporte para o pedido PUT/POST com grande payload

Servidor Proxy de APIM oferece suporte a pedido com o payload grandes ao utilizar certificados de cliente num HTTPS (por exemplo, o payload > entre 40 KB). Para impedir que o pedido do servidor congelando, os clientes podem definir a propriedade ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) sobre o nome de anfitrião do Proxy. Se a propriedade é definida como true, o cliente de certificado é solicitado ao tempo de ligação de SSL/TLS, antes de qualquer troca de pedido HTTP. Uma vez que a definição aplica-se no **nome de anfitrião do Proxy** nível, todos os pedidos de ligação pedir o certificado de cliente. Os clientes podem configurar até 20 domínios personalizados para o Proxy (apenas suportado no **Premium** escalão) e de trabalho resolver essa limitação.


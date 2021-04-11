---
title: Suporte de protocolo para cabeçalhos HTTP em Azure Front Door | Microsoft Docs
description: Este artigo descreve protocolos de cabeçalho HTTP que a Porta frontal suporta.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2020
ms.author: duau
ms.openlocfilehash: 5989f91233448c04d50ba1c69a06851b91426a03
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167809"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Suporte de protocolo para cabeçalhos HTTP na Porta frontal Azure
Este artigo descreve o protocolo que a Porta frontal suporta com partes do caminho de chamada (ver imagem). As seguintes secções fornecem mais informações sobre cabeçalhos HTTP suportados pela Porta frontal.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Protocolo de cabeçalhos HTTP porta frontal Azure":::

>[!IMPORTANT]
>A Porta da Frente não certifica quaisquer cabeçalhos HTTP que não estejam documentados aqui.

## <a name="client-to-front-door"></a>Cliente para Porta da Frente
A Porta da Frente aceita a maioria dos cabeçalhos para o pedido de entrada sem modificá-los. Alguns cabeçalhos reservados são removidos do pedido de entrada se enviados, incluindo cabeçalhos com o prefixo X-FD-*.

## <a name="front-door-to-backend"></a>Porta da frente para o backend

A Porta da Frente inclui cabeçalhos para um pedido de entrada, a menos que sejam removidos devido a restrições. Porta da Frente também adiciona os seguintes cabeçalhos:

| Cabeçalho  | Exemplo e descrição |
| ------------- | ------------- |
| Via |  *Via: 1.1 Azure* </br> A Porta frontal adiciona a versão HTTP do cliente seguida do *Azure* como o valor para o cabeçalho Via. Este cabeçalho indica a versão HTTP do cliente e que a Porta Frontal foi um destinatário intermédio para o pedido entre o cliente e o backend.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Representa o endereço IP do cliente associado ao pedido que está a ser processado. Por exemplo, um pedido proveniente de um proxy pode adicionar o cabeçalho X-Forwarded-For para indicar o endereço IP do chamador original. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Representa o endereço IP da tomada associado à ligação TCP de que o pedido atual teve origem. O endereço IP do cliente de um pedido pode não ser igual ao seu endereço IP de tomada, uma vez que pode ser arbitrariamente substituído por um utilizador.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAAABKMMOJBV2NT4TY6SQVJC0zV1NURURHRTA2MTKANDM3YzgyY2QtMzYwYS0YTU0LTU0LT0YZMTNHMNzA3NjQ3Nzgz* </br> Uma cadeia de referência única que identifica um pedido servido pela Porta da Frente. É usado para pesquisar registos de acesso e crítico para resolução de problemas.|
| X-Azure-RequestChain | *X-Azure-RequestChain: hops=1* </br> Um cabeçalho que a Porta Frontal usa para detetar loops de pedido, e os utilizadores não devem assumir uma dependência. |
| X-Azure-FDID | *X-Azure-FDID: 55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> Uma cadeia de referência que identifica o pedido veio de um recurso específico da Porta frontal. O valor pode ser visto no portal Azure ou recuperado usando a API de gestão. Pode utilizar este cabeçalho em combinação com ACLs IP para bloquear o seu ponto final para apenas aceitar pedidos de um recurso específico da Porta Frontal. Consulte as FAQ para [obter mais detalhes](front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
| X-Forward-For | *X-Forward-For: 127.0.0.1* </br> O campo de cabeçalho HTTP (XFF) X-Forwarded-For (XFF) identifica frequentemente o endereço IP originado de um cliente que se conecta a um servidor web através de um proxy http ou equilibrador de carga. Se houver um cabeçalho XFF existente, então a Porta Frontal anexa o IP da tomada do cliente ou adiciona o cabeçalho XFF com o IP da tomada do cliente. |
| X-Forward-Forward-Host | *X-Forward-Host: contoso.azurefd.net* </br> O campo de cabeçalho HTTP X-Forwarded-Host é um método comum utilizado para identificar o anfitrião original solicitado pelo cliente no cabeçalho de pedido HTTP do anfitrião. Isto porque o nome de anfitrião da Porta Frontal pode diferir para o servidor backend que manusece o pedido. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> O campo de cabeçalho X-Forwardd-Proto HTTP é frequentemente utilizado para identificar o protocolo de origem de um pedido HTTP. A Porta frontal com base na configuração pode comunicar com o backend utilizando HTTPS. Isto é verdade mesmo que o pedido para o representante inverso seja HTTP. |
| X-FD-HealthProbe | O campo de cabeçalho X-FD-HealthProbe HTTP é utilizado para identificar a sonda de saúde da Porta frontal. Se este cabeçalho estiver definido para 1, o pedido é sonda de saúde. Pode utilizar quando pretender aceder rigorosamente a partir de uma porta frontal específica com o campo de cabeçalho X-Forwarded-Host. |
| X-Azure-FDID | *Cabeçalho X-Azure-FDID: 437c82cd-360a-4a54-94c3-5ff707647783* </br> Este campo contém frontdoorID que pode ser usado para identificar de que porta frontal é o pedido de entrada. Este campo é povoado pelo serviço Front Door. | 

## <a name="front-door-to-client"></a>Porta da frente para o cliente

Quaisquer cabeçalhos enviados para a Porta da Frente a partir do backend também são passados para o cliente. Seguem-se os cabeçalhos enviados da Porta da Frente para os clientes.

| Cabeçalho  | Exemplo e descrição |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOJBV2NT4TY6SQVJC0zV1NURURHRTA2MTKANDM3YzgyY2QtMzYwYS0YTU0LTU0LT0YZMTNHMNzA3NjQ3Nzgz* </br> Esta é uma cadeia de referência única que identifica um pedido servido pela Porta da Frente, que é fundamental para a resolução de problemas, uma vez que é usado para pesquisar registos de acesso.|
| X-Cache | *X-Cache: TCP_HIT* </br> Este cabeçalho descreve o estado da cache do pedido, o que lhe permite identificar se o conteúdo de resposta está a ser servido a partir da cache da Porta frontal. |

Tem de enviar o cabeçalho de pedido "X-Azure-DebugInfo: 1" para ativar os seguintes cabeçalhos de resposta opcionais.

| Cabeçalho  | Exemplo e descrição |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> Este cabeçalho contém o código de estado HTTP devolvido pelo backend. Utilizando este cabeçalho, pode identificar o código de estado HTTP devolvido pela aplicação que está a correr no seu backend sem passar por registos de backend. Este código de estado pode ser diferente do código de estado HTTP na resposta enviada ao cliente pela Porta da Frente. Este cabeçalho permite-lhe determinar se o backend está a porta errada ou se o problema está com o serviço porta da frente. |
| X-Azure-InternalError | Este cabeçalho conterá o código de erro que a Porta frontal encontra ao processar o pedido. Este erro indica que o problema é interno para o serviço/infraestrutura da Porta frontal. Assunto de relatório para apoio.  |
| X-Azure-ExternalError | *X-Azure-ExternalError: 0x830c1011, a autoridade do certificado não é familiar.* </br> Este cabeçalho mostra o código de erro que os servidores da Porta Frontal deparam enquanto estabelecem conectividade com o servidor backend para processar um pedido. Este cabeçalho ajudará a identificar problemas na ligação entre a Porta Frontal e a aplicação backend. Este cabeçalho incluirá uma mensagem de erro detalhada para ajudá-lo a identificar problemas de conectividade no seu backend (por exemplo, resolução DNS, cert inválido, e assim por diante). |

## <a name="next-steps"></a>Passos seguintes

- [Criar uma Porta de Entrada](quickstart-create-front-door.md)
- [Como funciona a Porta da Frente](front-door-routing-architecture.md)

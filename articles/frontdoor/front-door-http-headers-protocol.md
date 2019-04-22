---
title: Protocolo de suporte para cabeçalhos HTTP no serviço de porta de entrada do Azure | Documentos da Microsoft
description: Este artigo descreve os protocolos de cabeçalho HTTP que suporta o serviço de porta de entrada.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58861847"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Suporte de protocolo para cabeçalhos HTTP no serviço de porta de entrada do Azure
Este artigo descreve o protocolo que suporte o serviço de porta de entrada com partes do caminho da chamada (veja a imagem). As secções seguintes fornecem mais informações sobre cabeçalhos HTTP suportados pelo serviço de porta de entrada.

![Protocolo de cabeçalhos de HTTP do serviço de porta de entrada do Azure][1]

>[!IMPORTANT]
>Serviço de porta de entrada não certificar qualquer cabeçalho HTTP que não é documentado aqui.

## <a name="client-to-front-door-service"></a>Cliente ao serviço de porta de entrada
Serviço de porta de entrada aceita a maioria dos cabeçalhos da solicitação recebida sem modificá-los. Alguns cabeçalhos reservados são removidos da solicitação recebida se enviado, incluindo cabeçalhos com o X - FD-* prefixo.

## <a name="front-door-service-to-backend"></a>Serviço de porta de entrada para o back-end

Serviço de porta de entrada inclui cabeçalhos a partir de uma solicitação de entrada, a menos que removido devido a restrições. Porta de entrada também adiciona os seguintes cabeçalhos:

| Cabeçalho  | Exemplo e uma descrição |
| ------------- | ------------- |
| Através de |  Via: 1.1 azure </br> Porta de entrada adiciona a versão HTTP do cliente seguido *Azure* como o valor para o cabeçalho de Via. Isto indica a versão HTTP do cliente e essa porta de entrada foi um destinatário intermediário para o pedido entre o cliente e o back-end.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Representa o endereço IP do cliente associado à solicitação a ser processada. Por exemplo, um pedido proveniente de um proxy poderá adicionar o cabeçalho X-reencaminhados-para indicar o endereço IP do chamador original. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Representa o endereço IP de soquete associado com a ligação de TCP que originou a solicitação atual. Endereço IP do cliente de um pedido pode não ser igual ao respetivo endereço IP de socket porque pode ser arbitrariamente substituída por um utilizador.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Uma cadeia de referência exclusivo que identifica um pedido servido por porta de entrada. É utilizado para aceder aos registos de pesquisa e críticas para resolução de problemas.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hops=1 </br> Um cabeçalho que utiliza a porta de entrada para detetar os loops de pedido e os usuários não devem tomar uma dependência no mesmo. |
| X-reencaminhados-para | X-reencaminhados-para: 127.0.0.1 </br> O campo de cabeçalho X-Forwarded-For (XFF) HTTP identifica, muitas vezes, o endereço IP de origem de um cliente ligar a um servidor web através de um balanceador de carga ou um proxy HTTP. Se houver um cabeçalho XFF existente, em seguida, porta da frente acrescenta o IP de soquete do cliente para o mesmo ou adiciona o cabeçalho XFF com o IP de soquete do cliente. |
| X-reencaminhados-anfitrião | X-Forwarded-Host: contoso.azurefd.net </br> O campo de cabeçalho HTTP X-reencaminhados-anfitrião é um método comum usado para identificar o anfitrião original solicitado pelo cliente no cabeçalho do pedido de HTTP de anfitrião. Isto acontece porque o nome de anfitrião de porta de entrada pode ser diferentes para o servidor de back-end processar a solicitação. |
| Proto X reencaminhados | Proto X reencaminhados: http </br> O campo de cabeçalho HTTP X-reencaminhados-Proto, muitas vezes, é utilizado para identificar o protocolo de origem de uma solicitação HTTP, porque a porta de entrada, com base na configuração, poderá comunicar com o back-end através de HTTPS. Isso vale mesmo que o pedido para o proxy inverso é HTTP. |

## <a name="front-door-service-to-client"></a>Serviço de porta de entrada para cliente

Os cabeçalhos enviados para a porta da frente do back-end também são transmitidos para o cliente. Seguem-se os cabeçalhos enviados a partir de porta de entrada para os clientes.

| Cabeçalho  | Exemplo |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Esta é uma cadeia de referência exclusivo que identifica um pedido servido por porta de entrada. Isto é fundamental para resolução de problemas de como ele é utilizado para aceder aos registos de pesquisa.|

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma porta de entrada](quickstart-create-front-door.md)
- [Como funciona a porta de entrada](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
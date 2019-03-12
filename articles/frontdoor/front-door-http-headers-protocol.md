---
title: Serviço de porta de entrada do Azure - suporte de protocolo de cabeçalhos HTTP | Documentos da Microsoft
description: Este artigo ajuda-o a compreender os protocolos de cabeçalho HTTP suportados por porta de entrada
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
ms.openlocfilehash: b34ab417ab1d9ef77c3141d5aa130c338fb89188
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726333"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Serviço de porta de entrada do Azure - suporte de protocolo de cabeçalhos HTTP
Este documento descreve o protocolo que suporte o serviço de porta de entrada do Azure com várias partes do caminho da chamada conforme descrito pela imagem abaixo. As seções abaixo fornecem mais informações sobre os cabeçalhos HTTP que suporta a porta de entrada.

![Protocolo de cabeçalhos de HTTP do serviço de porta de entrada do Azure][1]

>[!WARNING]
>O serviço de porta de entrada do Azure não fornece garantias em qualquer cabeçalho HTTP que não está documentado aqui.

## <a name="1-client-to-front-door"></a>1. Cliente para a porta de entrada
Porta de entrada aceita a maioria dos cabeçalhos da solicitação recebida (sem modificá-los), no entanto, existem alguns cabeçalhos reservados que serão removidos da solicitação recebida se eles são enviados. Isto inclui cabeçalhos com os prefixos seguintes:
 - X-FD-*

## <a name="2-front-door-to-backend"></a>2. Porta de entrada para o back-end

Porta da frente irão incluir os cabeçalhos da solicitação recebida, a menos que eles foram removidos devido a restrições mencionadas acima. Porta de entrada também adicionará os cabeçalhos seguintes:

| Cabeçalho  | Exemplo e uma descrição |
| ------------- | ------------- |
| Através de |  *Via: 1.1 azure* </br> Porta de entrada adiciona seguido de "Azure" como o valor de por meio do cabeçalho de versão do HTTP do cliente. Este valor é adicionado para indicar a versão HTTP do cliente e essa porta de entrada do Azure foi um destinatário intermediário para o pedido entre o cliente e o back-end.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Representa o endereço de protocolo de Internet de "cliente" associado à solicitação a ser processada. Por exemplo, um pedido proveniente de um proxy pode adicionar o cabeçalho X-reencaminhados-para indicar o endereço IP do chamador original. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Representa o endereço de protocolo de Internet de soquete associado com a ligação de TCP, a solicitação atual originada. Endereço de IP de cliente de um pedido pode não ser igual ao respetivo endereço IP de Socket porque ele pode ser arbitrariamente substituído por um utilizador final.|
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Esta é uma cadeia de referência exclusivo que identifica um pedido servido por porta de entrada. É fundamental para resolução de problemas como é usada para pesquisar os registos de acesso.|
| X-Azure-RequestChain |  *X-Azure-RequestChain: hops=1* </br> Este é um cabeçalho que utiliza a porta de entrada para detetar os loops de pedido e os usuários não devem tomar uma dependência no mesmo. |
| X-reencaminhados-para | *X-reencaminhados-para: 127.0.0.1* </br> O campo de cabeçalho de X-Forwarded-For (XFF) HTTP é um método comum de identificar o endereço IP de origem de um cliente ligar a um servidor web através de um balanceador de carga ou um proxy HTTP. Se tiver ocorrido um cabeçalho XFF existente, em seguida, a porta da frente acrescenta o IP de soquete do cliente, ao mesmo mais adiciona o cabeçalho XFF com o IP de soquete do cliente. |
| X-reencaminhados-anfitrião | *X-Forwarded-Host: contoso.azurefd.net* </br> O campo de cabeçalho HTTP X-reencaminhados-anfitrião é um método comum de identificar o anfitrião original solicitado pelo cliente no cabeçalho de pedido HTTP de anfitrião, uma vez que o nome de anfitrião de porta de entrada pode ser diferentes para o servidor de back-end processar a solicitação. |
| Proto X reencaminhados | *Proto X reencaminhados: http* </br> O campo de cabeçalho HTTP X-reencaminhados-Proto é um método comum de identificar o protocolo de origem de uma solicitação HTTP, uma vez que, dependendo da configuração da porta de entrada podem se comunicar com o back-end através de HTTPS, mesmo que o pedido para o proxy inverso é HTTP. |

## <a name="3-front-door-to-client"></a>3. Porta de entrada para o cliente

Seguem-se os cabeçalhos que são enviados do porta de entrada para os clientes. Os cabeçalhos enviados para a porta da frente do back-end são transmitidos para o cliente também.

| Cabeçalho  | Exemplo |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Esta é uma cadeia de referência exclusivo que identifica um pedido servido por porta de entrada. É fundamental para resolução de problemas como é usada para pesquisar os registos de acesso.|

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
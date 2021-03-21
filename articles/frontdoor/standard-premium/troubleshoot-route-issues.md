---
title: Problemas de configuração standard/premium da porta frontal de resolução de problemas
description: Neste tutorial, você vai aprender a resolver alguns dos problemas comuns que você pode enfrentar para o seu exemplo Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 4690a513494d794377ee0c2e8cfb101e8fd66a0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100202"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>Problemas de encaminhamento comuns com Azure Front Door Standard/Premium

Este artigo descreve como resolver problemas comuns de encaminhamento que pode enfrentar para a configuração da porta frontal Azure.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 resposta da Porta frontal de Azure após alguns segundos

### <a name="symptom"></a>Sintoma

* Os pedidos regulares enviados para o seu backend sem passar pela Porta frontal de Azure estão a ter sucesso. Passar pela Porta Frontal Azure resulta em 503 respostas de erro.
* A falha da Porta Frontal Azure normalmente mostra após cerca de 30 segundos.

### <a name="cause"></a>Causa

A causa deste problema pode ser uma de duas coisas:
 
* A sua origem está a demorar mais tempo do que o tempo de tempo configurado (o padrão é de 30 segundos) para receber o pedido da Porta Frontal Azure.
* O tempo que demora a enviar uma resposta ao pedido da Azure Front Door está a demorar mais do que o valor de tempo limite. 

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

* Envie o pedido diretamente para o seu backend (sem passar pela Porta frontal de Azure). Veja quanto tempo o seu backend geralmente demora a responder.
* Envie o pedido através da Porta frontal Azure e veja se está a receber 503 respostas. Se não, o problema pode não ser um problema de tempo. Contacte o suporte.
* Se passar pela Porta Frontal Azure resulta num código de resposta de erro 503, configuure o `sendReceiveTimeout` campo para Azure Front Door. Pode prolongar o intervalo de tempo padrão até 4 minutos (240 segundos). A definição está em baixo `Endpoint Setting` e chama-se `Origin response timeout` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Pedidos enviados para o domínio personalizado devolvam um código de estado de 400

### <a name="symptom"></a>Sintoma

* Criou uma instância da Porta Frontal Azure, mas um pedido para o anfitrião de domínio ou frontend está a devolver um código de estado HTTP 400.
* Criou um mapeamento DNS para um domínio personalizado para o anfitrião frontal que configuraste. No entanto, o envio de um pedido para o nome de anfitrião de domínio personalizado devolve um código de estado HTTP 400. Não parece ir para o backend que configuraste.

### <a name="cause"></a>Causa

O problema ocorre se não configurar uma regra de encaminhamento para o domínio personalizado que foi adicionado como anfitrião frontal. Uma regra de encaminhamento precisa de ser explicitamente adicionada para o anfitrião frontal. Isso é verdade mesmo que uma regra de encaminhamento já tenha sido configurada para o anfitrião frontal sob o subdomínio da porta frontal Azure (*.azurefd.net).

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Adicione uma regra de encaminhamento para o domínio personalizado para direcionar o tráfego para o grupo de origem selecionado.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door não redireciona HTTP para HTTPS

### <a name="symptom"></a>Sintoma

A Azure Front Door tem uma regra de encaminhamento que redireciona HTTP PARA HTTPS, mas aceder ao domínio ainda mantém HTTP como o protocolo.

### <a name="cause"></a>Causa

Este comportamento pode acontecer se não configurar corretamente as regras de encaminhamento para a Porta Frontal Azure. Basicamente, a sua configuração atual não é específica e pode ter regras contraditórias.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Pedido ao nome do anfitrião frontend devolve um código de estado 411

### <a name="symptom"></a>Sintoma

Criou uma instância Azure Front Door Standard/Premium e configura um anfitrião frontend, um grupo de origem com pelo menos uma origem, e uma regra de encaminhamento que liga o anfitrião frontal ao grupo de origem. O seu conteúdo não parece estar disponível quando um pedido vai para o anfitrião frontal configurado porque um código de estado HTTP 411 é devolvido.

As respostas a estes pedidos podem também conter uma página de erro HTML no organismo de resposta que inclui uma exposição explicativa. Por exemplo: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Causa

Há várias causas possíveis para este sintoma. A razão geral é que o seu pedido HTTP não é totalmente compatível com RFC. 

Um exemplo de incumprimento é um `POST` pedido enviado sem um ou um `Content-Length` `Transfer-Encoding` cabeçalho (por exemplo, `curl -X POST https://example-front-door.domain.com` utilizando). Este pedido não satisfaz os requisitos estabelecidos no [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). AZure Front Door bloqueá-lo-ia com uma resposta HTTP 411.

Este comportamento é separado da funcionalidade Web Application Firewall (WAF) da Porta Frontal Azure. Atualmente, não há como desativar este comportamento. Todos os pedidos HTTP devem satisfazer os requisitos, mesmo que a funcionalidade WAF não esteja a ser utilizada.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

- Verifique se os seus pedidos estão em conformidade com os requisitos estabelecidos nos RFCs necessários.

- Tome nota de qualquer corpo de mensagem HTML que seja devolvido em resposta ao seu pedido. Um corpo de mensagens explica frequentemente *como* o seu pedido é incompatível.

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um Padrão/Premium da Porta da Frente.](create-front-door-portal.md)

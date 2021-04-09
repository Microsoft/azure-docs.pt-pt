---
title: Problemas de configuração da porta frontal Azure
description: Neste tutorial, aprende-se a resolver alguns dos problemas comuns que poderá enfrentar para a sua instância da Porta frontal Azure.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629993"
---
# <a name="troubleshooting-common-routing-problems"></a>Problemas de encaminhamento comuns de resolução de problemas

Este artigo descreve como resolver problemas comuns de encaminhamento que pode enfrentar para a configuração da porta frontal Azure.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 resposta da Porta frontal de Azure após alguns segundos

### <a name="symptom"></a>Sintoma

* Os pedidos regulares enviados para o seu backend sem passar pela Porta frontal de Azure estão a ter sucesso. Passar pela Porta Frontal Azure resulta em 503 respostas de erro.
* A falha da Porta Frontal Azure normalmente mostra após cerca de 30 segundos.

### <a name="cause"></a>Causa

A causa deste problema pode ser uma de duas coisas:
 
* O seu backend está a demorar mais tempo do que o tempo de tempo configurado (o padrão é de 30 segundos) para receber o pedido da Porta Frontal Azure.
* O tempo que demora a enviar uma resposta ao pedido da Azure Front Door está a demorar mais do que o valor de tempo limite. 

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

* Envie o pedido diretamente para o seu backend (sem passar pela Porta frontal de Azure). Veja quanto tempo o seu backend geralmente demora a responder.
* Envie o pedido através da Porta frontal Azure e veja se está a receber 503 respostas. Se não, o problema pode não ser um problema de tempo. Contacte o suporte.
* Se passar pela Porta Frontal Azure resulta num código de resposta de erro 503, configuure o `sendReceiveTimeout` campo para Azure Front Door. Pode prolongar o intervalo de tempo padrão até 4 minutos (240 segundos). A definição está em baixo `backendPoolSettings` e chama-se `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Pedidos enviados para o domínio personalizado devolvam um código de estado de 400

### <a name="symptom"></a>Sintoma

* Criou uma instância da Porta Frontal Azure, mas um pedido para o anfitrião de domínio ou frontend está a devolver um código de estado HTTP 400.
* Criou um mapeamento DNS para um domínio personalizado para o anfitrião frontal que configuraste. No entanto, o envio de um pedido para o nome de anfitrião de domínio personalizado devolve um código de estado HTTP 400. Não parece ir para o backend que configuraste.

### <a name="cause"></a>Causa

O problema ocorre se não configurar uma regra de encaminhamento para o domínio personalizado que foi adicionado como anfitrião frontal. Uma regra de encaminhamento precisa de ser explicitamente adicionada para o anfitrião frontal. Isso é verdade mesmo que uma regra de encaminhamento já tenha sido configurada para o anfitrião frontal sob o subdomínio da porta frontal Azure (*.azurefd.net).

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Adicione uma regra de encaminhamento para o domínio personalizado para direcionar o tráfego para o pool de backend selecionado.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door não redireciona HTTP para HTTPS

### <a name="symptom"></a>Sintoma

A Azure Front Door tem uma regra de encaminhamento que redireciona HTTP PARA HTTPS, mas aceder ao domínio ainda mantém HTTP como o protocolo.

### <a name="cause"></a>Causa

Este comportamento pode acontecer se não configurar corretamente as regras de encaminhamento para a Porta Frontal Azure. Basicamente, a sua configuração atual não é específica e pode ter regras contraditórias.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>Pedido a um nome de anfitrião frontal devolve um código de estado 404

### <a name="symptom"></a>Sintoma

Você criou uma instância da Porta frontal Azure configurando um anfitrião frontal, uma piscina de backend com pelo menos um backend nele, e uma regra de encaminhamento que liga o anfitrião frontal à piscina de backend. O seu conteúdo não está disponível quando faz um pedido ao anfitrião de frontend configurado. Como resultado, o pedido devolve um código de estado HTTP 404.

### <a name="cause"></a>Causa

Existem várias causas possíveis para este sintoma:

* O backend não é um backend virado para o público e não é visível para a Porta da Frente Azure.
* O backend está mal configurado, fazendo com que a Porta frontal de Azure envie o pedido errado. Por outras palavras, o seu backend aceita apenas HTTP e não permitiu HTTPS. Então a Azure Front Door está a tentar transmitir pedidos HTTPS.
* O backend está rejeitando o cabeceamento do anfitrião que foi reencaminhado com o pedido para o backend.
* A configuração para o backend ainda não foi totalmente implantada.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

* Verifique o tempo de implantação:
   * Certifique-se de que esperou pelo menos 10 minutos para que a configuração fosse implantada.

* Verifique as definições de backend:
    * Vá para a piscina de backend para onde o pedido deve ser encaminhamento para. (Depende de como configurar a regra de encaminhamento.) Verifique se o tipo de hospedeiro de backend e o nome do anfitrião de backend estão corretos. Se o backend for um hospedeiro personalizado, certifique-se de que o soletrou corretamente. 

    * Consulte as portas HTTP e HTTPS. Na maioria dos casos, 80 e 443 (respectivamente) estão corretos e não são necessárias alterações. No entanto, há a possibilidade de o seu backend não estar configurado desta forma e estar a ouvir numa porta diferente.

    * Verifique o cabeçalho do anfitrião de backend configurado para os backends para os anfitriões frontend para. Na maioria dos casos, este cabeçalho deve ser o mesmo que o nome do hospedeiro de backend. No entanto, um valor incorreto pode causar vários códigos de estado HTTP 4xx se o backend espera algo diferente. Se introduzir o endereço IP do seu backend, poderá ter de definir o cabeçalho do anfitrião de backend para o nome de anfitrião do backend.

* Verifique as definições da regra de encaminhamento:
    * Vá para a regra de encaminhamento que deve ser rota do nome do anfitrião frontal em questão para uma piscina de backend. Certifique-se de que os protocolos aceites são configurados corretamente quando o pedido for reencaminhado. O campo **de protocolos aceites** determina quais os pedidos que a Porta Frontal Azure deve aceitar. O protocolo de encaminhamento determina o protocolo que a Porta Frontal Azure deve utilizar para encaminhar o pedido para o backend.
      
      Como exemplo, se o backend aceitar apenas pedidos HTTP, as seguintes configurações serão válidas:
            
      * Os protocolos aceites são HTTP e HTTPS. O protocolo de encaminhamento é HTTP. Um pedido de correspondência não funcionará, porque HTTPS é um protocolo permitido. Se um pedido chegasse como HTTPS, a Azure Front Door tentaria reencamizá-lo utilizando HTTPS.

      * O protocolo aceite é HTTP. O protocolo de encaminhamento é um pedido de correspondência ou HTTP.
    - **Url Rewrite** é desativado por padrão. Este campo é utilizado apenas se quiser reduzir o âmbito de aplicação dos recursos que pretende disponibilizar. Quando este campo estiver desativado, a Porta Frontal Azure encaminhará o mesmo caminho de pedido que recebe. É possível configurar mal este campo. Assim, quando a Porta Frontal Azure estiver a solicitar um recurso do backend que não está disponível, irá devolver um código de estado HTTP 404.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Pedido ao nome do anfitrião frontend devolve um código de estado 411

### <a name="symptom"></a>Sintoma

Você criou uma instância da Porta frontal Azure e configura um anfitrião frontal, uma piscina de backend com pelo menos um backend nele, e uma regra de encaminhamento que liga o anfitrião frontal à piscina de backend. O seu conteúdo não parece estar disponível quando um pedido vai para o anfitrião frontal configurado porque um código de estado HTTP 411 é devolvido.

As respostas a estes pedidos podem também conter uma página de erro HTML no organismo de resposta que inclui uma exposição explicativa. Por exemplo: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Causa

Há várias causas possíveis para este sintoma. A razão geral é que o seu pedido HTTP não é totalmente compatível com RFC. 

Um exemplo de incumprimento é um `POST` pedido enviado sem um ou um `Content-Length` `Transfer-Encoding` cabeçalho (por exemplo, `curl -X POST https://example-front-door.domain.com` utilizando). Este pedido não satisfaz os requisitos estabelecidos no [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). AZure Front Door bloqueá-lo-ia com uma resposta HTTP 411.

Este comportamento é separado da funcionalidade Web Application Firewall (WAF) da Porta Frontal Azure. Atualmente, não há como desativar este comportamento. Todos os pedidos HTTP devem satisfazer os requisitos, mesmo que a funcionalidade WAF não esteja a ser utilizada.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

- Verifique se os seus pedidos cumprem os requisitos estabelecidos nos RFCs necessários.

- Tome nota de qualquer corpo de mensagem HTML que seja devolvido em resposta ao seu pedido. Um corpo de mensagens explica frequentemente *como* o seu pedido é incompatível.

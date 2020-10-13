---
title: Problemas de configuração da porta frontal Azure
description: Neste tutorial, aprende-se a resolver alguns dos problemas comuns que pode enfrentar para a sua Porta da Frente.
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
ms.openlocfilehash: dbce9019e33c07dd4faa91ffd490eba4d313c675
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630615"
---
# <a name="troubleshooting-common-routing-issues"></a>Resolução de problemas problemas comuns de encaminhamento

Este artigo descreve como resolver alguns dos problemas comuns de encaminhamento que pode enfrentar para a configuração da porta frontal Azure.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 resposta da Porta da Frente após alguns segundos

### <a name="symptom"></a>Sintoma

* Os pedidos regulares enviados para o seu backend sem passar pela Porta frontal estão a ter sucesso, mas ir pela Porta da Frente resulta em 503 respostas de erro.
* A falha da Porta frontal mostra após alguns segundos (normalmente por volta de 30 segundos)

### <a name="cause"></a>Causa

A causa desta questão pode ser uma de duas coisas:
 
* O seu backend está a demorar mais tempo do que o tempo de tempo configurado (o padrão é de 30 segundos) para receber o pedido da Porta frontal.
* O tempo que demora a enviar uma resposta ao pedido da Porta frontal está a demorar mais do que o valor de tempo limite. 

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

* Envie o pedido diretamente para o seu backend (sem passar pela Porta da Frente) e veja qual é o tempo habitual que leva para que o seu backend responda.
* Envie o pedido pela Porta da Frente e veja se está a receber 503 respostas. Caso contrário, o problema pode não ser um problema de tempo limite. Contacte o suporte.
* Se a passagem pela Porta frontal resultar num código de resposta de erro 503, então configuure o `sendReceiveTimeout` campo para a porta da frente. Pode prolongar o intervalo de tempo padrão até 4 minutos (240 segundos). A definição está sob o `backendPoolSettings` e é chamado `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Pedidos enviados para o domínio personalizado devolve 400 Código de Estado

### <a name="symptom"></a>Sintoma

* Criou uma Porta frontal, mas um pedido para o anfitrião de domínio ou frontend está a devolver um código de estado HTTP 400.
* Criou um mapeamento DNS para um domínio personalizado para o anfitrião frontal que configuraste. No entanto, o envio de um pedido para o nome anfitrião de domínio personalizado devolve um código de estado HTTP 400. O que não parece ir para o backend que configuraste.

### <a name="cause"></a>Causa

O problema ocorre se não configurar uma regra de encaminhamento para o domínio personalizado que foi adicionado como anfitrião frontal. Uma regra de encaminhamento precisa de ser explicitamente adicionada para o anfitrião frontal. Mesmo que já tenha sido configurado para o anfitrião frontal sob o subdomínio da porta da frente (*.azurefd.net).

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Adicione uma regra de encaminhamento para o domínio personalizado para direcionar o tráfego para o pool de backend selecionado.

## <a name="front-door-doesnt-redirect-http-to-https"></a>Porta da Frente não redireciona HTTP para HTTPS

### <a name="symptom"></a>Sintoma

A porta da frente tem uma regra de encaminhamento que diz redirecionar HTTPS para HTTPS, mas aceder ao domínio ainda mantém HTTP como o protocolo.

### <a name="cause"></a>Causa

Este comportamento pode acontecer se não configurar as regras de encaminhamento corretamente para a sua Porta da Frente. Basicamente, a sua configuração atual não é específica e pode ter regras contraditórias.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Pedido de nome de anfitrião Frontend Devolve 404 Código de Estado

### <a name="symptom"></a>Sintoma

 Você criou uma Porta frontal configurando um anfitrião frontal, uma piscina de backend com pelo menos um backend nele, e uma regra de encaminhamento que liga o anfitrião frontal à piscina de backend. O seu conteúdo não está disponível quando faz um pedido ao anfitrião frontal configurado como resultado de um código de estado HTTP 404 ser devolvido.

### <a name="cause"></a>Causa

Existem várias causas possíveis para este sintoma:

* O backend não é um retrocesso público e não é visível para a Porta da Frente.
* O backend está mal configurado, fazendo com que a Porta da Frente envie o pedido errado. Por outras palavras, o seu backend apenas aceita HTTP e não foi verificado permitindo HTTPS. Assim, a Porta frontal está a tentar encaminhar pedidos HTTPS.
* O backend está rejeitando o cabeceamento do anfitrião que foi reencaminhado com o pedido para o backend.
* A configuração para o backend ainda não foi totalmente implantada.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

1. Tempo de implantação
   * Certifique-se de que esperou ~10 minutos para que a configuração fosse implantada.

2. Verifique as Definições de Backend
    * Navegue para a piscina de backend para a qual o pedido deve ser encaminhamento (depende de como você tem a regra de encaminhamento configurado). Verifique se o tipo de hospedeiro de *backend* e o nome do anfitrião de backend estão corretos. Se o backend for um hospedeiro personalizado, certifique-se de que o soletrou corretamente. 

    * Consulte as portas HTTP e HTTPS. Na maioria dos casos, 80 e 443 (respectivamente) estão corretos e não serão necessárias alterações. No entanto, há a possibilidade de o seu backend não estar configurado desta forma e estar a ouvir numa porta diferente.

        * Verifique o _cabeçalho do anfitrião Backend_ configurado para os backends para os fundos para os que o anfitrião frontend deve estar a encaminhar. Na maioria dos casos, este cabeçalho deve ser o mesmo que o *nome de hospedeiro backend*. No entanto, um valor incorreto pode causar vários códigos de estado HTTP 4xx se o backend espera algo diferente. Se inserir o endereço IP do seu backend, poderá ter de definir o *cabeçalho do anfitrião Backend* para o nome de anfitrião do backend.

3. Verifique as definições da regra de encaminhamento:
    * Navegue para a regra de encaminhamento que deve ser rota do nome anfitrião frontend em questão para uma piscina de backend. Certifique-se de que os protocolos aceites estão configurados corretamente ao encaminhar o pedido. O campo *de protocolos aceite* determina quais os pedidos que a Porta frontal deve aceitar. O *protocolo de encaminhamento* determina o protocolo que a Porta frontal deve utilizar para encaminhar o pedido para o backend.
         * Como exemplo, se o backend apenas aceitar http solicita as seguintes configurações seriam válidas:
            * *Os protocolos aceites* são HTTP e HTTPS. *O protocolo de reencaminhamento* é HTTP. O pedido de jogo não funcionará, uma vez que HTTPS é um protocolo permitido e se um pedido surgisse como HTTPS, a Porta Frontal tentaria encaminhá-lo usando HTTPS.

            * *Os protocolos aceites* são HTTP. *O protocolo de encaminhamento* é um pedido de correspondência ou HTTP.

    - *Url Rewrite* é desativado por padrão. Este campo é utilizado apenas se quiser reduzir o âmbito de aplicação dos recursos que pretende disponibilizar. Quando desativado, a Porta Frontal encaminhará o mesmo caminho de pedido que recebe. É possível configurar mal este campo. Assim, quando a Porta Frontal está a solicitar um recurso do backend que não está disponível, irá devolver um código de estado HTTP 404.

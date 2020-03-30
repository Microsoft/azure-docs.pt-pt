---
title: Problemas de resolução de problemas Azure Front Door problemas
description: Neste tutorial, aprende-se a resolver alguns dos problemas comuns que pode enfrentar para a porta da frente.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 962c884eb8adc05e5d50b6b254d5c3f0b18af556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471511"
---
# <a name="troubleshooting-common-routing-issues"></a>Resolução de problemas comum de encaminhamento

Este artigo descreve como resolver alguns dos problemas comuns de encaminhamento que pode enfrentar para a configuração da porta frontal do Azure.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 resposta da Porta da Frente após alguns segundos

### <a name="symptom"></a>Sintoma

- Os pedidos regulares enviados para o seu backend sem passar pela Porta da Frente estão a ter sucesso, mas passar pela Porta da Frente resulta em 503 respostas de erro.

- A falha da Porta da Frente mostra após alguns segundos (normalmente ao redor após 30 segundos)

### <a name="cause"></a>Causa

Este sintoma acontece quando o seu backend leva para além da configuração de tempo (padrão é de 30 segundos) para receber o pedido da Porta da Frente ou se demorar além deste valor de tempo para enviar uma resposta ao pedido da Porta da Frente. 

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

- Envie o pedido diretamente para o seu backend (sem passar pela Porta da Frente) e veja qual é o tempo habitual que leva para o seu backend responder.
- Envie o pedido via Porta da Frente e veja se está a ver alguma resposta de 503. Se não, então esta pode não ser uma questão de tempo. Por favor contacte o suporte.
- Se passar pela Porta Frontal resulta em 503 código de resposta a erros, então configure o campo de envio Do Tempo de Envio para a sua Porta Frontal para estender o tempo de predefinição até 4 minutos (240 segundos). A configuração `backendPoolSettings` está sob `sendRecvTimeoutSeconds`o e é chamado . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Pedidos enviados para o domínio personalizado devolvem 400 Código de Estado

### <a name="symptom"></a>Sintoma

- Criou uma Porta Da Frente, mas um pedido ao anfitrião de domínio ou frontend está a devolver um código de estado HTTP 400.

- Criou um mapeamento DNS de um domínio personalizado para o anfitrião frontal que configura. No entanto, o envio de um pedido para o nome de anfitrião de domínio personalizado devolve um código de estado HTTP 400 e não parece encaminhar para o backend(s) configurado.

### <a name="cause"></a>Causa

Este sintoma pode acontecer se não tiver configurado uma regra de encaminhamento para o domínio personalizado que adicionou como anfitrião frontal. Uma regra de encaminhamento precisa de ser explicitamente adicionada para esse anfitrião frontal, mesmo que já tenha sido configurado para o anfitrião frontal sob o subdomínio porta da frente (*.azurefd.net) para o qual o seu domínio personalizado tem um mapeamento DNS.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Adicione uma regra de encaminhamento do domínio personalizado à piscina de backend desejada.

## <a name="front-door-is-not-redirecting-http-to-https"></a>A Porta da Frente não está a redirecionar http para HTTPS

### <a name="symptom"></a>Sintoma

A Porta da Frente tem uma regra de encaminhamento que diz redirecionar HTTP para HTTPS, mas aceder ao domínio ainda mantém HTTP como o protocolo.

### <a name="cause"></a>Causa

Este comportamento pode acontecer se não tiver configurado corretamente as regras de encaminhamento para a sua Porta da Frente. Basicamente, a sua configuração atual não é específica e pode ter regras contraditórias.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Pedido para nome de anfitrião Frontend Devolve 404 Código de Estado

### <a name="symptom"></a>Sintoma

- Criou uma Porta da Frente e configurou um anfitrião frontal, uma piscina de backend com pelo menos uma parte traseira, e uma regra de encaminhamento que liga o anfitrião frontal à piscina de backend. O seu conteúdo não parece estar disponível ao enviar um pedido ao anfitrião frontal configurado porque um código de estado HTTP 404 é devolvido.

### <a name="cause"></a>Causa

Existem várias possíveis causas para este sintoma:

- O backend não é um backend virado para o público e não é visível para a Porta da Frente.
- O backend está mal configurado, o que está a fazer com que a Porta da Frente envie o pedido errado (isto é, o seu backend só aceita HTTP, mas não desverificado permitindo https para que a Porta Da Frente esteja a tentar reencaminhar pedidos HTTPS).
- O backend está rejeitando o cabeceamento do anfitrião que foi encaminhado com o pedido para o backend.
- A configuração para o backend ainda não foi totalmente implementada.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

1. Tempo de implantação
   - Certifique-se de que esperou ~10 minutos para que a configuração fosse implementada.

2. Verifique as definições de backend
    - Navegue para o pool de backend para o qual o pedido deve ser encaminhado (depende de como tem a regra de encaminhamento configurada) e verifique se o tipo de _anfitrião de backend_ e o nome do anfitrião do backend estão corretos. Se o backend for um hospedeiro personalizado, certifique-se de que o soletrou corretamente. 

    - Verifique as portas HTTP e HTTPS. Na maioria dos casos, 80 e 443 (respectivamente), estão corretos e não serão necessárias alterações. No entanto, existe a possibilidade de o seu backend não estar configurado desta forma e estar a ouvir numa porta diferente.

        - Verifique o cabeçalho do _hospedeiro Backend_ configurado para as extremidades traseiras para as as costas para as as que o anfitrião Frontend deve encaminhar. Na maioria dos casos, este cabeçalho deve ser o mesmo que o nome de _anfitrião backend_. No entanto, um valor incorreto pode causar vários códigos de estado HTTP 4xx se o backend esperar algo diferente. Se inserir o endereço IP do seu backend, poderá ter de definir o cabeçalho do _hospedeiro Backend_ para o nome de anfitrião do backend.


3. Verifique as definições da regra de encaminhamento
    - Navegue para a regra de encaminhamento que deve ir do nome de anfitrião frontend em questão para uma piscina de backend. Certifique-se de que os protocolos aceites estão corretamente configurados, ou se não, certifique-se de que o protocolo Porta Frontal utilizará ao reencaminhar o pedido corretamente configurado. O campo _de protocolos aceites_ determina quais os pedidos que a Porta Da Frente deve aceitar e o _protocolo de encaminhamento_ determina que protocolo a Porta Frontal deve usar para encaminhar o pedido para o backend.
         - Como exemplo, se o backend apenas aceitar pedidos HTTP, as seguintes configurações seriam válidas:
            - _Os protocolos aceites_ são HTTP e HTTPS. _O protocolo de reencaminhamento_ é HTTP. O pedido de jogo não funcionará, uma vez que https é um protocolo permitido e se um pedido veio como HTTPS, a Porta da Frente tentaria reencaminhar usando HTTPS.

            - _Os protocolos aceites_ são HTTP. _O protocolo de reencaminhamento_ é o pedido de correspondência ou https.

    - _A Reescrita_ do Url é desativada por padrão e só deve utilizar este campo se quiser reduzir o âmbito dos recursos hospedados no backend que pretende disponibilizar. Quando desativado, a Porta da Frente encaminhará o mesmo caminho de pedido que recebe. É possível que este campo esteja mal configurado e a Porta da Frente esteja a solicitar um recurso do backend que não está disponível, devolvendo assim um código de estado HTTP 404.


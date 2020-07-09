---
title: Problemas de configuração da porta frontal Azure
description: Neste tutorial, aprende-se a resolver alguns dos problemas comuns que pode enfrentar para a sua Porta da Frente.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: f4310350e83284d6a2839f8c3d86526d3cda74ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743580"
---
# <a name="troubleshooting-common-routing-issues"></a>Resolução de problemas problemas comuns de encaminhamento

Este artigo descreve como resolver alguns dos problemas comuns de encaminhamento que pode enfrentar para a configuração da porta frontal Azure.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 resposta da Porta da Frente após alguns segundos

### <a name="symptom"></a>Sintoma

- Os pedidos regulares enviados para o seu backend sem passar pela Porta frontal estão a ter sucesso, mas ir pela Porta da Frente resulta em 503 respostas de erro.

- A falha da Porta frontal mostra após alguns segundos (normalmente por volta de 30 segundos)

### <a name="cause"></a>Causa

Este sintoma acontece quando o seu backend leva para além da configuração do intervalo (o padrão é de 30 segundos) para receber o pedido da Porta frontal ou se for necessário para além deste valor de tempo limite para enviar uma resposta ao pedido da Porta frontal. 

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

- Envie o pedido diretamente para o seu backend (sem passar pela Porta da Frente) e veja qual é o tempo habitual que leva para que o seu backend responda.
- Envie o pedido pela Porta da Frente e veja se está a ver 503 respostas. Se não, então isto pode não ser um problema de tempo. Por favor contacte o suporte.
- Se passar pela Porta Frontal resulta em 503 código de resposta a erros, em seguida, configuure o campo sendReceiveTimeout para a sua Porta frontal para prolongar o intervalo de tempo predefinido até 4 minutos (240 segundos). A definição está sob o `backendPoolSettings` e é chamado `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Pedidos enviados para o domínio personalizado devolve 400 Código de Estado

### <a name="symptom"></a>Sintoma

- Criou uma Porta frontal, mas um pedido para o anfitrião de domínio ou frontend está a devolver um código de estado HTTP 400.

- Criou um mapeamento DNS de um domínio personalizado para o anfitrião frontal que configuraste. No entanto, o envio de um pedido para o nome anfitrião de domínio personalizado devolve um código de estado HTTP 400 e não parece encaminhar para o(s) backend(s) que configura.

### <a name="cause"></a>Causa

Este sintoma pode acontecer se não tiver configurado uma regra de encaminhamento para o domínio personalizado que adicionou como anfitrião frontal. Uma regra de encaminhamento precisa de ser explicitamente adicionada para esse anfitrião frontal, mesmo que já tenha sido configurada para o anfitrião frontal sob o subdomínio da porta da frente (*.azurefd.net) para o qual o seu domínio personalizado tem um mapeamento DNS.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Adicione uma regra de encaminhamento do domínio personalizado ao pool de backend desejado.

## <a name="front-door-is-not-redirecting-http-to-https"></a>Porta da Frente não está redirecionando HTTP para HTTPS

### <a name="symptom"></a>Sintoma

A porta da frente tem uma regra de encaminhamento que diz redirecionar HTTPS para HTTPS, mas aceder ao domínio ainda mantém HTTP como o protocolo.

### <a name="cause"></a>Causa

Este comportamento pode acontecer se não tiver configurado corretamente as regras de encaminhamento para a sua porta frontal. Basicamente, a sua configuração atual não é específica e pode ter regras contraditórias.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Pedido de nome de anfitrião Frontend Devolve 404 Código de Estado

### <a name="symptom"></a>Sintoma

- Você criou uma Porta frontal e configura um anfitrião frontal, uma piscina de backend com pelo menos um backend nele, e uma regra de encaminhamento que liga o anfitrião frontal à piscina de backend. O seu conteúdo não parece estar disponível ao enviar um pedido ao anfitrião frontal configurado porque é devolvido um código de estado HTTP 404.

### <a name="cause"></a>Causa

Existem várias causas possíveis para este sintoma:

- O backend não é um retrocesso virado para o público e não é visível para a Porta da Frente.
- O backend está mal configurado, o que está a fazer com que a Porta da Frente envie o pedido errado (ou seja, o seu backend só aceita HTTP mas não foi desmarcado permitindo HTTPS, pelo que a Porta Frontal está a tentar encaminhar pedidos HTTPS).
- O backend está rejeitando o cabeceamento do anfitrião que foi reencaminhado com o pedido para o backend.
- A configuração para o backend ainda não foi totalmente implantada.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

1. Tempo de implantação
   - Certifique-se de que esperou ~10 minutos para que a configuração fosse implantada.

2. Verifique as Definições de Backend
    - Navegue para o pool de backend para o qual o pedido deve ser encaminhamento (depende de como você tem a regra de encaminhamento configurado) e verifique se o _tipo de anfitrião de backend_ e o nome do anfitrião de backend estão corretos. Se o backend for um hospedeiro personalizado, certifique-se de que o soletrou corretamente. 

    - Consulte as portas HTTP e HTTPS. Na maioria dos casos, 80 e 443 (respectivamente), estão corretos e não serão necessárias alterações. No entanto, existe a possibilidade de o seu backend não estar configurado desta forma e estar a ouvir numa porta diferente.

        - Verifique o _cabeçalho do anfitrião Backend_ configurado para os backends para os fundos para os que o anfitrião frontend deve estar a encaminhar. Na maioria dos casos, este cabeçalho deve ser o mesmo que o _nome de hospedeiro backend_. No entanto, um valor incorreto pode causar vários códigos de estado HTTP 4xx se o backend espera algo diferente. Se inserir o endereço IP do seu backend, poderá ter de definir o _cabeçalho do anfitrião Backend_ para o nome de anfitrião do backend.


3. Verifique as definições da regra de encaminhamento
    - Navegue para a regra de encaminhamento que deve ser rota do nome anfitrião frontend em questão para uma piscina de backend. Certifique-se de que os protocolos aceites estão corretamente configurados, ou se não, certifique-se de que o protocolo porta frontal utilizará ao encaminhar o pedido está corretamente configurado. O campo _de protocolos aceites_ determina quais os pedidos que a Porta frontal deve aceitar e o _protocolo de encaminhamento_ determina qual o protocolo que a Porta Frontal deve utilizar para encaminhar o pedido para o backend.
         - Como exemplo, se o backend apenas aceitar http solicita as seguintes configurações seriam válidas:
            - _Os protocolos aceites_ são HTTP e HTTPS. _O protocolo de reencaminhamento_ é HTTP. O pedido de jogo não funcionará, uma vez que HTTPS é um protocolo permitido e se um pedido surgisse como HTTPS, a Porta Frontal tentaria encaminhá-lo usando HTTPS.

            - _Os protocolos aceites_ são HTTP. _O protocolo de encaminhamento_ é um pedido de correspondência ou HTTPS.

    - _O Url Rewrite_ é desativado por padrão e só deve utilizar este campo se pretender reduzir o âmbito de recursos hospedados por backend que pretende disponibilizar. Quando desativado, a Porta Frontal encaminhará o mesmo caminho de pedido que recebe. É possível que este campo esteja mal configurado e a Porta Frontal esteja a solicitar um recurso a partir do backend que não está disponível, devolvendo assim um código de estado HTTP 404.


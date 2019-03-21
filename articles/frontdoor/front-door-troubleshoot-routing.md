---
title: Resolução de problemas - resolver problemas com a sua configuração de serviço de porta de entrada do Azure | Documentos da Microsoft
description: Neste tutorial, irá aprender para Self-resolver alguns dos problemas comuns que poderá experimentar para sua porta de entrada.
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
ms.openlocfilehash: 7a261d65a7bd3eea150dd764c65b94ddd47466b3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100314"
---
# <a name="troubleshooting-common-routing-issues"></a>Resolução de problemas comuns de encaminhamento
Este artigo descreve como resolver problemas de alguns dos problemas comuns de roteamentos que pode enfrentar para a sua configuração de serviço de porta de entrada do Azure. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>Nome do anfitrião não encaminhamento para o back-end e devolve o código de estado 400


### <a name="symptom"></a>Sintoma
- Criar uma porta de entrada, mas um pedido para o anfitrião de front-end está a devolver um código de estado HTTP 400.

  - Criou um DNS mapeamento a partir de um domínio personalizado para o anfitrião de front-end que configurou. No entanto, enviar um pedido para o nome de anfitrião do domínio personalizado devolve um código de estado HTTP 400 e não é apresentada encaminhar para o backend(s) configurou.

### <a name="cause"></a>Causa
- Esse sintoma pode acontecer se não tiver configurado uma regra de encaminhamento para o domínio personalizado que adicionou como um anfitrião de front-end. Uma regra de roteamento tem de ser explicitamente adicionada desse anfitrião de front-end, mesmo se uma já foi configurada para o anfitrião de front-end sob o subdomínio de porta de entrada (*. azurefd.net) que o seu domínio personalizado tem um mapeamento de DNS para.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas
- Adicione uma regra de roteamento do domínio personalizado para o conjunto de back-end pretendido.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>O pedido para o código de estado 404 devolve do nome de anfitrião do front-end

### <a name="symptom"></a>Sintoma
- Criou uma porta de entrada e configurado um anfitrião de front-end, um conjunto de back-end com pelo menos um back-end no mesmo e uma regra de roteamento que liga o anfitrião de front-end para o conjunto de back-end. O conteúdo não parece estar disponíveis quando enviar um pedido para o anfitrião de front-end configurado, uma vez que é devolvido um código de estado HTTP 404.

### <a name="cause"></a>Causa
Existem várias causas possíveis para este sintoma:
 - O back-end não é um back-end destinado ao público e não é visível para o serviço de porta de entrada.

- O back-end configurada incorretamente, que está a causar o serviço de porta de entrada enviar o pedido incorreto (ou seja, o back-end aceita apenas HTTP, não o tiver feito, mas desmarcada, permitindo que solicitações HTTPS para que a porta de entrada está a tentar reencaminhar HTTPS).
- O back-end está a rejeitar o cabeçalho de anfitrião foi encaminhado com o pedido para o back-end.
- A configuração para o back-end ainda não foi totalmente implementada.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas
1. Tempo de implantação
    - Certifique-se de que se esperou ~ 10 minutos para a configuração a serem implantados.

2. Verifique as definições de back-end
   - Navegue para o conjunto de back-end que o pedido deve ser encaminhamento (depende de como tiver configurada a regra de encaminhamento) e certifique-se de que o _tipo de anfitrião de back-end_ e o nome de anfitrião de back-end estão corretos. Se o back-end é um host personalizado, certifique-se de que tenha escrito-lo corretamente. 

   - Verifique as portas HTTP e HTTPS. Na maioria dos casos, 80 e 443 (respectivamente), estão corretas e nenhuma alteração é necessária. No entanto, há uma chance de que o seu back-end não está configurado dessa forma e está à escuta numa porta diferente.

     - Verifique os _cabeçalho de anfitrião de back-end_ configurado para os back-ends que o anfitrião de front-end deve ser encaminhamento para. Na maioria dos casos, esse cabeçalho deve ser igual a _nome de anfitrião de back-end_. No entanto, um valor incorreto pode causar vários códigos de estado HTTP 4xx, se o back-end espera algo diferente. Se inserir o endereço IP do seu back-end, poderá ter de definir o _cabeçalho de anfitrião de back-end_ para o nome de anfitrião de back-end.


3. Verifique as definições de regra de encaminhamento
     - Navegue para a regra de encaminhamento que deve encaminhar a partir do nome de anfitrião de front-end em questão para um conjunto de back-end. Certifique-se de que os protocolos aceites estão corretamente configurados, ou se não estiver, certifique-se de que o protocolo, que porta da frente irá utilizar quando o pedido de reencaminhamento está corretamente configurado. O _aceites protocolos_ determina que solicita a porta de entrada deverá aceitar e o _reencaminhamento protocolo_ sob o _avançadas_ separador determina o que a porta de entrada de protocolo deve utilizar para reencaminhar o pedido para o back-end.
          - Por exemplo, se o back-end só aceita pedidos de HTTP as seguintes configurações será válidas:
               - _Aceite protocolos_ são HTTP e HTTPS. _Protocolo de reencaminhamento_ é HTTP. Pedido de correspondência não irá funcionar, uma vez que o HTTPS é um protocolo permitido e se um pedido foi HTTPS, porta da frente tentaria reencaminhá-lo através de HTTPS.

               - _Aceite protocolos_ são HTTP. _Protocolo de reencaminhamento_ é corresponder a um pedido ou HTTPS.

   - Clique nas _avançadas_ separador na parte superior do painel de configuração de regra de encaminhamento. _URL Rewrite_ está desativada por predefinição e só deve utilizar este campo se quiser restringir o âmbito de recursos de back-end alojado de mensagens em fila que pretende tornar disponível. Quando desativada, a porta da frente irá reencaminhar o mesmo caminho de pedido que recebe. É possível que este campo está mal configurado e a porta de entrada está a pedir um recurso de back-end que não está disponível, assim, retornando um código de estado HTTP 404.


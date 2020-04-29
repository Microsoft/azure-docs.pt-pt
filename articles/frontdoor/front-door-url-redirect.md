---
title: Porta da Frente Azure - URL Redirecionamento / Microsoft Docs
description: Este artigo ajuda-o a entender como a Porta Frontal Azure suporta a redirecção de URL para as suas rotas, se configurado.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295480"
---
# <a name="url-redirect"></a>Redirecionamento de URL
Pode usar a Porta frontal azure para redirecionar o tráfego. Pode redirecionar o tráfego a vários níveis (protocolo, nome de anfitrião, caminho, corda de consulta) e toda a funcionalidade pode ser configurada para microserviços individuais, uma vez que a reorientação é baseada na trajetória. Isto simplifica a configuração da aplicação, otimiza o uso de recursos e suporta novos cenários de reorientação, incluindo a reorientação global e baseada em caminhos.
</br>

![Redirecionamento de URL da porta da frente azure][1]

## <a name="redirection-types"></a>Tipos de reorientação
Um tipo de redirecionamento define o código de estado de resposta para que os clientes compreendam o propósito do redirecionamento. São apoiados os seguintes tipos de reorientação:

- **301 (Movido permanentemente)**: Indica que o recurso-alvo foi atribuído a um novo URI permanente e quaisquer referências futuras a este recurso devem utilizar uma das URIs fechadas. Utilize o código de estado 301 para http para a reorientação HTTPS. 
- **302 (Encontrado)**: Indica que o recurso-alvo reside temporariamente sob um URI diferente. Uma vez que a reorientação pode ser alterada ocasionalmente, o cliente deve continuar a utilizar o pedido eficaz URI para futuros pedidos.
- **307 (Redirecionamento temporário)**: Indica que o recurso-alvo reside temporariamente sob um URI diferente e o agente utilizador NÃO DEVE alterar o método de pedido se realizar uma reorientação automática para esse URI. Uma vez que a reorientação pode mudar ao longo do tempo, o cliente deve continuar a utilizar o pedido original e eficaz URI para futuros pedidos.
- **308 (Redirecionamento Permanente)**: Indica que o recurso-alvo foi atribuído a um novo URI permanente e quaisquer referências futuras a este recurso devem utilizar uma das URIs fechadas. Os clientes com capacidades de edição de link, devem religar automaticamente as referências ao pedido eficaz URI a uma ou mais das novas referências enviadas pelo servidor, sempre que possível.

## <a name="redirection-protocol"></a>Protocolo de reorientação
Pode definir o protocolo que será usado para reorientação. Isto permite um dos casos de utilização mais comuns de recurso de redirecionamento, ou seja, definir HTTP para redirecionamento HTTPS.

- **HTTPS apenas:** Desloque o protocolo apenas para HTTPS, se estiver a tentar redirecionar o tráfego de HTTP para HTTPS. A Porta Frontal Azure recomenda que deva sempre definir a reorientação apenas para HTTPS.
- **Apenas HTTP**: Isto redireciona o pedido de entrada para HTTP. Utilize este valor apenas se quiser manter o seu tráfego HTTP que não esteja encriptado.
- **Pedido de jogo**: Esta opção mantém o protocolo utilizado pelo pedido de entrada. Assim, um pedido HTTP permanece HTTP e um pedido HTTPS permanece HTTPS post redirection.

## <a name="destination-host"></a>Anfitrião de destino
Como parte da configuração de um encaminhamento de redirecionamento, também pode alterar o nome de anfitrião ou domínio para o pedido de redirecionamento. Pode definir este campo para alterar o nome de anfitrião no URL para a reorientação ou, de outra forma, preservar o nome de anfitrião do pedido de entrada. Assim, usando este campo pode redirecionar todos `https://www.contoso.com/*` `https://www.fabrikam.com/*`os pedidos enviados para .

## <a name="destination-path"></a>Caminho de destino
Para casos em que pretende substituir o segmento de percurso de um URL como parte da reorientação, pode definir este campo com o novo valor de percurso. Caso contrário, pode optar por preservar o valor do percurso como parte do redirecionamento. Assim, usando este campo, pode redirecionar todos `https://www.contoso.com/\*` `https://www.contoso.com/redirected-site`os pedidos enviados para .

## <a name="query-string-parameters"></a>Parâmetros de corda de consulta
Também pode substituir os parâmetros de cadeia de consulta no URL redirecionado. Para substituir qualquer cadeia de consulta existente do URL de pedido de entrada, delineie este campo para 'Substituir' e, em seguida, definir o valor apropriado. Caso contrário, pode reter o conjunto original de cordas de consulta, definindo o campo para 'Preservar'. Como exemplo, utilizando este campo, pode redirecionar `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`todo o tráfego enviado para . 

## <a name="destination-fragment"></a>Fragmento de destino
O fragmento de destino é a porção de URL após '#', normalmente usado pelos navegadores para aterrar numa secção específica numa página. Pode definir este campo para adicionar um fragmento ao URL de redirecionamento.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png
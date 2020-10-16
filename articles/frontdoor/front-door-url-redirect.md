---
title: Porta frontal Azure - REDIRECIONamento de URL / Microsoft Docs
description: Este artigo ajuda-o a entender como a Porta Frontal Azure suporta a reorientação de URL para as suas regras de encaminhamento.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 61077c7900530fd4c5be64054bedd9c5d087fe77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442060"
---
# <a name="url-redirect"></a>Redirecionamento de URL
A porta frontal Azure pode redirecionar o tráfego em cada um dos seguintes níveis: protocolo, nome de anfitrião, caminho, cadeia de consulta. Estas funcionalidades podem ser configuradas para microserviços individuais, uma vez que a reorientação é baseada em caminhos. Isto pode simplificar a configuração da aplicação otimizando o uso de recursos, e suporta novos cenários de redirecionamento, incluindo a reorientação global e baseada em caminhos.
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="Redirecionamento de URL de porta frontal Azure":::

## <a name="redirection-types"></a>Tipos de redirecionamento
Um tipo de redirecionamento define o código de estado de resposta para que os clientes compreendam o propósito do redirecionamento. São suportados os seguintes tipos de reorientação:

- **301 (Movido permanentemente)**: Indica que o recurso-alvo foi atribuído a um novo URI permanente. Quaisquer referências futuras a este recurso utilizarão uma das URIs fechadas. Utilize o código de estado 301 para https para reorientação HTTPS. 
- **302 (Encontrado)**: Indica que o recurso-alvo está temporariamente sob um URI diferente. Uma vez que a reorientação pode ser alterada ocasionalmente, o cliente deve continuar a utilizar o pedido eficaz URI para pedidos futuros.
- **307 (Reorientação temporária)**: Indica que o recurso-alvo se encontra temporariamente sob um URI diferente. O agente utilizador NÃO DEVE alterar o método de pedido se fizer uma reorientação automática para esse URI. Uma vez que a reorientação pode mudar ao longo do tempo, o cliente deve continuar a usar o pedido original efetivo URI para pedidos futuros.
- **308 (Reorientação permanente)**: Indica que o recurso-alvo foi atribuído a um novo URI permanente. Quaisquer referências futuras a este recurso devem utilizar uma das URIs incluídas.

## <a name="redirection-protocol"></a>Protocolo de redirecionamento
Pode definir o protocolo que será usado para reorientação. Os casos de utilização mais comuns da funcionalidade de redirecionamento é definir HTTP para reorientação HTTPS.

- **HTTPS apenas**: Desa esta medida apenas para HTTPS, se estiver a procurar redirecionar o tráfego de HTTP para HTTPS. A porta frontal Azure recomenda que sempre desa um pouco o redireccionamento para HTTPS.
- **HTTP apenas**: Redireciona o pedido de entrada para HTTP. Utilize este valor apenas se pretender manter o seu tráfego HTTP que, ou seja, não encriptado.
- **Pedido de jogo**: Esta opção mantém o protocolo utilizado pelo pedido de entrada. Assim, um pedido HTTP permanece HTTP E um pedido HTTPS permanece https post redirection.

## <a name="destination-host"></a>Anfitrião do destino
Como parte da configuração de um encaminhamento de redirecionamento, também pode alterar o nome ou domínio do anfitrião para o pedido de redirecionamento. Pode definir este campo para alterar o nome de anfitrião no URL para a reorientação ou preservar o nome de anfitrião a partir do pedido de entrada. Assim, usando este campo, pode redirecionar todos os pedidos enviados `https://www.contoso.com/*` para `https://www.fabrikam.com/*` .

## <a name="destination-path"></a>Caminho de destino
Para os casos em que pretende substituir o segmento de caminho de um URL como parte da reorientação, pode definir este campo com o novo valor do caminho. Caso contrário, pode optar por preservar o valor do caminho como parte do redirecionamento. Assim, usando este campo, você pode redirecionar todos os pedidos enviados `https://www.contoso.com/\*` para  `https://www.contoso.com/redirected-site` .

## <a name="query-string-parameters"></a>Parâmetros de cadeia de consulta
Também pode substituir os parâmetros de cadeia de consulta no URL redirecionado. Para substituir qualquer cadeia de consulta existente do URL de pedido de entrada, desaprote este campo para 'Substituir' e, em seguida, desaprote o valor adequado. Caso contrário, pode manter o conjunto original de cordas de consulta definindo o campo para 'Preservar'. Como exemplo, utilizando este campo, pode redirecionar todo o tráfego enviado `https://www.contoso.com/foo/bar` para `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

## <a name="destination-fragment"></a>Fragmento de destino
O fragmento de destino é a parte de URL após '#', que é usado pelo navegador para pousar em uma secção específica de uma página web. Pode definir este campo para adicionar um fragmento ao URL de redirecionamento.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).

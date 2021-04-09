---
title: Reorientação de URL e reescrita de URL com Azure Front Door Standard/Premium (Pré-visualização)
description: Este artigo ajuda-o a entender como a Porta Frontal Azure suporta a reorientação de URL e a reescrita de URL usando O Conjunto de Regras da Porta Frontal Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 382a4c040c7a519462ee3e35119b9471031e0724
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100005"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>Reorientação de URL e reescrita de URL com Azure Front Door Standard/Premium (Pré-visualização)

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Este artigo ajuda-o a entender como o Azure Front Door Standard/Premium suporta o redirecionamento de URL e a reescrita de URL usada num Conjunto de Regras.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="url-redirect"></a>Redirecionamento de URL

A porta frontal azul pode redirecionar o tráfego em cada um dos seguintes níveis: protocolo, nome de hospedeiro, caminho, cadeia de consulta e fragmento. Estas funcionalidades podem ser configuradas para micro-serviço individual, uma vez que a reorientação é baseada em caminhos. Com o reorientador de URL, pode simplificar a configuração da aplicação otimizando o uso de recursos e suporta novos cenários de reorientação, incluindo a reorientação global e baseada no caminho.

Pode configurar o redirecionamento de URL através do Conjunto de Regras.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="Screenshot de criar redirecionamento de url com conjunto de regras." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>Tipos de redirecionamento
Um tipo de redirecionamento define o código de estado de resposta para que os clientes compreendam o propósito do redirecionamento. São suportados os seguintes tipos de reorientação:

* **301 (Movido permanentemente)**: Indica que o recurso-alvo foi atribuído a um novo URI permanente. Quaisquer referências futuras a este recurso utilizarão uma das URIs fechadas. Utilize o código de estado 301 para https para reorientação HTTPS.
* **302 (Encontrado)**: Indica que o recurso-alvo está temporariamente sob um URI diferente. Uma vez que a reorientação pode ser alterada ocasionalmente, o cliente deve continuar a utilizar o pedido eficaz URI para pedidos futuros.
* **307 (Reorientação temporária)**: Indica que o recurso-alvo se encontra temporariamente sob um URI diferente. O agente utilizador NÃO DEVE alterar o método de pedido se fizer uma reorientação automática para esse URI. Uma vez que a reorientação pode mudar ao longo do tempo, o cliente deve continuar a usar o pedido original efetivo URI para pedidos futuros.
* **308 (Reorientação permanente)**: Indica que o recurso-alvo foi atribuído a um novo URI permanente. Quaisquer referências futuras a este recurso devem utilizar uma das URIs incluídas.

### <a name="redirection-protocol"></a>Protocolo de redirecionamento
Pode definir o protocolo que será usado para reorientação. Os casos de utilização mais comuns da funcionalidade de redirecionamento é definir HTTP para reorientação HTTPS.

* **HTTPS apenas**: Desa esta medida apenas para HTTPS, se estiver a procurar redirecionar o tráfego de HTTP para HTTPS. A porta frontal Azure recomenda que sempre desa um pouco o redireccionamento para HTTPS.
* **HTTP apenas**: Redireciona o pedido de entrada para HTTP. Utilize este valor apenas se pretender manter o seu tráfego HTTP que, ou seja, não encriptado.
* **Pedido de jogo**: Esta opção mantém o protocolo utilizado pelo pedido de entrada. Assim, um pedido HTTP permanece HTTP E um pedido HTTPS permanece https post redirection.

### <a name="destination-host"></a>Anfitrião do destino
Como parte da configuração de um encaminhamento de redirecionamento, também pode alterar o nome ou domínio do anfitrião para o pedido de redirecionamento. Pode definir este campo para alterar o nome de anfitrião no URL para a reorientação ou preservar o nome de anfitrião a partir do pedido de entrada. Assim, usando este campo, pode redirecionar todos os pedidos enviados `https://www.contoso.com/*` para `https://www.fabrikam.com/*` .

### <a name="destination-path"></a>Caminho de destino
Para os casos em que pretende substituir o segmento de caminho de um URL como parte da reorientação, pode definir este campo com o novo valor do caminho. Caso contrário, pode optar por preservar o valor do caminho como parte do redirecionamento. Assim, usando este campo, você pode redirecionar todos os pedidos enviados `https://www.contoso.com/\*` para  `https://www.contoso.com/redirected-site` .

### <a name="query-string-parameters"></a>Parâmetros de cadeia de consulta
Também pode substituir os parâmetros de cadeia de consulta no URL redirecionado. Para substituir qualquer cadeia de consulta existente do URL de pedido de entrada, desaprote este campo para 'Substituir' e, em seguida, desaprote o valor adequado. Caso contrário, pode manter o conjunto original de cordas de consulta definindo o campo para 'Preservar'. Como exemplo, utilizando este campo, pode redirecionar todo o tráfego enviado `https://www.contoso.com/foo/bar` para `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

### <a name="destination-fragment"></a>Fragmento de destino
O fragmento de destino é a parte de URL após '#', que é usado pelo navegador para pousar em uma secção específica de uma página web. Pode definir este campo para adicionar um fragmento ao URL de redirecionamento.

## <a name="url-rewrite"></a>Reescrever URL

Azure Front Door suporta a reescrita de URL para reescrever o caminho de um pedido que está a caminho da sua origem. A reescrita de URL permite-lhe adicionar condições para garantir que o URL ou os cabeçalhos especificados só sejam reescritos quando determinadas condições são satisfeitas. Estas condições baseiam-se nas informações do pedido e da resposta.

Com esta funcionalidade, é possível redirecionar os utilizadores para diferentes origens com base no cenário, tipo de dispositivo e tipo de ficheiro solicitado.

Pode configurar o redirecionamento de URL através do Conjunto de Regras.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="Screenshot de criar url reescrita com Conjunto de Regras." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>Padrão de origem

O padrão de origem é o caminho URL no pedido de origem para substituir. Atualmente, o padrão de origem usa uma correspondência baseada em prefixos. Para combinar todos os caminhos de URL, utilize um corte para a frente (/) como o valor do padrão de origem.

### <a name="destination"></a>Destino

Pode definir o caminho de destino a utilizar na reescrita. O caminho de destino substitui o padrão de origem.

### <a name="preserve-unmatched-path"></a>Preservar caminho inigualável

Preservar um caminho inigualável permite-lhe anexar o caminho restante após o padrão de origem para o novo caminho.

Por exemplo, se eu definir **Preservar caminho inigualável para Sim**.
* Se o pedido de entrada for `www.contoso.com/sub/1.jpg` , o padrão de origem é definido para `/` , o destino é definido para , e o conteúdo é servido a partir de `/foo/` .jpg da `/foo/sub/1` origem.

* Se o pedido de entrada for `www.contoso.com/sub/image/1.jpg` , o padrão de origem é definido para `/sub/` , o destino é definido para , o conteúdo é servido a partir da `/foo/` `/foo/image/1.jpg` origem.

Por exemplo, se eu definir **preservar um caminho inigualável para o nº**.
* Se o pedido de entrada for `www.contoso.com/sub/image/1.jpg` , o padrão de origem é definido para `/sub/` , o destino é definido para , o conteúdo será sempre servido a partir da `/foo/2.jpg` `/foo/2.jpg` origem, independentemente dos caminhos seguidos em `wwww.contoso.com/sub/` .

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o conjunto de regras Standard/Premium Da porta dianteira do Azure.](concept-rule-set.md)

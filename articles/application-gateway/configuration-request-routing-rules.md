---
title: Azure Application Gateway solicita a configuração das regras de encaminhamento
description: Este artigo descreve como configurar as regras de encaminhamento do Gateway de aplicação Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 047ce9b33836e2c23a37b1383942323d7c382485
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397540"
---
# <a name="application-gateway-request-routing-rules"></a>Aplicação Gateway solicita regras de encaminhamento

Quando cria um gateway de aplicação utilizando o portal Azure, cria uma regra padrão *(regra 1).* Esta regra liga o ouvinte predefinido *(appGatewayHttpListener*) com o pool back-end padrão *(appGatewayBackendPool*) e as definições HTTP de back-end predefinidos *(appGatewayBackendHttpSettings).* Depois de criar o gateway, pode editar as definições da regra padrão ou criar novas regras.

## <a name="rule-type"></a>Tipo de regra

Quando se cria uma regra, [ *escolhe-se* entre o *básico* e o caminho.](./application-gateway-components.md#request-routing-rules)

- Escolha o básico se quiser encaminhar todos os pedidos no ouvinte associado (por exemplo, *blog <i></i> .contoso.com/ \* )* para um único pool back-end.
- Escolha o caminho baseado se quiser encaminhar pedidos de caminhos de URL específicos para piscinas específicas de back-end. O padrão do caminho é aplicado apenas ao caminho do URL, não aos seus parâmetros de consulta.

### <a name="order-of-processing-rules"></a>Ordem das regras de processamento

Para o V1 e v2 SKU, a correspondência de padrões dos pedidos de entrada é processada na ordem em que os caminhos estão listados no mapa do caminho URL da regra baseada no caminho. Se um pedido corresponder ao padrão em dois ou mais caminhos no mapa do caminho, o caminho que está listado primeiro é combinado. E o pedido é reencaminhado para a parte de trás que está associado a esse caminho.

## <a name="associated-listener"></a>Ouvinte associado

Associar um ouvinte à regra de modo que a *regra de encaminhamento* de pedidos que está associada ao ouvinte seja avaliada para determinar o pool back-end para encaminhar o pedido para.

## <a name="associated-back-end-pool"></a>Piscina traseira associada

Associar à regra o pool back-end que contém os alvos de back-end que servem os pedidos que o ouvinte recebe.

 - Para uma regra básica, apenas uma piscina traseira é permitida. Todos os pedidos no ouvinte associado são encaminhados para a piscina de back-end.

 - Para uma regra baseada em caminhos, adicione várias piscinas de back-end que correspondem a cada caminho URL. Os pedidos que correspondem ao caminho URL que está inserido são reencaminhados para a piscina traseira correspondente. Além disso, adicione uma piscina traseira padrão. Os pedidos que não correspondam a qualquer caminho URL na regra são reencaminhados para essa piscina.

## <a name="associated-back-end-http-setting"></a>Definição HTTP de back-end associada

Adicione uma definição HTTP de back-end para cada regra. Os pedidos são encaminhados do gateway de aplicação para os alvos de back-end usando o número de porta, protocolo e outras informações especificadas nesta definição.

Para uma regra básica, apenas é permitida uma definição HTTP de back-end. Todos os pedidos no ouvinte associado são reencaminhados para os alvos de back-end correspondentes utilizando esta definição HTTP.

Para uma regra baseada em caminhos, adicione várias definições HTTP de back-end que correspondem a cada caminho URL. Os pedidos que correspondam ao caminho do URL nesta definição são reencaminhados para os alvos de back-end correspondentes, utilizando as definições HTTP que correspondem a cada caminho URL. Além disso, adicione uma definição HTTP predefinida. Os pedidos que não correspondam a qualquer caminho URL nesta regra são reencaminhados para o pool de back-end padrão utilizando a definição HTTP predefinida.

## <a name="redirection-setting"></a>Definição de redirecionamento

Se a reorientação for configurada para uma regra básica, todos os pedidos no ouvinte associado são redirecionados para o alvo. Isto é reorientação *global.* Se a reorientação for configurada para uma regra baseada em caminhos, apenas os pedidos numa área específica do site são redirecionados. Um exemplo é uma área de carrinhos de compras que é denotada por */carrinho/ \**. Esta é a reorientação *baseada no caminho.*

Para obter mais informações sobre redirecionamentos, consulte [a visão geral do redirecionamento do Gateway de Aplicação](redirect-overview.md).

### <a name="redirection-type"></a>Tipo de redirecionamento

Escolha o tipo de reorientação necessária: *Permanente(301)*, *Temporário(307)*, *Encontrado(302)* ou *Ver outro(303)*.

### <a name="redirection-target"></a>Alvo de redirecionamento

Escolha outro ouvinte ou um site externo como alvo de redirecionamento.

#### <a name="listener"></a>Serviço de Escuta

Escolha o ouvinte como o alvo de redirecionamento para redirecionar o tráfego de um ouvinte para outro no gateway. Esta definição é necessária quando pretende ativar a reorientação HTTP-to-HTTPS. Redireciona o tráfego do ouvinte de origem que verifica os pedidos http recebidos para o ouvinte de destino que verifica os pedidos https recebidos. Também pode optar por incluir a cadeia de consulta e o caminho a partir do pedido original no pedido que é reencaminhado para o alvo de redirecionamento.

![Caixa de diálogo de componentes do Gateway de aplicação](./media/configuration-overview/configure-redirection.png)

Para obter mais informações sobre a reorientação HTTP-to-HTTPS, consulte:
- [Reorientação HTTP-to-HTTPS utilizando o portal Azure](redirect-http-to-https-portal.md)
- [Reorientação HTTP-to-HTTPS utilizando o PowerShell](redirect-http-to-https-powershell.md)
- [Reorientação HTTP-to-HTTPS utilizando o Azure CLI](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>Site externo

Escolha o site externo quando pretender redirecionar o tráfego no ouvinte que está associado a esta regra para um site externo. Pode optar por incluir a cadeia de consulta do pedido original no pedido que é reencaminhado para o alvo de redirecionamento. Não se pode encaminhar o caminho para o site externo que estava no pedido original.

Para obter mais informações sobre a reorientação, consulte:
- [Redirecione o tráfego para um site externo utilizando o PowerShell](redirect-external-site-powershell.md)
- [Redirecione o tráfego para um site externo utilizando o CLI](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>Rescrever cabeçalhos HTTP e URL

Ao utilizar regras de reescrita, pode adicionar, remover ou atualizar os cabeçalhos de pedido e resposta HTTP(S), bem como os parâmetros de trajetória e de consulta de URL, à medida que os pacotes de pedido e resposta se movem entre o cliente e as piscinas de backend através do gateway de aplicação.

Os cabeçalhos e parâmetros URL podem ser definidos para valores estáticos ou para outros cabeçalhos e variáveis do servidor. Isto ajuda com casos importantes de utilização, tais como a extração de endereços IP do cliente, a remoção de informações sensíveis sobre o backend, adicionando mais segurança, e assim por diante.
Para obter mais informações, consulte:

 - [Reescrever cabeçalhos HTTP e visão geral do URL](rewrite-http-headers-url.md)
 - [Configure HTTP reescrita cabeçalho HTTP](rewrite-http-headers-portal.md)
 - [Configure URL reescrever](rewrite-url-portal.md)

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre as definições http](configuration-http-settings.md)
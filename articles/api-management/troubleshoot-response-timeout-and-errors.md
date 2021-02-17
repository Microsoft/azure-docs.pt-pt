---
title: Resolução de problemas de tempo de resposta do cliente e erros com a API Management
description: Resolução de problemas erros de ligação intermitentes e problemas de latência relacionados na Gestão da API
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 6cace4a02c8d45cacbbc34e9778b5c4a78ada27f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576529"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>Resolução de problemas de tempo de resposta do cliente e erros com a API Management

Este artigo ajuda-o a resolver erros de conexão intermitentes e problemas de latência relacionados na [Azure API Management](./api-management-key-concepts.md). Especificamente, este artigo fornecerá informações e resolução de problemas para o esgotamento das portas de tradução da rede de endereços de origem (SNAT). Se necessitar de mais ajuda, contacte os peritos da [Azure community Support](https://azure.microsoft.com/support/community/) ou apresente um pedido de apoio ao Suporte [Azure](https://azure.microsoft.com/support/options/).

## <a name="symptoms"></a>Sintomas

As aplicações de clientes que ligam para APIs através do seu serviço de Gestão de API (APIM) podem apresentar um ou mais dos seguintes sintomas:

* Erros intermitentes HTTP 500
* Mensagens de erro de tempo limite

Estes sintomas manifestam-se como instâncias dos registos de recursos do `BackendConnectionFailure` [Monitor Azure](../azure-monitor/essentials/resource-logs.md).

## <a name="cause"></a>Causa

Este padrão de sintomas ocorre frequentemente devido aos limites de tradução de endereços de rede (SNAT) com o seu serviço APIM.

Sempre que um cliente liga para uma das suas API APIs, o serviço de Gestão API da Azure abre uma porta SNAT para aceder à sua API de backend. Como discutido nas [ligações outbound em Azure,](../load-balancer/load-balancer-outbound-connections.md)a Azure utiliza tradução de endereços de rede de origem (SNAT) e um Balancer de Carga (não exposto aos [clientes)](../virtual-network/virtual-network-service-endpoints-overview.md)para comunicar com pontos finais fora de Azure no espaço de endereço IP público, bem como pontos finais internos para a Azure que não estão a usar pontos finais de serviço de Rede Virtual . Esta situação só é aplicável às APIs de backend expostas em IPs públicos.

Cada instância do serviço de Gestão da API recebe inicialmente um número pré-atribuído de portas SNAT. Este limite afeta a abertura das ligações ao mesmo hospedeiro e à combinação de porta. As portas SNAT são usadas quando tem chamadas repetidas para o mesmo endereço e combinação de portas. Uma vez que uma porta SNAT tenha sido liberada, a porta está disponível para reutilização conforme necessário. O equilibrador de carga da Rede Azure recupera as portas SNAT de ligações fechadas apenas após quatro minutos de espera.

Uma rápida sucessão de pedidos de clientes às suas APIs pode esgotar a quota pré-atribuída das portas SNAT se estas portas não forem fechadas e recicladas rapidamente o suficiente, impedindo o seu serviço APIM de processar os pedidos do cliente em tempo útil.

## <a name="mitigations-and-solutions"></a>Mitigações e soluções

Resolver o problema da exaustão portuária SNAT requer primeiro o diagnóstico e otimização do desempenho dos seus serviços de backend.

As estratégias gerais para mitigar a exaustão do porto SNAT são discutidas na [resolução de problemas de ligações de saída falhas](../load-balancer/troubleshoot-outbound-connection.md) da documentação do *Azure Load Balancer.* Destas estratégias, as seguintes são aplicáveis à API Management.

### <a name="scale-your-apim-instance"></a>Dimensione o seu exemplo APIM

Cada instância de Gestão da API é atribuída a uma série de portas SNAT, com base em unidades APIM. Pode alocar portas SNAT adicionais escalando a sua instância de Gestão de API com unidades adicionais. Para mais informações, consulte [Scale your API Management service](upgrade-and-scale.md#scale-your-api-management-service)

> [!NOTE]
> A utilização do porto SNAT não está atualmente disponível como métrica para unidades de Gestão de API autoscalantes.

### <a name="use-multiple-ips-for-your-backend-urls"></a>Use vários IPs para os urls de backend

Cada ligação desde a sua instância APIM até ao mesmo destino IP e porto de destino do seu serviço de backend utilizará uma porta SNAT, de forma a manter um fluxo de tráfego distinto. Sem diferentes portas SNAT para o tráfego de retorno do seu serviço de fundo, a APIM não teria forma de separar uma resposta de outra.

Como as portas SNAT podem ser reutilizadas se o destino IP ou a porta de destino forem diferentes, outra forma de evitar a exaustão da porta SNAT é usando vários IPs para o seu serviço de backend URLs.

Para mais informações, consulte [o proxy Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md).

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>Coloque o seu serviço apim e backend no mesmo VNet

Se a sua API de backend estiver hospedada num serviço Azure que suporta *pontos finais* de serviço como o Serviço de Aplicações, pode evitar problemas de exaustão por porta SNAT colocando o seu serviço de backend APIM na mesma rede virtual e expondo-o através [de pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço ou [pontos finais privados.](../private-link/private-endpoint-overview.md) Quando utiliza um VNet comum e coloca pontos finais de serviço na sub-rede de integração, o tráfego de saída da sua instância APIM para esses serviços contorna a internet, evitando assim as restrições de porta SNAT. Da mesma forma, se utilizar um VNet e pontos finais privados, não terá quaisquer problemas de saída da porta SNAT para esse destino.

Para mais detalhes, consulte [Como utilizar a Azure API Management com redes virtuais](api-management-using-with-vnet.md) e integrar o Serviço de [Aplicações com uma rede virtual Azure.](../app-service/web-sites-integrate-with-vnet.md)

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>Coloque o seu APIM numa rede virtual e encaminhe as chamadas para a Azure Firewall

Tal como colocar os seus serviços APIM e backend numa rede virtual, pode empregar a Azure Firewall num VNet com o seu serviço APIM e, em seguida, encaminhar chamadas APIM para Azure Firewall. Entre a APIM e a Azure Firewall (estando no mesmo VNet), não são necessárias portas SNAT. Para ligações SNAT aos seus serviços de backend, o Azure Firewall tem 64.000 portas disponíveis, um valor muito superior ao que é atribuído a instâncias APIM.

Consulte a documentação [do Azure Firewall](../firewall/overview.md) para mais informações.

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>Considere o caching de resposta e outras afinações de desempenho de backend

Outra potencial mitigação a considerar é melhorar os tempos de processamento para as APIs do seu backend. Uma forma de o fazer é configurar certas APIs com resposta para reduzir a latência entre aplicações de clientes que ligam para a sua API e a sua carga de backend APIM.

Para mais informações, consulte [o Add caching para melhorar o desempenho na Azure API Management](api-management-howto-cache.md).

### <a name="consider-implementing-access-restriction-policies"></a>Considere implementar políticas de restrição de acesso

Se fizer sentido para o seu cenário de negócio, pode implementar políticas de restrição de acesso para o seu produto API Management. Por exemplo, a `rate-limit-by-key` política pode ser utilizada para evitar picos de utilização da API numa base por chave, limitando a taxa de chamada por um período de tempo especificado.

Consulte [as políticas de restrição de acesso da API Management](api-management-access-restriction-policies.md) para obter mais informações.

## <a name="see-also"></a>Ver também

* [Balançador de carga Azure: Falhas nas ligações de saída de resolução de problemas](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service: Resolução de problemas de erros de ligação intermitentes de saída](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)

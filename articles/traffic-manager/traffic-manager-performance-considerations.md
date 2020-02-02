---
title: Considerações de desempenho para O Gestor de Tráfego do Azure Microsoft Docs
description: Compreenda o desempenho no Traffic Manager e como testar o desempenho do seu website ao utilizar o Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 84367a00643c48e7fe2fb7f907bab64589193b2e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938547"
---
# <a name="performance-considerations-for-traffic-manager"></a>Considerações de desempenho para o Gestor de Tráfego

Esta página explica considerações de desempenho usando o Traffic Manager. Considere o seguinte cenário:

Tem casos do seu website nas regiões da WestUS e da Ásia Oriental. Um dos casos é falhar no exame de saúde da sonda do gestor de tráfego. O tráfego de aplicações é direcionado para a região saudável. Esta falha é esperada, mas o desempenho pode ser um problema baseado na latência do tráfego que agora viaja para uma região distante.

## <a name="performance-considerations-for-traffic-manager"></a>Considerações de desempenho para o Gestor de Tráfego

O único impacto de desempenho que o Traffic Manager pode ter no seu website é a procura inicial do DNS. Um pedido dNS para o nome do seu perfil de Traffic Manager é tratado pelo servidor raiz DoDNS da Microsoft que acolhe a zona de trafficmanager.net. O Traffic Manager povoa e atualiza regularmente os servidores de raiz DNS da Microsoft com base na política do Traffic Manager e nos resultados da sonda. Assim, mesmo durante a investigação inicial do DNS, nenhuma consulta de DNS é enviada para o Traffic Manager.

O Traffic Manager é composto por vários componentes: servidores de nome DNS, um serviço API, a camada de armazenamento e um serviço de monitorização de ponto final. Se um componente de serviço do Gestor de Tráfego falhar, não há qualquer efeito no nome DNS associado ao seu perfil de Gestor de Tráfego. Os registos nos servidores DNS da Microsoft permanecem inalterados. No entanto, a monitorização do ponto final e a atualização do DNS não acontecem. Portanto, o Traffic Manager não é capaz de atualizar o DNS para apontar para o seu site de falha quando o seu site principal se desaponte.

A resolução de nomes DNS é rápida e os resultados estão em cache. A velocidade da procura inicial do DNS depende dos servidores DNS que o cliente utiliza para resolução de nomes. Normalmente, um cliente pode completar uma procura de DNS dentro de ~50 ms. Os resultados da procura são em cache durante a duração do DNS Time-to-live (TTL). O TTL padrão para o Gestor de Tráfego é de 300 segundos.

O tráfego NÃO flui através do Traffic Manager. Uma vez que a procura dNS esteja concluída, o cliente tem um endereço IP para uma instância do seu site. O cliente liga-se diretamente a esse endereço e não passa pelo Traffic Manager. A política do Gestor de Tráfego que escolher não tem influência no desempenho do DNS. No entanto, um método de encaminhamento de desempenho pode impactar negativamente a experiência da aplicação. Por exemplo, se a sua política redireciona o tráfego da América do Norte para um exemplo hospedado na Ásia, a latência da rede para essas sessões pode ser um problema de desempenho.

## <a name="measuring-traffic-manager-performance"></a>Medir o desempenho do gestor de tráfego

Existem vários sites que pode usar para entender o desempenho e comportamento de um perfil do Gestor de Tráfego. Muitos destes sites são gratuitos, mas podem ter limitações. Alguns sites oferecem monitorização e relatórios melhorados por uma taxa.

As ferramentas nestes sites medem as lateências dNS e exibem os endereços IP resolvidos para locais de clientes em todo o mundo. A maioria destas ferramentas não cache os resultados do DNS. Portanto, as ferramentas mostram a procura completa do DNS cada vez que um teste é executado. Quando testa a partir do seu próprio cliente, apenas experimenta o desempenho completo da procura de DNS uma vez durante a duração do TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Ferramentas de amostra para medir o desempenho do DNS

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    O SolveDNS oferece muitas ferramentas de desempenho. A ferramenta DNS Compare pode mostrar-lhe quanto tempo demora a resolver o seu nome DNS e como isso se compara a outros prestadores de serviços DNS.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Uma das ferramentas mais simples é o WebSitePulse. Introduza o URL para ver o tempo de resolução do DNS, First Byte, Last Byte e outras estatísticas de desempenho. Pode escolher entre três locais de teste diferentes. Neste exemplo, vê-se que a primeira execução mostra que a procura de DNS demora 0,204 segundos.

    ![pulso1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Como os resultados estão em cache, o segundo teste para o mesmo ponto final do Gestor de Tráfego a procura do DNS demora 0,002 seg.

    ![pulso2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Monitor sintético da Aplicação CA](https://asm.ca.com/en/checkit.php)

    Anteriormente conhecido como a ferramenta Watch-mouse Check Website, este site mostra-lhe o tempo de resolução dNS de várias regiões geográficas simultaneamente. Introduza o URL para ver o tempo de resolução do DNS, o tempo de ligação e a velocidade de vários locais geográficos. Use este teste para ver qual o serviço hospedado que é devolvido para diferentes locais em todo o mundo.

    ![pulso1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    Esta ferramenta fornece estatísticas de desempenho para cada elemento de uma página web. O separador Análise de Página mostra a percentagem de tempo gasto na procura de DNS.

* [Qual é o meu DNS?](https://www.whatsmydns.net/)

    Este site faz uma procura dNS de 20 locais diferentes e exibe os resultados num mapa.

* [Dig Web Interface](https://www.digwebinterface.com)

    Este site mostra informações mais detalhadas do DNS, incluindo CNAMEs e registos A. Certifique-se de que verifica a saída de 'Colorize' e 'Stats' em opções e selecione 'All' em Nomeservers.

## <a name="next-steps"></a>Próximos Passos

[Sobre os métodos de encaminhamento de tráfego do Gestor de Tráfego](traffic-manager-routing-methods.md)

[Teste as definições do gestor de tráfego](traffic-manager-testing-settings.md)

[Operações do Gestor de Tráfego (Referência da API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gestor de Tráfego Azure](https://docs.microsoft.com/powershell/module/az.trafficmanager)


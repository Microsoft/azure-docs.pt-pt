---
title: Considerações de desempenho para o Gestor de tráfego do Azure | Documentos da Microsoft
description: Compreender o desempenho no Gestor de tráfego e de como testar o desempenho do seu Web site ao utilizar o Gestor de tráfego
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 85dd3cca081d492bfeefa3e8ea0d143c9c37af8f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007943"
---
# <a name="performance-considerations-for-traffic-manager"></a>Considerações de desempenho para o Gestor de Tráfego

Esta página explica as considerações de desempenho com o Gestor de tráfego. Considere o seguinte cenário:

Tem instâncias do seu Web site nas regiões WestUS e Sudeste asiático. Uma das instâncias está a falhar a verificação de estado de funcionamento para a sonda de Gestor de tráfego. Tráfego de aplicativo é direcionado para a região de bom estado de funcionamento. Espera-se que esta ativação pós-falha, mas o desempenho pode ser um problema com base na latência do tráfego agora em trânsito para uma região distante.

## <a name="performance-considerations-for-traffic-manager"></a>Considerações de desempenho para o Gestor de Tráfego

O impacto no desempenho apenas que o Gestor de tráfego pode ter no seu Web site é a pesquisa DNS inicial. Um pedido DNS para o nome do perfil do Gestor de tráfego é processado pelo servidor de raiz de DNS da Microsoft que aloja a zona trafficmanager.net. O Gestor de tráfego preenche e atualiza regularmente, servidores de raiz DNS da Microsoft com base na política de Gestor de tráfego e os resultados de pesquisa. Portanto, mesmo durante a pesquisa DNS inicial, não existem consultas DNS são enviadas para o Gestor de tráfego.

O Gestor de tráfego é constituído por vários componentes: Servidores, um serviço de API, a camada de armazenamento e um ponto final de serviço de monitorização de nomes DNS. Se um componente de serviço do Gestor de tráfego falhar, não há nenhum efeito sobre o nome DNS associado de perfil do Traffic Manager. Os registos nos servidores Microsoft DNS permanecem inalterados. No entanto, a monitorização de ponto final e a atualizar o DNS não acontecem. Por conseguinte, o Gestor de tráfego não é possível atualizar o DNS para apontar para o seu site de ativação pós-falha quando o site primário fica inativo.

Resolução de nomes DNS é rápida e os resultados são armazenados em cache. A velocidade da pesquisa de DNS inicial depende dos servidores DNS, que o cliente utiliza para resolução de nomes. Normalmente, um cliente pode realizar uma pesquisa de DNS dentro de ~ 50 ms. Os resultados da pesquisa são colocadas em cache durante o DNS Time-to-live (TTL). O Gestor de tráfego do TTL predefinido é de 300 segundos.

O tráfego não fluir através do Gestor de tráfego. Uma vez concluída a pesquisa de DNS, o cliente tem um endereço IP para uma instância do seu web site. O cliente liga-se diretamente a esse endereço e não é transmitido através do Gestor de tráfego. A política de Gestor de tráfego que escolher tem não influência sobre o desempenho de DNS. No entanto, um desempenho-método de encaminhamento de pode afetar negativamente a experiência de aplicação. Por exemplo, se a sua política redireciona o tráfego da América do Norte para uma instância alojada na Ásia, a latência de rede para essas sessões pode ser um problema de desempenho.

## <a name="measuring-traffic-manager-performance"></a>Avaliando o desempenho do Gestor de tráfego

Existem vários Web sites, que pode utilizar para compreender o desempenho e comportamento de um perfil do Gestor de tráfego. Muitos destes sites são gratuitos, mas podem ter limitações. Alguns sites oferecem aprimorada de monitorização e relatórios para uma taxa.

As ferramentas sobre esses sites medida DNS latências e apresentar os resolver endereços IP para localizações de clientes em todo o mundo. A maioria dessas ferramentas não colocar em cache os resultados DNS. Por conseguinte, as ferramentas de mostram a pesquisa DNS completa sempre que um teste é executado. Quando testa a partir do seu próprio cliente, tiver apenas o desempenho de pesquisa DNS completo uma vez durante a duração do TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Ferramentas de exemplo para medir o desempenho de DNS

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS oferece muitas ferramentas de desempenho. A ferramenta de comparação de DNS pode mostrar o tempo que demora para resolver o nome de DNS e como que se compara com outros fornecedores de serviços DNS.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Uma das ferramentas mais simples é WebSitePulse. Introduza o URL para ver o tempo de resolução DNS, o primeiro Byte, o último Byte e outras estatísticas de desempenho. Pode escolher entre três localizações de teste diferentes. Neste exemplo, verá que a primeira execução mostra que a pesquisa de DNS leva 0.204 seg.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Como os resultados são armazenados em cache, o segundo teste para o mesmo ponto de final do Gestor de tráfego a pesquisa DNS leva 0.002 seg.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Monitor de aplicação Synthetic de AC](https://asm.ca.com/en/checkit.php)

    Anteriormente conhecido como a ferramenta Web site de verificação de Watch mouse, este site mostram-lhe o tempo de resolução de DNS de várias regiões geográficas em simultâneo. Introduza o URL para ver o tempo de resolução DNS, o tempo de ligação e velocidade de várias localizações geográficas. Utilize este teste para ver qual serviço alojado é devolvido para diferentes localizações em todo o mundo.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    Essa ferramenta fornece estatísticas de desempenho para cada elemento de uma página da web. O separador de análise de página mostra a percentagem de tempo gasto em pesquisa de DNS.

* [O que é o meu DNS?](https://www.whatsmydns.net/)

    Este site realiza uma pesquisa DNS a partir de 20 diferentes locais e exibe os resultados num mapa.

* [Aprofundar a Interface da Web](https://www.digwebinterface.com)

    Este site mostra que informações de DNS, incluindo CNAMEs e registros mais detalhadas. Certifique-se de que verifique a "Colorize output" e "Estatísticas" em Opções e selecione "Tudo" em servidores de nomes.

## <a name="next-steps"></a>Próximos Passos

[Sobre métodos de encaminhamento de tráfego do Gestor de tráfego](traffic-manager-routing-methods.md)

[Testar as definições do Gestor de tráfego](traffic-manager-testing-settings.md)

[Operações do Gestor de Tráfego (Referência da API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gestor de tráfego do Azure](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager)


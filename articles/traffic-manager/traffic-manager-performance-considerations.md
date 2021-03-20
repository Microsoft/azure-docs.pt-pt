---
title: Considerações de desempenho para a Azure Traffic Manager | Microsoft Docs
description: Compreenda o desempenho no Traffic Manager e como testar o desempenho do seu website ao utilizar o Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 5a0de772598877029649f8a985c79e28f5264535
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98186180"
---
# <a name="performance-considerations-for-traffic-manager"></a>Considerações de desempenho para o Gestor de Tráfego

Esta página explica considerações de desempenho usando o Traffic Manager. Considere o seguinte cenário:

Tem casos do seu website nas regiões westus e eastasia. Um dos casos é falhar o exame de saúde para a sonda do gerente de tráfego. O tráfego de aplicações é direcionado para a região saudável. Esta falha é esperada, mas o desempenho pode ser um problema baseado na latência do tráfego que agora viaja para uma região distante.

## <a name="performance-considerations-for-traffic-manager"></a>Considerações de desempenho para o Gestor de Tráfego

O único impacto de desempenho que o Traffic Manager pode ter no seu website é a primeira procura de DNS. Um pedido DNS para o nome do seu perfil de Gestor de Tráfego é tratado pelo servidor raiz do Microsoft DNS que acolhe a zona trafficmanager.net. O Gestor de Tráfego povoa e atualiza regularmente os servidores de raiz DNS da Microsoft com base na política do Gestor de Tráfego e nos resultados da sonda. Assim, mesmo durante a primeira procura de DNS, não são enviadas consultas de DNS ao Traffic Manager.

O Traffic Manager é composto por vários componentes: servidores de nome DNS, um serviço API, a camada de armazenamento e um serviço de monitorização de pontos finais. Se um componente de serviço de Traffic Manager falhar, não existe qualquer efeito no nome DNS associado ao seu perfil de Gestor de Tráfego. Os registos nos servidores DNS da Microsoft permanecem inalterados. No entanto, a monitorização do ponto final e a atualização do DNS não acontecem. Portanto, o Traffic Manager não é capaz de atualizar o DNS para apontar para o seu site de falha quando o seu site principal se avaria.

A resolução do nome DNS é rápida e os resultados estão em cache. A velocidade da procura inicial de DNS depende dos servidores DNS que o cliente utiliza para a resolução de nomes. Normalmente, um cliente pode completar uma procura de DNS dentro de ~50 ms. Os resultados da procura são em cache durante a duração do DNS Time-to-live (TTL). O TTL padrão para gestor de tráfego é de 300 segundos.

O tráfego NÃO flui através do Gestor de Tráfego. Uma vez concluída a procura do DNS, o cliente tem um endereço IP para uma instância do seu site. O cliente conecta-se diretamente a esse endereço e não passa pelo Gestor de Tráfego. A política do Gestor de Tráfego que escolher não tem influência no desempenho do DNS. No entanto, um método de encaminhamento de desempenho pode impactar negativamente a experiência da aplicação. Por exemplo, se a sua política redirecionar o tráfego da América do Norte para um caso hospedado na Ásia, a latência da rede para essas sessões pode ser uma questão de desempenho.

## <a name="measuring-traffic-manager-performance"></a>Medição do Desempenho do Gestor de Tráfego

Existem vários websites que pode usar para entender o desempenho e o comportamento de um perfil de Gestor de Tráfego. Muitos destes sites são gratuitos, mas podem ter limitações. Alguns sites oferecem monitorização e reporte melhorado por uma taxa.

As ferramentas nestes sites medem latências de DNS e exibem os endereços IP resolvidos para localizações de clientes em todo o mundo. A maioria destas ferramentas não cache os resultados do DNS. Por isso, as ferramentas mostram a procura completa de DNS sempre que um teste é executado. Ao testar a partir do seu próprio cliente, só experimenta o desempenho completo da procura de DNS uma vez durante a duração do TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Ferramentas de exemplo para medir o desempenho de DNS

* [SolvedNS](https://www.solvedns.com/dns-comparison/)

    A SolveDNS oferece muitas ferramentas de desempenho. A ferramenta DNS Compare pode mostrar-lhe quanto tempo demora a resolver o seu nome DNS e como isso se compara a outros prestadores de serviços DNS.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Uma das ferramentas mais simples é a WebSitePulse. Insira o URL para ver o tempo de resolução do DNS, First Byte, Last Byte e outras estatísticas de desempenho. Pode escolher entre três locais de teste diferentes. Neste exemplo, vê-se que a primeira execução mostra que a procura de DNS demora 0,204 segundos.

    ![Screenshot que mostra a ferramenta "WebSitePulse" com o resultado de procuração "DNS" realçado.](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Como os resultados estão em cache, o segundo teste para o mesmo ponto final do Gestor de Tráfego leva 0,002 segundos.

    ![pulso2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Monitor Sintético de Aplicativo CA](https://asm.ca.com/en/checkit.php)

    Anteriormente conhecido como a ferramenta Watch-mouse Check Website, este site mostra-lhe o tempo de resolução de DNS de várias regiões geográficas simultaneamente. Introduza o URL para ver o tempo de resolução do DNS, o tempo de ligação e a velocidade de vários locais geográficos. Utilize este teste para ver qual o serviço hospedado que é devolvido para diferentes locais em todo o mundo.

    ![pulso1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    Esta ferramenta fornece estatísticas de desempenho para cada elemento de uma página web. O separador Análise de Página mostra a percentagem de tempo gasto na procura de DNS.

* [Qual é o meu DNS?](https://www.whatsmydns.net/)

    Este site faz uma procura de DNS de 20 locais diferentes e exibe os resultados num mapa.

* [Dig Web Interface](https://www.digwebinterface.com)

    Este site mostra informações mais detalhadas sobre DNS, incluindo CNAMEs e registos A. Certifique-se de verificar a "saída colorize" e 'Stats' nas opções e selecione 'All' em Nameservers.

## <a name="next-steps"></a>Passos Seguintes

[Sobre os métodos de encaminhamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md)

[Testar as definições do Gestor de Tráfego (Test your Traffic Manager settings)](traffic-manager-testing-settings.md)

[Operações do Traffic Manager (Referência da API REST)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Comandantes do Gestor de Tráfego Azure](/powershell/module/az.trafficmanager)
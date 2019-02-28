---
title: Sobre a monitorização de rede no Azure Monitor registos | Documentos da Microsoft
description: Descrição geral de soluções, incluindo o NPM, para gerir redes através de ambientes de cloud, no local e híbridos de monitorização de rede.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 617dbae67c71eac4bfc38de4e63c1ba05516ff61
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984055"
---
# <a name="network-monitoring-solutions"></a>Soluções de monitorização de rede 

O Azure oferece uma série de soluções para monitorizar os seus ativos de rede. O Azure tem soluções e utilitários para monitorizar a conectividade de rede, o estado de funcionamento dos circuitos do ExpressRoute e analisar o tráfego de rede na cloud.

## <a name="network-performance-monitor-npm"></a>Monitor de desempenho de rede (NPM)

Monitor de desempenho de rede (NPM) é um conjunto de recursos, cada um dos quais é destinada a monitorizar o estado de funcionamento da sua rede, a conectividade de rede para seus aplicativos e fornece informações sobre o desempenho da sua rede. NPM é baseado na cloud e fornece uma solução que monitora a conectividade entre de monitorização de rede híbrida:
 
* Localizações de implementações e no local da cloud
* Vários datacenters e filiais
* Missão críticos com várias camados aplicativos/microsserviços
* Os locais do usuário e aplicativos baseados na web (HTTP/HTTPs) 

Monitor de desempenho, o Monitor do ExpressRoute e o Monitor de conectividade do serviço estão a monitorizar recursos em NPM e são descritos abaixo.

## <a name="performance-monitor"></a>Monitorização de Desempenho

Monitor de desempenho é parte do NPM e é a monitorização de rede para ambientes no local, híbridos e na cloud. Pode monitorizar a conectividade de rede em sucursais e escritórios, localizações de loja, datacenters e clouds. Pode detectar problemas de rede antes dos usuários se queixam. As principais vantagens são:

* Monitorizar a perda e latência em várias sub-redes e definir alertas
* Monitorizar todos os caminhos (incluindo caminhos redundantes) na rede
* Resolver problemas de rede transitórios e de ponto no tempo, o que são difíceis de replicar
* Determine o segmento específico na rede, que é responsável pela diminuição do desempenho
* Monitorizar o estado de funcionamento da rede, sem precisar do SNMP

![Mapa de topologia do NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Para obter mais informações, consulte os artigos seguintes:

* [Configurar uma solução de Monitor de desempenho de rede nos registos do Azure Monitor](../azure-monitor/insights/network-performance-monitor.md) 
* [Casos de utilização](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Atualizações de produto: [Fevereiro de 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [Agosto de 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>Monitor do ExpressRoute

NPM para ExpressRoute oferece ExpressRoute abrangente de monitorização para o peering privado do Azure e ligações de peering da Microsoft. Pode monitorizar a conectividade de E2E e de desempenho entre suas sucursais e o Azure através do ExpressRoute. As principais capacidades são:

* Deteção automática de circuitos de ER associados à subscrição
* Deteção da topologia de rede no local para as suas aplicações na cloud
* Planejamento de capacidade, análise da utilização de largura de banda
* Monitorização e alertas em caminhos primários e secundários
* Conectividade para o Azure de monitorização de serviços como o Office 365, Dynamics 365,... através do ExpressRoute
* Detetar degradação de conectividade para VNets

![Tráfego de Mostrar mapa geográfico em várias regiões](./media/network-monitoring-overview/expressroute-topology-map.png) 

Para obter mais informações, veja os artigos seguintes:

* [Configurar o Monitor de Desempenho de Rede para o ExpressRoute](../expressroute/how-to-npm.md)
* [Mensagem de blogue](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Monitor de Conectividade de Serviço

Com a monitorização de conectividade do serviço, pode agora testar a acessibilidade de aplicativos e detectar afunilamentos de desempenho no local, redes de deteção de carrier e centros de dados de cloud/privadas.

* Monitorizar a conectividade de rede ponto a ponto para aplicativos
* Correlacionar a entrega de aplicativos com o desempenho da rede, detectar a localização exata de degradação ao longo do caminho entre o utilizador e a aplicação
* Testar a acessibilidade de aplicação de vários locais do usuário em todo o mundo
* Determinar a perda de latência e pacotes de rede para a sua linha de negócio e aplicações SaaS
* Determinar os pontos de acesso da rede, o que poderá estar a causar o desempenho da aplicação insatisfatório
* Monitorizar a acessibilidade para aplicações do Office 365, usando testes internos para o Microsoft Office 365, Dynamics 365, Skype para empresas e outros serviços da Microsoft

Para obter mais informações, veja os artigos seguintes:

* [Configurar o Monitor de desempenho de rede para a monitorização de pontos finais de serviço](https://aka.ms/applicationconnectivitymonitorguide)
* [Mensagem de blogue](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Análise de Tráfego
Análise de tráfego é uma solução baseada na cloud que fornece visibilidade para a atividade de utilizador e da aplicação nas suas redes na cloud. Registos de fluxo de NSG são analisados para fornecer informações sobre:

* Fluxos de tráfego nas suas redes entre o Azure e Internet, regiões de cloud pública, VNETs e sub-redes
* Aplicativos e protocolos na sua rede, sem a necessidade de (farejadores) ou aplicações de recoletor de fluxo dedicado
* Principais tópicos de conversação, aplicativos chatty, conversações de VM na nuvem, hotspots de tráfego
* Origens e destinos de tráfego nas VNETs, entre as relações entre aplicativos e serviços críticos de negócio
* Segurança – tráfego malicioso, portas abrir para a Internet, aplicativos ou VMs tentar acesso à Internet...
* Utilização da capacidade - ajuda a eliminar problemas de sobreaprovisionar ou de subutilização, tendências de utilização de gateways de VPN e outros serviços de monitorização

Análise de tráfego equipa-o com informações acionáveis que ajuda a auditar a atividade de rede da sua organização, aplicativos seguros e dados, otimizar o desempenho da carga de trabalho e de que esteja em conformidade.

![Tráfego de Mostrar mapa geográfico em várias regiões](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Links relacionados:
* [Mensagem de blogue](https://aka.ms/trafficanalytics), [documentação](https://aka.ms/trafficanalyticsdocs), [FAQ](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>Análise de DNS
Criado para administradores de DNS, esta solução recolhe, analisa e correlaciona os registos DNS para fornecer segurança, as operações e insights relacionados ao desempenho.  Algumas das capacidades são:

* Identificação de clientes que tentar resolver domínios maliciosos
* Identificação de registos de recursos obsoletos
* Visibilidade em nomes de domínio consultados com frequência e clientes DNS conversador
* Visibilidade para a carga de pedidos em servidores DNS
* Monitoramento de falhas de registo DNS dinâmicas

![Dashboard de análise DNS](./media/network-monitoring-overview/dns-analytics-overview.png) 

Links relacionados:
* [Mensagem de blogue](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [documentação](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Diversos

* [Os novos preços](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)

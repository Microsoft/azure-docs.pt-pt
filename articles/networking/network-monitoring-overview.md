---
title: Sobre a Monitorização da Rede nos registos do Monitor Azure [ Microsoft Docs
description: Visão geral das soluções de monitorização da rede, incluindo NPM, para gerir redes em ambientes cloud, on-in-local e híbridos.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: agummadi
ms.openlocfilehash: 2912488286745bf8d2e567d09e445b0a44dc7c39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67672186"
---
# <a name="network-monitoring-solutions"></a>Soluções de monitorização da rede 

O Azure oferece uma série de soluções para monitorizar os seus ativos de networking. O Azure dispõe de soluções e utilidades para monitorizar a conectividade da rede, a saúde dos circuitos ExpressRoute e analisar o tráfego de rede na nuvem.

## <a name="network-performance-monitor-npm"></a>Monitor de Desempenho da Rede (NPM)

O Network Performance Monitor (NPM) é um conjunto de capacidades, cada uma das quais está orientada para monitorizar a saúde da sua rede, conectividade de rede com as suas aplicações e fornece informações sobre o desempenho da sua rede. O NPM é baseado na nuvem e fornece uma solução híbrida de monitorização da rede que monitoriza a conectividade entre:
 
* Implantações em nuvem e locais no local
* Múltiplos centros de dados e sucursais
* Aplicações/micro-serviços críticos da missão
* Localizações dos utilizadores e aplicações baseadas na Web (HTTP/HTTPs) 

Monitor de Desempenho, Monitor ExpressRoute e Monitor de Conectividade de Serviço estão a monitorizar as capacidades dentro do NPM e são descritos abaixo.

## <a name="performance-monitor"></a>Monitorização de Desempenho

O Performance Monitor faz parte do NPM e é monitorização de rede para ambientes de nuvem, híbridos e no local. Você pode monitorizar a conectividade da rede através de escritórios remotos de sucursais e de campo, locais de loja, centros de dados e nuvens. Pode detetar problemas de rede antes de os seus utilizadores se queixarem. As principais vantagens são:

* Monitorizar perda e latência em várias subredes e alertas definidos
* Monitorize todos os caminhos (incluindo caminhos redundantes) na rede
* Problemas transitórios e problemas de rede pontual, que são difíceis de replicar
* Determine o segmento específico da rede, que é responsável pelo desempenho degradado
* Monitorizar a saúde da rede, sem necessidade de SNMP

![Mapa de topologia npm](./media/network-monitoring-overview/npm-topology-map.png) 

Para mais informações, consulte os seguintes artigos:

* [Configure uma solução de monitor de desempenho da rede nos registos do Monitor Azure](../azure-monitor/insights/network-performance-monitor.md) 
* [Casos de utilização](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
* Atualizações do produto:
  * [Fevereiro de 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/)
  * [Agosto de 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute Monitor

O NPM para o ExpressRoute oferece uma monitorização abrangente do ExpressRoute para o peering privado do Azure e ligações de pares da Microsoft. Pode monitorizar a conectividade e desempenho do E2E entre as suas filiais e o Azure sobre o ExpressRoute. As principais capacidades são:

* Deteção automática de circuitos ER associados à sua subscrição
* Deteção de topologia de rede a partir do local para as suas aplicações em nuvem
* Planeamento de capacidades, análise de utilização da largura de banda
* Monitorização e alerta tanto nos caminhos primários como secundários
* Monitorização da conectividade com serviços Azure tais como Office 365, Dynamics 365, ... sobre ExpressRoute
* Detetar a degradação da conectividade com os VNets

![Geo-mapa mostrando tráfego em regiões](./media/network-monitoring-overview/expressroute-topology-map.png) 

Para obter mais informações, veja os artigos seguintes:

* [Configurar o Monitor de Desempenho de Rede para o ExpressRoute](../expressroute/how-to-npm.md)
* [Post de blog](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Monitor de Conectividade do Serviço

Com a monitorização da Conectividade de Serviço, pode agora testar a capacidade de acesso das aplicações e detetar estrangulamentos de desempenho em todo o local, redes de porta-aviões e centros de dados cloud/private.

* Monitorizar a conectividade da rede de ponta a ponta às aplicações
* Correlacionar a entrega de aplicações com o desempenho da rede, detetar localização precisa de degradação ao longo do caminho entre o utilizador e a aplicação
* Capacidade de acesso à aplicação de teste de várias localizações de utilizadores em todo o mundo
* Determine a latência da rede e a perda de pacotes para a sua linha de negócios e aplicações SaaS
* Determine pontos quentes na rede, que podem estar a causar um fraco desempenho na aplicação
* Monitor alcance para aplicações do Office 365, utilizando testes incorporados para microsoft Office 365, Dynamics 365, Skype para negócios e outros serviços da Microsoft

Para obter mais informações, veja os artigos seguintes:

* [Configure o Monitor de Desempenho da Rede para monitorizar os pontos finais do serviço](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Post de blog](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Análise de Tráfego
Traffic Analytics é uma solução baseada na nuvem que proporciona visibilidade na atividade do utilizador e aplicação nas suas redes de nuvem. Os registos de fluxo NSG são analisados para fornecer informações sobre:

* Fluxos de tráfego através das suas redes entre Azure e Internet, regiões públicas de nuvem, VNETs e subnets
* Aplicações e protocolos na sua rede, sem necessidade de sniffers ou aparelhos de coleção de fluxos dedicados
* Melhores talkers, aplicações tagarelas, conversas vm na nuvem, hotspots de tráfego
* Fontes e destinos de tráfego através de VNETs, inter-relações entre serviços comerciais críticos e aplicações
* Segurança – tráfego malicioso, portas abertas à Internet, aplicações ou VMs que tentam aceder à Internet...
* Utilização da capacidade - ajuda-o a eliminar questões de sobreprovisionamento ou subutilização, monitorizando as tendências de utilização dos gateways VPN e outros serviços

O Traffic Analytics dota-o de informações atol que o ajudam a auditar a atividade de rede da sua organização, a garantir aplicações e dados, a otimizar o desempenho da carga de trabalho e a manter-se conforme.

![Geo-mapa mostrando tráfego em regiões](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Ligações relacionadas:
* [Post de blog](https://aka.ms/trafficanalytics), [Documentação,](https://aka.ms/trafficanalyticsdocs) [FAQ](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>Análise de DNS
Construída para administradores dNS, esta solução recolhe, analisa e correlaciona os registos dNS para fornecer informações de segurança, operações e desempenho.  Algumas das capacidades são:

* Identificação de clientes que tentam resolver domínios maliciosos
* Identificação de registos de recursos antigos
* Visibilidade em nomes de domínio frequentemente questionados e clientes dNS faladores
* Visibilidade na carga de pedido nos servidores DNS
* Monitorização de falhas dinâmicas de registo de DNS

![Painel de Análise DNS](./media/network-monitoring-overview/dns-analytics-overview.png) 

Ligações relacionadas:
* [Post de blog](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [Documentação](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Diversos

* [Novos Preços](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)

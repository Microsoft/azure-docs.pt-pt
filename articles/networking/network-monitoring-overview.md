---
title: Sobre a monitorização da rede nos registos do Monitor Azure | Microsoft Docs
description: Visão geral das soluções de monitorização da rede, incluindo NPM, para gerir redes em ambientes em nuvem, no local e em ambientes híbridos.
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
ms.openlocfilehash: a92c6789723dc42ac8f3a9c471e494079dc19328
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233445"
---
# <a name="network-monitoring-solutions"></a>Soluções de monitorização de rede 

O Azure oferece uma série de soluções para monitorizar os seus ativos de networking. O Azure tem soluções e utilitários para monitorizar a conectividade da rede, a saúde dos circuitos ExpressRoute e analisar o tráfego de rede na nuvem.

## <a name="network-performance-monitor-npm"></a>Monitor de desempenho da rede (NPM)

O Network Performance Monitor (NPM) é um conjunto de capacidades, cada uma das quais está orientada para monitorizar a saúde da sua rede, conectividade de rede com as suas aplicações, e fornece insights sobre o desempenho da sua rede. O NPM é baseado em nuvem e fornece uma solução de monitorização de rede híbrida que monitoriza a conectividade entre:
 
* Implantações em nuvem e locais no local
* Vários centros de dados e sucursais
* Aplicações/micro-serviços críticos de missão
* Localizações do utilizador e aplicações baseadas na Web (HTTP/HTTPs) 

Monitor de Desempenho, Monitor ExpressRoute e Monitor de Conectividade de Serviço são capacidades de monitorização dentro de NPM e são descritos abaixo.

## <a name="performance-monitor"></a>Monitorização de Desempenho

O Monitor de Desempenho faz parte do NPM e está a monitorizar a rede para ambientes em nuvem, híbrido e no local. Pode monitorizar a conectividade da rede através de escritórios de ramo remoto e de campo, locais de armazenamento, centros de dados e nuvens. Pode detetar problemas de rede antes que os seus utilizadores se queixem. As principais vantagens são:

* Monitorizar a perda e a latência em vários sub-redes e definir alertas
* Monitorize todos os caminhos (incluindo caminhos redundantes) na rede
* Problemas de resolução de problemas problemas transitórios e problemas pontuais da rede, que são difíceis de replicar
* Determinar o segmento específico da rede, que é responsável pelo desempenho degradado
* Monitorizar a saúde da rede, sem necessidade do SNMP

![Mapa da topologia do NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Para mais informações, consulte os seguintes artigos:

* [Configure uma solução de monitor de desempenho de rede em registos do Monitor Azure](../azure-monitor/insights/network-performance-monitor.md) 
* [Casos de utilização](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor)
* Atualizações do produto:
  * [Fevereiro de 2017](/archive/blogs/msoms/oms-network-performance-monitor-is-now-generally-available)
  * [Agosto de 2017](/archive/blogs/msoms/improvements-to-oms-network-performance-monitor)

## <a name="expressroute-monitor"></a>ExpressRoute Monitor

O NPM para o ExpressRoute oferece uma monitorização completa do ExpressRoute para o Azure Private e as ligações de observação da Microsoft. Pode monitorizar a conectividade e desempenho do E2E entre as suas sucursais e a Azure over ExpressRoute. As capacidades-chave são:

* Deteção automática de circuitos ER associados à sua subscrição
* Deteção da topologia da rede desde as instalações até às suas aplicações em nuvem
* Planeamento de capacidade, análise de utilização da largura de banda
* Monitorização e alerta nos caminhos primário e secundário
* Monitorização da conectividade com serviços Azure como Microsoft 365, Dynamics 365, ... sobre ExpressRoute
* Detetar a degradação da conectividade com os VNets

![Geo-mapa mostrando tráfego em regiões](./media/network-monitoring-overview/expressroute-topology-map.png) 

Para obter mais informações, veja os seguintes artigos:

* [Configurar o Monitor de Desempenho de Rede para o ExpressRoute](../expressroute/how-to-npm.md)
* [Blog post](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Monitor de Conectividade do Serviço

Com a monitorização da Conectividade de Serviço, pode agora testar a capacidade de acesso das aplicações e detetar estrangulamentos de desempenho em todos os locais, redes transportadoras e centros de dados em nuvem/privado.

* Monitorizar a conectividade da rede de ponta a ponta às aplicações
* Correlacionar a entrega da aplicação com o desempenho da rede, detetar localização precisa de degradação ao longo do caminho entre o utilizador e a aplicação
* Alcance da aplicação de teste de vários locais de utilizadores em todo o mundo
* Determine a latência da rede e a perda de pacotes para a sua linha de negócios e aplicações SaaS
* Determinar pontos quentes na rede, que podem estar a causar um fraco desempenho da aplicação
* Monitor de alcance para aplicações Microsoft 365, utilizando testes incorporados para Microsoft 365, Dynamics 365, Skype para empresas e outros serviços da Microsoft

Para obter mais informações, veja os seguintes artigos:

* [Configure monitor de desempenho da rede para monitorizar os pontos finais do serviço](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Blog post](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Análise de Tráfego
Traffic Analytics é uma solução baseada em nuvem que proporciona visibilidade à atividade do utilizador e da aplicação nas suas redes em nuvem. Os registos do Fluxo NSG são analisados para fornecer informações sobre:

* O tráfego flui através das suas redes entre o Azure e a Internet, regiões de nuvem pública, VNETs e sub-redes
* Aplicações e protocolos na sua rede, sem necessidade de sniffers ou aparelhos dedicados de coletor de fluxos
* Principais talkers, aplicações tagarelas, conversas VM na nuvem, hotspots de tráfego
* Fontes e destinos de tráfego através de VNETs, inter-relações entre serviços empresariais críticos e aplicações
* Segurança – tráfego malicioso, portas abertas à Internet, aplicações ou VMs a tentar aceder à Internet...
* Utilização da capacidade - ajuda a eliminar questões de sobre-aprovisionamento ou subutilização através da monitorização das tendências de utilização dos gateways VPN e outros serviços

O Traffic Analytics equipa-o com informações acccáveis que o ajudam a auditar a atividade de rede da sua organização, aplicações e dados seguros, otimizar o desempenho da carga de trabalho e manter-se em conformidade.

![Geo-mapa mostrando tráfego em regiões 2](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Links relacionados:
* [Blog post](https://aka.ms/trafficanalytics), [Documentação,](../network-watcher/traffic-analytics.md) [FAQ](../network-watcher/traffic-analytics-faq.md)

## <a name="dns-analytics"></a>Análise de DNS
Construída para Administradores DNS, esta solução recolhe, analisa e correlaciona registos DNS para fornecer informações de segurança, operações e desempenho.  Algumas das capacidades são:

* Identificação de clientes que tentam resolver domínios maliciosos
* Identificação de registos de recursos obsoletos
* Visibilidade em nomes de domínio frequentemente questionados e clientes de DNS falantes
* Visibilidade na carga de pedido nos servidores DNS
* Monitorização de falhas dinâmicas de registo de DNS

![Painel DE ANÁLISE DNS](./media/network-monitoring-overview/dns-analytics-overview.png) 

Links relacionados:
* [Blog post](/archive/blogs/msoms/introducing-oms-dns-analytics), [Documentação](../azure-monitor/insights/dns-analytics.md)

## <a name="miscellaneous"></a>Diversos

* [Novos preços](../azure-monitor/insights/network-performance-monitor-pricing-faq.md)
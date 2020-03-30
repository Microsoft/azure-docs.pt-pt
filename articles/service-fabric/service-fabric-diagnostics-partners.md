---
title: Parceiros de Monitorização de Tecidos de Serviço Azure
description: Saiba como monitorizar aplicações, clusters e infraestruturas do Azure Service Fabric com soluções de monitorização de parceiros.
author: srrengar
ms.topic: article
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: fd24d65ebdf4e458870819286024d1ea2e13d83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645723"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Parceiros de Monitorização de Tecidos de Serviço Azure

Este artigo ilustra como se pode monitorizar as suas aplicações, clusters e infraestruturas de Tecido de Serviço com um punhado de soluções parceiras. Trabalhámos com cada um dos parceiros abaixo para criar ofertas integradas para o Service Fabric.

## <a name="dynatrace"></a>Dynatrace

A nossa integração com a Dynatrace fornece muitas funcionalidades fora da caixa para monitorizar os seus clusters de Tecido de Serviço. A instalação do Dynatrace OneAgent nas suas instâncias VMSS dá-lhe contadores de desempenho e uma topologia da implementação do seu Tecido de Serviço até ao nível da App. A Dynatrace também é uma ótima escolha para a monitorização no local. Confira mais funcionalidades listadas no [anúncio](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) e [instruções](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) para ativar a Dynatrace no seu cluster. 

## <a name="datadog"></a>Cão de Dados

Datadog tem uma extensão para VMSS tanto para casos Windows como Linux. Utilizando o Datadog pode recolher registos de eventos do Windows e, assim, recolher eventos da plataforma Service Fabric no Windows. Consulte aqui as instruções sobre como enviar os seus dados de diagnóstico para o [Datadog](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

A integração do Tecido de Serviço com a AppDynamics está ao nível da aplicação. Ao atualizar variáveis ambientais e utilizar o App Dynamics NuGets, pode enviar a telemetria da aplicação para a AppDynamics. Consulte estas [instruções](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) para como integrar as suas aplicações .NET Service Fabric com a AppDynamics.

## <a name="new-relic"></a>New Relic

A New Relic é outra ferramenta de Gestão de Desempenho de Aplicações que se integra bem com aplicações de Tecido de Serviço. Pode instalar os pacotes New Relic NuGet e adicionar variáveis ambientais específicas nos seus ficheiros manifestos para enviar a sua aplicação para a New Relic. Consulte estas [instruções](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) para ativar a telemetria New Relic para as suas aplicações .NET Service Fabric.

## <a name="elk"></a>ALCE 

A pilha ELK é uma coleção de tecnologias de código aberto: Elasticsearch, Logstash e Kibana. Ao utilizar estas tecnologias em combinação, pode recolher, armazenar e analisar dados de monitorização e diagnóstico de Tecidos de Serviço. Temos aqui um tutorial para como fazê-lo com [aplicações](service-fabric-tutorial-java-elk.md)java nativas de Tecido de Serviço. 

## <a name="humio"></a>Humio

Humio é um serviço de recolha de registos que pode recolher registos das suas aplicações e eventos da Service Fabric na nuvem ou no local em tempo real. Além da observabilidade ao vivo, a Humio oferece capacidades de análise e visualização de última geração para visualizar e recolher informações dos seus diagnósticos. Humio tem planos de preços eficazes em termos de custos e é construído à escala, mantendo a sua velocidade rápida de sumução. Integra-se diretamente com eventos da plataforma Service Fabric e telemetria de aplicações. Pode ler mais sobre a integração humio e de tecido de serviço [aqui](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Passos seguintes

* Obtenha uma [visão geral da monitorização e diagnósticos](service-fabric-diagnostics-overview.md) em Tecido de Serviço
* Saiba como [diagnosticar cenários comuns](service-fabric-diagnostics-common-scenarios.md) com as nossas ferramentas de primeiro partido

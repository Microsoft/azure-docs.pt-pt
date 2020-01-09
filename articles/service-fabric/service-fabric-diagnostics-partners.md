---
title: Parceiros de monitoramento de Service Fabric do Azure
description: Saiba como monitorar aplicativos, clusters e infraestrutura do Azure Service Fabric com soluções de monitoramento de parceiros.
author: srrengar
ms.topic: article
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: fd24d65ebdf4e458870819286024d1ea2e13d83e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645723"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Parceiros de monitoramento de Service Fabric do Azure

Este artigo ilustra como um pode monitorar seus aplicativos Service Fabric, clusters e infraestrutura com uma série de soluções de parceiros. Trabalhamos com cada um dos parceiros abaixo para criar ofertas integradas para Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Nossa integração com o dynaTrace fornece muitos recursos prontos para monitorar seus clusters de Service Fabric. Instalar o dynaTrace OneAgent em suas instâncias de VMSS fornece contadores de desempenho e uma topologia de sua implantação de Service Fabric para o nível do aplicativo. O dynaTrace também é uma ótima opção para o monitoramento local. Confira mais dos recursos listados no [comunicado](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) e [as instruções](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) para habilitar o dynaTrace em seu cluster. 

## <a name="datadog"></a>Datadog

Datadog tem uma extensão para VMSS para instâncias do Windows e do Linux. Usando o Datadog, você pode coletar logs de eventos do Windows e, assim, coletar Service Fabric eventos da plataforma no Windows. Confira as instruções sobre como enviar seus dados de diagnóstico para Datadog [aqui](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

A integração do Service Fabric com o AppDynamics está no nível do aplicativo. Ao atualizar as variáveis de ambiente e usar o aplicativo Dynamics NuGets, você pode enviar telemetria de aplicativo para AppDynamics. Consulte estas [instruções](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) para saber como integrar seus aplicativos .Net Service Fabric com o AppDynamics.

## <a name="new-relic"></a>New Relic

O New Relic é outra ferramenta de gerenciamento de desempenho de aplicativos que se integra bem com aplicativos Service Fabric. Você pode instalar os novos pacotes NuGet do Relic e adicionar variáveis de ambiente específicas em seus arquivos de manifesto para enviar a telemetria do aplicativo para o New Relic. Confira estas [instruções](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) para habilitar a telemetria do New Relic para seus aplicativos .Net Service Fabric.

## <a name="elk"></a>ELK 

A pilha ELK é uma coleção de tecnologias de código aberto: Elasticsearch, Logstash e Kibana. Usando essas tecnologias em combinação, você pode coletar, armazenar e analisar Service Fabric dados de monitoramento e diagnóstico. Temos um tutorial sobre como fazer isso com Service Fabric aplicativos Java nativos [aqui](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio

Humio é um serviço de coleta de log que pode coletar logs de seus aplicativos e eventos de Service Fabric na nuvem ou localmente em tempo real. Além da observação ao vivo, o Humio oferece recursos de análise e visualização de última geração para exibir e coletar informações de seu diagnóstico. O Humio tem planos de preços econômicos e é criado para ser dimensionado enquanto retém a velocidade rápida. Ele integra-se diretamente com Service Fabric eventos da plataforma e telemetria do aplicativo. Você pode ler mais sobre a integração do Humio e do Service Fabric [aqui](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Passos seguintes

* Obtenha uma [visão geral do monitoramento e diagnóstico](service-fabric-diagnostics-overview.md) no Service Fabric
* Saiba como [diagnosticar cenários comuns](service-fabric-diagnostics-common-scenarios.md) com nossas primeiras ferramentas de terceiros

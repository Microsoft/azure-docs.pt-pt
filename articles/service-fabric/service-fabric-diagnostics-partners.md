---
title: Monitorização de parceiros do Azure Service Fabric | Documentos da Microsoft
description: Saiba como monitorizar o Azure Service Fabric com o parceiro de soluções de monitorização
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: 45e6de347cbb3ecfcc45ca20ab03805359207565
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444672"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Parceiros de monitorização de recursos de infraestrutura do serviço do Azure

Este artigo ilustra como um monitorar seus aplicativos, clusters e infraestrutura com um punhado de soluções de parceiros do Service Fabric. Trabalhamos com cada um dos parceiros abaixo para criar ofertas integradas para o Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Nossa integração com o Dynatrace fornece muitos fora os recursos de caixa para monitorizar os seus clusters do Service Fabric. Instalar o Dynatrace OneAgent em suas instâncias VMSS fornece contadores de desempenho e uma topologia de implementação do Service Fabric até o nível de aplicação. A Dynatrace também é uma ótima opção para monitorização no local. Confira mais os recursos listados na [anúncio](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) e [instruções](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) para ativar a Dynatrace no seu cluster. 

## <a name="datadog"></a>Datadog

O Datadog tem uma extensão para o VMSS para instâncias de computadores Windows e Linux. Com o Datadog pode recolher registos de eventos do Windows e, deste modo, recolher eventos de plataforma do Service Fabric no Windows. Veja as instruções sobre como enviar os seus dados de diagnóstico para o Datadog [aqui](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

A integração do Service Fabric com o AppDynamics é no nível do aplicativo. Ao atualizar as variáveis de ambiente e utilizar a aplicação Dynamics NuGets, pode enviar a telemetria da aplicação para AppDynamics. Veja estes [instruções](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) para saber como integrar as suas aplicações de .NET do Service Fabric com o AppDynamics.

## <a name="new-relic"></a>New Relic

O New Relic é outra ferramenta de gestão de desempenho de aplicações que se integra bem com aplicações do Service Fabric. Pode instalar os pacotes de NuGet de Relic novo e adicione variáveis de ambiente específico nos seus ficheiros de manifestos para enviar a telemetria da sua aplicação para o New Relic. Confira estes [instruções](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) para ativar a telemetria de New Relic para as suas aplicações de .NET do Service Fabric.

## <a name="elk"></a>ELK 

A pilha ELK é uma coleção de tecnologias de código-fonte aberto: Elasticsearch, Logstash e Kibana. Ao utilizar estes em combinação, pode recolher, armazenar e analisar dados de monitorização e diagnóstico do Service Fabric. Temos um tutorial para saber como fazê-lo com aplicações de Java do Service Fabric nativas [aqui](service-fabric-tutorial-java-elk.md). 


## <a name="next-steps"></a>Passos Seguintes

* Obter um [descrição geral da monitorização e diagnóstico](service-fabric-diagnostics-overview.md) no Service Fabric
* Saiba como [cenários comuns de diagnosticar](service-fabric-diagnostics-common-scenarios.md) com nossas ferramentas de terceiros primeiro

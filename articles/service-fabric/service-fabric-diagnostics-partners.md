---
title: Parceiros de monitorização de tecidos de serviço Azure
description: Saiba como monitorizar aplicações, clusters e infraestruturas do Azure Service Fabric com soluções de monitorização de parceiros.
ms.topic: article
ms.date: 10/16/2018
ms.openlocfilehash: 92ac0627f02ccefdd4c93aa51cac7c9dd7790460
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627732"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Parceiros de monitorização de tecidos de serviço Azure

Este artigo ilustra como se pode monitorizar as suas aplicações de Tecido de Serviço, clusters e infraestruturas com um punhado de soluções parceiras. Trabalhámos com cada um dos parceiros abaixo para criar ofertas integradas para o Service Fabric.

## <a name="dynatrace"></a>Dynatrace

A nossa integração com a Dynatrace fornece muitas funcionalidades fora da caixa para monitorizar os seus clusters de Tecido de Serviço. A instalação do Dynatrace OneAgent nas suas instâncias VMSS dá-lhe contadores de desempenho e uma topologia da sua implementação de Tecido de Serviço até ao nível da App. A Dynatrace é também uma ótima escolha para a monitorização no local. Confira mais das funcionalidades listadas no [anúncio](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) e [instruções](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) para ativar a Dynatrace no seu cluster. 

## <a name="datadog"></a>Datadog

Datadog tem uma extensão para VMSS tanto para as instâncias Windows como Linux. Utilizando o Datadog, pode recolher registos de eventos do Windows e, assim, recolher eventos da plataforma Service Fabric no Windows. Consulte aqui as instruções sobre como enviar os seus dados de diagnóstico para o [Datadog.](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric)

## <a name="appdynamics"></a>AppDynamics

A integração do Tecido de Serviço com a AppDynamics está ao nível da aplicação. Ao atualizar variáveis ambientais e utilizando o App Dynamics NuGets, pode enviar telemetria de aplicação para AppDynamics. Consulte estas [instruções](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) sobre como integrar as suas aplicações .NET Service Fabric com AppDynamics.

## <a name="new-relic"></a>New Relic

A New Relic é outra ferramenta de Gestão de Desempenho de Aplicações que se integra bem com aplicações de Tecido de Serviço. Pode instalar os pacotes New Relic NuGet e adicionar variáveis ambientais específicas nos seus ficheiros manifestos para enviar a telemetria da sua aplicação para a Nova Relíquia. Consulte estas [instruções](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) para ativar a telemetria New Relic para as suas aplicações .NET Service Fabric.

## <a name="elk"></a>ALCE 

A pilha ELK é uma coleção de tecnologias de código aberto: Elasticsearch, Logstash e Kibana. Ao utilizar estas tecnologias em combinação, pode recolher, armazenar e analisar dados de monitorização e diagnóstico do Service Fabric. Temos um tutorial para como fazê-lo com aplicações java nativas do Service Fabric [aqui.](service-fabric-tutorial-java-elk.md) 

## <a name="humio"></a>Rio Humio

Humio é um serviço de recolha de registos que pode recolher registos das suas aplicações e eventos a partir de Service Fabric na nuvem ou no local em tempo real. Além da observabilidade ao vivo, Humio oferece capacidades de análise e visualização de última geração para visualizar e recolher informações a partir dos seus diagnósticos. Humio tem planos de preços eficazes e é construído à escala, mantendo a sua velocidade de iluminação rápida. Integra-se diretamente com eventos da plataforma Service Fabric e telemetria de aplicação. Pode ler mais sobre a integração do Humio and Service Fabric [aqui.](https://github.com/humio/service-fabric-humio)

## <a name="next-steps"></a>Passos seguintes

* Obtenha uma [visão geral da monitorização e diagnósticos](service-fabric-diagnostics-overview.md) em Tecido de Serviço
* Saiba como [diagnosticar cenários comuns](service-fabric-diagnostics-common-scenarios.md) com as nossas primeiras ferramentas partidárias

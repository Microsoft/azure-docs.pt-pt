---
title: Monitorize as suas apps sem alterações de código - auto-instrumentação para Azure Monitor Application Insights | Microsoft Docs
description: Visão geral da auto-instrumentação para Azure Monitor Application Insights - gestão de desempenho de aplicações sem código
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 0dda015d820d81fdd13eced384f97362e2ee3339
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587564"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>O que é auto-instrumentação ou anexação codeless - Azure Monitor Application Insights?

A instrumentação automática, ou anexação sem código, permite-lhe ativar a monitorização da aplicação com insights de aplicação sem alterar o seu código.  

A Application Insights está integrada com vários fornecedores de recursos e trabalha em diferentes ambientes. No fundo, tudo o que tem de fazer é ativar e - em alguns casos - configurar o agente, que recolherá automaticamente a caixa de telemetria. Em pouco tempo, você verá as métricas, dados e dependências no seu recurso Application Insights, que lhe permitirá detetar a origem de problemas potenciais antes de ocorrerem, e analisar a causa raiz com vista de transação de ponta a ponta.

## <a name="supported-environments-languages-and-resource-providers"></a>Ambientes, linguagens e fornecedores de recursos apoiados

À medida que adicionamos integrações adicionais, a matriz de capacidade de auto-instrumentação torna-se complexa. O quadro abaixo mostra-lhe o estado atual da questão no que diz respeito ao apoio a vários fornecedores de recursos, línguas e ambientes.

|Fornecedor de Ambiente/Recursos          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Serviço de aplicações Azure no Windows           | GA, OnBD*       | GA, opt-in      | Em curso     | Em curso     | Não suportado   |
|Serviço de Aplicações Azure em Linux             | N/D             | Não suportado   | Em curso     | Pré-visualização pública  | Não suportado   |
|Serviço de Aplicações Azure em AKS               | N/D             | No design       | No design       | No design       | Não suportado   |
|Funções Azure - básico                | GA, OnBD*       | GA, OnBD*       | GA, OnBD*       | GA, OnBD*       | GA, OnBD*       |
|Azure Functions Windows - dependências | Não suportado   | Não suportado   | Pré-visualização pública  | Não suportado   | Não suportado   |
|Azure Kubernetes Service               | N/D             | No design       | Através do agente   | No design       | Não suportado   |
|Janelas Azure VMs                      | Pré-visualização pública  | Não suportado   | Não suportado   | Não suportado   | Não suportado   |
|Janelas VMs nas instalações                | GA, opt-in      | Não suportado   | Através do agente   | Não suportado   | Não suportado   |
|Agente autónomo- qualquer env.            | Não suportado   | Não suportado   | GA              | Não suportado   | Não suportado   |

*O OnBD é abreviado para On by Predefinido - os Insights de Aplicação serão ativados automaticamente assim que implementar a sua aplicação em ambientes suportados. 

## <a name="azure-app-service"></a>Serviço de Aplicações do Azure

### <a name="windows"></a>Windows

#### <a name="net"></a>.NET
A monitorização de aplicações no Serviço de Aplicações Azure no Windows está disponível para [aplicações .NET](./azure-web-apps.md?tabs=net) .NET e está ativada por padrão.

#### <a name="netcore"></a>. NETCore
Monitorização para [. As aplicações NETCore](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=netcore) podem ser ativadas com um clique.

#### <a name="java"></a>Java
A integração do portal para monitorização de aplicações Java no Serviço de Aplicações no Windows não existe atualmente disponível, no entanto, pode adicionar o agente autónomo Da Aplicação Insights [Java 3.0](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) à sua aplicação sem alterações de código antes de implementar as aplicações no Serviço de Aplicações. O agente Application Insights Java 3.0 está geralmente disponível.

#### <a name="nodejs"></a>Node.js
A monitorização de aplicações Node.js no Windows não pode ser ativada a partir do portal. Para monitorizar Node.js aplicações, utilize o [SDK](https://docs.microsoft.com/azure/azure-monitor/app/nodejs).

### <a name="linux"></a>Linux

#### <a name="netcore"></a>. NETCore
Para monitorizar . Aplicações NETCore em execução no Linux, utilize o [SDK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

#### <a name="java"></a>Java 
A monitorização da aplicação Java para o Serviço de Aplicações no Linux não está disponível, mas pode adicionar [o agente App Insights Java 3.0](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) à sua app antes de implementar as aplicações no Serviço de Aplicações. O agente Application Insights Java 3.0 está geralmente disponível.

#### <a name="nodejs"></a>Node.js
[A monitorização Node.js aplicações no Serviço de Aplicações no Linux](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=nodejs) está em pré-visualização pública e pode ser ativada no portal Azure, disponível em todas as regiões. 

#### <a name="python"></a>Python
Utilize o SDK para [monitorizar a sua aplicação Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python) 

## <a name="azure-functions"></a>Funções do Azure

A monitorização básica das Funções Azure é ativada por padrão para recolher pedidos de registo, desempenho, erro e HTTP. Para aplicações Java, você pode ativar uma monitorização mais rica com rastreio distribuído e obter os detalhes de transação de ponta a ponta. Esta funcionalidade para Java está em visualização pública e pode [ative-la no portal Azure.](./monitor-functions.md)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

A instrumentação codeless do Serviço Azure Kubernetes está atualmente disponível para aplicações Java através do [agente autónomo](./java-in-process-agent.md). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>VMs do Azure Windows e conjunto de escala de máquina virtual

A auto-instrumentação para VMs Azure e conjunto de escala de máquina virtual está disponível para [.NET](./azure-vm-vmss-apps.md) e [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent).  

## <a name="on-premises-servers"></a>Servidores no local
Pode facilmente ativar a monitorização dos [servidores Windows no local para aplicações .NET](./status-monitor-v2-overview.md) e para [aplicações Java.](./java-in-process-agent.md)

## <a name="other-environments"></a>Outros ambientes
O versátil agente autónomo java trabalha em qualquer ambiente, não há necessidade de instrumentar o seu código. [Siga o guia](./java-in-process-agent.md) para ativar o Application Insights e leia sobre as incríveis capacidades do agente Java. O agente está em pré-visualização pública e disponível em todas as regiões. 

## <a name="next-steps"></a>Passos seguintes

* [Visão geral de insights de aplicação](./app-insights-overview.md)
* [Mapa de aplicação](./app-map.md)
* [Monitorização de desempenho de ponta a ponta](../app/tutorial-performance.md)


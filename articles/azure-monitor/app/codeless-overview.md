---
title: Monitorize as suas apps sem alterações de código - auto-instrumentação para Azure Monitor Application Insights / Microsoft Docs
description: Visão geral da auto-instrumentação para Azure Monitor Application Insights - gestão de desempenho de aplicações sem código
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 87342dcd316b0364522baa01e632b704665c998e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85363769"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>O que é auto-instrumentação ou anexação codeless - Azure Monitor Application Insights?

A instrumentação automática, ou anexação sem código, permite-lhe ativar a monitorização da aplicação com insights de aplicação sem alterar o seu código.  

A Application Insights está integrada com vários fornecedores de recursos e trabalha em diferentes ambientes. No fundo, tudo o que tem de fazer é ativar e - em alguns casos - configurar o agente, que recolherá a telemetria fora da caixa. Em pouco tempo, você verá as métricas, dados e dependências no seu recurso Application Insights, que lhe permitirá detetar a origem de problemas potenciais antes de ocorrerem, e analisar a causa raiz com vista de transação de ponta a ponta.

## <a name="supported-environments-languages-and-resource-providers"></a>Ambientes, linguagens e fornecedores de recursos apoiados

À medida que adicionamos cada vez mais integrações, a matriz de capacidade de auto-instrumentação torna-se complexa. O quadro abaixo mostra-lhe o estado atual da questão no que diz respeito ao apoio a vários fornecedores de recursos, línguas e ambientes.

|Fornecedor de Ambiente/Recursos | .NET            | .NET Core       | Java            | Node.js         |
|------------------------------|-----------------|-----------------|-----------------|-----------------|
|Serviço de aplicações Azure no Windows  | GA, OnBD*       | GA, opt-in      | Pré-visualização privada | Pré-visualização privada |
|Serviço de Aplicações Azure em Linux    | N/D             | Não suportado   | Pré-visualização pública  | Pré-visualização pública  |
|Serviço de Aplicações Azure em AKS      | N/D             | No design       | No design       | No design       |
|Funções Azure - básico       | GA, OnBD*       | GA, OnBD*       | GA, OnBD*       | GA, OnBD*       |
|Funções Azure - dependências| Não suportado   | Não suportado   | Pré-visualização pública  | Não suportado   |
|Azure Kubernetes Service      | N/D             | No design       | Através do agente   | No design       |
|Janelas Azure VMs             | Pré-visualização pública  | Não suportado   | Não suportado   | Não suportado   |
|Janelas VMs nas instalações       | GA, opt-in      | Não suportado   | Através do agente   | Não suportado   |
|Agente autónomo- qualquer env.   | Não suportado   | Não suportado   | Pré-visualização pública  | Não suportado   |

*O OnBD é abreviado para On by Predefinido - os Insights de Aplicação serão ativados automaticamente assim que implementar a sua aplicação em ambientes suportados. 

## <a name="azure-app-service"></a>Serviço de Aplicações do Azure

### <a name="windows"></a>Windows

[A monitorização de aplicações no Azure App Service](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=net) está disponível para aplicação .NET e está ativada por padrão, .NET Core pode ser ativado com um clique, e Java e Node.js estão em pré-visualização privada.

### <a name="linux"></a>Linux 

A monitorização das aplicações Java e Node.js no Serviço de Aplicações está em pré-visualização pública e pode ser ativada no portal Azure, disponível em todas as regiões.

## <a name="azure-functions"></a>Funções do Azure

A monitorização básica das Funções Azure é ativada por padrão para recolher pedidos de registo, desempenho, erro e HTTP. Para aplicações Java, você pode ativar uma monitorização mais rica com rastreio distribuído e obter os detalhes de transação de ponta a ponta. Esta funcionalidade para Java está em visualização pública e pode [ative-la no portal Azure.](https://docs.microsoft.com/azure/azure-monitor/app/monitor-functions)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

A instrumentação codeless do Serviço Azure Kubernetes está atualmente disponível para aplicações Java através do [agente autónomo](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>VMs do Azure Windows e conjunto de escala de máquina virtual

[A auto-instrumentação para VMs Azure e conjunto de escala de máquina virtual](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps) está disponível para aplicações .NET 

## <a name="on-premises-servers"></a>Servidores no local
Pode facilmente ativar a monitorização dos [servidores Windows no local para aplicações .NET](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) e para [aplicações Java.](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)

## <a name="other-environments"></a>Outros ambientes
O versátil agente autónomo java trabalha em qualquer ambiente, não há necessidade de instrumentar o seu código. [Siga o guia](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) para ativar o Application Insights e leia sobre as incríveis capacidades do agente Java. O agente está em pré-visualização pública e disponível em todas as regiões. 

## <a name="next-steps"></a>Próximos passos

* [Visão geral de insights de aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* [Mapa de aplicação](./../../azure-monitor/app/app-map.md)
* [Monitorização de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)

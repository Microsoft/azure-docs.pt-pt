---
title: Monitor do Azure no Azure Stack | Documentos da Microsoft
description: Saiba mais sobre o Monitor do Azure no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2018
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: b8df87e5d5091219b9b44a6b957250e254c97fec
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341817"
---
# <a name="azure-monitor-on-azure-stack"></a>Monitor do Azure no Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo fornece uma descrição geral do serviço do Azure Monitor no Azure Stack. Ele aborda a operação do Azure Monitor e informações adicionais sobre como utilizar o Azure Monitor no Azure Stack. 

Para obter uma introdução, visão geral e como começar a trabalhar com o Azure Monitor, consulte o artigo do Azure global [introdução ao Azure Monitor no Azure Stack](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Painel de Monitor de pilha do Azure](./media/azure-stack-metrics-azure-data/azs-monitor.png)

O Azure Monitor é o serviço de plataforma que fornece uma única origem para a monitorização de recursos do Azure. Com o Azure Monitor, pode visualizar, consultar, encaminhar, arquivar e caso contrário, tomar medidas relativamente a métricas e registos provenientes de recursos no Azure. Pode trabalhar com estes dados utilizando o portal de administração do Azure Stack, o Monitor de Cmdlets do PowerShell, CLI de várias plataformas ou APIs de REST do Azure Monitor. Para a conectividade específica suportada pelo Azure Stack, veja [como consumir dados de monitorização do Azure Stack](azure-stack-metrics-monitor.md)

> [!Note]  
Métricas e registos de diagnóstico não estão disponíveis para o Azure Stack Development Kit.

## <a name="prerequisites-for-azure-monitor-on-azure-stack"></a>Pré-requisitos para o Monitor do Azure no Azure Stack

Registe-se a **Microsoft. insights** fornecedor de recursos em definições de fornecedores de recursos de oferta da sua subscrição. Pode verificar que o fornecedor de recursos está disponível na oferta associada à sua subscrição:

1. Abra o portal de administração do Azure Stack.
2. Selecione **oferece**.
3. Selecione a oferta associada à subscrição.
4. Selecione **fornecedores de recursos** sob **definições.** 
5. Encontrar **Microsoft. insights** na lista e certifique-se de que o estado é **registado.**.

## <a name="overview-of-azure-monitor-on-azure-stack"></a>Descrição geral do Monitor do Azure no Azure Stack

Como o Azure Monitor, no Azure, o Azure Monitor no Azure Stack fornece registos e métricas de infraestrutura de nível de base para a maioria dos serviços.

## <a name="azure-monitor-sources-compute-subset"></a>Origens de Monitor do Azure: subconjunto de computação

![Monitor do Azure no Azure Stack origens - subconjunto de computação](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

O **Microsoft. Compute** fornecedor de recursos no Azure Stack inclui:
 - Virtual Machines 
 - Conjuntos de dimensionamento de máquinas virtuais

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Aplicação – registos de diagnóstico, registos de aplicações e métricas

Aplicativos podem ser executados no SO de uma VM em execução com o **Microsoft. Compute** fornecedor de recursos. Estas aplicações e as VMs emitem seu próprio conjunto de registos e métricas. O Azure Monitor baseia-se na extensão de diagnóstico do Azure (Windows ou Linux) para recolher a maioria dos registos e métricas ao nível de aplicação. 

Os tipos de medidas incluem:
 - Contadores de desempenho
 - Registos de aplicações
 - Registos de eventos do Windows
 - Origem de evento do .NET
 - Registos do IIS
 - ETW baseado no manifesto
 - Informações de falha de sistema
 - Registos de erros do cliente

> [!Note]  
> Extensão de diagnóstico do Linux no Azure Stack não são suportadas.

### <a name="host-and-guest-vm-metrics"></a>Métricas de anfitrião e convidado de VM

Os recursos de computação listadas anteriormente tem uma VM de anfitrião dedicada e o SO convidado. A VM de anfitrião e o SO convidado são equivalentes a VM raiz e de convidado VM no hipervisor Hyper-V. Pode recolher métricas para o anfitrião VM e o SO convidado. Além disso, é possível recolher os registos de diagnóstico para o SO convidado. Uma lista de métricas collectible para métricas de anfitrião e convidado de VM no Azure Stack estão disponíveis em [suportado métricas com o Azure Monitor no Azure Stack](azure-stack-metrics-supported.md). 

### <a name="activity-log"></a>Registo de atividades

Pode pesquisar os registos de atividade para informações sobre os recursos de computação, como visto pela infraestrutura do Azure Stack. O registo contém informações como as horas a que os recursos são criados ou destruídos. Os registos de atividades no Azure Stack é consistente com o Azure. Para obter mais informações, consulte a descrição da [descrição geral do registo de atividade no Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Origens de monitor do Azure: tudo o resto

![O Azure Monitor origens do Azure Stack - tudo o resto](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>Registos de recursos - métricas e diagnósticos

Registos de diagnóstico e métricas collectible variam consoante o tipo de recurso. Uma lista de métricas collectible para cada recurso no Azure Stack está disponível em métricas suportadas. Para obter mais informações, consulte [suportado métricas com o Azure Monitor no Azure Stack](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Registo de atividades

O registo de atividades é que o mesmo para recursos de computação. 

### <a name="uses-for-monitoring-data"></a>Utilizações de dados de monitorização

**Store e de arquivo**  

Alguns dados de monitorização já estão armazenados e disponíveis no Azure Monitor por um período de tempo definido. 
 - As métricas ficam armazenadas durante 90 dias. 
 - As entradas de registo de atividades são armazenadas durante 90 dias. 
 - Os registos de diagnóstico não são armazenados.
 - Arquive os dados para uma conta de armazenamento para retenção mais longa.

**Consulta**  

Pode utilizar a API de REST do Azure Monitor, comandos de Interface de linha de comandos (CLI) para várias plataformas, cmdlets do PowerShell ou o SDK do .NET para acessar os dados no sistema ou o armazenamento do Azure. 

**Visualização**

Visualizar os seus dados de monitorização nos elementos visuais e gráficos ajuda-o a localizar tendências mais rapidamente do que analisando os dados propriamente ditos. 

Alguns métodos de visualização incluem:
 - Utilizar o portal de utilizador e administrador do Azure Stack
 - Dados da rota para o Microsoft Power BI
 - Encaminhar os dados para uma ferramenta de visualização de terceiros utilizando transmissão em fluxo em direto ou fazendo com que a ferramenta leia a partir de um arquivo no armazenamento do Azure

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Monitorizar a métodos do acesso do Azure no Azure Stack

Em geral, pode manipular o rastreio, encaminhamento e obtenção de dados através de um dos seguintes métodos. Nem todos os métodos estão disponíveis para todas as ações ou tipos de dados.

 - [Portal do Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-use-portal)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Interface(CLI) de linha de comandos para várias plataformas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [API REST](https://docs.microsoft.com/rest/api/monitor)
 - [SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as opções do consumo de dados monitorização no Azure Stack no artigo [Consume dados de monitorização do Azure Stack](azure-stack-metrics-monitor.md).

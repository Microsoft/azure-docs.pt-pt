---
title: Utilize o armazenamento de blob para IIS e armazenamento de mesa para eventos no Monitor Azure [ Monitor] Microsoft Docs
description: O Azure Monitor pode ler os registos dos serviços Azure que escrevem diagnósticos para armazenamento de mesa ou registos IIS escritos para armazenamento de bolhas.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672400"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Recolher dados da extensão de diagnóstico do Azure aos Registos do Monitor do Azure
A extensão de diagnóstico do Azure é um [agente no Azure Monitor](agents-overview.md) que recolhe dados de monitorização do sistema operativo convidado de recursos computacionais do Azure, incluindo máquinas virtuais. Este artigo descreve como recolher os dados recolhidos pela extensão de diagnóstico do Armazenamento Azure para os Registos do Monitor Azure.

> [!NOTE]
> O agente Log Analytics no Monitor Azure é tipicamente o método preferido para recolher dados do sistema operativo de hóspedes em Registos de Monitor Estoque. Consulte [a visão geral dos agentes do Monitor Azure](agents-overview.md) para uma comparação detalhada dos agentes.

## <a name="supported-data-types"></a>Tipos de dados suportados
A extensão de diagnóstico azure armazena dados numa conta de Armazenamento Azure. Para que o Azure Monitor Logs recolha estes dados, devem estar nos seguintes locais:

| Tipo de Registo | Tipo de Recurso | Localização |
| --- | --- | --- |
| Registos do IIS |Virtual Machines <br> Funções da Web <br> Funções de trabalho |WAD-iis-logfiles (armazenamento de BLOBs) |
| Syslog |Virtual Machines |LinuxsyslogVer2v0 (armazenamento de tabelas) |
| Eventos operacionais do Service Fabric |Nós do Service Fabric |WADServiceFabricSystemEventTable |
| Eventos de Reliable Actor do Service Fabric |Nós do Service Fabric |WADServiceFabricReliableActorEventTable |
| Eventos de Reliable Services do Service Fabric |Nós do Service Fabric |WADServiceFabricReliableServiceEventTable |
| Registos de Eventos do Windows |Nós do Service Fabric <br> Virtual Machines <br> Funções da Web <br> Funções de trabalho |WADWindowsEventLogsTable (armazenamento de tabelas) |
| Registos ETW do Windows |Nós do Service Fabric <br> Virtual Machines <br> Funções da Web <br> Funções de trabalho |WADETWEventTable (armazenamento de tabelas) |

## <a name="data-types-not-supported"></a>Tipos de dados não suportados

- Dados de desempenho do sistema operativo convidado
- Registos IIS dos websites do Azure


## <a name="enable-azure-diagnostics-extension"></a>Ativar extensão de diagnóstico azure
Consulte instalar e configurar a extensão de [diagnóstico do Windows Azure (WAD)](diagnostics-extension-windows-install.md) ou utilize a [extensão de diagnóstico do Linux para monitorizar métricas e registos](../../virtual-machines/extensions/diagnostics-linux.md) para obter detalhes sobre a instalação e configuração da extensão de diagnóstico. Isto irá ausentar-se para especificar a conta de armazenamento e configurar a recolha dos dados que pretende encaminhar para o Azure Monitor Logs.


## <a name="collect-logs-from-azure-storage"></a>Recolha registos do Armazenamento Azure
Utilize o seguinte procedimento para permitir a recolha de dados de extensão de diagnóstico a partir de uma conta de Armazenamento Azure:

1. No portal Azure, vá a **Log Analytics Workspaces** e selecione o seu espaço de trabalho.
1. Clique em **registos** de contas de armazenamento na secção **Fontes** de Dados do Espaço de Trabalho do menu.
2. Clique em **Adicionar**.
3. Selecione a **conta de Armazenamento** que contém os dados a recolher.
4. Selecione o **Tipo de Dados** que pretende recolher.
5. O valor para A Fonte é automaticamente povoado com base no tipo de dados.
6. Clique **em OK** para salvar a configuração.
7. Repita para tipos de dados adicionais.

Em aproximadamente 30 minutos, é possível ver dados da conta de armazenamento no espaço de trabalho log Analytics. Verá apenas dados que são escritos para o armazenamento depois da configuração é aplicada. O espaço de trabalho não lê os dados pré-existentes da conta de armazenamento.

> [!NOTE]
> O portal não valida que a fonte existe na conta de armazenamento ou se estão a ser escritos novos dados.



## <a name="next-steps"></a>Passos seguintes

* [Recolha registos e métricas para serviços Azure](collect-azure-metrics-logs.md) para serviços Azure suportados.
* [Ativar Soluções](../../azure-monitor/insights/solutions.md) para fornecer informações sobre os dados.
* [Utilize consultas](../../azure-monitor/log-query/log-query-overview.md) de pesquisa para analisar os dados.

---
title: Utilize o armazenamento de bolhas para iis e armazenamento de mesa para eventos em Azure Monitor | Microsoft Docs
description: O Azure Monitor pode ler os registos dos serviços Azure que escrevem diagnósticos para armazenamento de mesa ou registos IIS escritos para o armazenamento de bolhas.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 2b7648155c28ec9b6cf2048246c17805b5f22404
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700733"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Recolher dados da extensão de diagnósticos do Azure para os Registos do Monitor Azure
A extensão de diagnóstico Azure é um [agente no Azure Monitor](../agents/agents-overview.md) que recolhe dados de monitorização do sistema operativo convidado de recursos compute da Azure, incluindo máquinas virtuais. Este artigo descreve como recolher dados recolhidos pela extensão de diagnóstico de Azure Storage para Azure Monitor Logs.

> [!NOTE]
> O agente Log Analytics no Azure Monitor é tipicamente o método preferido para recolher dados do sistema operativo do hóspede em Registos monitores Azure. Consulte [a visão geral dos agentes do Monitor Azure](../agents/agents-overview.md) para uma comparação detalhada dos agentes.

## <a name="supported-data-types"></a>Tipos de dados suportados
A azure diagnostica extensões armazena dados numa conta de Armazenamento Azure. Para que os Registos do Monitor Azure recolham estes dados, devem estar nos seguintes locais:

| Tipo de Registo | Tipo de Recurso | Localização |
| --- | --- | --- |
| Registos do IIS |Máquinas Virtuais <br> Funções web <br> Funções de trabalhador |wad-iis-logfiles (Blob Storage) |
| Syslog |Máquinas Virtuais |LinuxsyslogVer2v0 (Armazenamento de Mesa) |
| Eventos Operacionais do Tecido de Serviço |Nó de tecido de serviço |WADServiceFabricSystemEventTable |
| Eventos de ator confiáveis de tecido de serviço |Nó de tecido de serviço |WADServiceFabricReliableActorEventTable |
| Eventos de serviço confiáveis de tecido de serviço |Nó de tecido de serviço |WADServiceFabricReliableServiceEventTable |
| Registos do Evento Windows |Nó de tecido de serviço <br> Máquinas Virtuais <br> Funções web <br> Funções de trabalhador |WADWindowsEventLogsTable (Armazenamento de Mesa) |
| Registos ETW do Windows |Nó de tecido de serviço <br> Máquinas Virtuais <br> Funções web <br> Funções de trabalhador |WADETWEventTable (Armazenamento de Mesa) |

## <a name="data-types-not-supported"></a>Tipos de dados não suportados

- Dados de desempenho do sistema operativo convidado
- Registos do IIS a partir de websites da Azure


## <a name="enable-azure-diagnostics-extension"></a>Ativar a extensão de diagnósticos do Azure
Consulte [instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)](../agents/diagnostics-extension-windows-install.md) ou [utilizar a extensão de diagnóstico do Linux para monitorizar métricas e registos](../../virtual-machines/extensions/diagnostics-linux.md) para obter detalhes sobre a instalação e configuração da extensão de diagnóstico. Isto irá permitir-lhe especificar a conta de armazenamento e configurar a recolha dos dados que pretende encaminhar para os Registos do Monitor Azure.


## <a name="collect-logs-from-azure-storage"></a>Recolher registos do Azure Storage
Utilize o seguinte procedimento para permitir a recolha de dados de extensão de diagnósticos a partir de uma conta de Armazenamento Azure:

1. No portal Azure, vá ao **Log Analytics Workspaces** e selecione o seu espaço de trabalho.
1. Clique em **registos de contas de Armazenamento** na secção Fontes de **Dados** do Espaço de Trabalho do menu.
2. Clique  **em Adicionar**.
3. Selecione a **conta de Armazenamento** que contém os dados a recolher.
4. Selecione o **Tipo de Dados** que pretende recolher.
5. O valor de Origem é automaticamente povoado com base no tipo de dados.
6. Clique **em OK** para guardar a configuração.
7. Repita para tipos de dados adicionais.

Em aproximadamente 30 minutos, é possível ver dados da conta de armazenamento no espaço de trabalho Do Log Analytics. Só verá dados que são escritos para armazenamento após a aplicação da configuração. O espaço de trabalho não lê os dados pré-existentes da conta de armazenamento.

> [!NOTE]
> O portal não valida que a fonte exista na conta de armazenamento ou se estiver a ser escritos novos dados.



## <a name="next-steps"></a>Passos seguintes

* [Recolher registos e métricas para serviços Azure](./resource-logs.md#send-to-log-analytics-workspace) para serviços Azure suportados.
* [Permitir soluções](../insights/solutions.md) para fornecer informações sobre os dados.
* [Utilize consultas de pesquisa](../logs/log-query-overview.md) para analisar os dados.
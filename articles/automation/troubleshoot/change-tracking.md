---
title: Problemas de resolução de alterações de automação Azure
description: Este artigo diz como resolver problemas e resolver problemas com a funcionalidade de Rastreio e Inventário de Alterações da Automação Azure.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3ecd4330381db1f63a40e9ba5ff63108c6fa78fe
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205613"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Resolver problemas do Controlo de Alterações e Inventário

Este artigo descreve como resolver problemas e resolver problemas de rastreio e inventário de alterações de automação da Azure. Para obter informações gerais sobre o Rastreio e Inventário de Alterações, consulte [a visão geral do Change Tracking e Do Inventário](../change-tracking/overview.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Cenário: Os registos de rastreio e inventário de alteração não estão a aparecer para máquinas windows

#### <a name="issue"></a>Problema

Não vê nenhum resultado de Change Tracking e Inventário para máquinas Windows que tenham sido ativadas para a funcionalidade.

#### <a name="cause"></a>Causa

Este erro pode ter as seguintes causas:

* O agente Azure Log Analytics para o Windows não está a funcionar.
* A comunicação de volta à conta de Automação está a ser bloqueada.
* Os pacotes de gestão de Rastreio e Inventário de Mudança não são descarregados.
* O VM ativado pode ter vindo de uma máquina clonada que não foi preparada com a Preparação do Sistema (sysprep) com o agente Log Analytics para windows instalado.

#### <a name="resolution"></a>Resolução

Na máquina de agente do Log Analytics, vá a **C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** e execute os seguintes comandos:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Se ainda precisar de ajuda, pode recolher informações de diagnóstico e suporte de contacto.

> [!NOTE]
> O agente Log Analytics permite o rastreio de erro por predefinição. Para ativar as mensagens de erro verbose como no exemplo anterior, utilize o `VER` parâmetro. Para obter vestígios de informação, utilize `INF` quando invocar `StartTracing.cmd` .

##### <a name="log-analytics-agent-for-windows-not-running"></a>Log Analytics agente para Windows não em execução

Verifique se o agente Log Analytics do Windows** (HealthService.exe) **está a funcionar na máquina.

##### <a name="communication-to-automation-account-blocked"></a>Comunicação à conta de Automação bloqueada

Verifique o Espectador de Eventos na máquina e procure quaisquer eventos que tenham a palavra `changetracking` neles.

Para conhecer endereços e portas que devem ser permitidos para o Rastreio de Alterações e Inventário funcionar, consulte [o planeamento da Rede](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Pacotes de gestão não descarregados

Verifique se os seguintes pacotes de gestão de Rastreio de Alterações e Inventários são instalados localmente:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM de máquina clonada que não foi sysprepped

Se utilizar uma imagem clonada, sisprep a imagem primeiro e, em seguida, instale o agente Log Analytics para windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Cenário: Sem alteração de rastreio e inventário em máquinas Linux

#### <a name="issue"></a>Problema

Não vê nenhum resultado de Change Tracking e Inventário para máquinas Linux que estejam ativadas para a funcionalidade. 

#### <a name="cause"></a>Causa
Aqui estão possíveis causas específicas para esta questão:
* O agente do Log Analytics para o Linux não está a funcionar.
* O agente do Log Analytics para o Linux não está configurado corretamente.
* Existem conflitos de monitorização da integridade dos ficheiros (FIM).

#### <a name="resolution"></a>Resolução 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics agente para Linux não correr

Verifique se o daemon do agente Log Analytics do Linux **(omsagent)** está a funcionar na sua máquina. Execute a seguinte consulta no espaço de trabalho Log Analytics que está ligado à sua conta Automation.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Se não vir a sua máquina nos resultados da consulta, não fez o check-in recentemente. Deve haver um problema de configuração local e deve reinstalar o agente. Para obter informações sobre a instalação e configuração, consulte [recolher dados de registo com o agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).

Se a sua máquina aparecer nos resultados da consulta, verifique a configuração do âmbito. Consulte [soluções de monitorização de direções no Azure Monitor](../../azure-monitor/insights/solution-targeting.md).

Para uma maior resolução de problemas desta questão, consulte [A questão: Não está a ver quaisquer dados do Linux](../../azure-monitor/platform/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics agente para Linux não configurado corretamente

O agente Log Analytics do Linux pode não estar configurado corretamente para a recolha de registos e linha de comando utilizando a ferramenta OMS Log Collector. Consulte [o tracking de alterações e a visão geral do inventário](../change-tracking/overview.md).

##### <a name="fim-conflicts"></a>Conflitos FIM

A funcionalidade FIM do Azure Security Center pode estar a validar incorretamente a integridade dos seus ficheiros Linux. Verifique se o FIM está operacional e corretamente configurado para a monitorização de ficheiros Linux. Consulte [o tracking de alterações e a visão geral do inventário](../change-tracking/overview.md).

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) , a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga a comunidade Azure a respostas, suporte e especialistas.
* Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Apoio**.

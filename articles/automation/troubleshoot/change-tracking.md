---
title: Problemas de resolução de problemas De rastreio de automatização e problemas de inventário
description: Este artigo diz como resolver problemas e resolver problemas com a funcionalidade de Rastreio e Inventário de Alterações de Automação azure.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3fe28ba0871009785b1bb8b263b42f453c2918be
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684860"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Problemas de mudança de mudança e problemas de inventário

Este artigo descreve como resolver problemas e resolver problemas de rastreio e inventário de alterações de automatização do Azure. Para obter informações gerais sobre o rastreio e o inventário de alterações, consulte a visão geral do [Rastreio de Alterações e do Inventário](../change-tracking.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Cenário: Alterar registos de rastreio e inventário não estão a aparecer para máquinas Windows

#### <a name="issue"></a>Problema

Não se vê quaisquer resultados de Rastreio de Alterações e Inventário para máquinas Windows que tenham sido ativadas para a funcionalidade.

#### <a name="cause"></a>Causa

Este erro pode ter as seguintes causas:

* O agente Azure Log Analytics para windows não está a funcionar.
* A comunicação de volta à conta da Automação está a ser bloqueada.
* Os pacotes de gestão de Rastreio de Mudanças e Inventário não são descarregados.
* O VM ativado pode ter vindo de uma máquina clonada que não foi preparada com a Preparação do Sistema (sysprep) com o agente Log Analytics para windows instalado.

#### <a name="resolution"></a>Resolução

Na máquina do agente Log Analytics, aceda a **C:\Program Files\Microsoft Monitoring Agent\Tools** e execute os seguintes comandos:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Se ainda precisar de ajuda, pode recolher informações de diagnóstico e suporte de contato.

> [!NOTE]
> O agente Log Analytics permite o rastreio de erros por padrão. Para ativar as mensagens de erro verbosas como no exemplo anterior, utilize o `VER` parâmetro. Para obter vestígios de informação, utilize `INF` quando invocar `StartTracing.cmd` .

##### <a name="log-analytics-agent-for-windows-not-running"></a>Agente de Log Analytics para Windows não está a funcionar

Verifique se o agente Log Analytics para Windows **(HealthService.exe)** está a funcionar na máquina.

##### <a name="communication-to-automation-account-blocked"></a>Conta de comunicação à Automação bloqueada

Consulte o Espectador de Eventos na máquina e procure quaisquer eventos que tenham a palavra `changetracking` neles.

Para conhecer endereços e portas que devem ser permitidos para que o Rastreio de Alterações e Inventário funcione, consulte o [planeamento da rede.](../automation-hybrid-runbook-worker.md#network-planning)

##### <a name="management-packs-not-downloaded"></a>Pacotes de gestão não descarregados

Verifique se os seguintes pacotes de gestão de rastreio de alterações e inventário estão instalados localmente:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM de máquina clonada que não foi sysprepped

Se utilizar uma imagem clonada, siprepare a imagem primeiro e, em seguida, instale o agente Log Analytics para windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Cenário: Sem alterações de rastreio e inventário resultados nas máquinas Linux

#### <a name="issue"></a>Problema

Não se vê nenhum resultado de Rastreio de Alterações e Inventário para as máquinas Linux que estejam ativadas para a funcionalidade. 

#### <a name="cause"></a>Causa
Aqui estão possíveis causas específicas para esta questão:
* O agente de Log Analytics do Linux não está a funcionar.
* O agente Log Analytics do Linux não está configurado corretamente.
* Existem conflitos de monitorização da integridade dos ficheiros (FIM).

#### <a name="resolution"></a>Resolução 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agente de Log Analytics para Linux não correndo

Verifique se o daemon do agente Log Analytics para o Linux **(omsagent)** está a funcionar na sua máquina. Faça a seguinte consulta no espaço de trabalho log Analytics que está ligado à sua conta Deautomação.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Se não vir a sua máquina nos resultados da consulta, não fez o check-in recentemente. Deve haver um problema de configuração local e deve reinstalar o agente. Para obter informações sobre instalação e configuração, consulte [Recolher dados de registo com o agente Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent).

Se a sua máquina aparecer nos resultados da consulta, verifique a configuração do âmbito. Consulte soluções de [monitorização de direcionamento no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Para mais resolução de problemas desta questão, consulte [Edição: Não está a ver nenhum dado linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agente de Log Analytics para Linux não configurado corretamente

O agente Log Analytics para o Linux pode não estar corretamente configurado para a recolha de saída de log e linha de comando utilizando a ferramenta OMS Log Collector. Consulte a visão geral do [Rastreio de Alterações e do Inventário](../change-tracking.md).

##### <a name="fim-conflicts"></a>Conflitos fim

A funcionalidade FIM do Azure Security Center pode estar a validar incorretamente a integridade dos seus ficheiros Linux. Verifique se o FIM está operacional e corretamente configurado para a monitorização de ficheiros Linux. Consulte a visão geral do [Rastreio de Alterações e do Inventário](../change-tracking.md).

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga a comunidade Azure a respostas, apoios e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Suporte**.

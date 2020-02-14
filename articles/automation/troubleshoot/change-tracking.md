---
title: Problemas de resolução de problemas com o Rastreio de Mudanças Azure
description: Saiba como resolver problemas e resolver problemas com a funcionalidade de Rastreio e Inventário de Alterações de Automação Azure.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198535"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Resolver Problemas do Controlo de Alterações e do Inventário

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Cenário: Change Tracking records não estão a aparecer para máquinas Windows

#### <a name="issue"></a>Problema

Não se vê nenhum resultado de Rastreio de Alterações ou Inventário para máquinas Windows que estejam a bordo para O Rastreio de Alterações.

#### <a name="cause"></a>Causa

Este erro pode ter as seguintes causas:

* O Agente de Monitorização da Microsoft não está a funcionar.
* A comunicação de volta à conta da Automação está a ser bloqueada.
* Os pacotes de gestão para Change Tracking não são descarregados.
* O VM a bordo pode ter vindo de uma máquina clonada que não foi sysprepped com o Agente de Monitorização da Microsoft instalado.

#### <a name="resolution"></a>Resolução

As soluções descritas abaixo podem ajudar a resolver o seu problema. Se ainda precisar de ajuda, pode recolher informações de diagnóstico e suporte de contato. Na máquina do agente, navegue para C:\Program Files\Microsoft Monitoring Agent\Tools e executar os seguintes comandos:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Por predefinição, o rastreio de erros está ativado. Para ativar as mensagens de erro verbosas como no exemplo anterior, utilize o parâmetro *VER.* Para obter vestígios de informações, utilize *o INF* ao invocar **startTracing.cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent não está a funcionar

Verifique se o Agente de Monitorização da Microsoft (HealthService.exe) está a funcionar na máquina.

##### <a name="communication-to-automation-account-blocked"></a>Conta de comunicação à Automação bloqueada

Consulte o Espectador de Eventos na máquina e procure quaisquer eventos que tenham a palavra "changetracking" neles.

Consulte [os recursos automate no seu datacenter ou nuvem utilizando](../automation-hybrid-runbook-worker.md#network-planning) o Hybrid Runbook Worker para saber sobre endereços e portas que devem ser permitidos para o Change Tracking funcionar.

##### <a name="management-packs-not-downloaded"></a>Pacotes de gestão não descarregados

Verifique se os seguintes pacotes de gestão de rastreio de alterações e inventário estão instalados localmente:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM de máquina clonada que não foi sysprepped

Se utilizar uma imagem clonada, size a imagem primeiro e, em seguida, instale o Microsoft Monitoring Agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Cenário: Sem alterações de rastreio ou inventário resultados em máquinas Linux

#### <a name="issue"></a>Problema

Não se vê nenhum inventário ou resultado de Rastreio de Alterações para máquinas Linux que estejam a bordo para O Rastreio de Alterações. 

#### <a name="cause"></a>Causa
Aqui estão possíveis causas específicas para esta questão:
* O agente de Log Analytics do Linux não está a funcionar.
* O agente Log Analytics do Linux não está configurado corretamente.
* Existem conflitos de monitorização da integridade dos ficheiros (FIM).

#### <a name="resolution"></a>Resolução 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agente de Log Analytics para Linux não correndo

Verifique se o daemon do agente Log Analytics para o Linux (omsagent) está a funcionar na sua máquina. Faça a seguinte consulta no espaço de trabalho log Analytics que está ligado à sua conta Deautomação.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Se não vir a sua máquina nos resultados da consulta, não fez o check-in recentemente. Deve haver um problema de configuração local e deve reinstalar o agente. Para obter informações sobre instalação e configuração, consulte [Recolher dados de registo com o agente Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Se a sua máquina aparecer nos resultados da consulta, verifique a configuração do âmbito. Consulte soluções de [monitorização de direcionamento no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Para mais resolução de problemas desta questão, consulte a [Edição: Não está a ver nenhum dado linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agente de Log Analytics para Linux não configurado corretamente

O agente Log Analytics para o Linux pode não estar corretamente configurado para a recolha de saída da linha de registo e de comando utilizando a ferramenta OMS Log Collector. Consulte [as alterações de rastreio no seu ambiente com a solução Change Tracking](../change-tracking.md).

##### <a name="fim-conflicts"></a>Conflitos fim

A funcionalidade FIM do Azure Security Center pode estar a validar incorretamente a integridade dos seus ficheiros Linux. Verifique se o FIM está operacional e corretamente configurado para a monitorização de ficheiros Linux. Consulte [as alterações de rastreio no seu ambiente com a solução Change Tracking](../change-tracking.md).

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema ou não conseguir resolver o seu problema, utilize um dos seguintes canais para obter mais suporte.

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

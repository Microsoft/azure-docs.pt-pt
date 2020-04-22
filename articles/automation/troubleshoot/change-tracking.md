---
title: Problemas de resolução de problemas com rastreio de mudança e inventário
description: Aprenda a resolver problemas e a resolver problemas com a solução azure Automation Change Tracking and Inventory.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 11c1fd05055922b07801c20d525d852d5360b069
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679355"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Problemas de mudança de mudança e problemas de inventário

Este artigo descreve como resolver problemas em questões de Rastreio e Inventário de Mudança.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Cenário: Alterar registos de rastreio e inventário não estão a aparecer para máquinas Windows

#### <a name="issue"></a>Problema

Não se vê nenhum resultado de Rastreio de Alterações e Inventário para máquinas Windows que estejam a bordo.

#### <a name="cause"></a>Causa

Este erro pode ter as seguintes causas:

* O agente Log Analytics para windows não está a funcionar.
* A comunicação de volta à conta da Automação está a ser bloqueada.
* Os pacotes de gestão de Rastreio de Mudanças e Inventário não são descarregados.
* O VM a bordo pode ter vindo de uma máquina clonada que não foi sysprepped com o agente Log Analytics para windows instalado.

#### <a name="resolution"></a>Resolução

Na máquina do agente Log Analytics, navegue para **C:\Program Files\Microsoft Monitoring Agent\Tools** e executar os seguintes comandos:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Se ainda precisar de ajuda, pode recolher informações de diagnóstico e suporte de contato. 

> [!NOTE]
> O agente Log Analyticss permite o rastreio de erros por padrão. Para ativar as mensagens de erro verbosas como no exemplo anterior, utilize o `VER` parâmetro. Para obter vestígios `INF` de `StartTracing.cmd`informação, utilize ao invocar .

##### <a name="log-analytics-agent-for-windows-not-running"></a>Agente de Log Analytics para Windows não está a funcionar

Verifique se o agente Log Analytics para Windows **(HealthService.exe)** está a funcionar na máquina.

##### <a name="communication-to-automation-account-blocked"></a>Conta de comunicação à Automação bloqueada

Consulte o Espectador de Eventos na máquina `changetracking` e procure quaisquer eventos que tenham a palavra neles.

Consulte [os recursos automate no seu datacenter ou nuvem utilizando](../automation-hybrid-runbook-worker.md#network-planning) o Hybrid Runbook Worker para saber sobre endereços e portas que devem ser permitidos para o Rastreio de Alterações e Inventário funcionar.

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

Não se vê nenhum inventário e resultados de Rastreio de Alterações para máquinas Linux que estejam a bordo para a solução. 

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

O agente Log Analytics para o Linux pode não estar corretamente configurado para a recolha de saída da linha de registo e de comando utilizando a ferramenta OMS Log Collector. Consulte [as alterações de rastreio no seu ambiente com a solução de Rastreio e Inventário](../change-tracking.md)de Alterações .

##### <a name="fim-conflicts"></a>Conflitos fim

A funcionalidade FIM do Azure Security Center pode estar a validar incorretamente a integridade dos seus ficheiros Linux. Verifique se o FIM está operacional e corretamente configurado para a monitorização de ficheiros Linux. Consulte [as alterações de rastreio no seu ambiente com a solução de Rastreio e Inventário](../change-tracking.md)de Alterações .

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema acima ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport)a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

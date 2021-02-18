---
title: Problemas de resolução de alterações de automação Azure
description: Este artigo diz como resolver problemas e resolver problemas com a funcionalidade de Rastreio e Inventário de Alterações da Automação Azure.
services: automation
ms.subservice: change-inventory-management
ms.date: 02/15/2021
ms.topic: troubleshooting
ms.openlocfilehash: 9fe53a343a9f6675519b60d37d077886adaf8a9d
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651170"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Resolver problemas do Controlo de Alterações e Inventário

Este artigo descreve como resolver problemas e resolver problemas de rastreio e inventário de alterações de automação da Azure. Para obter informações gerais sobre o Rastreio e Inventário de Alterações, consulte [a visão geral do Change Tracking e Do Inventário](../change-tracking/overview.md).

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Cenário: A máquina já está registada numa conta diferente

### <a name="issue"></a>Problema

Recebe a seguinte mensagem de erro:

```error
Unable to Register Machine for Change Tracking, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

A máquina já foi implantada noutro espaço de trabalho para o Change Tracking.

### <a name="resolution"></a>Resolução

1. Certifique-se de que a sua máquina está a reportar para o espaço de trabalho correto. Para obter orientações sobre como verificar isto, consulte [verificar a conectividade do agente com o Azure Monitor](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Certifique-se também de que este espaço de trabalho está ligado à sua conta Azure Automation. Para confirmar, vá à sua conta de Automação e selecione **espaço de trabalho linked** under Related **Resources**.

1. Certifique-se de que as máquinas aparecem no espaço de trabalho Do Log Analytics ligado à sua conta Automation. Executar a seguinte consulta no espaço de trabalho Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

   Se não vir a sua máquina nos resultados da consulta, não fez o check-in recentemente. Deve haver um problema de configuração local. Deverá reinstalar o agente Log Analytics.

   Se a sua máquina estiver listada nos resultados da consulta, verifique na propriedade Solutions que **a changeTracking** está listada. Isto verifica-se que está registado no Change Tracking and Inventory. Se não for, verifique se existem problemas de configuração de âmbito. A configuração de âmbito determina quais as máquinas configuradas para o Rastreio de Alterações e Inventário. Para configurar a configuração de âmbito para a máquina-alvo, consulte [Ativar o Rastreio e Inventário de Alterações a partir de uma conta de Automação](../change-tracking/enable-from-automation-account.md).

   No seu espaço de trabalho, faça esta consulta.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

1. Se obtém um ```Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota``` resultado, a quota definida no seu espaço de trabalho foi alcançada, o que impediu que os dados fossem guardados. No seu espaço de trabalho, vá ao **Uso e custos estimados.** Selecione um novo **nível de preços** que lhe permita utilizar mais dados, ou clique na **tampa diária** e remova a tampa.

:::image type="content" source="./media/change-tracking/change-tracking-usage.png" alt-text="Utilização e custos estimados." lightbox="./media/change-tracking/change-tracking-usage.png":::

Se o seu problema ainda não estiver resolvido, siga os passos no [Deploy a Windows Hybrid Runbook Worker](../automation-windows-hrw-install.md) para reinstalar o Trabalhador Híbrido para o Windows. Para o Linux, siga os passos em [Implementar um Trabalhador de Runbook Híbrido Linux.](../automation-linux-hrw-install.md)

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

Verifique se o agente Log Analytics do Windows **(HealthService.exe)** está a funcionar na máquina.

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

Se não vir a sua máquina nos resultados da consulta, não fez o check-in recentemente. Deve haver um problema de configuração local e deve reinstalar o agente. Para obter informações sobre a instalação e configuração, consulte [recolher dados de registo com o agente Log Analytics](../../azure-monitor/agents/log-analytics-agent.md).

Se a sua máquina aparecer nos resultados da consulta, verifique a configuração do âmbito. Consulte [soluções de monitorização de direções no Azure Monitor](../../azure-monitor/insights/solution-targeting.md).

Para uma maior resolução de problemas desta questão, consulte [A questão: Não está a ver quaisquer dados do Linux](../../azure-monitor/agents/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics agente para Linux não configurado corretamente

O agente Log Analytics do Linux pode não estar configurado corretamente para a recolha de registos e linha de comando utilizando a ferramenta OMS Log Collector. Consulte [o tracking de alterações e a visão geral do inventário](../change-tracking/overview.md).

##### <a name="fim-conflicts"></a>Conflitos FIM

A funcionalidade FIM do Azure Security Center pode estar a validar incorretamente a integridade dos seus ficheiros Linux. Verifique se o FIM está operacional e corretamente configurado para a monitorização de ficheiros Linux. Consulte [o tracking de alterações e a visão geral do inventário](../change-tracking/overview.md).

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) , a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga a comunidade Azure a respostas, suporte e especialistas.
* Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Apoio**.

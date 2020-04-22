---
title: Resolução de problemas no embarque de soluções de gestão da Automação Azure
description: Aprenda a resolver problemas com erros de embarque.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679222"
---
# <a name="troubleshoot-solution-onboarding"></a>Solução de resolução de problemas no embarque

Poderá receber erros ao embarcar na solução de Gestão de Atualizações ou na solução de Rastreio e Inventário de Alterações. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Cenário: Renomear um nó registado requer não registar ou registar-se novamente

#### <a name="issue"></a>Problema

Um nó está registado na Azure Automation e, em seguida, o nome do computador do sistema operativo é alterado. Os relatórios do nó continuam a aparecer com o nome original.

#### <a name="cause"></a>Causa

Renomear os nódosos registados não atualiza o nome do nó na Automatização Azure.

#### <a name="resolution"></a>Resolução

Desregistre o nó da Configuração do Estado da Automação Azure e, em seguida, registe-o novamente. Os relatórios publicados ao serviço antes desse tempo deixarão de estar disponíveis.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Cenário: Não é suportado a reassinatura de certificados através de procuração https

#### <a name="issue"></a>Problema

Ao ligar através de uma solução de procuração que interrompa o tráfego HTTPS e depois volta a encriptar o tráfego utilizando um novo certificado, o serviço não permite a ligação.

#### <a name="cause"></a>Causa

A Azure Automation não suporta a reassinatura de certificados utilizados para encriptar o tráfego.

#### <a name="resolution"></a>Resolução

Atualmente, não existe qualquer suposição para esta questão.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Cenário: O embarque falha com a mensagem - A solução não pode ser ativada

#### <a name="issue"></a>Problema

Recebe uma das seguintes mensagens quando tenta embarcar num VM para uma solução:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Este erro é causado por permissões incorretas ou em falta no VM ou no espaço de trabalho, ou para o utilizador.

#### <a name="resolution"></a>Resolução

Certifique-se de que tem [permissões corretas necessárias para a bordo das máquinas](../automation-role-based-access-control.md#onboarding-permissions) e, em seguida, tente embarcar novamente a bordo da solução. Se receber o `The solution cannot be enabled on this VM because the permission to read the workspace is missing`erro, certifique-se de que tem a `Microsoft.OperationalInsights/workspaces/read` permissão para poder descobrir se o VM está a bordo de um espaço de trabalho.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Cenário: O embarque falha com a mensagem: Falha na configuração da conta de automatização para o registo de diagnóstico

#### <a name="issue"></a>Problema

Recebe a seguinte mensagem quando tenta embarcar num VM para uma solução:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Este erro pode ser causado se o nível de preços não corresponder ao modelo de faturação da subscrição. Consulte [a utilização de monitorização e os custos estimados no Monitor Azure](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Resolução

Crie manualmente o seu espaço de trabalho Log Analytics e repita o processo de embarque para selecionar o espaço de trabalho criado.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Cenário: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Este código de erro significa que a consulta de grupo de pesquisa guardada utilizada para direcionar a solução não está formatada corretamente. 

#### <a name="cause"></a>Causa

Podes ter alterado a consulta, ou o sistema pode tê-la alterado.

#### <a name="resolution"></a>Resolução

Pode eliminar a consulta para a solução e, em seguida, embarcar novamente a bordo da solução, o que recria a consulta. A consulta pode ser encontrada no seu espaço de trabalho, sob **pesquisas guardadas**. O nome da consulta é **MicrosoftDefaultComputerGroup**, e a categoria da consulta é o nome da solução associada. Se várias soluções estiverem ativadas, a consulta do **MicrosoftDefaultComputerGroup** mostra várias vezes em **Pesquisas Guardadas**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Cenário: Violação de políticas

#### <a name="issue"></a>Problema

Este código de erro indica que a implementação falhou devido à violação de uma ou mais políticas.

#### <a name="cause"></a>Causa 

Uma política está a impedir a operação de concluir.

#### <a name="resolution"></a>Resolução

Para implementar com sucesso a solução, deve considerar alterar a política indicada. Como existem muitos tipos diferentes de políticas que podem ser definidas, as mudanças necessárias dependem da política que é violada. Por exemplo, se uma política for definida num grupo de recursos que nega a permissão para alterar o conteúdo de alguns recursos contidos, poderá escolher uma destas correções:

* Remova completamente a política.
* Tente embarcar na solução para um grupo de recursos diferente.
* Redirecione a política para um recurso específico, por exemplo, uma conta de Automação.
* Reveja o conjunto de recursos que a política está configurada para negar.

Verifique as notificações no canto superior direito do portal Azure ou navegue para o grupo de recursos que contenha a sua conta de Automação e selecione **Implementações** em **Definições** para visualizar a implementação falhada. Para saber mais sobre a Política Azure, consulte a [visão geral da Política Azure.](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Cenário: Erros que tentam desvincular um espaço de trabalho

#### <a name="issue"></a>Problema

Recebe o seguinte erro ao tentar desligar um espaço de trabalho:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Este erro ocorre quando ainda tem soluções ativas no seu espaço de trabalho Log Analytics que dependem da sua conta de Automação e do espaço de trabalho log Analytics.

### <a name="resolution"></a>Resolução

Retire as seguintes soluções do seu espaço de trabalho se estiver a utilizá-las:

* Gestão de Atualizações
* Controlo de Alterações e Inventário
* Iniciar/Parar VMs durante horas de inatividade

Assim que remover as soluções, pode desligar o seu espaço de trabalho. É importante limpar quaisquer artefactos existentes a partir destas soluções do seu espaço de trabalho e da sua conta de Automação 

* Para gestão de atualizações, remova as Implementações de Atualização (Horários) da sua conta Deautomação.
* Para os VMs de arranque/paragem durante o horário de folga, remova quaisquer fechaduras nos componentes da solução na sua conta De automatização em **'Fechaduras de** **Definições** > ' . Ver [Remover os VMs de arranque/paragem durante a solução fora de horas](../automation-solution-vm-management.md#remove-the-solution).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Log Analytics para falhas de extensão do Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Uma instalação do agente Log Analytics para extensão do Windows pode falhar por uma variedade de razões. A secção seguinte descreve problemas de embarque que podem causar falhas durante a implementação do agente Log Analytics para a extensão do Windows.

>[!NOTE]
>O agente Log Analytics para windows é o nome utilizado atualmente na Automatização Azure para o Agente de Monitorização da Microsoft (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Cenário: Ocorreu uma exceção durante um pedido do WebClient

O Log Analytics para a extensão do Windows no VM não consegue comunicar com recursos externos e a implementação falha.

#### <a name="issue"></a>Problema

Seguem-se exemplos de mensagens de erro que são devolvidas:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Causa

Algumas causas potenciais para este erro são:

* Um proxy configurado no VM só permite portas específicas.
* Uma definição de firewall bloqueou o acesso às portas e endereços necessários.

#### <a name="resolution"></a>Resolução

Certifique-se de que tem as portas e endereços adequados abertos para comunicação. Para obter uma lista de portas e endereços, consulte o planeamento da [sua rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Cenário: Instalação falhou devido a problemas ambientais transitórios

A instalação do Log Analytics para extensão do Windows falhou durante a implementação devido a outra instalação ou ação que bloqueou a instalação

#### <a name="issue"></a>Problema

Seguem-se exemplos de mensagens de erro:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Causa

Algumas causas potenciais para este erro são:

* Outra instalação está em andamento.
* O sistema é acionado para reiniciar durante a implementação do modelo.

#### <a name="resolution"></a>Resolução

Este erro é de natureza transitória. Tente novamente a instalação para instalar a extensão.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Cenário: Tempo de instalação

A instalação do Log Analytics para a extensão do Windows não terminou devido a um intervalo.

#### <a name="issue"></a>Problema

Segue-se um exemplo de uma mensagem de erro que pode ser devolvida:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Este tipo de erro ocorre porque o VM está sob uma carga pesada durante a instalação.

### <a name="resolution"></a>Resolução

Tente instalar o agente Log Analytics para a extensão do Windows quando o VM estiver sob uma carga mais baixa.

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema acima ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport)a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

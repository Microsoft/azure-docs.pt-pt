---
title: Soluções de gestão da Automação Azure
description: Saiba como resolver erros de embarque com as soluções de Gestão de Atualização, Rastreio de Alterações e Inventário
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ae359e5210a9a11c33dd3ff9b474e28aa2548c57
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536969"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Erros de resolução de problemas ao embarcar na Gestão de Atualizações, Rastreio de Alterações e Inventário

Pode encontrar erros ao embarcar em soluções como Gestão de Atualizações ou Rastreio de Alterações e Inventário. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>Cenário: Renomear um nó registado requer desregistar/registar novamente

#### <a name="issue"></a>Problema

Um nó está registado na Azure Automation e, em seguida, o nome do computador do sistema operativo é alterado.  Os relatórios do nó continuam a aparecer com o nome original.

#### <a name="cause"></a>Causa

Renomear os nódosos registados não atualiza o nome do nó na Automatização Azure.

#### <a name="resolution"></a>Resolução

Desregistre o nó da Configuração do Estado da Automação Azure e, em seguida, registe-o novamente.  Os relatórios publicados ao serviço antes desse tempo deixarão de estar disponíveis.


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Cenário: Não é suportado a reassinatura de certificados através de procuração https

#### <a name="issue"></a>Problema

Os clientes relataram que ao ligar através de uma solução de procuração que termina o tráfego https e depois reencripta o tráfego usando um novo certificado, o serviço não permite a ligação.

#### <a name="cause"></a>Causa

A Azure Automation não suporta a reassinatura de certificados utilizados para encriptar o tráfego.

#### <a name="resolution"></a>Resolução

Não há supor para esta questão.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Cenário: O embarque falha com a mensagem - A solução não pode ser ativada

#### <a name="issue"></a>Problema

Recebe uma das seguintes mensagens quando tenta embarcar numa máquina virtual para uma solução:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Este erro é causado por permissões incorretas ou em falta na máquina virtual, no espaço de trabalho ou no utilizador.

#### <a name="resolution"></a>Resolução

Certifique-se de que tem permissões corretas para embarcar na máquina virtual. Reveja as [permissões necessárias para a bordo das máquinas](../automation-role-based-access-control.md#onboarding-permissions) e tente voltar a bordo da solução. Se receber o `The solution cannot be enabled on this VM because the permission to read the workspace is missing`erro, certifique-se de que tem a `Microsoft.OperationalInsights/workspaces/read` permissão para poder descobrir se o VM está a bordo de um espaço de trabalho.

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Cenário: O embarque falha com a mensagem - Falha na configuração da Conta de Automação para o registo de diagnóstico

#### <a name="issue"></a>Problema

Recebe a seguinte mensagem quando tenta embarcar numa máquina virtual para uma solução:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Este erro pode ser causado se o nível de preços não corresponder ao modelo de faturação da subscrição. Para mais informações, consulte a utilização do Monitor e os [custos estimados no Monitor Azure.](https://aka.ms/PricingTierWarning)

#### <a name="resolution"></a>Resolução

Crie manualmente o seu espaço de trabalho Log Analytics e repita o processo de embarque para selecionar o espaço de trabalho criado.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Cenário: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Este código de erro significa que a consulta de grupo de pesquisa guardada utilizada para direcionar a solução não foi formatada corretamente. 

#### <a name="cause"></a>Causa

Pode ter alterado a consulta, ou pode ter sido alterada pelo sistema.

#### <a name="resolution"></a>Resolução

Pode eliminar a consulta para esta solução e reembarcar a solução, que recria a consulta. A consulta pode ser encontrada dentro do seu espaço de trabalho, sob **pesquisas guardadas**. O nome da consulta é **MicrosoftDefaultComputerGroup**, e a categoria da consulta é o nome da solução associada a esta consulta. Se várias soluções estiverem ativadas, o **MicrosoftDefaultComputerGroup** mostra várias vezes em **Pesquisas Guardadas**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Cenário: Violação de políticas

#### <a name="issue"></a>Problema

Este código de erro significa que a implementação falhou devido à violação de uma ou mais políticas.

#### <a name="cause"></a>Causa 

Está em vigor uma política que está a bloquear a conclusão da operação.

#### <a name="resolution"></a>Resolução

Para implementar com sucesso a solução, é necessário considerar alterar a política indicada. Como existem muitos tipos diferentes de políticas que podem ser definidas, as mudanças específicas necessárias dependem da política que é violada. Por exemplo, se uma política fosse definida num grupo de recursos que negasse a permissão para alterar o conteúdo de certos tipos de recursos dentro desse grupo de recursos, poderia, por exemplo, fazer qualquer um dos seguintes recursos:

* Remova completamente a política.
* Tente embarcar para um grupo de recursos diferente.
* Rever a política, por exemplo:
  * Redirecionando a política a um recurso específico (como por exemplo, a uma conta específica de Automação).
  * Revendo o conjunto de recursos que a política foi configurada para negar.

Verifique as notificações no canto superior direito do portal Azure ou navegue para o grupo de recursos que contenha a sua conta de automação e selecione **Implementações** em **Definições** para visualizar a implementação falhada. Para saber mais sobre a visita da Política Azure: [Visão geral da Política azure.](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Cenário: Erros que tentam desvincular um espaço de trabalho

#### <a name="issue"></a>Problema

Recebe o seguinte erro ao tentar desligar um espaço de trabalho:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Este erro ocorre quando ainda tem soluções ativas no seu espaço de trabalho Log Analytics que dependem da sua Conta de Automação e do espaço de trabalho do Log Analytics.

### <a name="resolution"></a>Resolução

Para resolver isto, terá de remover as seguintes soluções do seu Espaço de Trabalho se estiver a utilizá-las:

* Gestão de Atualizações
* Monitorização de Alterações
* Iniciar/Parar VMs durante horas de inatividade

Assim que remover as soluções, pode desligar o seu espaço de trabalho. É importante limpar também quaisquer artefactos existentes a partir dessas soluções do seu espaço de trabalho e Conta de Automação.  

* Gestão de Atualizações
  * Remover Implementações de Atualizações (Horários) da sua Conta de Automação
* Iniciar/Parar VMs durante horas de inatividade
  * Remova quaisquer fechaduras nos componentes da solução na sua Conta de Automação em **'Fechaduras de** **Definições** > ' .
  * Para medidas adicionais para remover os VMs de arranque/paragem durante a solução off-hours, [remova o VM de arranque/paragem durante a solução off-hours](../automation-solution-vm-management.md#remove-the-solution).

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>Falhas na extensão do MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Ao implementar uma solução, são implantados vários recursos conexos. Um desses recursos é a extensão do agente de monitorização da Microsoft ou o agente Log Analytics para o Linux. Estas são extensões de máquina virtual instaladas pelo Agente Convidado da máquina virtual que é responsável pela comunicação com o espaço de trabalho configurado Log Analytics, com o propósito de posterior coordenação do download de binários e outros ficheiros de que a solução de que está a embarcar dependem assim que começa a execução.
Normalmente, fica a tomar conhecimento do MMA ou do agente Log Analytics para falhas de instalação do Linux a partir de uma notificação que aparece no Centro de Notificações. Clicar nessa notificação dá mais informações sobre a falha específica. A navegação para o recurso dos Grupos de Recursos e, em seguida, para o elemento Deimplantação no seu interior também fornece detalhes sobre as falhas de implantação que ocorreram.
A instalação do agente MMA ou Log Analytics para o Linux pode falhar por uma variedade de razões, e as medidas a tomar para resolver estas falhas variam, dependendo do problema. Seguir-se-ão passos específicos de resolução de problemas.

A secção seguinte descreve vários problemas que pode encontrar ao embarcar que causam uma falha na implementação da extensão mma.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Cenário: Ocorreu uma exceção durante um pedido do WebClient

A extensão de MMA na máquina virtual não consegue comunicar com recursos externos e falha a implantação.

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

* Há um proxy configurado no VM, que só permite portas específicas.

* Uma definição de firewall bloqueou o acesso às portas e endereços necessários.

#### <a name="resolution"></a>Resolução

Certifique-se de que tem as portas e endereços adequados abertos para comunicação. Para obter uma lista de portas e endereços, consulte o planeamento da [sua rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Cenário: Instalação falhou devido a problemas ambientais transitórios

A instalação da extensão do Agente de Monitorização da Microsoft falhou durante a implementação devido a outra instalação ou ação que bloqueou a instalação

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

* Outra instalação está em andamento
* O sistema é acionado para reiniciar durante a implementação do modelo

#### <a name="resolution"></a>Resolução

Este erro é um erro transitório na natureza. Tente novamente a instalação para instalar a extensão.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Cenário: Tempo de instalação

A instalação da extensão MMA não terminou devido a um intervalo.

#### <a name="issue"></a>Problema

O exemplo seguinte é de uma mensagem de erro que pode ser devolvida:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Este erro ocorre porque a máquina virtual está sob uma carga pesada durante a instalação.

### <a name="resolution"></a>Resolução

Tente instalar a extensão MMA quando o VM estiver sob uma carga mais baixa.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

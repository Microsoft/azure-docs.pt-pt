---
title: Problemas de resolução de problemas Azure Automation problemas de implementação
description: Este artigo diz como resolver problemas e resolver problemas que surgem ao implementar funcionalidades da Azure Automation.
services: automation
ms.date: 06/30/2020
ms.topic: conceptual
ms.service: automation
ms.openlocfilehash: ca2f866dc882e003469163a22d32d3d72031443a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85801034"
---
# <a name="troubleshoot-feature-deployment-issues"></a>Resolver problemas de implementação de funcionalidades

Pode receber mensagens de erro quando implementar a função de Gestão de Atualização da Automatização Azure ou a função de Rastreio e Inventário de Alterações nos seus VMs. Este artigo descreve os erros que podem ocorrer e como resolvê-los.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Cenário: Renomear um nó registado requer não registro ou registar-se novamente

#### <a name="issue"></a>Problema

Um nó é registado na Azure Automation e, em seguida, o nome do computador do sistema operativo é alterado. Os relatórios do nó continuam a aparecer com o nome original.

#### <a name="cause"></a>Causa

Renomear os nós registados não atualiza o nome do nó na Azure Automation.

#### <a name="resolution"></a>Resolução

Desagregar o nó da Configuração do Estado da Automação Azure e, em seguida, registá-lo novamente. Os relatórios publicados no serviço antes desse tempo deixarão de estar disponíveis.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>Cenário: A reconsessão de certificados através do proxy HTTPS não é suportada

#### <a name="issue"></a>Problema

Quando se liga através de um proxy que termina o tráfego HTTPS e depois reencriptam o tráfego usando um novo certificado, o serviço não permite a ligação.

#### <a name="cause"></a>Causa

A Azure Automation não suporta certificados de resignação usados para encriptar o tráfego.

#### <a name="resolution"></a>Resolução

Não há atualmente nenhuma solução para esta questão.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-feature-deployment-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Cenário: A implementação da funcionalidade falha com a mensagem "A solução não pode ser ativada"

#### <a name="issue"></a>Problema

Recebe uma das seguintes mensagens quando tenta ativar uma funcionalidade num VM:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Este erro é causado por permissões incorretas ou em falta no VM ou no espaço de trabalho, ou para o utilizador.

#### <a name="resolution"></a>Resolução

Certifique-se de que tem permissões de implementação de [funcionalidades corretas](../automation-role-based-access-control.md#feature-setup-permissions)e, em seguida, tente implementar novamente a funcionalidade. Se receber a mensagem de `The solution cannot be enabled on this VM because the permission to read the workspace is missing` erro, consulte as [seguintes informações de resolução de problemas](update-management.md#failed-to-enable-error).

### <a name="scenario-feature-deployment-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Cenário: A implementação da funcionalidade falha com a mensagem "Falha na configuração da conta da automatização para a registo de diagnóstico"

#### <a name="issue"></a>Problema

Recebe a seguinte mensagem quando tenta ativar uma funcionalidade num VM:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Este erro pode ser causado se o nível de preços não corresponder ao modelo de faturação da subscrição. Para obter mais informações, consulte [a utilização da monitorização e os custos estimados no Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Resolução

Crie manualmente o seu espaço de trabalho Log Analytics e repita o processo de implementação da funcionalidade para selecionar o espaço de trabalho criado.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Cenário: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Este código de erro significa que a consulta de grupo de computador de pesquisa guardado utilizada para direcionar a funcionalidade não está formatada corretamente. 

#### <a name="cause"></a>Causa

Podes ter alterado a consulta, ou o sistema pode tê-la alterado.

#### <a name="resolution"></a>Resolução

Pode eliminar a consulta para a funcionalidade e, em seguida, voltar a ativar a funcionalidade, que recria a consulta. A consulta pode ser encontrada no seu espaço de trabalho sob **pesquisas Save**. O nome da consulta é **MicrosoftDefaultComputerGroup,** e a categoria da consulta é o nome da funcionalidade associada. Se várias funcionalidades estiverem ativadas, a consulta **do MicrosoftDefaultComputerGroup** mostra várias vezes em **pesquisas Saved**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Cenário: Autenticação política

#### <a name="issue"></a>Problema

Este código de erro indica que a implementação falhou devido à violação de uma ou mais políticas.

#### <a name="cause"></a>Causa 

Uma política está a impedir a operação de ser concluída.

#### <a name="resolution"></a>Resolução

Para implementar com sucesso a funcionalidade, deve considerar alterar a política indicada. Como há muitos tipos diferentes de políticas que podem ser definidas, as mudanças necessárias dependem da política que é violada. Por exemplo, se uma política for definida num grupo de recursos que negue permissão para alterar o conteúdo de alguns recursos contidos, poderá escolher uma destas correções:

* Remova completamente a apólice.
* Tente ativar a funcionalidade para um grupo de recursos diferente.
* Retarget a política para um recurso específico, por exemplo, uma conta Automation.
* Reveja o conjunto de recursos que a política está configurada a negar.

Verifique as notificações no canto superior direito do portal Azure ou vá ao grupo de recursos que contém a sua conta Demôm automação e selecione **Implementações** em **Definições** para visualizar a implementação falhada. Para saber mais sobre a Política Azure, consulte [a Visão Geral da Política Azure.](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Cenário: Erros que tentam desvincular um espaço de trabalho

#### <a name="issue"></a>Problema

Recebe a seguinte mensagem de erro quando tenta desvincular um espaço de trabalho:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Este erro ocorre quando ainda tem funcionalidades ativas no seu espaço de trabalho Log Analytics que dependem da sua conta de Automação e do espaço de trabalho do Log Analytics.

### <a name="resolution"></a>Resolução

Remova os recursos para as seguintes funcionalidades do seu espaço de trabalho se estiver a usá-los:

* Gestão de Atualizações
* Controlo de Alterações e Inventário
* Iniciar/Parar VMs durante horas de inatividade

Depois de remover os recursos de funcionalidade, pode desvincular o seu espaço de trabalho. É importante limpar quaisquer artefactos existentes a partir destas funcionalidades do seu espaço de trabalho e da sua conta de Automação:

* Para a Gestão de Atualizações, **remova as Implementações de Atualização (Agendas)** da sua conta de Automação.
* Para iniciar/parar VMs durante o período de folga, remova **Settings**quaisquer bloqueios nos componentes de funcionalidades da sua conta de Automação em  >  **Configurações Fechaduras**. Para obter mais informações, consulte [Remover a função](../automation-solution-vm-management.md#remove-the-feature).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Registar Análises para falhas de extensão do Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Uma instalação do agente Log Analytics para a extensão do Windows pode falhar por uma variedade de razões. A seguinte secção descreve problemas de implementação de funcionalidades que podem causar falhas durante a implementação do agente Log Analytics para extensão do Windows.

>[!NOTE]
>Log Analytics agent for Windows é o nome usado atualmente na Azure Automation para o Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Cenário: Uma exceção ocorreu durante um pedido de WebClient

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

Algumas causas potenciais deste erro são:

* Um proxy configurado no VM só permite portas específicas.
* Uma definição de firewall bloqueou o acesso às portas e endereços necessários.

#### <a name="resolution"></a>Resolução

Certifique-se de que tem as portas e endereços adequados abertos para comunicação. Para obter uma lista de portas e endereços, consulte [Planeamento da sua rede.](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>Cenário: Instalação falhada devido a problemas ambientais transitórios

A instalação do Log Analytics para a extensão do Windows falhou durante a implantação devido a outra instalação ou ação que bloqueia a instalação.

#### <a name="issue"></a>Problema

Seguem-se exemplos de mensagens de erro que podem ser devolvidas:

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

Algumas causas potenciais deste erro são:

* Outra instalação está em andamento.
* O sistema é acionado para reiniciar durante a implementação do modelo.

#### <a name="resolution"></a>Resolução

Este erro é transitório por natureza. Redaça a implantação para instalar a extensão.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Cenário: Tempo limite de instalação

A instalação do Log Analytics para a extensão do Windows não foi concluída devido a um intervalo.

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

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) , a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga a comunidade Azure a respostas, suporte e especialistas.
* Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Apoio**.

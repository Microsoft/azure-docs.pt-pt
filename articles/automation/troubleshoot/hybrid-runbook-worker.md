---
title: Resolução de Problemas Azure Automation Hybrid Runbook Workers
description: Este artigo fornece informações para a resolução de problemas azure Automation Hybrid Runbook Workers.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679339"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Trabalhadores do livro híbrido de troubleshoot

Este artigo fornece informações sobre problemas de resolução de problemas com os Trabalhadores híbridos do livro de corridas.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="general"></a>Geral

O Trabalhador do Livro Híbrido depende de um agente para comunicar com a sua conta De automação para registar o trabalhador, receber trabalhos de livro e reportar o estado. Para windows, este agente é o agente Log Analytics para Windows. Para linux, é o agente de Log Analytics para linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Cenário: Execução do livro falha

#### <a name="issue"></a>Problema

A execução do livro de corridas falha e recebe o seguinte erro.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

O seu livro de corridas é suspenso pouco depois de tentar executar três vezes. Há condições que podem interromper o livro de execução de completar. A mensagem de erro relacionada pode não incluir qualquer informação adicional.

#### <a name="cause"></a>Causa

As seguintes são as possíveis causas:

* Os livros não podem autenticar com recursos locais.
* O trabalhador híbrido está por trás de um proxy ou firewall.
* O computador configurado para executar o Hybrid Runbook Worker não satisfaz os requisitos mínimos de hardware.

#### <a name="resolution"></a>Resolução

Verifique se o computador tem acesso de saída a ***.azure-automation.net** na porta 443.

Os computadores que executam o Hybrid Runbook Worker devem cumprir os requisitos mínimos de hardware antes de o trabalhador ser configurado para hospedar esta funcionalidade. Os livros de execução e o processo de fundo que utilizam podem fazer com que o sistema seja usado em excesso e causar atrasos ou intervalos de trabalho.

Confirme que o computador para executar a função Hybrid Runbook Worker cumpre os requisitos mínimos de hardware. Se isso acontecer, monitorize o CPU e o uso da memória para determinar qualquer correlação entre o desempenho dos processos híbridos do Runbook Worker e do Windows. Qualquer pressão de memória ou CPU pode indicar a necessidade de atualizar os recursos. Também pode selecionar um recurso computacional diferente que suporte os requisitos mínimos e a escala quando as exigências de carga de trabalho indicam que é necessário um aumento.

Verifique o registo do evento **Microsoft-SMA** para obter um evento correspondente com descrição `Win32 Process Exited with code [4294967295]`. A causa deste erro é que não configurau a autenticação nos seus livros de execução ou especificou a Execução Como credenciais para o grupo Operário de Runbook Híbrido. Reveja as permissões do livro de execução em runbook running [on a Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) para confirmar que você tem corretamente configurado a autenticação para os seus livros de execução.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Cenário: Evento 15011 no Trabalhador do Livro Híbrido

#### <a name="issue"></a>Problema

O Trabalhador do Livro Híbrido recebe o evento 15011, indicando que um resultado de consulta não é válido. O seguinte erro aparece quando o trabalhador tenta abrir uma ligação com o [servidor SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Causa

O Trabalhador do Livro híbrido não foi configurado corretamente para a solução de implementação automatizada. Esta solução contém uma peça que liga o VM ao espaço de trabalho log Analytics. O script PowerShell procura o espaço de trabalho na subscrição com o nome fornecido. Neste caso, o espaço de trabalho log Analytics encontra-se numa subscrição diferente. O guião não consegue encontrar o espaço de trabalho e tenta criar um, mas o nome já está tomado. Assim, a implantação falha.

#### <a name="resolution"></a>Resolução

Tem duas opções para resolver esta questão:

* Modifique o script PowerShell para procurar o espaço de trabalho do Log Analytics noutra subscrição. Esta é uma boa solução se planeia implementar muitas máquinas híbridas do Runbook Worker no futuro.

* Configure manualmente a máquina operária para funcionar numa caixa de areia Orchestrator. Em seguida, executar um livro de execução criado na conta Azure Automation sobre o trabalhador para testar a funcionalidade.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Cenário: VMs do Windows Azure automaticamente retirados do grupo de trabalhadores híbridos

#### <a name="issue"></a>Problema

Não se pode ver o Trabalhador híbrido ou VMs quando a máquina operária está desligada há muito tempo.

#### <a name="cause"></a>Causa

A máquina híbrida do Runbook Worker não pisa a Automação Azure há mais de 30 dias. Como resultado, a Automação purgou o grupo Híbrido Runbook Worker ou o grupo System Worker. 

#### <a name="resolution"></a>Resolução

Ligue a máquina de trabalho e, em seguida, reregique-a com a Automação Azure. Consulte as instruções para instalar o ambiente do livro de execução e ligar-se à Automação Azure em implementar um Trabalhador do [Livro híbrido do Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Cenário: Não foi encontrado nenhum certificado na loja de certificados da Hybrid Runbook Worker

#### <a name="issue"></a>Problema

Um livro de corridas em execução num Trabalhador de Runbook Híbrido falha com a seguinte mensagem de erro.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Causa

Este erro ocorre quando se tenta utilizar uma [conta Run As](../manage-runas-account.md) num livro de execução que funciona num Trabalhador de Runbook Híbrido onde o certificado de conta Run As não está presente. Os trabalhadores híbridos não têm o ativo do certificado localmente por padrão. A conta Run As requer que este ativo funcione corretamente.

#### <a name="resolution"></a>Resolução

Se o seu Executbook Worker híbrido for um VM Azure, pode utilizar [identidades geridas para recursos Azure.](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) Este cenário simplifica a autenticação, permitindo-lhe autenticar recursos Azure utilizando a identidade gerida do VM Azure em vez da conta Run As. Quando o Trabalhador do Livro Híbrido é uma máquina no local, é necessário instalar o certificado de conta Run As na máquina. Para saber instalar o certificado, consulte os passos para executar o livro de execução PowerShell **Export-RunAsCertificateToHybridWorker** em [runbook em execução de um trabalhador de resta híbrida](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Cenário: Erro 403 durante o registo do Trabalhador do Livro Híbrido

#### <a name="issue"></a>Problema

A fase inicial de registo do trabalhador falha e recebe o seguinte erro (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Causa

As seguintes são as possíveis causas:

* Há uma identificação do espaço de trabalho enevoado ou chave do espaço de trabalho (primária) nas configurações do agente. 

* O Trabalhador do Livro Híbrido não pode descarregar a configuração, causando um erro de ligação de conta. Quando o Azure permite soluções, suporta apenas certas regiões para ligar um espaço de trabalho log Analytics e uma conta de Automação. Também é possível que uma data e/ou hora incorretas seja definida no computador. Se o tempo for de +/-15 minutos a partir do tempo atual, o embarque falha.

#### <a name="resolution"></a>Resolução

##### <a name="mistyped-workspace-idkey"></a>ID/chave do espaço de trabalho mistyped
Para verificar se o ID ou a chave do espaço de trabalho do agente foram enevoados, consulte [adicionar ou remover um espaço](../../azure-monitor/platform/agent-manage.md#windows-agent) de trabalho – agente windows para o agente Windows ou Adicionar ou remover um espaço de trabalho – agente [Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) para o agente Linux.  Certifique-se de selecionar a cadeia completa do portal Azure e copiá-la e colar cuidadosamente.

##### <a name="configuration-not-downloaded"></a>Configuração não descarregada

O seu espaço de trabalho log Analytics e a sua conta de Automação devem estar numa região ligada. Para obter uma lista de regiões apoiadas, consulte [o Azure Automation e o Log Analytics workspace mapeamentos.](../how-to/region-mappings.md)

Também poderá necessitar de atualizar a data e/ou fuso horário do seu computador. Se selecionar um intervalo de tempo personalizado, certifique-se de que o intervalo está em UTC, que pode diferir do seu fuso horário local.

## <a name="linux"></a>Linux

O Linux Hybrid Runbook Worker depende do agente Log Analytics para o [Linux](../../azure-monitor/platform/log-analytics-agent.md) comunicar com a sua conta Automation para registar o trabalhador, receber trabalhos de livro e reportar o estado. Se o registo do trabalhador falhar, eis algumas possíveis causas para o erro:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Cenário: O agente log analytics do Linux não está a funcionar

#### <a name="issue"></a>Problema

O agente log analytics do Linux não está a funcionar.

#### <a name="cause"></a>Causa

Se o agente não estiver a funcionar, impede que o Trabalhador do Livro Híbrido Linux se comunicasse com a Azure Automation. O agente pode não estar a fugir por várias razões.

#### <a name="resolution"></a>Resolução

 Verifique se o agente está `ps -ef | grep python`a funcionar entrando no comando . Deve ver a saída semelhante à seguinte, os processos Python com conta de utilizador **de nxautomation.** Se a solução De Gestão de Atualização ou De solução De Automação Azure não estiver ativada, nenhum dos seguintes processos está em execução.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

A lista seguinte mostra os processos iniciados para um Linux Hybrid Runbook Worker. Estão todos localizados no **diretório /var/opt/microsoft/omsagent/state/automationworker/** diretório.

* **oms.conf** - O processo de gerente de trabalho. Começou diretamente da DSC.

* **worker.conf** - O processo de trabalhador híbrido auto-registado. É iniciado pelo gerente dos trabalhadores. Este processo é utilizado pela Update Management e é transparente para o utilizador. Este processo não está presente se a solução de Gestão de Atualização não estiver ativada na máquina.

* **diy/worker.conf** - O processo de trabalhador híbrido DIY. O processo de trabalhador híbrido DIY é usado para executar livros de execução de utilizadores no Trabalhador do Livro híbrido. Apenas difere do processo de trabalhador híbrido auto-registado no detalhe chave que utiliza uma configuração diferente. Este processo não está presente se a solução Deautomação Azure estiver desativada e o Diy Linux Hybrid Worker não estiver registado.

Se o agente não estiver a funcionar, execute `sudo /opt/microsoft/omsagent/bin/service_control restart`o seguinte comando para iniciar o serviço: .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Cenário: A classe especificada não existe

Se vir o `The specified class does not exist..` erro no **/var/opt/microsoft/omsconfig/omsconfig.log**, o agente Log Analytics para o Linux precisa de ser atualizado. Executar o seguinte comando para reinstalar o agente:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

O Windows Hybrid Runbook Worker depende do [agente Log Analytics para](../../azure-monitor/platform/log-analytics-agent.md) o Windows comunicar com a sua conta Automation para registar o trabalhador, receber trabalhos de livro e reportar o estado. Se o registo do trabalhador falhar, esta secção inclui algumas razões possíveis.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Cenário: O agente Log Analytics para Windows não está a funcionar

#### <a name="issue"></a>Problema

Não `healthservice` está a funcionar na máquina híbrida do Runbook Worker.

#### <a name="cause"></a>Causa

Se o log analytics para o serviço Windows não estiver em funcionamento, o Trabalhador híbrido do livro não pode comunicar com a Azure Automation.

#### <a name="resolution"></a>Resolução

Verifique se o agente está a funcionar ao `Get-Service healthservice`introduzir o seguinte comando no PowerShell: . Se o serviço for interrompido, introduza o seguinte `Start-Service healthservice`comando na PowerShell para iniciar o serviço: .

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Cenário: Evento 4502 no registo do Gestor de Operações

#### <a name="issue"></a>Problema

No registo de eventos do Gestor de **Registos de Aplicações e Serviços\Diretor de Operações,** vê o evento 4502 e o EventMessage que contém `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` com a seguinte descrição:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Causa

Este problema pode ser causado pelo seu proxy ou firewall de rede bloqueando a comunicação ao Microsoft Azure. Verifique se o computador tem acesso de saída a ***.azure-automation.net** na porta 443. 

#### <a name="resolution"></a>Resolução

Os registos são armazenados localmente em cada trabalhador híbrido em **C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**. Pode verificar se existem algum aviso ou erros nos registos de registos de **aplicações e serviços\Microsoft-SMA\Operações** e **Registos de Aplicações e Serviços\Operations Manager** registos de eventos. Estes registos indicam uma conectividade ou outro tipo de problema que afeta o embarque do papel para a Automação Azure, ou um problema encontrado em operações normais. Para obter problemas adicionais de resolução de problemas com o agente Log Analytics, consulte [problemas de resolução](../../azure-monitor/platform/agent-windows-troubleshoot.md)de problemas com o agente do Log Analytics Windows .

Os trabalhadores híbridos enviam [saída seletiva e mensagens](../automation-runbook-output-and-messages.md) para a Automação Azure da mesma forma que os trabalhos de runbook que funcionam na nuvem enviam saída e mensagens. Pode ativar os fluxos Verbose e Progress, tal como você para livros de execução.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Cenário: Orchestrator.Sandbox.exe não pode ligar-se ao Office 365 por procuração

#### <a name="issue"></a>Problema

Um guião em execução num Windows Hybrid Runbook Worker não pode ligar-se como esperado ao Office 365 numa caixa de areia Orchestrator. O script está a utilizar o [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) para a ligação. 

Se ajustar **o Orchestrator.Sandbox.exe.config** para definir o proxy e a lista de bypass, a caixa de areia ainda não se liga corretamente. Um ficheiro **Powershell_ise.exe.config** com as mesmas definições de lista de procuração e bypass parece funcionar como espera. Os registos de Automação de Gestão de Serviços (SMA) e os registos PowerShell não fornecem qualquer informação sobre procuração.

#### <a name="cause"></a>Causa

A ligação aos Serviços da Federação de Diretórios Ativos (ADFS) no servidor não pode contornar o proxy. Lembre-se que uma caixa de areia PowerShell funciona como o utilizador registado. No entanto, uma caixa de areia Orchestrator é fortemente personalizada e pode ignorar as definições de ficheiro **Orchestrator.Sandbox.exe.config.** Tem um código especial para manusear a máquina ou configurações de procuração de MMA, mas não para manusear outras definições de procuração personalizadas. 

#### <a name="resolution"></a>Resolução

Pode resolver o problema da caixa de areia Orchestrator migrando o seu script para utilizar os módulos Azure AD em vez do módulo MSOnline para cmdlets PowerShell. Ver [Migrar do Orquestrador para a Automação Azure (Beta)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Se pretender continuar a utilizar os cmdlets do módulo MSOnline, altere o seu script para utilizar [o Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Especifique `ComputerName` `Credential` valores para os e parâmetros. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Esta alteração de código inicia uma sessão inteiramente nova da PowerShell no contexto das credenciais especificadas. Deve permitir que o tráfego flua através de um servidor proxy que esteja a autenticar o utilizador ativo.

>[!NOTE]
>Esta solução torna desnecessário manipular o ficheiro de configuração da caixa de areia. Mesmo que consiga fazer com que o ficheiro de configuração funcione com o seu script, o ficheiro é apagado cada vez que o agente Hybrid Runbook Worker é atualizado.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Cenário: Trabalhador híbrido do runbook não reportando

#### <a name="issue"></a>Problema

A sua máquina híbrida de trabalho de runbook está em funcionamento, mas não vê dados de batimentos cardíacos para a máquina no espaço de trabalho.

A seguinte consulta de exemplo mostra as máquinas num espaço de trabalho e o seu último batimento cardíaco:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Causa

Esta questão pode ser causada por uma cache corrupta no Trabalhador híbrido do livro.

#### <a name="resolution"></a>Resolução

Para resolver este problema, inscreva-se no Hybrid Runbook Worker e execute o seguinte script. Este script para o agente Log Analytics para windows, remove a cache e reinicia o serviço. Esta ação obriga o Trabalhador do Livro Híbrido a rebaixar a sua configuração a partir da Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Cenário: Não se pode adicionar um Trabalhador híbrido do livro de corridas

#### <a name="issue"></a>Problema

Recebe a seguinte mensagem ao tentar adicionar um `Add-HybridRunbookWorker` Trabalhador do Livro Híbrido utilizando o cmdlet.

```error
Machine is already registered
```

#### <a name="cause"></a>Causa

Este problema pode ser causado se a máquina já estiver registada com uma conta de Automação diferente ou se tentar ler o Trabalhador do Livro Híbrido depois de o retirar de uma máquina.

#### <a name="resolution"></a>Resolução

Para resolver este problema, retire a seguinte `HealthService`tecla de `Add-HybridRunbookWorker` registo, reinicie a , e volte a tentar o cmdlet.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema acima ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport)a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
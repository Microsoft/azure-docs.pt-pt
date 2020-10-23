---
title: Problemas de resolução da Azure Automation Hybrid Runbook Worker problemas
description: Este artigo diz como resolver problemas e resolver problemas que surgem com os Trabalhadores híbridos da Azure Automation.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1386dd820b10b63862ddab38c441f251bea1d83d
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428392"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Resolver problemas da Função de Trabalho de Runbook Híbrida

Este artigo fornece informações sobre resolução de problemas e resolução de problemas com trabalhadores híbridos da Azure Automation. Para obter informações gerais, consulte [a visão geral do Trabalhador do Runbook Híbrido](../automation-hybrid-runbook-worker.md).

## <a name="general"></a>Geral

O Trabalhador de Runbook Híbrido depende de um agente para comunicar com a sua conta Azure Automation para registar o trabalhador, receber trabalhos de runbook e reportar o estado. Para o Windows, este agente é o agente Log Analytics para windows. Para o Linux, é o agente do Log Analytics para o Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Cenário: Execução de runbook falha

#### <a name="issue"></a>Problema

A execução do runbook falha e recebe a seguinte mensagem de erro:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

O seu livro de corridas é suspenso pouco depois de tentar executar três vezes. Existem condições que podem interromper o livro de execução de completar. A mensagem de erro relacionada pode não incluir nenhuma informação adicional.

#### <a name="cause"></a>Causa

São possíveis causas:

* Os livros não podem autenticar com recursos locais.
* O trabalhador híbrido está por trás de um proxy ou firewall.
* O computador configurado para executar o Trabalhador de Runbook Híbrido não cumpre os requisitos mínimos de hardware.

#### <a name="resolution"></a>Resolução

Verifique se o computador tem acesso de saída a ** \* .azure-automation.net** na porta 443.

Os computadores que executam o Trabalhador de Runbook Híbrido devem cumprir os requisitos mínimos de hardware antes de o trabalhador ser configurado para hospedar esta funcionalidade. Os livros de execução e o processo de fundo que utilizam podem fazer com que o sistema seja usado em excesso e causar atrasos no trabalho ou intervalos de tempo.

Confirme que o computador para executar a função Trabalhador do Runbook Híbrido cumpre os requisitos mínimos de hardware. Se o fizer, monitorize o CPU e a utilização da memória para determinar qualquer correlação entre o desempenho dos processos híbridos do Trabalhador runbook e do Windows. Qualquer memória ou pressão cpu pode indicar a necessidade de atualizar recursos. Também pode selecionar um recurso de cálculo diferente que suporte os requisitos mínimos e a escala quando as exigências da carga de trabalho indicam que é necessário um aumento.

Verifique o registo **de eventos microsoft-SMA** para obter um evento correspondente com a descrição `Win32 Process Exited with code [4294967295]` . A causa deste erro é que não configuraste a autenticação nos teus runbooks ou especificou as credenciais run as para o grupo Híbrido Runbook Worker. Reveja as permissões de runbook em [Running runbooks num Trabalhador de Runbook Híbrido](../automation-hrw-run-runbooks.md) para confirmar que configuraram corretamente a autenticação para os seus runbooks.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Cenário: Evento 15011 no Trabalhador De Runbook Híbrido

#### <a name="issue"></a>Problema

O Trabalhador De Runbook Híbrido recebe o evento 15011, indicando que um resultado de consulta não é válido. O seguinte erro aparece quando o trabalhador tenta abrir uma ligação com o [servidor SignalR](/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Causa

O Trabalhador de Runbook Híbrido não foi configurado corretamente para a implementação de funcionalidades automatizadas, por exemplo, para a Gestão de Atualizações. A implementação contém uma parte que liga o VM ao espaço de trabalho Log Analytics. O script PowerShell procura o espaço de trabalho na subscrição com o nome fornecido. Neste caso, o espaço de trabalho Log Analytics encontra-se numa subscrição diferente. O guião não consegue encontrar o espaço de trabalho e tenta criar um, mas o nome já está tomado. Como resultado, a implantação falha.

#### <a name="resolution"></a>Resolução

Tem duas opções para resolver esta questão:

* Modifique o script PowerShell para procurar o espaço de trabalho Do Log Analytics em outra subscrição. Esta é uma boa resolução para usar se planeia implementar no futuro muitas máquinas híbridas de trabalhadores de runbook.

* Configurar manualmente a máquina operária para funcionar numa caixa de areia do Orquestrador. Em seguida, execute um runbook criado na conta Azure Automation no trabalhador para testar a funcionalidade.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Cenário: Os VMs do Windows Azure caíram automaticamente de um grupo de trabalhadores híbridos

#### <a name="issue"></a>Problema

Não se pode ver o Operário Híbrido ou VMs quando a máquina de trabalhadores está desligada há muito tempo.

#### <a name="cause"></a>Causa

A máquina híbrida runbook worker não pinga Azure Automation por mais de 30 dias. Como resultado, a Automação expurgou o grupo híbrido Runbook Worker ou o grupo System Worker. 

#### <a name="resolution"></a>Resolução

Ligue a máquina de trabalho e, em seguida, rereregister-la com a Azure Automation. Para obter instruções sobre como instalar o ambiente do runbook e ligar-se à Azure Automation, consulte [implementar um Trabalhador de Runbook Híbrido do Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Cenário: Não foi encontrado nenhum certificado na loja de certificados do Trabalhador De Runbook Híbrido

#### <a name="issue"></a>Problema

Um runbook em execução num Trabalhador de Runbook Híbrido falha com a seguinte mensagem de erro:

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Causa

Este erro ocorre quando se tenta utilizar uma [conta Run As](../manage-runas-account.md) num livro de contas que funciona num Trabalhador de Runbook Híbrido onde o certificado de conta Run As não está presente. Os Trabalhadores híbridos runbook não têm o certificado localmente por padrão. A conta Run As requer que este ativo funcione corretamente.

#### <a name="resolution"></a>Resolução

Se o seu Trabalhador De Runbook Híbrido for um VM Azure, pode utilizar [a autenticação de runbook com identidades geridas.](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) Este cenário simplifica a autenticação permitindo-lhe autenticar recursos da Azure utilizando a identidade gerida do VM Azure em vez da conta Run As. Quando o Trabalhador de Runbook Híbrido é uma máquina no local, é necessário instalar o certificado de conta Run As na máquina. Para aprender a instalar o certificado, consulte os passos para executar o runbook PowerShell **Export-RunAsCertificateToHybridWorker** em [runbooks runbooks num Trabalhador de Runbook Híbrido](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Cenário: Erro 403 durante o registo de um Trabalhador De Runbook Híbrido

#### <a name="issue"></a>Problema

A fase inicial de registo do trabalhador falha e recebe o seguinte erro (403):

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Causa

As seguintes questões são possíveis causas:

* Há uma identificação de espaço de trabalho mal configurada ou chave de espaço de trabalho (primária) nas definições do agente. 
* O Trabalhador de Runbook Híbrido não consegue descarregar a configuração, o que provoca um erro de ligação de conta. Quando o Azure permite funcionalidades em máquinas, suporta apenas certas regiões para ligar um espaço de trabalho log Analytics e uma conta Automation. Também é possível que uma data ou hora incorreta seja definida no computador. Se o tempo for de +/- 15 minutos a partir da hora atual, a implementação da função falha.

#### <a name="resolution"></a>Resolução

##### <a name="mistyped-workspace-id-or-key"></a>ID ou chave do espaço de trabalho mal-ajustado
Para verificar se a chave de iD ou espaço de trabalho do agente foi mal escrita, consulte [adicionar ou remover um espaço de trabalho – agente Windows](../../azure-monitor/platform/agent-manage.md#windows-agent) para o agente Windows ou adicionar ou remover um espaço de trabalho – agente [Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) para o agente Linux. Certifique-se de selecionar a cadeia completa do portal Azure e copiá-la e colá-la cuidadosamente.

##### <a name="configuration-not-downloaded"></a>Configuração não descarregada

O seu espaço de trabalho e a sua conta de Automação Log Analytics devem estar numa região ligada. Para obter uma lista de regiões apoiadas, consulte [mapeamentos de espaço de trabalho Azure Automation e Log Analytics](../how-to/region-mappings.md).

Também pode ser necessário atualizar a data ou o fuso horário do seu computador. Se selecionar um intervalo de tempo personalizado, certifique-se de que o alcance está no UTC, que pode diferir do seu fuso horário local.

## <a name="linux"></a>Linux

O Trabalhador de Runbook Híbrido Linux depende do agente Log Analytics para que a [Linux](../../azure-monitor/platform/log-analytics-agent.md) comunique com a sua conta Automation para registar o trabalhador, receber trabalhos de runbook e reportar o estado. Se o registo do trabalhador falhar, eis algumas possíveis causas para o erro.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Cenário: Linux Hybrid Runbook Worker recebe pedido de senha ao assinar um runbook

#### <a name="issue"></a>Problema

Executar o `sudo` comando de um Trabalhador de Runbook Híbrido Linux recupera uma solicitação inesperada para uma senha.

#### <a name="cause"></a>Causa

A conta **nxautomationuser** do agente Log Analytics para o Linux não está corretamente configurada no ficheiro **sudoadores.** O Trabalhador De Runbook Híbrido precisa da configuração adequada de permissões de conta e outros dados para que possa assinar livros no Linux Runbook Worker.

#### <a name="resolution"></a>Resolução

* Certifique-se de que o Trabalhador de Runbook Híbrido tem o GnuPG (GPG) executável na máquina.

* Verifique a configuração da conta **nxautomationuser** no ficheiro **sudoers.** Consulte [os runbooks running on a Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Cenário: O agente do Log Analytics para o Linux não está a funcionar

#### <a name="issue"></a>Problema

O agente do Log Analytics para o Linux não está a funcionar.

#### <a name="cause"></a>Causa

Se o agente não estiver a funcionar, impede o Trabalhador de Runbook Híbrido Linux de comunicar com a Azure Automation. O agente pode não estar a fugir por várias razões.

#### <a name="resolution"></a>Resolução

 Verifique se o agente está a executar ao entrar no comando `ps -ef | grep python` . Deverá ver um resultado semelhante ao seguinte. O Python processa com a conta de utilizador **de nxautomation.** Se a função Azure Automation não estiver ativada, nenhum dos seguintes processos está a ser executada.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

A lista a seguir mostra os processos que são iniciados para um Trabalhador de Runbook Híbrido Linux. Todos estão localizados no /var/opt/microsoft/omsagent/state/automationworker/diretório.

* **oms.conf**: O processo de gerente de trabalhador. Começou diretamente da DSC.
* **trabalhador.conf**: O processo de trabalhador híbrido auto-registado. É iniciado pelo gerente do trabalhador. Este processo é utilizado pela Update Management e é transparente para o utilizador. Este processo não está presente se a Gestão de Atualização não estiver ativada na máquina.
* **diy/worker.conf**: O processo de trabalhador híbrido DIY. O processo de trabalhador híbrido DIY é usado para executar livros de utilizador no Híbrido Runbook Worker. Difere apenas do processo de trabalhador híbrido registado automaticamente no detalhe chave que utiliza uma configuração diferente. Este processo não está presente se a Azure Automation for desativada e o Trabalhador Híbrido DIY Linux não estiver registado.

Se o agente não estiver em funcionamento, executar o seguinte comando para iniciar o serviço: `sudo /opt/microsoft/omsagent/bin/service_control restart` .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Cenário: A classe especificada não existe

Se vir a mensagem de erro `The specified class does not exist..` em **/var/opt/microsoft/omsconfig/omsconfig.log,** o agente Log Analytics do Linux tem de ser atualizado. Executar o seguinte comando para reinstalar o agente.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

O Trabalhador de Runbook Híbrido do Windows depende do [agente Log Analytics para](../../azure-monitor/platform/log-analytics-agent.md) que o Windows comunique com a sua conta Automation para registar o trabalhador, receber trabalhos de runbook e reportar o estado. Se o registo do trabalhador falhar, esta secção inclui algumas razões possíveis.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Cenário: O agente do Log Analytics para o Windows não está a funcionar

#### <a name="issue"></a>Problema

Não `healthservice` está a funcionar na máquina híbrida do Trabalhador do Runbook.

#### <a name="cause"></a>Causa

Se o serviço Desacorrido do Registo para Windows não estiver em funcionamento, o Trabalhador de Runbook Híbrido não pode comunicar com a Azure Automation.

#### <a name="resolution"></a>Resolução

Verifique se o agente está em funcionamento introduzindo o seguinte comando no PowerShell: `Get-Service healthservice` . Se o serviço for interrompido, insira o seguinte comando no PowerShell para iniciar o serviço: `Start-Service healthservice` .

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Cenário: Evento 4502 no registo do Gestor de Operações

#### <a name="issue"></a>Problema

No registo de **eventos de Registos de Aplicações e Serviços\Operations Manager,** vê o evento 4502 e uma mensagem de evento que contém `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` com a seguinte descrição:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Causa

Este problema pode ser causado pelo seu proxy ou firewall de rede bloqueando a comunicação ao Microsoft Azure. Verifique se o computador tem acesso de saída a ** \* .azure-automation.net** na porta 443.

#### <a name="resolution"></a>Resolução

Os registos são armazenados localmente em cada trabalhador híbrido em C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Pode verificar se existem algum aviso ou erro nos registos de **aplicações e serviços\Microsoft-SMA\Operações** e **Registos de Aplicações e Serviços\Operations Manager.** Estes registos indicam uma conectividade ou outro tipo de problema que afeta a habilitação da função para a Azure Automation, ou um problema encontrado em operações normais. Para obter problemas adicionais de resolução de problemas com o agente Log Analytics, consulte [problemas de resolução de problemas com o agente Do Windows Log Analytics](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Os trabalhadores híbridos enviam [produção e mensagens runbook](../automation-runbook-output-and-messages.md) para a Azure Automation da mesma forma que os trabalhos de runbook que funcionam na nuvem enviam saídas e mensagens. Pode ativar os streams Verbose e Progress tal como faz para os livros de execução.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Cenário: Orchestrator.Sandbox.exe não consegue ligar-se à Microsoft 365 através de procuração

#### <a name="issue"></a>Problema

Um script que está a ser executado num Windows Hybrid Runbook Worker não pode ligar-se como esperado ao Microsoft 365 numa caixa de areia do Orquestrador. O script está a utilizar [o Connect-MsolService](/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) para ligação. 

Se ajustar **Orchestrator.Sandbox.exe.config** para definir o proxy e a lista de bypass, a caixa de areia ainda não se conecta corretamente. Um ** ficheiroPowershell_ise.exe.config** com as mesmas definições de lista de procuração e bypass parece funcionar como espera. Os registos de Automação de Gestão de Serviços (SMA) e os registos PowerShell não fornecem qualquer informação sobre o proxy.

#### <a name="cause"></a>Causa

A ligação aos Serviços da Federação de Diretório Ativo (AD FS) no servidor não pode contornar o proxy. Lembre-se que uma caixa de areia PowerShell funciona como utilizador registado. No entanto, uma caixa de areia do Orquestrador é fortemente personalizada e pode ignorar as **definições de ficheirosOrchestrator.Sandbox.exe.config.** Tem um código especial para manusear configurações de procuração de máquinas ou de agente do Log Analytics, mas não para manusear outras definições de procuração personalizadas. 

#### <a name="resolution"></a>Resolução

Pode resolver o problema para a caixa de areia do Orquestrador migrando o seu script para utilizar os módulos Azure Ative Directory em vez do módulo MSOnline para cmdlets PowerShell. Para mais informações, consulte [Migração de Orquestrador para Azure Automation (Beta)](../automation-orchestrator-migration.md).

Se pretender continuar a utilizar os cmdlets do módulo MSOnline, altere o seu script para utilizar [o Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Especifique valores para os `ComputerName` parâmetros e `Credential` parâmetros. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Esta alteração de código inicia uma sessão PowerShell inteiramente nova no contexto das credenciais especificadas. Deve permitir que o tráfego flua através de um servidor proxy que está a autenticar o utilizador ativo.

>[!NOTE]
>Esta resolução torna desnecessário manipular o ficheiro de configuração da caixa de areia. Mesmo que consiga fazer com que o ficheiro de configuração funcione com o seu script, o ficheiro é eliminado cada vez que o agente Híbrido Runbook Worker é atualizado.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Cenário: Trabalhador de runbook híbrido não reportando

#### <a name="issue"></a>Problema

A sua máquina híbrida runbook worker está a funcionar, mas não vê dados de batimentos cardíacos para a máquina no espaço de trabalho.

A seguinte consulta de exemplo mostra as máquinas num espaço de trabalho e o seu último batimento cardíaco:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Causa

Este problema pode ser causado por uma cache corrupta no Trabalhador de Runbook Híbrido.

#### <a name="resolution"></a>Resolução

Para resolver este problema, inscreva-se no Hybrid Runbook Worker e execute o seguinte script. Este script para o agente Log Analytics para Windows, remove a sua cache e reinicia o serviço. Esta ação obriga o Trabalhador De Runbook Híbrido a refilou a sua configuração da Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>Cenário: Não é possível adicionar um Trabalhador de Runbook Híbrido do Windows

#### <a name="issue"></a>Problema

Recebe a seguinte mensagem quando tenta adicionar um Trabalhador De Runbook Híbrido utilizando o `Add-HybridRunbookWorker` cmdlet:

```error
Machine is already registered
```

#### <a name="cause"></a>Causa

Este problema pode ser causado se a máquina já estiver registada com uma conta de Automação diferente ou se tentar voltar a adicionar o Trabalhador de Runbook Híbrido depois de a retirar de uma máquina.

#### <a name="resolution"></a>Resolução

Para resolver este problema, retire a seguinte chave de registo, `HealthService` reinicie, e tente novamente o `Add-HybridRunbookWorker` cmdlet.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>Cenário: Não se pode adicionar um Trabalhador de Runbook Híbrido Linux

#### <a name="issue"></a>Problema

Recebe a seguinte mensagem quando tenta adicionar um Trabalhador De Runbook Híbrido utilizando o `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` script python:

```error
Unable to register, an existing worker was found. Please deregister any existing worker and try again.
```

Além disso, tentar desregistar um Trabalhador De Runbook Híbrido utilizando o `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` script python:

```error
Failed to deregister worker. [response_status=404]
```

#### <a name="cause"></a>Causa

Este problema pode ocorrer se a máquina já estiver registada com uma conta de Automação diferente, se o Grupo De Trabalhadores Híbridos Azure for eliminado, ou se tentar voltar a adicionar o Trabalhador de Runbook Híbrido depois de o retirar de uma máquina.

#### <a name="resolution"></a>Resolução

Para resolver este problema:

1. Retire o `sudo sh onboard_agent.sh --purge` agente.

1. Execute estes comandos:

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. Voltar a bordo do `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` agente.

1. Aguarde que a pasta `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` preencha.

1. Tente o `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` guião python de novo.

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) , a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga a comunidade Azure a respostas, suporte e especialistas.
* Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Apoio**.

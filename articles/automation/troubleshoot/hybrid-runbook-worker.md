---
title: Solução de problemas-Hybrid runbook Workers de automação do Azure
description: Este artigo fornece informações sobre como solucionar problemas de Hybrid runbook Workers da automação do Azure
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 02/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 39cf6126f6212b6e83f1974dae7aaab0038e69c6
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240989"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Solucionar problemas de Hybrid runbook Workers

Este artigo fornece informações sobre como solucionar problemas com Hybrid runbook Workers.

## <a name="general"></a>Geral

A Hybrid Runbook Worker depende de um agente para se comunicar com sua conta de automação para registrar o trabalho, receber trabalhos de runbook e relatar o status. Para o Windows, esse agente é o Microsoft Monitoring Agent. Para o Linux, é a Agente do OMS para Linux.

### <a name="runbook-execution-fails"></a>Cenário Falha na execução do runbook

#### <a name="issue"></a>Problema

A execução do runbook falha e você recebe o seguinte erro:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Seu runbook será suspenso logo após tentar executá-lo três vezes. Há condições, que podem interromper a conclusão do runbook. Quando isso acontece, a mensagem de erro relacionada pode não incluir informações adicionais que lhe dizem por quê.

#### <a name="cause"></a>Causa

A seguir estão possíveis causas possíveis:

* Os runbooks não podem se autenticar com recursos locais

* O Hybrid Worker está atrás de um proxy ou firewall

* Os runbooks não podem se autenticar com recursos locais

* O computador configurado para executar o recurso de Hybrid Runbook Worker não atende aos requisitos mínimos de hardware.

#### <a name="resolution"></a>Resolução

Verifique se o computador tem acesso de saída para *. azure-automation.net na porta 443.

Os computadores que executam o Hybrid Runbook Worker devem atender aos requisitos mínimos de hardware antes de serem configurados para hospedar esse recurso. Os Runbooks e os processos em segundo plano que eles usam podem fazer com que o sistema seja usado em excesso e causar atrasos ou tempos limite de trabalho do runbook.

Confirme se o computador que executará o recurso de Hybrid Runbook Worker atende aos requisitos mínimos de hardware. Se tiver, monitore o uso da CPU e da memória para determinar qualquer correlação entre o desempenho de processos de Hybrid Runbook Worker e o Windows. Se houver memória ou pressão de CPU, isso poderá indicar a necessidade de atualizar os recursos. Você também pode selecionar um recurso de computação diferente que possa dar suporte aos requisitos mínimos e Dimensionar quando as demandas de carga de trabalho indicarem um aumento necessário.

Verifique o log de eventos do **sma da Microsoft** para obter um evento correspondente com descrição *processo Win32 encerrado com código [4294967295]* . A causa desse erro é que você não configurou a autenticação em seus runbooks ou especificou as credenciais executar como para o grupo do Hybrid Worker. Examine [as permissões de runbook](../automation-hrw-run-runbooks.md#runbook-permissions) para confirmar que você configurou corretamente a autenticação para seus runbooks.

### <a name="no-cert-found"></a>Cenário Nenhum certificado foi encontrado no repositório de certificados no Hybrid Runbook Worker

#### <a name="issue"></a>Problema

Um runbook em execução em um Hybrid Runbook Worker falha com a seguinte mensagem de erro:

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Causa

Esse erro ocorre quando você tenta usar uma [conta Executar como](../manage-runas-account.md) em um runbook que é executado em um Hybrid runbook Worker em que o certificado da conta Executar como não está presente. Hybrid runbook Workers não têm o ativo de certificado localmente por padrão, o que é exigido pela conta Executar como para funcionar corretamente.

#### <a name="resolution"></a>Resolução

Se o seu Hybrid Runbook Worker for uma VM do Azure, você poderá usar [identidades gerenciadas para recursos do Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) em vez disso. Esse cenário permite que você se autentique nos recursos do Azure usando a identidade gerenciada da VM do Azure em vez da conta Executar como, simplificando a autenticação. Quando o Hybrid Runbook Worker é um computador local, você precisa instalar o certificado da conta Executar como no computador. Para saber como instalar o certificado, consulte as etapas para executar o runbook [Export-RunAsCertificateToHybridWorker](../automation-hrw-run-runbooks.md#runas-script) .

## <a name="linux"></a>Linux

O Hybrid Runbook Worker do Linux depende do Agente do OMS para Linux para se comunicar com sua conta de automação para registrar o trabalho, receber trabalhos de runbook e relatar o status. Se o registro do trabalho falhar, aqui estão algumas causas possíveis para o erro:

### <a name="oms-agent-not-running"></a>Cenário O Agente do OMS para Linux não está em execução

#### <a name="issue"></a>Problema

O Agente do OMS para Linux não está em execução

#### <a name="cause"></a>Causa

Se o Agente do OMS para Linux não estiver em execução, ele impedirá que o Hybrid Runbook Worker do Linux se comunique com a automação do Azure. O agente pode não estar em execução por vários motivos.

#### <a name="resolution"></a>Resolução

 Verifique se o agente está em execução digitando o seguinte `ps -ef | grep python`comando:. Você deverá ver uma saída semelhante à seguinte, os processos do Python com a conta de usuário do **nxautomation** . Se as soluções de Gerenciamento de Atualizações ou de automação do Azure não estiverem habilitadas, nenhum dos processos a seguir será executado.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

A lista a seguir mostra os processos que são iniciados para um Hybrid Runbook Worker do Linux. Eles estão todos localizados no `/var/opt/microsoft/omsagent/state/automationworker/` diretório.


* **OMS. conf** -esse valor é o processo do Gerenciador de trabalho. Ele é iniciado diretamente do DSC.

* **Worker. conf** -esse processo é o processo de trabalho híbrido registrado automaticamente, ele é iniciado pelo Gerenciador de trabalho. Esse processo é usado pelo Gerenciamento de Atualizações e é transparente para o usuário. Esse processo não estará presente se a solução de Gerenciamento de Atualizações não estiver habilitada no computador.

* **DIY/Worker. conf** – esse processo é o processo de trabalho híbrido DIY. O processo de trabalho híbrido do DIY é usado para executar runbooks do usuário no Hybrid Runbook Worker. Ele difere apenas do processo de trabalho híbrido registrado automaticamente no detalhe da chave que usa uma configuração diferente. Esse processo não estará presente se a solução de automação do Azure estiver desabilitada e o Hybrid Worker DIY do Linux não estiver registrado.

Se o Agente do OMS para Linux não estiver em execução, execute o seguinte comando para iniciar o `sudo /opt/microsoft/omsagent/bin/service_control restart`serviço:.

### <a name="class-does-not-exist"></a>Cenário A classe especificada não existe

Se vir o erro: **A classe especificada não existe..** `/var/opt/microsoft/omsconfig/omsconfig.log` no, o agente do OMS para Linux precisa ser atualizado. Execute o seguinte comando para reinstalar o agente do OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

O Hybrid Runbook Worker do Windows depende do Microsoft Monitoring Agent para se comunicar com sua conta de automação para registrar o trabalho, receber trabalhos de runbook e relatar o status. Se o registro do trabalho falhar, aqui estão algumas causas possíveis para o erro:

### <a name="mma-not-running"></a>Cenário O Microsoft Monitoring Agent não está em execução

#### <a name="issue"></a>Problema

O `healthservice` serviço não está em execução na máquina Hybrid runbook Worker.

#### <a name="cause"></a>Causa

Se o serviço do Windows Microsoft Monitoring Agent não estiver em execução, esse estado impedirá que o Hybrid Runbook Worker se comunique com a automação do Azure.

#### <a name="resolution"></a>Resolução

Verifique se o agente está em execução digitando o seguinte comando no `Get-Service healthservice`PowerShell:. Se o serviço for interrompido, digite o seguinte comando no PowerShell para iniciar o serviço: `Start-Service healthservice`.

### <a name="event-4502"></a>Evento 4502 no log Operations Manager

#### <a name="issue"></a>Problema

No log de eventos **Application and Services logs Manager** , você vê o evento 4502 e o eventMessage que contém **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent** com a seguinte descrição: *O certificado apresentado pelo serviço \<WSID\>. OMS.opinsights.Azure.com não foi emitido por uma autoridade de certificação usada para serviços da Microsoft. Entre em contato com seu administrador de rede para ver se eles estão executando um proxy que intercepta a comunicação TLS/SSL. O artigo KB3126513 tem informações adicionais para solução de problemas de conectividade.*

#### <a name="cause"></a>Causa

Esse problema pode ser causado pelo proxy ou firewall de rede que está bloqueando a comunicação para Microsoft Azure. Verifique se o computador tem acesso de saída para *. azure-automation.net nas portas 443.

#### <a name="resolution"></a>Resolução

Os logs são armazenados localmente em cada Hybrid Worker em C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Você pode verificar se há algum evento de aviso ou de erro no log de eventos **Application and Services Logs\Microsoft-SMA\Operations** e **Application and Services logs Manager** que indicaria uma conectividade ou outro problema que afeta a integração da função com a automação do Azure ou com o problema em operações normais.

A [saída do runbook e as mensagens](../automation-runbook-output-and-messages.md) são enviadas para a automação do Azure de Hybrid Workers, assim como trabalhos de runbook que são executados na nuvem. Você também pode habilitar os fluxos detalhados e de progresso da mesma maneira que faria para outros runbooks.

### <a name="corrupt-cache"></a>Hybrid Runbook Worker não está relatando

#### <a name="issue"></a>Problema

Sua máquina Hybrid Runbook Worker está em execução, mas você não vê dados de pulsação para o computador no espaço de trabalho.

A consulta de exemplo a seguir mostra os computadores em um espaço de trabalho e sua última pulsação:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Causa

Esse problema pode ser causado por um cache corrompido no Hybrid Runbook Worker.

#### <a name="resolution"></a>Resolução

Para resolver esse problema, entre no Hybrid Runbook Worker e execute o script a seguir. Esse script interrompe a Microsoft Monitoring Agent, remove seu cache e reinicia o serviço. Essa ação força a Hybrid Runbook Worker a baixar novamente sua configuração da automação do Azure.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Cenário Não é possível adicionar um Hybrid Runbook Worker

#### <a name="issue"></a>Problema

Você recebe a seguinte mensagem ao tentar adicionar um Hybrid runbook Worker usando o `Add-HybridRunbookWorker` cmdlet.

```error
Machine is already registered
```

#### <a name="cause"></a>Causa

Isso pode ser causado se o computador já estiver registrado com uma conta de automação diferente ou se você tentar adicionar novamente o Hybrid Runbook Worker depois de removê-lo de um computador.

#### <a name="resolution"></a>Resolução

Para resolver esse problema, remova a seguinte chave do registro e reinicie o `HealthService` e tente o `Add-HybridRunbookWorker` cmdlet novamente:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você poderá arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.


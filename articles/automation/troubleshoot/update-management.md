---
title: Solucionar erros com Gerenciamento de Atualizações
description: Saiba como solucionar problemas com Gerenciamento de Atualizações
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 024cc18f537334a4e9fb260864b13ac027af582f
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982692"
---
# <a name="troubleshooting-issues-with-update-management"></a>Solucionando problemas com Gerenciamento de Atualizações

Este artigo aborda soluções para resolver problemas que você pode encontrar ao usar o Gerenciamento de Atualizações.

Há um solucionador de problemas de agente para Hybrid Worker Agent para determinar o problema subjacente. Para saber mais sobre a solução de problemas, consulte [solucionar problemas do agente de atualização](update-agent-issues.md). Para todos os outros problemas, consulte as informações detalhadas abaixo sobre possíveis problemas.

## <a name="general"></a>Geral

### <a name="rp-register"></a>Cenário Não é possível registrar o provedor de recursos de automação para assinaturas

#### <a name="issue"></a>Problema

Você pode receber o seguinte erro ao trabalhar com soluções em sua conta de automação.

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

#### <a name="cause"></a>Causa

O provedor de recursos de automação não está registrado na assinatura.

#### <a name="resolution"></a>Resolução

Você pode registrar os provedores de recursos de automação concluindo as seguintes etapas no portal do Azure:

1. Clique em **todos os serviços** na lista inferior de serviços do Azure e, em seguida, selecione **assinaturas** no grupo de serviços _geral_ .
2. Selecione a sua subscrição.
3. Clique em **provedores de recursos** em _configurações_.
4. Na lista de provedores de recursos, verifique se o provedor de recursos **Microsoft. Automation** está registrado.
5. Se o provedor não estiver listado, registre o provedor **Microsoft. Automation** com as etapas listadas [ ](/azure/azure-resource-manager/resource-manager-register-provider-errors)em.

### <a name="mw-exceeded"></a>Cenário Falha no agendamento do gerenciamento de atualizações com o erro MaintenanceWindowExceeded

#### <a name="issue"></a>Problema

A janela de manutenção padrão para atualizações é de 120 minutos. Você pode aumentar a janela de manutenção para um máximo de seis (6) horas ou 360 minutos.

#### <a name="resolution"></a>Resolução

Edite todas as implantações de atualização agendadas com falha e aumente a janela de manutenção.

Para obter mais informações sobre janelas de manutenção, consulte [instalar atualizações](../automation-update-management.md#install-updates).

### <a name="components-enabled-not-working"></a>Cenário Os componentes da solução ' Gerenciamento de Atualizações ' foram habilitados e agora esta máquina virtual está sendo configurada

#### <a name="issue"></a>Problema

Você continua a ver a seguinte mensagem em uma máquina virtual 15 minutos após a integração:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Causa

Esse erro pode ser causado pelos seguintes motivos:

1. A comunicação de volta com a conta de automação está sendo bloqueada.
2. A VM que está sendo integrada pode ter vindo de um computador clonado que não foi Sysprep com o Microsoft Monitoring Agent instalado.

#### <a name="resolution"></a>Resolução

1. Visite o [planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning) para saber mais sobre quais endereços e portas precisam ser permitidos para que gerenciamento de atualizações funcionem.
2. Se estiver usando uma imagem clonada:
   1. No espaço de trabalho log Analytics, remova a VM da pesquisa salva para a configuração `MicrosoftDefaultScopeConfig-Updates` de escopo se ela for mostrada. As pesquisas salvas podem ser encontradas em **geral** em seu espaço de trabalho.
   2. Execute `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Execute `Restart-Service HealthService` para reiniciar o `HealthService`. Isso recriará a chave e gerará um novo UUID.
   4. Se isso não funcionar, execute o Sysprep na imagem primeiro e instale o agente MMA após o fato.

### <a name="multi-tenant"></a>Cenário Você recebe um erro de assinatura vinculado ao criar uma implantação de atualização para computadores em outro locatário do Azure.

#### <a name="issue"></a>Problema

Você recebe o seguinte erro ao tentar criar uma implantação de atualização para computadores em outro locatário do Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Causa

Esse erro ocorre quando você cria uma implantação de atualização que tem máquinas virtuais do Azure em outro locatário incluído em uma implantação de atualização.

#### <a name="resolution"></a>Resolução

Você precisará usar a seguinte solução para obtê-las agendadas. Você pode usar o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) com a opção `-ForUpdate` para criar um agendamento e usar o [cmdlet New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passar os computadores no outro locatário para o `-NonAzureComputer` parâmetro. O exemplo a seguir mostra um exemplo de como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Cenário Os computadores não aparecem no portal em Gerenciamento de Atualizações

#### <a name="issue"></a>Problema

Você pode executar entre os seguintes cenários:

* Seu computador mostra **não configurado** na exibição Gerenciamento de atualizações de uma VM

* Seus computadores estão ausentes na exibição Gerenciamento de Atualizações da sua conta de automação

* Você tem computadores que mostram como **não avaliados** sob **conformidade**, mas você vê dados de pulsação em logs de Azure monitor para o Hybrid Runbook Worker, mas não gerenciamento de atualizações.

#### <a name="cause"></a>Causa

Isso pode ser causado por problemas potenciais de configuração local ou pela configuração de escopo configurada incorretamente.

O Hybrid Runbook Worker pode precisar ser registrado novamente e reinstalado.

Você pode ter definido uma cota em seu espaço de trabalho que foi atingido e parando que os dados sejam armazenados.

#### <a name="resolution"></a>Resolução

* Verifique se seu computador está se comunicando com o espaço de trabalho correto. Verifique em qual espaço de trabalho seu computador está subordinado. Para obter instruções sobre como verificar isso, consulte [verificar a conectividade do agente para log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Em seguida, verifique se esse é o espaço de trabalho vinculado à sua conta de automação do Azure. Para confirmar isso, navegue até sua conta de automação e clique em **espaço de trabalho vinculado** em **recursos relacionados**.

* Verifique se as máquinas aparecem no espaço de trabalho Log Analytics. Execute a consulta a seguir em seu espaço de trabalho Log Analytics que está vinculado à sua conta de automação. Se você não vir seu computador nos resultados da consulta, seu computador não está pulsando, o que significa que há muito provavelmente um problema de configuração local. Você pode executar a solução de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline) dependendo do sistema operacional, ou você pode [reinstalar o agente](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Se o computador aparecer nos resultados da consulta, você precisará muito da configuração de escopo especificada no seguinte marcador.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Verifique se há problemas de configuração de escopo. A [configuração de escopo](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quais computadores são configurados para a solução. Se seu computador estiver aparecendo no seu espaço de trabalho, mas não estiver aparecendo, você precisará configurar a configuração de escopo para direcionar os computadores. Para saber como fazer isso, confira [carregar computadores no espaço de trabalho](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Se as etapas acima não resolverem o problema, siga as etapas em [implantar um Hybrid runbook Worker do Windows](../automation-windows-hrw-install.md) para reinstalar o Hybrid Worker para Windows ou [implantar um Hybrid runbook Worker do Linux](../automation-linux-hrw-install.md) para Linux.

* Em seu espaço de trabalho, execute a consulta a seguir. Se você vir o resultado `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` , terá uma cota definida em seu espaço de trabalho que foi atingido e parou os dados de serem salvos. Em seu espaço de trabalho, navegue até **uso e custos** > estimados**Gerenciamento de volume de dados** e verifique sua cota ou remova a cota que você tem.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

Se você encontrar problemas ao tentar integrar a solução em uma máquina virtual, verifique o log de eventos **Operations Manager** em logs de **aplicativos e serviços** no computador local para eventos com a ID de evento **4502** e mensagem de evento contendo **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

A seção a seguir realça as mensagens de erro específicas e uma possível resolução para cada uma. Para outros problemas de integração, consulte solução de [problemas de integração de solução](onboarding.md).

### <a name="machine-already-registered"></a>Cenário O computador já está registrado em uma conta diferente

#### <a name="issue"></a>Problema

Você receberá a seguinte mensagem de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Causa

O computador já está integrado a outro espaço de trabalho para Gerenciamento de Atualizações.

#### <a name="resolution"></a>Resolução

Execute a limpeza de artefatos antigos no computador [excluindo o grupo de runbooks híbrido](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) e tente novamente.

### <a name="machine-unable-to-communicate"></a>Cenário O computador não consegue se comunicar com o serviço

#### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Causa

Pode haver um proxy, gateway ou firewall bloqueando a comunicação da rede.

#### <a name="resolution"></a>Resolução

Examine sua rede e verifique se as portas e os endereços apropriados são permitidos. Consulte [requisitos de rede](../automation-hybrid-runbook-worker.md#network-planning), para obter uma lista de portas e endereços que são necessários para gerenciamento de atualizações e Hybrid runbook Workers.

### <a name="unable-to-create-selfsigned-cert"></a>Cenário Não é possível criar o certificado autoassinado

#### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Causa

O Hybrid Runbook Worker não conseguiu gerar um certificado autoassinado

#### <a name="resolution"></a>Resolução

Verifique se a conta do sistema tem acesso de leitura à pasta **C:\ProgramData\Microsoft\Crypto\RSA** e tente novamente.

### <a name="failed-to-start"></a>Cenário Uma máquina mostra falha ao iniciar em uma implantação de atualização

#### <a name="issue"></a>Problema

Um computador tem o status **falha ao iniciar** para um computador. Ao exibir os detalhes específicos do computador, você verá o seguinte erro:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Causa

Esse erro pode ocorrer devido a um dos seguintes motivos:

* O computador não existe mais.
* A máquina está desligada e inacessível.
* O computador tem um problema de conectividade de rede e o trabalhador híbrido no computador está inacessível.
* Houve uma atualização para o Microsoft Monitoring Agent que alterou o SourceComputerId
* Sua execução de atualização pode ter sido limitada se você atingir o limite de 2.000 trabalhos simultâneos em uma conta de automação. Cada implantação é considerada um trabalho e cada computador em uma contagem de implantação de atualização como um trabalho. Qualquer outro trabalho de automação ou implantação de atualização em execução no momento na conta de automação é contabilizada em relação ao limite de trabalhos simultâneos.

#### <a name="resolution"></a>Resolução

Quando aplicável, use [grupos dinâmicos](../automation-update-management.md#using-dynamic-groups) para suas implantações de atualização.

* Verifique se o computador ainda existe e está acessível. Se ele não existir, edite a implantação e remova o computador.
* Consulte a seção sobre [planejamento de rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços que são necessários para gerenciamento de atualizações e verificar se seu computador atende a esses requisitos.
* Execute a consulta a seguir em log Analytics para localizar computadores em seu ambiente `SourceComputerId` , cuja alteração foi alterada. Procure computadores que tenham o mesmo `Computer` valor, mas com valor diferente. `SourceComputerId` Depois de encontrar os computadores afetados, você deve editar as implantações de atualização direcionadas a essas máquinas e remover e adicionar novamente as máquinas para `SourceComputerId` que o reflita o valor correto.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Cenário A máquina é mostrada como não avaliada e mostra uma exceção HResult

#### <a name="issue"></a>Problema

Você tem computadores que mostram como **não avaliados** em **conformidade**e você vê uma mensagem de exceção abaixo dele.

#### <a name="cause"></a>Causa

O Windows Update ou o WSUS não está configurado corretamente no computador. Gerenciamento de Atualizações conta com o Windows Update ou o WSUS para fornecer as atualizações necessárias, o status do patch e os resultados dos patches implantados. Sem essas informações Gerenciamento de Atualizações não é possível relatar corretamente os patches necessários ou instalados.

#### <a name="resolution"></a>Resolução

Clique duas vezes na exceção exibida em vermelho para ver a mensagem de exceção inteira. Examine a tabela a seguir para obter as possíveis soluções ou ações a serem executadas:

|Exceção  |Resolução ou ação  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Pesquise o código de erro relevante na [lista de códigos de erro do Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) para encontrar detalhes adicionais sobre a causa da exceção.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Esses erros são problemas de conectividade de rede. Verifique se seu computador tem a conectividade de rede apropriada para Gerenciamento de Atualizações. Consulte a seção sobre [planejamento de rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços necessários.        |
|`0x8024001E`| A operação de atualização não foi concluída porque o serviço ou o sistema estava sendo desligado.|
|`0x8024002E`| Windows Update serviço está desabilitado.|
|`0x8024402C`     | Se você estiver usando um servidor WSUS, verifique se os valores do registro `WUServer` para `WUStatusServer` e sob a chave `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` do registro têm o servidor WSUS correto.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Verifique se o serviço de Windows Update (wuauserv) está em execução e não está desabilitado.        |
|Qualquer outra exceção genérica     | Faça uma pesquisa na Internet para obter as soluções possíveis e trabalhe com o suporte de ti local.         |

A revisão `windowsupdate.log` do pode ajudá-lo a tentar determinar a possível causa também. Para obter mais informações sobre como ler o log, consulte [como ler o arquivo windowsupdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Além disso, você pode baixar e executar a [solução de problemas Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) para verificar se há algum problema com Windows Update no computador.

> [!NOTE]
> O [Windows Update solução de problemas](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indica que ele é para clientes Windows, mas também funciona no Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Cenário: Falha ao iniciar a execução da atualização

#### <a name="issue"></a>Problema

Uma execução de atualização falha ao iniciar em um computador Linux.

#### <a name="cause"></a>Causa

O Hybrid Worker do Linux não está íntegro.

#### <a name="resolution"></a>Resolução

Faça uma cópia do seguinte arquivo de log e preserve-o para fins de solução de problemas:

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Cenário: A execução de atualização é iniciada, mas encontra erros

#### <a name="issue"></a>Problema

Uma execução de atualização é iniciada, mas encontra erros durante a execução.

#### <a name="cause"></a>Causa

As possíveis causas podem ser:

* O Gerenciador de pacotes não está íntegro
* Pacotes específicos podem interferir na aplicação de patch com base na nuvem
* Outras razões

#### <a name="resolution"></a>Resolução

Se ocorrerem falhas durante uma execução de atualização depois que ela for iniciada com êxito no Linux, verifique a saída do trabalho do computador afetado na execução. Você pode encontrar mensagens de erro específicas do Gerenciador de pacotes do seu computador que você pode pesquisar e tomar medidas. Gerenciamento de Atualizações exige que o Gerenciador de pacotes esteja íntegro para implantações de atualização bem-sucedidas.

Em alguns casos, as atualizações de pacote podem interferir Gerenciamento de Atualizações impedindo a conclusão de uma implantação de atualização. Se você vir isso, precisará excluir esses pacotes de execuções de atualização futuras ou instalá-los manualmente.

Se você não puder resolver um problema de aplicação de patch, faça uma cópia do seguinte arquivo de log e preserve-o **antes** de a próxima implantação de atualização iniciar para fins de solução de problemas:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-are-not-installed"></a>Os patches não estão instalados

### <a name="machines-do-not-install-updates"></a>Os computadores não instalam atualizações

* Experimente executar as atualizações diretamente na máquina. Se a máquina não se conseguir atualizar, veja a [lista de potenciais erros no guia de resolução de problemas](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Se as atualizações forem executadas localmente, experimente remover e reinstalar o agente na máquina com as instruções em ["Remove a VM from Update Management"](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-from-update-management) (“Remover VMs da Gestão de Atualizações”).

### <a name="i-know-updates-are-available-but-they-dont-show-as-needed-on-my-machines"></a>Sei que as atualizações estão disponíveis, mas elas não são mostradas conforme necessário em meus computadores

* Isto acontece muitas vezes se as máquinas estiverem configuradas para obterem as atualizações a partir do WSUS/SCCM, mas o WSUS/SCCM não aprovou as atualizações.
* Para verificar se as máquinas estão configuradas para o WSUS/SCCM, pode [cruzar as referências da chave do registo "UseWUServer" com as chaves do registo na secção "Configurar as Atualizações Automáticas ao Editar o Registo" deste documento](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>**As atualizações aparecem como tendo sido instaladas, mas não as encontro na minha máquina**

* Muitas vezes, as atualizações são substituídas por outras. Para obter mais informações, veja ["Update is superseded" ("A atualização foi substituída"), no guia de Resolução de Problemas do Windows Update](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)

### <a name="installing-updates-by-classification-on-linux"></a>**Instalar atualizações por classificação no Linux**

* A implementação de atualizações no Linux por classificação ("atualizações críticas e de segurança") tem limitações importantes, sobretudo para o CentOS. Estas [limitações estão documentadas na página de descrição geral Gestão de Atualizações](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)

### <a name="kb2267602-is-consistently--missing"></a>**KB2267602 está constantemente em falta**

* KB2267602 é a [atualização de definições do Windows Defender](https://www.microsoft.com/wdsi/definitions). Esta é atualizada diariamente.

## <a name="other"></a>Cenário Meu problema não está listado acima

### <a name="issue"></a>Problema

Você tem um problema que não é resolvido pelos outros cenários listados.

### <a name="cause"></a>Causa

Chaves do registro mal configuradas ou ausentes podem causar problemas com Gerenciamento de Atualizações.

### <a name="resolution"></a>Resolução

Exclua a chave `HKLM:\SOFTWARE\Microsoft\HybridRunbookWorker` do registro e reinicie o **HealthService**.

Você também pode usar os comandos do PowerShell a seguir.

```powershell
Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
Restart-Service healthservice
```

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você poderá arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

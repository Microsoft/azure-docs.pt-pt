---
title: Solucionar erros com o Azure Gerenciamento de Atualizações
description: Saiba como solucionar problemas com a solução Gerenciamento de Atualizações no Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: f60ec802af0c88ee8cb3809bf27feef89e11570a
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769800"
---
# <a name="troubleshooting-issues-with-update-management"></a>Solucionando problemas com Gerenciamento de Atualizações

Este artigo aborda soluções para problemas que você pode encontrar ao usar o Gerenciamento de Atualizações.

Há uma solução de problemas de agente para o agente de Hybrid Worker para determinar o problema subjacente. Para saber mais sobre a solução de problemas, consulte [solucionar problemas do agente de atualização](update-agent-issues.md). Para todos os outros problemas, use as diretrizes de solução de problemas a seguir.

Se você encontrar problemas enquanto estiver tentando integrar a solução em uma VM (máquina virtual), verifique o log de **Operations Manager** em logs de **aplicativos e serviços** no computador local em busca de eventos com a ID de evento 4502 e detalhes de eventos que contenham **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

A seção a seguir realça mensagens de erro específicas e possíveis resoluções para cada uma delas. Para outros problemas de integração, consulte [solucionar problemas de integração da solução](onboarding.md).

## <a name="nologs"></a>Cenário: os computadores não aparecem no portal em Gerenciamento de Atualizações

### <a name="issue"></a>Problema

Você enfrenta os seguintes sintomas:

* Seu computador mostra **não configurado** na exibição Gerenciamento de atualizações de uma VM.

* Seus computadores estão ausentes na exibição Gerenciamento de Atualizações da sua conta de automação do Azure.

* Você tem computadores que mostram como **não avaliados** em **conformidade**. No entanto, você vê dados de pulsação em logs de Azure Monitor para o Hybrid Runbook Worker, mas não para Gerenciamento de Atualizações.

### <a name="cause"></a>Causa

Esse problema pode ser causado por problemas de configuração local ou pela configuração de escopo configurada incorretamente.

Talvez seja necessário registrar novamente e reinstalar o Hybrid Runbook Worker.

Você pode ter definido uma cota em seu espaço de trabalho que foi atingido e que está impedindo o armazenamento de dados adicional.

### <a name="resolution"></a>Resolução

* Execute a solução de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline), dependendo do sistema operacional.

* Verifique se seu computador está se comunicando com o espaço de trabalho correto. Para obter orientação sobre como verificar esse aspecto, consulte [verificar a conectividade do agente para log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Verifique também se esse espaço de trabalho está vinculado à sua conta de automação do Azure. Para confirmar, acesse sua conta de automação e selecione **espaço de trabalho vinculado** em **recursos relacionados**.

* Verifique se os computadores aparecem no espaço de trabalho Log Analytics. Execute a seguinte consulta no espaço de trabalho Log Analytics que está vinculado à sua conta de automação:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  Se você não vir seu computador nos resultados da consulta, ele não fez check-in recentemente, o que significa que provavelmente há um problema de configuração local e você deve [reinstalar o agente](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Se o computador aparecer nos resultados da consulta, você precisará verificar a configuração de escopo especificada no próximo item com marcadores nesta lista.

* Verifique se há problemas de configuração de escopo. A [configuração de escopo](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quais computadores são configurados para a solução. Se seu computador estiver aparecendo no seu espaço de trabalho, mas não no portal de **Gerenciamento de atualizações** , você precisará configurar a configuração de escopo para direcionar os computadores. Para saber como fazer isso, confira [carregar computadores no espaço de trabalho](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Em seu espaço de trabalho, execute a seguinte consulta:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  Se você obtiver um resultado `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`, haverá uma cota definida em seu espaço de trabalho que foi atingida e que parou os dados de serem salvos. Em seu espaço de trabalho, acesse **uso e custos estimados** > **Gerenciamento de volume de dados** e verifique sua cota ou remova-o.

* Se essas etapas não resolverem o problema, siga as etapas em [implantar um Hybrid runbook Worker do Windows](../automation-windows-hrw-install.md) para reinstalar o Hybrid Worker para Windows. Ou, para Linux, [implante um Hybrid runbook Worker do Linux](../automation-linux-hrw-install.md).

## <a name="rp-register"></a>Cenário: não é possível registrar o provedor de recursos de automação para assinaturas

### <a name="issue"></a>Problema

Ao trabalhar com soluções em sua conta de automação, você encontrará o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

O provedor de recursos de automação não está registrado na assinatura.

### <a name="resolution"></a>Resolução

Para registrar o provedor de recursos de automação, siga estas etapas na portal do Azure:

1. Na lista de serviços do Azure, na parte inferior do portal, selecione **todos os serviços**e, em seguida, selecione **assinaturas** no grupo de serviços geral.
2. Selecione a sua subscrição.
3. Em **configurações**, selecione **provedores de recursos**.
4. Na lista de provedores de recursos, verifique se o provedor de recursos **Microsoft. Automation** está registrado.
5. Se não estiver listado, registre o provedor **Microsoft. Automation** seguindo as etapas em [resolver erros para o registro do provedor de recursos](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="components-enabled-not-working"></a>Cenário: os componentes para a solução de Gerenciamento de Atualizações foram habilitados e agora esta máquina virtual está sendo configurada

### <a name="issue"></a>Problema

Você continua a ver a seguinte mensagem em uma máquina virtual 15 minutos após a integração:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer pelos seguintes motivos:

- A comunicação com a conta de automação está sendo bloqueada.
- A VM que está sendo integrada pode ter vindo de um computador clonado que não foi Sysprep com o Microsoft Monitoring Agent (MMA) instalado.

### <a name="resolution"></a>Resolução

1. Vá para [planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning) para saber mais sobre quais endereços e portas devem ser permitidos para que gerenciamento de atualizações funcionem.
2. Se você estiver usando uma imagem clonada:
   1. No espaço de trabalho Log Analytics, remova a VM da pesquisa salva para a configuração de escopo de `MicrosoftDefaultScopeConfig-Updates` se ela for mostrada. As pesquisas salvas podem ser encontradas em **geral** em seu espaço de trabalho.
   2. Execute `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`.
   3. Execute `Restart-Service HealthService` para reiniciar o `HealthService`. Isso recria a chave e gera um novo UUID.
   4. Se essa abordagem não funcionar, execute o Sysprep na imagem primeiro e, em seguida, instale o MMA.

## <a name="multi-tenant"></a>Cenário: você recebe um erro de assinatura vinculada quando cria uma implantação de atualização para computadores em outro locatário do Azure

### <a name="issue"></a>Problema

Você encontrará o seguinte erro ao tentar criar uma implantação de atualização para computadores em outro locatário do Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Causa

Esse erro ocorre quando você cria uma implantação de atualização que tem VMs do Azure em outro locatário que está incluído em uma implantação de atualização.

### <a name="resolution"></a>Resolução

Use a solução alternativa a seguir para obter esses itens agendados. Você pode usar o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) com a opção `-ForUpdate` para criar uma agenda. Em seguida, use o cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passe os computadores no outro locatário para o parâmetro `-NonAzureComputer`. O exemplo a seguir mostra como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Cenário: reinicializações não explicadas

### <a name="issue"></a>Problema

Embora você tenha definido a opção de **controle de reinicialização** para **nunca reinicializar**, as máquinas ainda são reinicializadas após a instalação das atualizações.

### <a name="cause"></a>Causa

Windows Update pode ser modificado por várias chaves do registro, qualquer uma das quais pode modificar o comportamento de reinicialização.

### <a name="resolution"></a>Resolução

Examine as chaves do registro listadas em [Configurando atualizações automáticas editando o registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) e [as chaves do registro usadas para gerenciar a reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) para garantir que seus computadores estejam configurados corretamente.

## <a name="failed-to-start"></a>Cenário: o computador mostra "falha ao iniciar" em uma implantação de atualização

### <a name="issue"></a>Problema

Um computador mostra um status de **falha ao iniciar** . Ao exibir os detalhes específicos do computador, você verá o seguinte erro:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Causa

Este erro pode ocorrer por um dos seguintes motivos:

* O computador não existe mais.
* A máquina está desligada e inacessível.
* O computador tem um problema de conectividade de rede e, portanto, o Hybrid Worker no computador está inacessível.
* Houve uma atualização para o MMA que alterou o SourceComputerId.
* A execução de atualização foi limitada se você atingiu o limite de 2.000 trabalhos simultâneos em uma conta de automação. Cada implantação é considerada um trabalho e cada computador em uma implantação de atualização conta como um trabalho. Qualquer outro trabalho de automação ou implantação de atualização em execução no momento em sua conta de automação conta para o limite de trabalhos simultâneos.

### <a name="resolution"></a>Resolução

Quando aplicável, use [grupos dinâmicos](../automation-update-management-groups.md) para suas implantações de atualização. Além disso:

* Verifique se o computador ainda existe e acessível. Se ele não existir, edite a implantação e remova o computador.
* Consulte a seção [planejamento de rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços necessários para gerenciamento de atualizações e, em seguida, verifique se o computador atende a esses requisitos.
* Execute a consulta a seguir em Log Analytics para localizar computadores em seu ambiente cujo `SourceComputerId` foi alterado. Procure computadores que tenham o mesmo valor de `Computer`, mas um valor diferente de `SourceComputerId`. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   Depois de encontrar as máquinas afetadas, edite as implantações de atualização direcionadas a essas máquinas e, em seguida, remova-as e adicione-as novamente para que `SourceComputerId` reflita o valor correto.

## <a name="updates-nodeployment"></a>Cenário: as atualizações são instaladas sem uma implantação

### <a name="issue"></a>Problema

Quando você registra um computador Windows no Gerenciamento de Atualizações, você vê as atualizações instaladas sem uma implantação.

### <a name="cause"></a>Causa

No Windows, as atualizações são instaladas automaticamente assim que estiverem disponíveis. Esse comportamento pode causar confusão se você não agendou uma atualização para ser implantada no computador.

### <a name="resolution"></a>Resolução

A chave do registro de `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` usa como padrão uma configuração de 4: **download automático e instalação**.

Para clientes Gerenciamento de Atualizações, é recomendável definir essa chave como 3: **download automático, mas não instalar automaticamente**.

Para obter mais informações, consulte [Configurando atualizações automáticas](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="machine-already-registered"></a>Cenário: o computador já está registrado para uma conta diferente

### <a name="issue"></a>Problema

Você receberá a seguinte mensagem de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

O computador já foi integrado a outro espaço de trabalho para Gerenciamento de Atualizações.

### <a name="resolution"></a>Resolução

1. Siga as etapas em [computadores que não aparecem no portal em gerenciamento de atualizações](#nologs) para certificar-se de que a máquina está relatando para o espaço de trabalho correto.
2. Limpe os artefatos antigos no computador [excluindo o grupo de runbooks híbrido](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)e tente novamente.

## <a name="machine-unable-to-communicate"></a>Cenário: a máquina não pode se comunicar com o serviço

### <a name="issue"></a>Problema

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

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Causa

Um proxy, gateway ou firewall pode estar bloqueando A comunicação de rede. 

### <a name="resolution"></a>Resolução

Examine sua rede e verifique se as portas e os endereços apropriados são permitidos. Consulte [requisitos de rede](../automation-hybrid-runbook-worker.md#network-planning) para obter uma lista de portas e endereços que são necessários para gerenciamento de atualizações e Hybrid runbook Workers.

## <a name="unable-to-create-selfsigned-cert"></a>Cenário: não é possível criar um certificado autoassinado

### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Causa

O Hybrid Runbook Worker não pôde gerar um certificado autoassinado.

### <a name="resolution"></a>Resolução

Verifique se a conta do sistema tem acesso de leitura à pasta **C:\ProgramData\Microsoft\Crypto\RSA** e tente novamente.

## <a name="mw-exceeded"></a>Cenário: falha na atualização agendada com um erro MaintenanceWindowExceeded

### <a name="issue"></a>Problema

A janela de manutenção padrão para atualizações é de 120 minutos. Você pode aumentar a janela de manutenção para um máximo de 6 horas ou 360 minutos.

### <a name="resolution"></a>Resolução

Edite todas as implantações de atualização agendadas com falha e aumente a janela de manutenção.

Para obter mais informações sobre janelas de manutenção, consulte [instalar atualizações](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="hresult"></a>Cenário: a máquina é mostrada como "não avaliada" e mostra uma exceção HResult

### <a name="issue"></a>Problema

* Você tem computadores que mostram como **não avaliados** em **conformidade**e você vê uma mensagem de exceção abaixo dele.
* Você tem computadores que mostram como não avaliados.
* Você verá um código de erro HRESULT no Portal.

### <a name="cause"></a>Causa

O agente de atualização (Windows Update agente no Windows; o Gerenciador de pacotes para uma distribuição do Linux) não está configurado corretamente. Gerenciamento de Atualizações se baseia no agente de atualização da máquina para fornecer as atualizações necessárias, o status do patch e os resultados dos patches implantados. Sem essas informações, Gerenciamento de Atualizações não pode relatar corretamente os patches necessários ou instalados.

### <a name="resolution"></a>Resolução

Tente executar atualizações localmente no computador. Se isso falhar, isso normalmente significa que há um erro de configuração com o agente de atualização.

Esse problema é frequentemente causado por problemas de firewall e configuração de rede. Experimente o seguinte:

* Para o Linux, verifique a documentação apropriada para ter certeza de que você pode acessar o ponto de extremidade de rede do seu repositório de pacotes.
* Para o Windows, verifique a configuração do agente, conforme listado em [atualizações não está baixando do ponto de extremidade da intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Se os computadores estiverem configurados para Windows Update, verifique se você pode acessar os pontos de extremidade descritos em [problemas relacionados ao http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Se os computadores estiverem configurados para o Windows Server Update Services (WSUS), verifique se você pode acessar o servidor do WSUS configurado pela [chave do registro WUServer](/windows/deployment/update/waas-wu-settings).

Se você vir um HRESULT, clique duas vezes na exceção exibida em vermelho para ver a mensagem de exceção inteira. Examine a tabela a seguir para obter possíveis soluções ou ações recomendadas:

|Exceção  |Resolução ou ação  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Pesquise o código de erro relevante na [lista de códigos de erro do Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) para encontrar detalhes adicionais sobre a causa da exceção.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Isso indica problemas de conectividade de rede. Verifique se o computador tem conectividade de rede para Gerenciamento de Atualizações. Consulte a seção [planejamento de rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços necessários.        |
|`0x8024001E`| A operação de atualização não foi concluída porque o serviço ou o sistema estava sendo desligado.|
|`0x8024002E`| Windows Update serviço está desabilitado.|
|`0x8024402C`     | Se você estiver usando um servidor WSUS, verifique se os valores do registro para `WUServer` e `WUStatusServer` na chave do registro `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` especifique o servidor do WSUS correto.        |
|`0x80072EE2`|Há um problema de conectividade de rede ou um problema em conversar com um servidor WSUS configurado. Verifique as configurações do WSUS e verifique se o serviço pode ser acessado do cliente.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Verifique se o serviço de Windows Update (wuauserv) está em execução e não está desabilitado.        |
|`0x80070005`| Um erro de acesso negado pode ser causado por qualquer um dos seguintes:<br> Computador infectado<br> Windows Update configurações não configuradas corretamente<br> Erro de permissão de arquivo com a pasta%WinDir%\SoftwareDistribution<br> Espaço em disco insuficiente na unidade do sistema (C:).
|Qualquer outra exceção genérica     | Execute uma pesquisa na Internet para obter soluções possíveis e trabalhe com o suporte de ti local.         |

A revisão do arquivo%Windir%\Windowsupdate.log também pode ajudá-lo a determinar possíveis causas. Para obter mais informações sobre como ler o log, consulte [como ler o arquivo windowsupdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Você também pode baixar e executar a [solução de problemas Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) para verificar se há problemas com Windows Update no computador.

> [!NOTE]
> A documentação da [solução de problemas Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indica que ele é para uso em clientes Windows, mas também funciona no Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Cenário: a execução de atualização retorna o status "falha" (Linux)

### <a name="issue"></a>Problema

Uma execução de atualização é iniciada, mas encontra erros durante a execução.

### <a name="cause"></a>Causa

Causas possíveis:

* O Gerenciador de pacotes não está íntegro.
* O agente de atualização (WUA para Windows, Gerenciador de pacotes específico do distribuição para Linux) está configurado incorretamente.
* Os pacotes específicos estão interferindo com a aplicação de patch baseada em nuvem.
* O computador está inacessível.
* As atualizações tinham dependências que não foram resolvidas.

### <a name="resolution"></a>Resolução

Se ocorrerem falhas durante uma execução de atualização depois que ela for iniciada com êxito, [Verifique a saída do trabalho](../manage-update-multi.md#view-results-of-an-update-deployment) do computador afetado na execução. Você pode encontrar mensagens de erro específicas de seus computadores nos quais você pode pesquisar e tomar medidas. Gerenciamento de Atualizações exige que o Gerenciador de pacotes esteja íntegro para implantações de atualização bem-sucedidas.

Se patches, pacotes ou atualizações específicas forem vistos imediatamente antes da falha do trabalho, você poderá tentar [exclui](../automation-tutorial-update-management.md#schedule-an-update-deployment) -los da próxima implantação de atualização. Para coletar informações de log de Windows Update, consulte [Windows Update arquivos de log](/windows/deployment/update/windows-update-logs).

Se você não puder resolver um problema de aplicação de patch, faça uma cópia do seguinte arquivo de log e preserve-o para fins de solução de problemas *antes* da próxima implantação de atualização iniciar:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Os patches não estão instalados

### <a name="machines-dont-install-updates"></a>Os computadores não instalam atualizações

* Experimente executar as atualizações diretamente na máquina. Se o computador não puder aplicar as atualizações, consulte a [lista de possíveis erros no guia de solução de problemas](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Se as atualizações forem executadas localmente, tente remover e reinstalar o agente no computador seguindo as orientações em [remover uma VM do gerenciamento de atualizações](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Sei que as atualizações estão disponíveis, mas elas não são mostradas como disponíveis em meus computadores

* Isso geralmente acontece se os computadores estiverem configurados para obter atualizações do WSUS ou System Center Configuration Manager (SCCM), mas o WSUS e o SCCM ainda não aprovaram as atualizações.
* Você pode verificar se os computadores estão configurados para o WSUS e o SCCM [fazendo referência cruzada à chave do registro UseWUServer para as chaves do registro na seção "Configurando o atualizações automáticas editando o registro" deste artigo](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).
* Se as atualizações não forem aprovadas no WSUS, elas não serão instaladas. Você pode verificar se há atualizações não aprovadas em Log Analytics executando a seguinte consulta:

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>As atualizações são exibidas como instaladas, mas não consigo encontrá-las no meu computador

* Muitas vezes, as atualizações são substituídas por outras. Para obter mais informações, consulte a [atualização é substituída](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) no guia de solução de problemas de Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalar atualizações por classificação no Linux

* A implementação de atualizações no Linux por classificação ("atualizações críticas e de segurança") tem limitações importantes, sobretudo para o CentOS. Essas limitações estão documentadas na [página Visão geral do gerenciamento de atualizações](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 está consistentemente ausente

* KB2267602 é a [atualização de definições do Windows Defender](https://www.microsoft.com/wdsi/definitions). Ele é atualizado diariamente.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto o problema ou não conseguir resolver o problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport), a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Arquivo de um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

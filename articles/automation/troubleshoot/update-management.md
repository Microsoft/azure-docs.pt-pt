---
title: Erros de resolução de problemas com a Azure Update Management
description: Saiba como resolver problemas e resolver problemas com a solução de Gestão de Atualizações no Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 1b0047cda3664759f4f1b6499c8a54ee22f98ab3
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227459"
---
# <a name="troubleshooting-issues-with-update-management"></a>Problemas de resolução de problemas com gestão de atualizações

Este artigo discute soluções para questões que poderá encontrar quando utilizar a Atualização.

Há um agente que desarma problemas para o agente híbrido para determinar o problema subjacente. Para saber mais sobre o problema, consulte problemas de agente de [atualização da Troubleshoot](update-agent-issues.md). Para todas as outras questões, utilize as seguintes orientações de resolução de problemas.

Se encontrar problemas enquanto tenta embarcar na solução numa máquina virtual (VM), verifique o registo do Gestor de **Operações** em Registos de **Aplicação e Serviços** na máquina local para eventos com id de evento 4502 e detalhes do evento que contenham **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

A secção seguinte destaca mensagens de erro específicas e possíveis resoluções para cada um. Para outras questões de embarque, consulte a [solução Troubleshoot no embarque.](onboarding.md)

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Cenário: Atualização supersedindicada indicada como desaparecida na Gestão de Atualizações

### <a name="issue"></a>Problema

As atualizações antigas estão a aparecer na Atualização da Conta Azure como desaparecidas, apesar de terem sido substituídos. Uma atualização supersed é uma que não tem de ser instalada porque uma atualização posterior que corrige a mesma vulnerabilidade está disponível. A Atualização Management ignora a atualização sobresedada e torna-a não aplicável a favor da atualização de superseding. Para obter informações sobre um problema relacionado, consulte [a Atualização é substituído](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Causa

As atualizações supersed não estão a ser corretamente indicadas como recusadas para que possam ser consideradas não aplicáveis.

### <a name="resolution"></a>Resolução

Quando uma atualização supersed se torna 100% não aplicável, deve alterar o estado de aprovação dessa atualização para **Declinado**. Para fazer isto para todas as suas atualizações:

1. Na Conta de Automação, selecione **Gestão de Atualização** para visualizar o estado da máquina. Ver [Ver avaliações](../manage-update-multi.md#view-an-update-assessment)de atualização .

2. Verifique a atualização sobresedida para se certificar de que não é aplicável a 100%. 

3. Marque a atualização como recusada, a menos que tenha alguma dúvida sobre a atualização. 

4. Selecione Computadores e, na coluna Compliance, force uma rescanpara o cumprimento. Ver [Gerir atualizações para várias máquinas](../manage-update-multi.md).

5. Repita os passos acima para outras atualizações supersed.

6. Execute o assistente de limpeza para eliminar ficheiros das atualizações recusadas. 

7. Para a WSUS, limpe manualmente todas as atualizações supersed para refrescar a infraestrutura.

8. Repita este procedimento regularmente para corrigir o problema do visor e minimizar a quantidade de espaço em disco utilizado para a gestão da atualização.

## <a name="nologs"></a>Cenário: Máquinas não aparecem no portal sob Gestão de Atualizações

### <a name="issue"></a>Problema

Experimenta os seguintes sintomas:

* A sua máquina mostra **não configurada** a partir da visão de Gestão de Atualização de um VM.

* Faltam as suas máquinas na visão de Gestão de Atualização da sua conta Azure Automation.

* Tem máquinas que mostram que não são **avaliadas** em **conformidade.** No entanto, você vê dados de batimentos cardíacos em registos do Monitor Azure para o Trabalhador do Livro Híbrido, mas não para Gestão de Atualizações.

### <a name="cause"></a>Causa

Este problema pode ser causado por problemas de configuração locais ou por configuração de âmbito configurada indevidamente.

Pode ter de voltar a registar e reinstalar o Trabalhador híbrido do livro.

Pode ter definido uma quota no seu espaço de trabalho que foi atingida e que está a impedir o armazenamento de dados.

### <a name="resolution"></a>Resolução

* Execute o resolução de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline), dependendo do SISTEMA.

* Certifique-se de que a sua máquina está a reportar ao espaço de trabalho correto. Para obter orientações sobre como verificar este aspeto, consulte verificar a conectividade do [agente para registar o Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Certifique-se também de que este espaço de trabalho está ligado à sua conta De automação Azure. Para confirmar, vá à sua conta de Automação e selecione **Linked workspace** em **Recursos Relacionados**.

* Certifique-se de que as máquinas aparecem no seu espaço de trabalho Log Analytics. Faça a seguinte consulta no espaço de trabalho log Analytics que está ligado à sua conta de Automação:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  Se não vir a sua máquina nos resultados da consulta, não fez o check-in recentemente, o que significa que provavelmente há um problema de configuração local e deve [reinstalar o agente.](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows) Se a sua máquina aparecer nos resultados da consulta, tem de verificar a configuração de âmbito especificada no próximo item com balas nesta lista.

* Verifique se há problemas de configuração de âmbito. [A configuração do âmbito](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quais as máquinas configuradas para a solução. Se a sua máquina estiver a aparecer no seu espaço de trabalho, mas não no portal de Gestão de **Atualizações,** terá de configurar a configuração de âmbito para direcionar as máquinas. Para aprender a fazer isto, consulte [as máquinas de bordo no espaço de trabalho.](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)

* No seu espaço de trabalho, execute a seguinte consulta:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  Se obtém um resultado `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`, há uma quota definida no seu espaço de trabalho que foi alcançada e que impediu que os dados fossem salvos. No seu espaço de trabalho, vá ao **Usage e custos estimados** > **gestão** do volume de dados e verifique a sua quota ou remova-a.

* Se estes passos não resolverem o seu problema, siga os passos no [Windows Hybrid Runbook Worker](../automation-windows-hrw-install.md) para reinstalar o Trabalhador Híbrido para Windows. Ou, para o Linux, implementar um Trabalhador de [Runbook Híbrido Linux.](../automation-linux-hrw-install.md)

## <a name="rp-register"></a>Cenário: Incapaz de registar Fornecedor de Recursos Automation para subscrições

### <a name="issue"></a>Problema

Quando trabalha com soluções na sua conta Automation, encontra o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

O Fornecedor de Recursos Automation não está registado na subscrição.

### <a name="resolution"></a>Resolução

Para registar o Fornecedor de Recursos Automation, siga estes passos no portal Azure:

1. Na lista de serviços Azure na parte inferior do portal, selecione **Todos os serviços**e, em seguida, selecione **Subscrições** no grupo de serviço Geral.
2. Selecione a sua subscrição.
3. Em **Definições,** selecione **Fornecedores de Recursos**.
4. A partir da lista de fornecedores de recursos, verifique se o fornecedor de recursos **da Microsoft.Automation** está registado.
5. Se não estiver listado, registe o fornecedor **Microsoft.Automation** seguindo os passos no [Resolve erros para o registo](/azure/azure-resource-manager/resource-manager-register-provider-errors)do fornecedor de recursos .

## <a name="components-enabled-not-working"></a>Cenário: Os componentes da solução de Gestão de Atualização foram ativados, e agora esta máquina virtual está a ser configurada

### <a name="issue"></a>Problema

Continua a ver a seguinte mensagem numa máquina virtual 15 minutos depois de embarcar:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Causa

Este erro pode ocorrer pelas seguintes razões:

- A comunicação com a conta Automation está a ser bloqueada.
- O VM a bordo pode ter vindo de uma máquina clonada que não foi sysprepped com o Microsoft Monitoring Agent (MMA) instalado.

### <a name="resolution"></a>Resolução

1. Vá à [Rede planejando](../automation-hybrid-runbook-worker.md#network-planning) saber quais endereços e portas devem ser autorizados a atualizar gestão para funcionar.
2. Se estiver a usar uma imagem clonada:
   1. No seu espaço de trabalho Log Analytics, retire o VM da pesquisa guardada para a configuração de âmbito `MicrosoftDefaultScopeConfig-Updates` se for mostrada. Pesquisas guardadas podem ser encontradas sob **o General** no seu espaço de trabalho.
   2. Execute `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`.
   3. Executar `Restart-Service HealthService` para reiniciar o `HealthService`. Isto recria a chave e gera um novo UUID.
   4. Se esta abordagem não funcionar, faça a sysprep na imagem primeiro e, em seguida, instale o MMA.

## <a name="multi-tenant"></a>Cenário: Recebe um erro de subscrição ligado quando cria uma implementação de atualização para máquinas noutro inquilino do Azure

### <a name="issue"></a>Problema

Encontra-se o seguinte erro quando tenta criar uma implementação de atualização para máquinas noutro inquilino azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Causa

Este erro ocorre quando cria uma implementação de atualização que tem VMs Azure em outro inquilino que está incluído numa implementação de atualização.

### <a name="resolution"></a>Resolução

Utilize a seguinte suver para agendar estes itens. Pode utilizar o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) com o interruptor `-ForUpdate` para criar um horário. Em seguida, utilize o [cmdlet New-AzureRmAutomationSoftwareUpdateConfiguração](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passe as máquinas do outro inquilino para o parâmetro `-NonAzureComputer`. O exemplo que se segue mostra como fazê-lo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Cenário: Reboots inexplicáveis

### <a name="issue"></a>Problema

Apesar de ter definido a opção **De controlo de reiniciar** para Never **Reboot,** as máquinas continuam a reiniciar após a instalação das atualizações.

### <a name="cause"></a>Causa

O Windows Update pode ser modificado por várias teclas de registo, qualquer uma das quais pode modificar o comportamento de reboot.

### <a name="resolution"></a>Resolução

Reveja as teclas de registo listadas em [Configurar Atualizações Automáticas, editando as](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) teclas de registo e [registo utilizadas para gerir](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) o reinício para se certificar de que as suas máquinas estão corretamente configuradas.

## <a name="failed-to-start"></a>Cenário: Máquina mostra "Falhou no arranque" numa implementação de atualização

### <a name="issue"></a>Problema

Uma máquina mostra um **estado falhado para iniciar.** Quando visualiza os detalhes específicos da máquina, vê o seguinte erro:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Causa

Este erro pode ocorrer por um dos seguintes motivos:

* A máquina já não existe.
* A máquina está desligada e inacessível.
* A máquina tem um problema de conectividade de rede, pelo que o trabalhador híbrido na máquina é inacessível.
* Houve uma atualização para o MMA que alterou o SourceComputerId.
* A sua atualização foi acelerada se atingir o limite de 2.000 empregos simultâneos numa conta de Automação. Cada implantação é considerada um trabalho, e cada máquina numa atualização conta como um trabalho. Qualquer outro trabalho de automação ou implementação de atualização atualmente em execução na sua conta Automation conta para o limite de trabalho simultâneo.

### <a name="resolution"></a>Resolução

Quando aplicável, utilize [grupos dinâmicos](../automation-update-management-groups.md) para as suas implementações de atualização. Além disso:

* Verifique se a máquina ainda existe e está acessível. Se não existir, edite a sua implantação e retire a máquina.
* Consulte a secção de planeamento da [rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços que são necessários para gestão de atualizações e, em seguida, verifique se a sua máquina cumpre estes requisitos.
* Faça a seguinte consulta no Log Analytics para encontrar máquinas no seu ambiente cujo `SourceComputerId` mudou. Procure computadores com o mesmo valor `Computer`, mas um valor `SourceComputerId` diferente. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   Depois de encontrar as máquinas afetadas, edite as implementações de atualização que visam essas máquinas e, em seguida, remova-as e readicione-as de modo a que `SourceComputerId` reflita o valor correto.

## <a name="updates-nodeployment"></a>Cenário: As atualizações são instaladas sem uma implementação

### <a name="issue"></a>Problema

Ao inscrever uma máquina windows na Atualização, vê atualizações instaladas sem uma implementação.

### <a name="cause"></a>Causa

No Windows, as atualizações são instaladas automaticamente assim que estão disponíveis. Este comportamento pode causar confusão se não tiver agendado uma atualização para ser implementada na máquina.

### <a name="resolution"></a>Resolução

A chave de registo `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` falha numa definição de 4: **descarregamento automático e instalação.**

Para clientes de Gestão de Atualizações, recomendamos que esta chave seja de 3: **download automático mas não instalação automática**.

Para mais informações, consulte [Configurar Atualizações Automáticas](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="machine-already-registered"></a>Cenário: Máquina já está registada numa conta diferente

### <a name="issue"></a>Problema

Recebe a seguinte mensagem de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

A máquina já foi a bordo de outro espaço de trabalho para gestão de atualizações.

### <a name="resolution"></a>Resolução

1. Siga os [passos em Machines não apareça no portal sob Gestão](#nologs) de Atualização para se certificar de que a máquina está reportando ao espaço de trabalho correto.
2. Limpe artefactos antigos na [máquina, apagando o grupo de livros híbridos](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)e tente novamente.

## <a name="machine-unable-to-communicate"></a>Cenário: Máquina não pode comunicar com o serviço

### <a name="issue"></a>Problema

Recebe uma das seguintes mensagens de erro:

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

Um proxy, gateway ou firewall pode estar bloqueando a comunicação da rede. 

### <a name="resolution"></a>Resolução

Reveja a sua rede e certifique-se de que as portas e endereços apropriados são permitidos. Consulte [os requisitos](../automation-hybrid-runbook-worker.md#network-planning) de rede para uma lista de portas e endereços que são exigidos pela Update Management e pelos Trabalhadores híbridos do livro de corridas.

## <a name="unable-to-create-selfsigned-cert"></a>Cenário: Incapaz de criar certificado auto-assinado

### <a name="issue"></a>Problema

Recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Causa

O Trabalhador híbrido do livro não podia gerar um certificado auto-assinado.

### <a name="resolution"></a>Resolução

Verifique se a conta do sistema leu o acesso à pasta **C:\ProgramData\Microsoft\Crypto\RSA** e tente novamente.

## <a name="mw-exceeded"></a>Cenário: A atualização programada falhou com um erro de ManutençãoWindowExceeded

### <a name="issue"></a>Problema

A janela de manutenção predefinida para atualizações é de 120 minutos. Pode aumentar a janela de manutenção para um máximo de 6 horas, ou 360 minutos.

### <a name="resolution"></a>Resolução

Editar quaisquer implementações de atualizações programadas falhadas e aumentar a janela de manutenção.

Para obter mais informações sobre as janelas de manutenção, consulte [Instalar atualizações](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="hresult"></a>Cenário: Máquina mostra como "Não avaliada" e mostra uma exceção ao HResult

### <a name="issue"></a>Problema

* Tem máquinas que mostram como **Não Avaliadas** em **Conformidade**, e vê uma mensagem de exceção abaixo.
* Tens máquinas que não são avaliadas.
* Vê um código de erro HRESULT no portal.

### <a name="cause"></a>Causa

O Agente de Atualização (Windows Update Agent on Windows; o gestor de pacotes para uma distribuição Linux) não está configurado corretamente. A Atualização Management conta com o Agente de Atualização da máquina para fornecer as atualizações que são necessárias, o estado do patch e os resultados dos patches implantados. Sem esta informação, a Atualização não pode reportar adequadamente sobre os patches que são necessários ou instalados.

### <a name="resolution"></a>Resolução

Tente efetuar atualizações localmente na máquina. Se isto falhar, normalmente significa que há um erro de configuração com o agente de atualização.

Este problema é frequentemente causado por problemas de configuração de rede e firewall. Experimente o seguinte:

* Para o Linux, consulte a documentação adequada para se certificar de que pode chegar ao ponto final da rede do seu repositório de pacotes.
* Para windows, verifique a configuração do seu agente tal como listado nas [Atualizações não está a ser descarregada a partir do ponto final da intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Se as máquinas estiverem configuradas para a Atualização do Windows, certifique-se de que pode chegar aos pontos [finais descritos em Problemas relacionados com HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Se as máquinas estiverem configuradas para os Serviços de Atualização do Servidor do Windows (WSUS), certifique-se de que pode chegar ao servidor WSUS configurado pela chave de [registo WUServer](/windows/deployment/update/waas-wu-settings).

Se vir um HRESULT, clique duas vezes na exceção exibida a vermelho para ver toda a mensagem de exceção. Reveja o seguinte quadro para soluções potenciais ou ações recomendadas:

|Exceção  |Resolução ou ação  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Procure o código de erro relevante na lista de códigos de erro da [atualização do Windows](https://support.microsoft.com/help/938205/windows-update-error-code-list) para encontrar detalhes adicionais sobre a causa da exceção.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Estes indicam problemas de conectividade da rede. Certifique-se de que a sua máquina tem conectividade de rede com a Atualização de Gestão. Consulte a secção de planeamento da [rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços necessários.        |
|`0x8024001E`| A operação de atualização não terminou porque o serviço ou o sistema estavam a desligar-se.|
|`0x8024002E`| O serviço De atualização do Windows está desativado.|
|`0x8024402C`     | Se estiver a utilizar um servidor WSUS, certifique-se de que os valores de registo para `WUServer` e `WUStatusServer` sob a chave de registo `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` especificar o servidor WSUS correto.        |
|`0x80072EE2`|Há um problema de conectividade de rede ou um problema em falar com um servidor WSUS configurado. Verifique as definições da WSUS e certifique-se de que o serviço está acessível a partir do cliente.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Certifique-se de que o serviço De atualização do Windows (wuauserv) está em funcionamento e não está desativado.        |
|`0x80070005`| Um erro negado de acesso pode ser causado por qualquer um dos seguintes erros:<br> Computador infetado<br> Definições de Atualização do Windows não configuradas corretamente<br> Erro de permissão de ficheiro com a pasta %WinDir%\SoftwareDistribution<br> Espaço de disco insuficiente na unidade do sistema (C:).
|Qualquer outra exceção genérica     | Procure na internet possíveis soluções e trabalhe com o suporte de TI local.         |

Rever o ficheiro %Windir%\Windowsupdate.log também pode ajudá-lo a determinar possíveis causas. Para mais informações sobre como ler o registo, consulte [Como ler o ficheiro Windowsupdate.log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Também pode descarregar e executar o resoluto de [problemas do Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) para verificar quaisquer problemas com o Windows Update na máquina.

> [!NOTE]
> A documentação de resolução de [problemas do Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indica que é para utilização em clientes windows, mas também funciona no Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Cenário: Atualização de retorna ao estado "Falhado" (Linux)

### <a name="issue"></a>Problema

Uma atualização começa, mas encontra erros durante a corrida.

### <a name="cause"></a>Causa

Causas possíveis:

* O gestor do pacote não é saudável.
* O Agente de Atualização (WUA para Windows, gestor de pacotes específico para o Linux) está mal configurado.
* Pacotes específicos estão interferindo com remendos baseados em nuvem.
* A máquina é inalcançável.
* As atualizações tinham dependências que não estavam resolvidas.

### <a name="resolution"></a>Resolução

Se ocorrerem falhas durante uma atualização após o início com sucesso, [verifique a saída](../manage-update-multi.md#view-results-of-an-update-deployment) de trabalho da máquina afetada durante a execução. Pode encontrar mensagens de erro específicas das suas máquinas que pode pesquisar e tomar medidas. A Update Management exige que o gestor do pacote seja saudável para implementações de atualização bem sucedidas.

Se patches, pacotes ou atualizações específicos forem vistos imediatamente antes de o trabalho falhar, pode tentar [excluir](../automation-tutorial-update-management.md#schedule-an-update-deployment) os da próxima implementação da atualização. Para recolher informações de registo do Windows Update, consulte [os ficheiros](/windows/deployment/update/windows-update-logs)de registo do Windows Update .

Se não conseguir resolver um problema de correção, faça uma cópia do seguinte ficheiro de registo e preserve-o para fins de resolução de problemas *antes* do início da próxima implementação da atualização:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Patches não são instalados

### <a name="machines-dont-install-updates"></a>Máquinas não instalam atualizações

* Experimente executar as atualizações diretamente na máquina. Se a máquina não puder aplicar as atualizações, consulte a [lista de potenciais erros no guia de resolução de problemas](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Se as atualizações forem executadas localmente, tente remover e reinstalar o agente na máquina seguindo a orientação em [Remover um VM da Atualização](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)de Gestão .

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Sei que as atualizações estão disponíveis, mas não aparecem como disponíveis nas minhas máquinas.

* Isto acontece frequentemente se as máquinas estiverem configuradas para obter atualizações do WSUS ou do Microsoft Endpoint Configuration Manager, mas o WSUS e o Gestor de Configuração não aprovaram as atualizações.
* Pode verificar se as máquinas estão configuradas para WSUS e SCCM através de uma referência cruzada à chave de [registo UseWUServer para as teclas de registo na secção "Configurar atualizações automáticas editando o Registo" deste artigo](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).
* Se as atualizações não forem aprovadas no WSUS, não serão instaladas. Pode verificar se há atualizações não aprovadas no Log Analytics executando a seguinte consulta:

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>As atualizações mostram como instaladas, mas não as encontro na minha máquina.

* Muitas vezes, as atualizações são substituídas por outras. Para mais informações, consulte a Atualização no guia de resolução de [problemas](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) do Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalar atualizações por classificação no Linux

* A implementação de atualizações no Linux por classificação ("atualizações críticas e de segurança") tem limitações importantes, sobretudo para o CentOS. Estas limitações estão documentadas na página de visão geral da Gestão de [Atualizações.](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 está sempre ausente

* KB2267602 é a [atualização de definições do Windows Defender](https://www.microsoft.com/wdsi/definitions). É atualizado diariamente.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

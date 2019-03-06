---
title: Resolver erros de gerenciamento de atualizações
description: Saiba como resolver problemas de gerenciamento de atualizações
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: a8be44201a2181ab252dfba501469719dd675ffa
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410167"
---
# <a name="troubleshooting-issues-with-update-management"></a>Resolução de problemas de gerenciamento de atualizações

Este artigo aborda soluções para resolver problemas que pode encontrar ao utilizar a gestão de atualizações.

Há uma resolução de problemas do agente para o agente de função de trabalho híbrida determinar o problema subjacente. Para saber mais sobre a resolução de problemas, consulte [problemas do agente de atualização de resolução de problemas](update-agent-issues.md). Para todos os outros problemas, consulte as informações detalhadas abaixo sobre problemas possíveis.

## <a name="general"></a>Geral

### <a name="components-enabled-not-working"></a>Cenário: Os componentes da solução de gestão de atualizações foram ativados e agora esta máquina virtual está a ser configurada

#### <a name="issue"></a>Problema

Continuar a ver a seguinte mensagem numa máquina virtual de 15 minutos após a integração:

```
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Causa

Este erro pode dever-se pelos seguintes motivos:

1. Comunicação de volta para a conta de automatização está a ser bloqueada.
2. A VM que se pretende incluir talvez proveniente de uma máquina clonada que não foi processado pelo Sysprep com o Microsoft Monitoring Agent instalado.

#### <a name="resolution"></a>Resolução

1. Visitar [planeamento de rede](../automation-hybrid-runbook-worker.md#network-planning) para saber quais são os endereços e portas têm de ser permitidos para gerenciamento de atualizações trabalhar.
2. Se utilizar uma imagem clonada:
   1. Na área de trabalho do Log Analytics, remover a VM da pesquisa guardada para a configuração de âmbito `MicrosoftDefaultScopeConfig-Updates` se é apresentado. Pesquisas guardadas podem ser encontradas na **gerais** na sua área de trabalho.
   2. Execute `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Execute `Restart-Service HealthService` para reiniciar o `HealthService`. Esta ação irá recriar a chave e gerar um UUID novo.
   4. Caso não funcione, a imagem do sysprep primeiro e instalar o agente MMA após o fato.

### <a name="multi-tenant"></a>Cenário: Recebe um erro de subscrição ligada ao criar uma implementação de atualizações para máquinas no outro inquilino do Azure.

#### <a name="issue"></a>Problema

Receber o seguinte erro ao tentar criar uma implementação de atualizações para máquinas no outro inquilino do Azure:

```
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Causa

Este erro ocorre quando cria uma implementação de atualização que tenha máquinas virtuais do Azure noutro inquilino incluídos numa implementação de atualização.

#### <a name="resolution"></a>Resolução

Terá de utilizar a solução abaixo para que eles agendada. Pode utilizar o [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule?view=azurermps-6.13.0) cmdlet com o comutador `-ForUpdate` para criar uma agenda e utilizar os [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration?view=azurermps-6.13.0
) cmdlet e passar o as máquinas no outro inquilino para o `-NonAzureComputer` parâmetro. O exemplo seguinte mostra um exemplo sobre como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Cenário: Atualizar os dados de gestão não está a mostrar registos do Azure Monitor para uma máquina

#### <a name="issue"></a>Problema

Tiver máquinas que mostram como **não avaliados** sob **conformidade**, mas ver dados de heartbeat nos registos do Azure Monitor para o trabalho de Runbook híbrida, mas não a gestão de atualizações.

#### <a name="cause"></a>Causa

A função de trabalho de Runbook híbrida poderá ter de ser registado novamente e reinstalado.

#### <a name="resolution"></a>Resolução

Siga os passos indicados em [implementar um Runbook Worker híbrido Windows](../automation-windows-hrw-install.md) reinstalar a função de trabalho híbrida para Windows ou [implementar um Runbook Worker híbrido Linux](../automation-linux-hrw-install.md) para Linux.

## <a name="windows"></a>Windows

Se tiver problemas ao tentar carregar a solução numa máquina virtual, verifique os **Operations Manager** registo de eventos na **registos de serviços de aplicações e** no computador local para eventos com ID de evento **4502** e mensagens de evento com **hybridagent**.

A secção seguinte realça as mensagens de erro específicas e uma resolução possível para cada um. Para integração de outra problemas, consulte [resolver problemas de inclusão da solução](onboarding.md).

### <a name="machine-already-registered"></a>Cenário: Máquina já está registada para outra conta

#### <a name="issue"></a>Problema

Receberá a seguinte mensagem de erro:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Causa

A máquina já está incluído para outra área de trabalho para gestão de atualizações.

#### <a name="resolution"></a>Resolução

Executar a limpeza dos artefactos antigos na máquina por [a eliminação do grupo de runbook híbrida](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) e tente novamente.

### <a name="machine-unable-to-communicate"></a>Cenário: Máquina não consegue comunicar com o serviço

#### <a name="issue"></a>Problema

Receber uma das seguintes mensagens de erro:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Causa

Pode haver um proxy, um gateway ou uma firewall a bloquear a comunicação de rede.

#### <a name="resolution"></a>Resolução

Reveja o seu trabalho em rede e certifique-se de que são permitidos endereços e portas apropriadas. Ver [requisitos de rede](../automation-hybrid-runbook-worker.md#network-planning), para obter uma lista de portas e endereços que são necessários para a gestão de atualizações e os Runbook Workers híbridos.

### <a name="unable-to-create-selfsigned-cert"></a>Cenário: Não é possível criar o certificado autoassinado

#### <a name="issue"></a>Problema

Receber uma das seguintes mensagens de erro:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Causa

A função de trabalho de Runbook híbrida não foi capaz de gerar um certificado autoassinado

#### <a name="resolution"></a>Resolução

Verifique se conta de sistema tem acesso de leitura à pasta **C:\ProgramData\Microsoft\Crypto\RSA** e tente novamente.

### <a name="hresult"></a>Cenário: Máquina mostra como não avaliados e mostra uma exceção de HResult

#### <a name="issue"></a>Problema

Tiver máquinas que mostram como **não avaliados** sob **conformidade**, e verá uma mensagem de exceção abaixo dele.

#### <a name="cause"></a>Causa

Windows Update ou dos WSUS não está configurado corretamente na máquina. Baseia-se da gestão de atualizações do Windows Update ou WSUS para fornecer as atualizações que são necessários, o status de patch e os resultados de patches implementados. Sem essas informações a gestão de atualizações não pode corretamente relatório sobre as correções que são necessárias ou instaladas.

#### <a name="resolution"></a>Resolução

Faça duplo clique na exceção apresentada a vermelho para ver a mensagem de exceção de todo. Reveja a tabela seguinte para potenciais soluções ou ações a efetuar:

|Exceção  |Resolução ou ação  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Pesquise o código de erro relevantes na [lista de código de erro de atualização do Windows](https://support.microsoft.com/help/938205/windows-update-error-code-list) para obter detalhes adicionais sobre a causa da exceção.        |
|`0x8024402C` ou `0x8024401C`     | Estes erros são problemas de conectividade de rede. Certifique-se de que o seu computador tem a conectividade de rede adequada para a gestão de atualizações. Consulte a secção sobre [planeamento de rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços que são necessários.        |
|`0x8024402C`     | Se estiver a utilizar um servidor WSUS, certifique-se os valores de registo `WUServer` e `WUStatusServer` sob a chave de registo `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` o servidor WSUS correto.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Certifique-se de que o serviço de atualização do Windows (wuauserv) está em execução e não está desabilitado.        |
|Qualquer outra exceção genérica     | Fazer uma pesquisa na internet para encontrar possíveis soluções e trabalhar com seu suporte de TI local.         |

Além disso, pode transferir e executar o [resolução de problemas de atualização do Windows](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) para verificar se existem quaisquer problemas com o Windows Update no computador.

> [!NOTE]
> O [resolução de problemas de atualização do Windows](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) Estados destina-se os clientes do Windows, mas ele funciona bem no Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Cenário: Falha ao iniciar o execução da atualização

#### <a name="issue"></a>Problema

Uma falha de execuções de atualização para iniciar uma máquina Linux.

#### <a name="cause"></a>Causa

Função de trabalho do Linux híbrida está danificada.

#### <a name="resolution"></a>Resolução

Faça uma cópia do ficheiro de registo seguinte e preservá-la para fins de resolução de problemas:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Cenário: Execução da atualização é iniciada, mas se encontra erros

#### <a name="issue"></a>Problema

Uma execução de atualização é iniciada, mas encontra erros durante a execução.

#### <a name="cause"></a>Causa

Foi possível obter as causas possíveis:

* Gestor de pacotes está danificado
* Pacotes específicos podem interferir com a aplicação de patches com base na cloud
* Outras razões

#### <a name="resolution"></a>Resolução

Se ocorrerem falhas durante uma atualização executada depois de iniciar com êxito no Linux, verifique a saída do computador afetado na execução da tarefa. Pode encontrar mensagens de erro específicas do Gestor de pacotes da sua máquina que pode pesquisar e tomar medidas em. Gestão de atualizações requer o Gestor de pacotes ser bom estado de funcionamento para implementações de atualização bem sucedida.

Em alguns casos, as atualizações de pacotes podem interferir com a gestão de atualizações que impede a conclusão de uma implementação de atualização. Se vir, terá de excluir esses pacotes de execuções de atualização futura ou instalá-los manualmente por conta própria.

Se não conseguir resolver um problema de aplicação de patches, fazer uma cópia do ficheiro de registo seguinte e preservar a ele **antes de** a seguinte implementação da atualização é iniciada para fins de resolução de problemas:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Passos Seguintes

Se não vir o seu problema ou não é possível resolver o problema, visite um dos seguintes canais de suporte mais:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
---
title: Configuração do Estado da Automação Azure
description: Este artigo fornece informações sobre a resolução de problemas da Configuração do Estado da Automação Azure.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d0801bb44fc0c08df1adee1f817e8fccab166fb5
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652813"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration"></a>Problemas de resolução de problemas com configuração do Estado da Automação Azure

Este artigo fornece informações sobre problemas de resolução de problemas que surgem enquanto compila ou implementa configurações na Configuração do Estado da Automação Azure.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente seguindo os passos em Como atualizar os [módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="diagnose-an-issue"></a>Diagnosticar um problema

Quando recebe um erro de compilação ou implementação para configuração, aqui estão alguns passos para ajudá-lo a diagnosticar o problema.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Certifique-se de que a sua configuração compila com sucesso na máquina local

A configuração do Estado da Automação Azure é construída com a configuração do Estado desejado pela PowerShell (DSC). Pode encontrar a documentação para a língua DSC e sintaxe nos [Docs Docs Docs PowerShell DSC](https://docs.microsoft.com/powershell/scripting/overview).

Ao compilar uma configuração DSC na sua máquina local, pode descobrir e resolver erros comuns, tais como:

   - Módulos desaparecidos.
   - Erros de sintaxe.
   - Erros lógicos.

### <a name="2-view-dsc-logs-on-your-node"></a>2. Ver registos dSC no seu nó

Se a sua configuração se compilar com sucesso, mas falhar quando aplicada a um nó, pode encontrar informações detalhadas nos registos do DSC. Para obter informações sobre onde encontrar estes registos, consulte [onde estão os Registos de Eventos DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

O módulo [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) pode ajudá-lo a analisar informações detalhadas dos registos do DSC. Se contactar o suporte, eles requerem estes registos para diagnosticar o seu problema.

Pode instalar `xDscDiagnostics` o módulo na sua máquina local seguindo as instruções no módulo de [versão estável](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Para instalar `xDscDiagnostics` o módulo na sua máquina Azure, utilize o [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). Também pode utilizar a opção **de comando Run** no portal Azure seguindo os passos nas [scripts Run PowerShell no seu Windows VM com Comando de Execução](../../virtual-machines/windows/run-command.md).

Para obter informações sobre a utilização de **xDscDiagnostics,** consulte [Utilizar xDscDiagnostics para analisar os registos de DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Consulte também [xDscDiagnostics Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Certifique-se de que os nódosos e o espaço de trabalho da Automação têm módulos necessários

O DSC depende dos módulos instalados no nó. Quando utilizar a Configuração do Estado da Automatização Azure, importe os módulos necessários para a sua conta Deautomação seguindo os passos em [Módulos de Importação](../shared-resources/modules.md#import-modules). As configurações também podem ter uma dependência de versões específicas de módulos. Para mais informações, consulte [os módulos Troubleshoot](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Cenário: Uma configuração com caracteres especiais não pode ser eliminada do portal

### <a name="issue"></a>Problema

Quando tenta eliminar uma configuração DSC do portal, vê o seguinte erro:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Causa

Este erro é uma questão temporária que está planeada para ser resolvida.

### <a name="resolution"></a>Resolução

Utilize a configuração [Remover-AzAutomationDscConfiguração]parahttps://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 eliminar a configuração.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Cenário: Não registou o Agente DSC

### <a name="issue"></a>Problema

Quando [o Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) ou outro cmdlet DSC, recebe o erro:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>Causa

Este erro é normalmente causado por uma firewall, a máquina estar por trás de um servidor proxy, ou outros erros de rede.

### <a name="resolution"></a>Resolução

Verifique se a sua máquina tem acesso aos pontos finais adequados para o DSC e tente novamente. Para obter uma lista de portas e endereços necessários, consulte o planeamento da [rede.](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Cenário: Relatórios de estado devolvem o código de resposta não autorizado

### <a name="issue"></a>Problema

Ao registar um nó com configuração do Estado da Automação Azure, recebe uma das seguintes mensagens de erro:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Causa

Esta questão é causada por um certificado mau ou caducado. Ver [Expiração do certificado e reinscrição](../automation-dsc-onboarding.md#re-registering-a-node).

Este problema também pode ser causado por uma configuração de procuração que não permite o acesso a ***.azure-automation.net**. Para mais informações, consulte [Configuração de redes privadas.](../automation-dsc-overview.md#network-planning) 

### <a name="resolution"></a>Resolução

Utilize os seguintes passos para voltar a registar o nó DSC em falha.

#### <a name="step-1-unregister-the-node"></a>Passo 1: Desregistar o nó

1. No portal Azure, vá às Contas de**Automação** **Doméstica** > > (a sua conta de Automação) > **configuração do Estado (DSC)**.
1. Selecione Nós e selecione o nó com **problemas.**
1. Selecione **Não registar** para não registar o nó.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Passo 2: Desinstalar a extensão DSC do nó

1. No portal Azure, vá ao **Home** > **Virtual Machine** > (nó falhado) > **Extensões.**
1. Selecione **Microsoft.Powershell.DSC,** a extensão PowerShell DSC.
1. Selecione **Desinstalar** para desinstalar a extensão.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Passo 3: Retire todos os certificados maus ou expirados do nó

No nó falhado de um pedido elevado da PowerShell, execute estes comandos:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>Passo 4: Reregistar o nó em falência

1. No portal Azure, vá às Contas de**Automação** **Doméstica** > > (a sua conta de Automação) > **configuração do Estado (DSC)**.
1. Selecione **Nóesos**.
1. Selecione **Adicionar**.
1. Selecione o nó de falha.
1. Selecione **Connect**e selecione as opções desejadas.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Cenário: Nó está em estado falhado com um erro "Não encontrado"

### <a name="issue"></a>Problema

O nó tem um relatório com estado falhado e contém o erro:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Causa

Este erro ocorre tipicamente quando o nó é atribuído a um nome de configuração, por exemplo, **ABC**, em vez de um nome de configuração de nó (ficheiro MOF), por exemplo, **ABC. WebServer**.

### <a name="resolution"></a>Resolução

* Certifique-se de que está a atribuir o nó com o nome de configuração do nó e não com o nome de configuração.
* Pode atribuir uma configuração do nó a um nó utilizando o portal Azure ou com um cmdlet PowerShell.

  * No portal Azure, vá às Contas de**Automação** **Doméstica** > > (a sua conta de Automação) > **configuração do Estado (DSC)**. Em seguida, selecione um nó e selecione **configuração**do nó de atribuir .
  * Utilize o [set-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) cmdlet.

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Cenário: Não foram produzidas configurações de nó (ficheiros MOF) quando foi compilada uma configuração

### <a name="issue"></a>Problema

O seu trabalho de compilação DSC suspende com o erro:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Causa

Quando a expressão `Node` que segue a palavra-chave `$null`na configuração DSC avalia, não são produzidas configurações de nó.

### <a name="resolution"></a>Resolução

Utilize uma das seguintes soluções para corrigir o problema:

* Certifique-se de que `Node` a expressão ao lado da palavra-chave na definição de configuração não está a avaliar a Nula.
* Se estiver a passar o [ConfigurationData](../automation-dsc-compile.md) quando compilar a configuração, certifique-se de que está a passar os valores que a configuração espera dos dados de configuração.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Cenário: O relatório do nó do CDS fica preso no estado de Progresso

### <a name="issue"></a>Problema

As saídas do agente DSC:

```error
No instance found with given property values
```

### <a name="cause"></a>Causa

Atualizou a sua versão Do Windows Management Framework (WMF) e corrompeu a Instrumentação de Gestão do Windows (OMI).

### <a name="resolution"></a>Resolução

Siga as instruções em [DSC questões e limitações conhecidas](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Cenário: Incapaz de utilizar uma credencial numa configuração DSC

### <a name="issue"></a>Problema

O seu trabalho de compilação DSC suspenso com o erro:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Causa

Usaste uma credencial numa configuração, mas `ConfigurationData` não `PSDscAllowPlainTextPassword` forneceste adequadamente para ser verdade para cada configuração do nó.

### <a name="resolution"></a>Resolução

Certifique-se de passar `ConfigurationData` no `PSDscAllowPlainTextPassword` adequado para ser fiel a cada configuração do nó que é mencionada na configuração. Consulte a compilação de [configurações dSC na configuração do Estado da Automação Azure](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Cenário: Erro de "extensão de processamento de falhas" ao embarcar a partir de uma extensão DSC

### <a name="issue"></a>Problema

Ao embarcar utilizando uma extensão DSC, ocorre uma falha que contém o erro:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Causa

Este erro ocorre tipicamente quando o nó é atribuído um nome de configuração de nó que não existe no serviço.

### <a name="resolution"></a>Resolução

* Certifique-se de que está a atribuir o nó com um nome que corresponda exatamente ao nome do serviço.
* Pode optar por não incluir o nome de configuração do nó, o que resulta em embarcar no nó, mas não atribuir uma configuração de nó.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Cenário: Erro de "Um ou mais erros ocorreram" ao registar um nó utilizando o PowerShell

### <a name="issue"></a>Problema

Ao registar um nó utilizando o [Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) ou [o Register-AzureRMAutomationDSCNode,](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)recebe o seguinte erro:

```error
One or more errors occurred.
```

### <a name="cause"></a>Causa

Este erro ocorre quando se tenta registar um nó numa subscrição separada da utilizada pela conta Automation.

### <a name="resolution"></a>Resolução

Trate o nó de subscrição cruzada como se estivesse definido para uma nuvem separada, ou no local. Registe o nó utilizando uma destas opções de embarque:

* Janelas: [Máquinas de Janelas físicas/virtuais no local ou numa nuvem diferente do Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux: [Máquinas linux físicas/virtuais no local, ou numa nuvem diferente do Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Cenário: "Provisão falhou" mensagem de erro

### <a name="issue"></a>Problema

Ao registar um nó, vê o erro:

```error
Provisioning has failed
```

### <a name="cause"></a>Causa

Esta mensagem ocorre quando há um problema com a conectividade entre o nó e Azure.

### <a name="resolution"></a>Resolução

Determine se o seu nó está numa rede privada virtual (VPN) ou tem outros problemas ligados ao Azure. Consulte [erros de resolução de problemas ao embarcar em soluções](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Cenário: Falha com erro geral ao aplicar uma configuração no Linux

### <a name="issue"></a>Problema

Quando se aplica uma configuração no Linux, ocorre uma falha que contém o erro:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Causa

Se a localização **/tmp** estiver definida para `noexec`, a versão atual do DSC não aplica configurações.

### <a name="resolution"></a>Resolução

Retire `noexec` a opção da localização **/tmp.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Cenário: Nomes de configuração do nó que se sobrepõem podem resultar num mau lançamento

### <a name="issue"></a>Problema

Quando utiliza um único script de configuração para gerar múltiplas configurações de nó e alguns nomes de configuração de nó são subconjuntos de outros nomes, o serviço de compilação pode acabar por atribuir a configuração errada. Este problema só ocorre quando se utiliza um único script para gerar configurações com dados de configuração por nó, e apenas quando o nome se sobreposição ocorre no início da cadeia. Um exemplo é um único script de configuração usado para gerar configurações com base em dados do nó passados como um hashtable usando cmdlets, e os dados do nó incluem servidor nomeado **servidor** e **1servidor**.

### <a name="cause"></a>Causa

Esta é uma questão conhecida com o serviço de compilação.

### <a name="resolution"></a>Resolução

A melhor sucinta é compilar localmente ou num oleoduto CI/CD e carregar os ficheiros MOF de configuração do nó diretamente para o serviço. Se a compilação no serviço é um requisito, a próxima melhor suposição é dividir os trabalhos de compilação para que não haja sobreposição de nomes.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Cenário: Erro de tempo de saída de gateway no upload de configuração DSC

#### <a name="issue"></a>Problema

Recebe um `GatewayTimeout` erro ao fazer o upload de uma configuração DSC. 

### <a name="cause"></a>Causa

As configurações dSC que demoram muito tempo a compilar podem causar este erro.

### <a name="resolution"></a>Resolução

Pode fazer com que as suas configurações dSC analisem mais rapidamente, incluindo explicitamente o `ModuleName` parâmetro para quaisquer chamadas [Import-DSCResource.](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport)a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga a comunidade Azure a respostas, apoios e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/), e selecione **Obter Suporte**.

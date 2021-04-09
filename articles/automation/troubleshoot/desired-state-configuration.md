---
title: Problemas de resolução de problemas Problemas de Configuração do Estado da Automação Azure
description: Este artigo diz como resolver problemas e resolver problemas de Configuração do Estado da Automação Azure.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.openlocfilehash: e6caf3fed708e89b55a88719ca5358f6174c2ac8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896533"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Problemas de resolução de problemas Problemas de Configuração do Estado da Automação Azure

Este artigo fornece informações sobre resolução de problemas e resolução de problemas que surgem enquanto compila ou implementa configurações na Configuração do Estado da Automação Azure. Para obter informações gerais sobre a funcionalidade de Configuração do Estado, consulte [a visão geral da configuração do Estado da Automação Azure](../automation-dsc-overview.md).

## <a name="diagnose-an-issue"></a>Diagnosticar um problema

Quando recebe um erro de compilação ou implementação para configuração, aqui estão alguns passos para ajudá-lo a diagnosticar o problema.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Certifique-se de que a sua configuração compila com sucesso na máquina local

A configuração do Estado da Automação Azure é construída na Configuração de Estado Desejada por PowerShell (DSC). Pode encontrar a documentação para a língua DSC e sintaxe nos [Docs DSC powerShell](/powershell/scripting/overview).

Ao compilar uma configuração DSC na sua máquina local, pode descobrir e resolver erros comuns, tais como:

   - Módulos desaparecidos.
   - Erros de sintaxe.
   - Erros lógicos.

### <a name="2-view-dsc-logs-on-your-node"></a>2. Ver registos DSC no seu nó

Se a sua configuração compilar com sucesso, mas falhar quando aplicada a um nó, pode encontrar informações detalhadas nos registos DSC. Para obter informações sobre onde encontrar estes registos, consulte [onde estão os Registos de Eventos DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

O módulo [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) pode ajudá-lo a analisar informações detalhadas a partir dos registos DSC. Se contactar o suporte, estes registos requerem que estes registos diagnosticem o seu problema.

Pode instalar o `xDscDiagnostics` módulo na sua máquina local seguindo as instruções na [Instalação do módulo de versão estável](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Para instalar o `xDscDiagnostics` módulo na sua máquina Azure, utilize [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand). Também pode utilizar a opção **de comando Executar** no portal Azure seguindo os passos nos [scripts Run PowerShell no seu Windows VM com Comando de Execução](../../virtual-machines/windows/run-command.md).

Para obter informações sobre a utilização **de xDscDiagnostics**, consulte [utilizar xDscDiagnostics para analisar registos DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Consulte também [os cmdlets xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Certifique-se de que os nós e o espaço de trabalho da Automatização têm módulos necessários

O DSC depende dos módulos instalados no nó. Quando utilizar a Configuração do Estado da Automação Azure, importe os módulos necessários para a sua conta de Automação seguindo os passos nos [Módulos de Importação](../shared-resources/modules.md#import-modules). As configurações também podem ter uma dependência de versões específicas de módulos. Para obter mais informações, consulte [os módulos de resolução de problemas.](shared-resources.md#modules)

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Cenário: Uma configuração com caracteres especiais não pode ser eliminada do portal

### <a name="issue"></a>Problema

Quando tenta eliminar uma configuração DSC do portal, vê o seguinte erro:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Causa

Este erro é um problema temporário que está planeado para ser resolvido.

### <a name="resolution"></a>Resolução

Utilize o [cmdlet Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) para eliminar a configuração.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Cenário: Não registou o Agente do DSC

### <a name="issue"></a>Problema

Quando [o Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) ou outro cmdlet DSC, recebe o erro:

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

Este erro é normalmente causado por uma firewall, estando a máquina por trás de um servidor proxy ou outros erros de rede.

### <a name="resolution"></a>Resolução

Verifique se a sua máquina tem acesso aos pontos finais adequados para dSC e tente novamente. Para obter uma lista de portas e endereços necessários, consulte [o planeamento da rede](../automation-dsc-overview.md#network-planning).

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Cenário: Relatórios de estado devolvem o código de resposta não autorizado

### <a name="issue"></a>Problema

Quando regista um nó com a Configuração do Estado da Automação Azure, recebe uma das seguintes mensagens de erro:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Causa

Esta questão é causada por um certificado mau ou caducado. Consulte [re-registar um nó](../automation-dsc-onboarding.md#re-register-a-node).

Este problema também pode ser causado por uma configuração de procuração que não permite o acesso a ***.azure-automation.net**. Para obter mais informações, consulte [configuração de redes privadas.](../automation-dsc-overview.md#network-planning) 

### <a name="resolution"></a>Resolução

Utilize os seguintes passos para voltar a registar o nó DSC em falha.

#### <a name="step-1-unregister-the-node"></a>Passo 1: Não registar o nó

1. No portal Azure, aceda às Contas de  >  **Automação** Domiciliária > (a sua conta de Automação) > **configuração estatal (DSC)**.
1. Selecione **Nós** e selecione o nó com problemas.
1. **Selecione Não registar** para não registar o nó.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Passo 2: Desinstalar a extensão DSC a partir do nó

1. No portal Azure, vá para **a Máquina** Virtual Doméstica >  >   (nó falhado) > **Extensões**.
1. Selecione **Microsoft.Powershell.DSC,** a extensão DSC powerShell.
1. Selecione **Desinstalar** para desinstalar a extensão.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Passo 3: Remover todos os certificados maus ou caducados do nó

No nó falhado de um pedido elevado de PowerShell, executar estes comandos:

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

#### <a name="step-4-reregister-the-failing-node"></a>Passo 4: Reregisterar o nó falhado

1. No portal Azure, aceda às Contas de  >  **Automação** Domiciliária > (a sua conta de Automação) > **configuração estatal (DSC)**.
1. Selecione **Nós**.
1. Selecione **Adicionar**.
1. Selecione o nó de falha.
1. Selecione **Connect** e selecione as opções desejadas.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Cenário: Nó está em estado de falha com um erro "Não encontrado"

### <a name="issue"></a>Problema

O nó tem um relatório com estado falhado e contém o erro:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Causa

Este erro ocorre normalmente quando o nó é atribuído a um nome de configuração, por exemplo, **ABC**, em vez de um nome de configuração de nó (ficheiro MOF), por exemplo, **ABC. WebServer**.

### <a name="resolution"></a>Resolução

* Certifique-se de que está a atribuir o nó com o nome de configuração do nó e não o nome de configuração.
* Pode atribuir uma configuração de nó a um nó utilizando o portal Azure ou com um cmdlet PowerShell.

  * No portal Azure, aceda às Contas de  >  **Automação** Domiciliária > (a sua conta de Automação) > **configuração estatal (DSC)**. Em seguida, selecione um nó e selecione **Configuração do nó de atribuição**.
  * Utilize o [set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) cmdlet.

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Cenário: As configurações do nó (ficheiros MOF) foram produzidas quando uma configuração foi compilada

### <a name="issue"></a>Problema

O seu trabalho de compilação DSC suspende com o erro:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Causa

Quando a expressão que segue a `Node` palavra-chave na configuração DSC avalia, `$null` não são produzidas configurações de nó.

### <a name="resolution"></a>Resolução

Utilize uma das seguintes soluções para corrigir o problema:

* Certifique-se de que a expressão ao lado da `Node` palavra-chave na definição de configuração não está a avaliar a Nulo.
* Se estiver a passar [o ConfigurationData](../automation-dsc-compile.md) quando compilar a configuração, certifique-se de que está a passar os valores que a configuração espera dos dados de configuração.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Cenário: Relatório do nó do DSC fica preso no estado em curso

### <a name="issue"></a>Problema

As saídas do agente DSC:

```error
No instance found with given property values
```

### <a name="cause"></a>Causa

Atualizou a sua versão Do Windows Management Framework (WMF) e corrompeu a instrumentação de gestão do Windows (WMI).

### <a name="resolution"></a>Resolução

Siga as instruções em [DSC questões e limitações conhecidas](/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Cenário: Incapaz de usar uma credencial numa configuração DSC

### <a name="issue"></a>Problema

O seu trabalho de compilação DSC suspenso com o erro:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Causa

Usou uma credencial numa configuração, mas não forneceu o adequado `ConfigurationData` para definir para ser verdadeiro para cada `PSDscAllowPlainTextPassword` configuração de nó.

### <a name="resolution"></a>Resolução

Certifique-se de que passa no adequado `ConfigurationData` para ser definido para verdadeiro para cada `PSDscAllowPlainTextPassword` configuração de nó que é mencionado na configuração. Consulte [as configurações de DSC de compilação na Configuração do Estado da Automação Azure](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Cenário: erro de "extensão de processamento de falha" ao permitir uma máquina a partir de uma extensão DSC

### <a name="issue"></a>Problema

Quando ativa uma máquina utilizando uma extensão DSC, ocorre uma falha que contém o erro:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Causa

Este erro ocorre normalmente quando o nó é atribuído um nome de configuração de nó que não existe no serviço.

### <a name="resolution"></a>Resolução

* Certifique-se de que está a atribuir o nó com um nome que corresponde exatamente ao nome do serviço.
* Pode optar por não incluir o nome de configuração do nó, o que resulta em ativar o nó, mas não atribuir uma configuração de nó.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Cenário: erro "Ocorreu um ou mais erros" ao registar um nó utilizando o PowerShell

### <a name="issue"></a>Problema

Quando regista um nó utilizando [o Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) ou [o Register-AzureRMAutomationDSCNode,](/powershell/module/azurerm.automation/register-azurermautomationdscnode)recebe o seguinte erro:

```error
One or more errors occurred.
```

### <a name="cause"></a>Causa

Este erro ocorre quando se tenta registar um nó numa subscrição separada da utilizada pela conta Automation.

### <a name="resolution"></a>Resolução

Trate o nó de subscrição cruzada como se estivesse definido para uma nuvem separada, ou no local. Registe o nó utilizando uma destas opções para ativar máquinas:

* Windows: [Máquinas físicas/virtuais do Windows no local, ou numa nuvem que não seja Azure/AWS](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines).
* Linux: [Máquinas Linux físicas/virtuais no local, ou numa nuvem diferente de Azure](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Cenário: Mensagem de erro "Provisioning has failed"

### <a name="issue"></a>Problema

Quando regista um nó, vê o erro:

```error
Provisioning has failed
```

### <a name="cause"></a>Causa

Esta mensagem ocorre quando há um problema com a conectividade entre o nó e o Azure.

### <a name="resolution"></a>Resolução

Determine se o seu nó está numa rede privada virtual (VPN) ou se tem outros problemas ligados ao Azure. Consulte [problemas de implementação de funcionalidades de resolução de problemas](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Cenário: Falha com um erro geral ao aplicar uma configuração no Linux

### <a name="issue"></a>Problema

Quando se aplica uma configuração no Linux, ocorre uma falha que contém o erro:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Causa

Se a localização **/tmp** estiver definida para `noexec` , a versão atual do DSC não aplica configurações.

### <a name="resolution"></a>Resolução

Retire a `noexec` opção da localização **/tmp.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Cenário: Nomes de configuração de nó que se sobrepõem podem resultar numa má libertação

### <a name="issue"></a>Problema

Quando utiliza um único script de configuração para gerar múltiplas configurações de nó e alguns nomes de configuração de nó são subconjuntos de outros nomes, o serviço de compilação pode acabar por atribuir a configuração errada. Este problema só ocorre quando se utiliza um único script para gerar configurações com dados de configuração por nó, e apenas quando o nome se sobrepõe no início da cadeia. Um exemplo é um único script de configuração usado para gerar configurações com base em dados de nó passados como um hashtable usando cmdlets, e os dados do nó incluem **servidores** nomeados servidor e **1server**.

### <a name="cause"></a>Causa

Este é um problema conhecido com o serviço de compilação.

### <a name="resolution"></a>Resolução

A melhor solução é compilar localmente ou num oleoduto CI/CD e carregar os ficheiros MOF de configuração de nó diretamente para o serviço. Se a compilação no serviço é um requisito, a próxima melhor solução é dividir os trabalhos de compilação para que não haja sobreposição de nomes.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Cenário: Erro de tempo limite de gateway no upload de configuração do DSC

#### <a name="issue"></a>Problema

Recebe um `GatewayTimeout` erro quando faz o upload de uma configuração DSC. 

### <a name="cause"></a>Causa

As configurações de DSC que demoram muito tempo a compilar podem causar este erro.

### <a name="resolution"></a>Resolução

Pode fazer com que as suas configurações DSC analisem mais rapidamente, incluindo explicitamente o `ModuleName` parâmetro para quaisquer chamadas [Import-DSCResource.](/powershell/scripting/dsc/configurations/import-dscresource)

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) , a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga a comunidade Azure a respostas, suporte e especialistas.
* Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Apoio**.

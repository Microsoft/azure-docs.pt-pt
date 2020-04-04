---
title: Configuração de Estado desejada pela automatização de suposição de problemas (DSC)
description: Este artigo fornece informações sobre a resolução de problemas de configuração do Estado Desejado (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9f33dc9528d5f7043dda2c6fad207a9a51347a2b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631482"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Problemas de resolução de problemas com a Configuração do Estado Desejada pela Automação Azure (DSC)

Este artigo fornece informações sobre problemas de resolução de problemas com a Configuração do Estado Desejado (DSC).

## <a name="diagnosing-an-issue"></a>Diagnosticar um problema

Quando tiver erros ao compilar ou implementar configurações na Configuração do Estado do Azure, aqui estão alguns passos para ajudá-lo a diagnosticar o problema.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Certifique-se de que a sua configuração compila com sucesso na máquina local

A Configuração do Estado Azure é construída em PowerShell DSC. Pode encontrar a documentação para a língua DSC e sintaxe nos [Docs Docs Docs PowerShell DSC](https://docs.microsoft.com/powershell/scripting/overview).

Compilando a sua configuração DSC na sua máquina local, pode descobrir e resolver erros comuns, tais como:

   - Módulos em falta
   - Erros de sintaxe
   - Erros lógicos

### <a name="2-view-dsc-logs-on-your-node"></a>2. Ver registos dSC no seu nó

Se a sua configuração se compilar com sucesso, mas falhar quando aplicada a um nó, pode encontrar informações detalhadas nos registos do DSC. Para obter informações sobre onde encontrar estes registos, consulte [onde estão os Registos de Eventos DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

O módulo [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) pode ajudá-lo a analisar informações detalhadas dos registos do DSC. Se contactar o suporte, eles requerem estes registos para diagnosticar o seu problema.

Pode instalar o módulo xDscDiagnostics na sua máquina local utilizando as instruções encontradas no [módulo de versão estável](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Para instalar o módulo xDscDiagnostics na sua máquina Azure, utilize o [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Também pode utilizar a opção **de comando Executar** a partir do portal, seguindo os [passos encontrados nos scripts Run PowerShell no seu Windows VM com Comando de Execução](../../virtual-machines/windows/run-command.md).

Para obter informações sobre a utilização de xDscDiagnostics, consulte [Utilizar xDscDiagnostics para analisar os registos de DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Consulte também [xDscDiagnostics Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Certifique-se de que os nódosos e o espaço de trabalho da Automação têm módulos necessários

O DSC depende dos módulos instalados no nó. Ao utilizar a Configuração do Estado da Automação Azure, importe os módulos necessários para a sua conta Deautomação utilizando os passos listados nos [Módulos de Importação](../shared-resources/modules.md#importing-modules). As configurações também podem ter uma dependência de versões específicas de módulos. Para mais informações, consulte [Os Módulos de Resolução de Problemas](shared-resources.md#modules).

## <a name="common-errors-when-working-with-dsc"></a>Erros comuns ao trabalhar com a DSC

### <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Cenário: Uma configuração com caracteres especiais não pode ser eliminada do portal

#### <a name="issue"></a>Problema

Ao tentar eliminar uma configuração DSC do portal, vê o seguinte erro:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Causa

Este erro é uma questão temporária que está prevista para ser resolvida.

#### <a name="resolution"></a>Resolução

* Utilize o Az Cmdlet "Remove-AzAutomationDscConfiguration" para eliminar a configuração.
* A documentação para este cmdlet ainda não foi atualizada.  Até lá, consulte a documentação para o módulo AzureRM.
  * [Remover-AzureRmAutomationDSCConfiguração](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Cenário: Não registou o Agente DSC

#### <a name="issue"></a>Problema

Ao tentar correr `Set-DscLocalConfigurationManager` ou outro cmdlet DSC recebe o erro:

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

#### <a name="cause"></a>Causa

Este erro é normalmente causado por uma firewall, a máquina estar por trás de um servidor proxy, ou outros erros de rede.

#### <a name="resolution"></a>Resolução

Verifique se a sua máquina tem acesso aos pontos finais adequados para o Azure Automation DSC e tente novamente. Para obter uma lista de portas e endereços necessários, consulte [o planeamento](../automation-dsc-overview.md#network-planning) da rede

### <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Cenário: Relatórios de estado devolvem código de resposta "Não autorizado"

#### <a name="issue"></a>Problema

Ao registar um Nó com Configuração do Estado (DSC) recebe uma das seguintes mensagens de erro:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Causa

Esta questão é causada por um certificado mau ou caducado.  Para mais informações, consulte a [caducidade do certificado e o reregisto](../automation-dsc-onboarding.md#re-registering-a-node).

### <a name="resolution"></a>Resolução

Siga os passos listados abaixo para voltar a registar o nó DSC em falta.

Primeiro, desregilhe o nó utilizando os seguintes passos.

1. A partir do portal Azure, **em** -> **contas de domótica**-> {A sua conta de Automação} -> **Configuração do Estado (DSC)**
2. Clique em "Nós", e clique no nó com problemas.
3. Clique em "Desregistar" para desregistar o nó.

Em segundo lugar, desinstale a extensão DSC do nó.

1. A partir do portal Azure, sob**a Máquina Virtual** **Doméstica** -> -> {Nó falhado} -> **extensões**
2. Clique em "Microsoft.Powershell.DSC".
3. Clique em "Desinstalar", para desinstalar a extensão DSC powerShell.

Terceiro, retire todos os certificados maus ou caducados do nó.

No nó falhado de um Powershell Prompt elevado, execute o seguinte:

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

Por último, registe novamente o nó em queda utilizando os seguintes passos.

1. A partir do portal Azure, **em** -> **contas de domótica** -> {A sua conta de Automação} -> **Configuração do Estado (DSC)**
2. Clique em "Nóesos".
3. Clique no botão "Adicionar".
4. Selecione o nó de falha.
5. Clique em "Connect", e selecione as opções desejadas.

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Cenário: Nó está em estado falhado com um erro "Não encontrado"

#### <a name="issue"></a>Problema

O nó tem um relatório com estado **falhado** e contém o erro:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Causa

Este erro ocorre tipicamente quando o nó é atribuído a um nome de configuração (por exemplo, ABC) em vez de um nome de configuração do nó (por exemplo, ABC. WebServer).

#### <a name="resolution"></a>Resolução

* Certifique-se de que está a atribuir o nó com "nome de configuração do nó" e não com o "nome de configuração".
* Pode atribuir uma configuração do nó a um nó utilizando o portal Azure ou com um cmdlet PowerShell.

  * Para atribuir uma configuração do nó a um nó utilizando o portal Azure, abra a página **De nós DSC,** em seguida, selecione um nó e clique no botão de **configuração do nó de atribuir.**
  * Para atribuir uma configuração do nó a um nó utilizando o cmdlet PowerShell, utilize **o Set-AzureRmAutomationDscNode** cmdlet

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a><a name="no-mof-files"></a>Cenário: Não foram produzidas configurações de nó (ficheiros MOF) quando uma configuração é compilada

#### <a name="issue"></a>Problema

O seu trabalho de compilação DSC suspende com o erro:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Causa

Quando a expressão **Node** que segue a palavra-chave do `$null`nó na configuração DSC avalia, então não são produzidas configurações de nó.

#### <a name="resolution"></a>Resolução

Qualquer uma das seguintes soluções resolve o problema:

* Certifique-se de que a expressão ao lado da palavra-chave do **nó** na definição de configuração não está a avaliar para $null.
* Se estiver a passar o ConfigurationData ao compilar a configuração, certifique-se de que está a passar os valores esperados que a configuração necessita do [ConfigurationData](../automation-dsc-compile.md).

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a><a name="dsc-in-progress"></a>Cenário: O relatório do nó do CDS fica preso "em curso"

#### <a name="issue"></a>Problema

As saídas do agente DSC:

```error
No instance found with given property values
```

#### <a name="cause"></a>Causa

Atualizou a sua versão WMF e corrompeu o WMI.

#### <a name="resolution"></a>Resolução

Para corrigir o problema, siga as instruções no artigo de [questões e limitações conhecidas do DSC.](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Cenário: Incapaz de utilizar uma credencial numa configuração DSC

#### <a name="issue"></a>Problema

O seu trabalho de compilação dSC foi suspenso com o erro:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Causa

Usou uma credencial numa configuração, mas não forneceu o devida **ConfiguraçãoData** para definir **psDscAllowPlainTextPassword** verdadeiro para cada configuração do nó.

#### <a name="resolution"></a>Resolução

* Certifique-se de passar no Tempo de **Configuração** adequado para definir **PSDscAllowPlainTextPassword** fiel para cada configuração do nó que é mencionada na configuração. Para mais informações, consulte a compilação de [configurações dSC na Configuração do Estado da Automação Azure](../automation-dsc-compile.md).

### <a name="scenario-onboarding-from-dsc-extension-failure-processing-extension-error"></a><a name="failure-processing-extension"></a>Cenário: Embarque a partir da extensão do DSC, erro de "extensão de processamento de falhas"

#### <a name="issue"></a>Problema

Ao embarcar utilizando a extensão DSC, ocorre uma falha que contém o erro:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Causa

Este erro ocorre tipicamente quando o nó é atribuído um nome de configuração nó que não existe no serviço.

#### <a name="resolution"></a>Resolução

* Certifique-se de que está a atribuir o nó com um nome de configuração de nó que corresponda exatamente ao nome do serviço.
* Você pode optar por não incluir o nome de configuração do nó, o que resultará em embarcar no nó, mas não atribuir uma configuração de nó

### <a name="scenario-registering-a-node-with-powershell-returns-the-error-one-or-more-errors-occurred"></a><a name="cross-subscription"></a>Cenário: Registar um nó com powerShell devolve o erro "Um ou mais erros ocorreram"

#### <a name="issue"></a>Problema

Ao registar um nó `Register-AzAutomationDSCNode` `Register-AzureRMAutomationDSCNode`usando ou, recebe o seguinte erro.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Causa

Este erro ocorre quando se tenta registar um nó que vive numa subscrição separada do que a conta Automation.

#### <a name="resolution"></a>Resolução

Trate o nó de subscrição cruzada como se vivesse numa nuvem separada, ou no local.

Siga os passos abaixo para registar o nó.

* Windows - [Máquinas de Janelas Físicas/Virtuais no local, ou numa nuvem diferente do Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances).
* Linux - [Máquinas linux físicas/virtuais no local, ou numa nuvem diferente do Azure.](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure)

### <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Cenário: Mensagem de erro - "Provisioning Failed"

#### <a name="issue"></a>Problema

Ao registar um nó, vê o erro:

```error
Provisioning has failed
```

#### <a name="cause"></a>Causa

Esta mensagem ocorre quando há um problema de conectividade entre o nó e Azure.

#### <a name="resolution"></a>Resolução

Determine se o seu nó está numa rede virtual privada ou tem outros problemas ligados ao Azure.

Para mais informações, consulte [erros de resolução de problemas ao embarcar em soluções](onboarding.md).

### <a name="scenario-applying-a-configuration-in-linux-a-failure-occurs-with-a-general-error"></a><a name="failure-linux-temp-noexec"></a>Cenário: Aplicação de uma configuração em Linux, uma falha ocorre com um erro geral

#### <a name="issue"></a>Problema

Ao aplicar uma configuração em Linux, ocorre uma falha que contém o erro:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Causa

Os clientes identificaram `/tmp` que, se `noexec`a localização for definida, a versão atual do DSC não aplicará configurações.

#### <a name="resolution"></a>Resolução

* Retire `noexec` a opção do `/tmp` local.

### <a name="scenario-node-configuration-names-that-overlap-could-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Cenário: Nomes de configuração do nó que se sobrepõem podem resultar em mau lançamento

#### <a name="issue"></a>Problema

Se um único script de configuração for usado para gerar múltiplas configurações de nó, e algumas das configurações do nó têm um nome que é um subconjunto de outros, um problema no serviço de compilação pode resultar na atribuição da configuração errada.  Isto só ocorre quando se utiliza um único script para gerar configurações com dados de configuração por nó, e apenas quando o nome se sobreposição ocorre no início da cadeia.

Exemplo, se um único script de configuração for usado para gerar configurações baseadas em dados do nó passados como um hashtable usando cmdlets, e os dados do nó incluem um servidor chamado "servidor" e "1server".

#### <a name="cause"></a>Causa

Problema conhecido com o serviço de compilação.

#### <a name="resolution"></a>Resolução

A melhor sucinta seria compilar localmente ou num oleoduto CI/CD e carregar os ficheiros MOF diretamente para o serviço.  Se a compilação no serviço for um requisito, a próxima melhor suposição seria dividir os trabalhos de compilação para que não haja sobreposição de nomes.

### <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Cenário: Erro de tempo de saída de gateway no upload de configuração DSC

#### <a name="issue"></a>Problema

Recebe um `GatewayTimeout` erro ao carregar uma configuração DSC. 

#### <a name="cause"></a>Causa

As configurações dSC que demoram muito tempo a compilar podem causar este erro.

#### <a name="resolution"></a>Resolução

Pode fazer com que as suas configurações dSC analisem mais rapidamente, incluindo explicitamente o `ModuleName` parâmetro para quaisquer `Import-DscResource` chamadas. Para mais informações, consulte [A Utilização de Import-DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1).

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

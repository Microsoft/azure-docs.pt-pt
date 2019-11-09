---
title: Solucionando problemas com a DSC (configuração de estado desejado) da automação do Azure
description: Este artigo fornece informações sobre como solucionar problemas de DSC (configuração de estado desejado)
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7be5e814d8092b523fa69fdd84f0e1476736fda2
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887705"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Solucionar problemas de DSC (configuração de estado desejado)

Este artigo fornece informações sobre como solucionar problemas com a DSC (configuração de estado desejado).

## <a name="steps-to-troubleshoot-desired-state-configuration-dsc"></a>Etapas para solucionar problemas de DSC (configuração de estado desejado)

Quando você tem erros de compilação ou implantação de configurações na configuração de estado do Azure, aqui estão algumas etapas para ajudá-lo a diagnosticar o problema.

1. **Verifique se a configuração foi compilada com êxito no computador local:**  A configuração de estado do Azure é criada na DSC do PowerShell. Você pode encontrar a documentação para a linguagem DSC e a sintaxe nos [documentos DSC do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).

   Ao compilar sua configuração DSC em seu computador local, você pode descobrir e resolver erros comuns, como:

   - **Módulos ausentes**
   - **Erros de sintaxe**
   - **Erros lógicos**

2. **Exibir logs de DSC em seu nó:** Se sua configuração for compilada com êxito, mas falhar quando aplicada a um nó, você poderá encontrar informações detalhadas nos logs. Para obter informações sobre onde encontrar logs de DSC, consulte [onde estão os logs de eventos de DSC](/powershell/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

   Além disso, o [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) pode ajudá-lo a analisar informações detalhadas dos logs de DSC. Se você contatar o suporte, eles precisarão desses logs para diagnosticar o problema.

   Você pode instalar o **xDscDiagnostics** em seu computador local usando as instruções encontradas em [instalar o módulo de versão estável](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

   Para instalar o **xDscDiagnostics** em seu computador do Azure, você pode usar [AZ VM execute-Command](/cli/azure/vm/run-command) ou [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Você também pode usar a opção **executar comando** do portal, seguindo as etapas encontradas em [executar scripts do PowerShell em sua VM do Windows com o comando executar](../../virtual-machines/windows/run-command.md).

   Para obter informações sobre como usar o **xDscDiagnostics**, consulte [usando o xDscDiagnostics para analisar os logs de DSC](/powershell/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs), bem como os [cmdlets do xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).
3. **Verifique se os nós e o espaço de trabalho de automação têm os módulos necessários:** A configuração de estado desejado depende dos módulos instalados no nó.  Ao usar a configuração de estado da automação do Azure, importe os módulos necessários para sua conta de automação usando as etapas listadas em [Importar módulos](../shared-resources/modules.md#import-modules). As configurações também podem ter uma dependência em versões específicas de módulos.  Para obter mais informações, consulte [solucionar problemas de módulos](shared-resources.md#modules).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Erros comuns ao trabalhar com a DSC (configuração de estado desejado)

### <a name="unsupported-characters"></a>Cenário: uma configuração com caracteres especiais não pode ser excluída do portal

#### <a name="issue"></a>Problema

Ao tentar excluir uma configuração DSC do portal, você verá o seguinte erro:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Causa

Esse erro é um problema temporário que está planejado para ser resolvido.

#### <a name="resolution"></a>Resolução

* Use o cmdlet AZ "Remove-AzAutomationDscConfiguration" para excluir a configuração.
* A documentação deste cmdlet ainda não foi atualizada.  Até lá, consulte a documentação do módulo AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Cenário: falha ao registrar o agente DSC

#### <a name="issue"></a>Problema

Ao tentar executar `Set-DscLocalConfigurationManager` ou outro cmdlet de DSC, você receberá o erro:

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

Esse erro normalmente é causado por um firewall, o computador está atrás de um servidor proxy ou outros erros de rede.

#### <a name="resolution"></a>Resolução

Verifique se seu computador tem acesso aos pontos de extremidade apropriados para o Azure DSC de Automação e tente novamente. Para obter uma lista de portas e endereços necessários, consulte [planejamento de rede](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>Cenário: o nó está em status de falha com um erro "não encontrado"

#### <a name="issue"></a>Problema

O nó tem um relatório com status de **falha** e que contém o erro:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó é atribuído a um nome de configuração (por exemplo, ABC) em vez de um nome de configuração de nó (por exemplo, ABC. Servidor da Web).

#### <a name="resolution"></a>Resolução

* Certifique-se de que você está atribuindo o nó com "nome de configuração do nó" e não o "nome da configuração".
* Você pode atribuir uma configuração de nó a um nó usando portal do Azure ou com um cmdlet do PowerShell.

  * Para atribuir uma configuração de nó a um nó usando portal do Azure, abra a página **nós DSC** , selecione um nó e clique no botão **atribuir configuração de nó** .
  * Para atribuir uma configuração de nó a um nó usando o cmdlet do PowerShell, use o cmdlet **set-AzureRmAutomationDscNode**

### <a name="no-mof-files"></a>Cenário: não foram produzidas configurações de nó (Arquivos MOF) quando uma configuração é compilada

#### <a name="issue"></a>Problema

Seu trabalho de compilação DSC é suspenso com o erro:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Causa

Quando a expressão após a palavra-chave **node** na configuração DSC é avaliada como `$null`, nenhuma configuração de nó é produzida.

#### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Certifique-se de que a expressão ao lado da palavra-chave **node** na definição de configuração não esteja avaliando como $NULL.
* Se você estiver passando ConfigurationData ao compilar a configuração, certifique-se de que você está passando os valores esperados que a configuração requer do [ConfigurationData](../automation-dsc-compile.md).

### <a name="dsc-in-progress"></a>Cenário: o relatório do nó DSC fica preso no estado "em andamento"

#### <a name="issue"></a>Problema

O agente DSC gera:

```error
No instance found with given property values
```

#### <a name="cause"></a>Causa

Você atualizou a versão do WMF e corrompeu o WMI.

#### <a name="resolution"></a>Resolução

Para corrigir o problema, siga as instruções no artigo [problemas conhecidos e limitações do DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) .

### <a name="issue-using-credential"></a>Cenário: não é possível usar uma credencial em uma configuração DSC

#### <a name="issue"></a>Problema

Seu trabalho de compilação DSC foi suspenso com o erro:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Causa

Você usou uma credencial em uma configuração, mas não forneceu **ConfigurationData** adequados para definir **PSDscAllowPlainTextPassword** como true para cada configuração de nó.

#### <a name="resolution"></a>Resolução

* Certifique-se de passar o **ConfigurationData** apropriado para definir **PSDscAllowPlainTextPassword** como true para cada configuração de nó mencionada na configuração. Para obter mais informações, consulte [ativos no Azure DSC de automação](../automation-dsc-compile.md#working-with-assets-in-azure-automation-during-compilation).

### <a name="failure-processing-extension"></a>Cenário: integração da extensão de DSC, erro "extensão de processamento de falha"

#### <a name="issue"></a>Problema

Ao realizar a integração usando a extensão de DSC, ocorre uma falha que contém o erro:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó recebe um nome de configuração de nó que não existe no serviço.

#### <a name="resolution"></a>Resolução

* Certifique-se de que você está atribuindo o nó com um nome de configuração de nó que corresponda exatamente ao nome no serviço.
* Você pode optar por não incluir o nome da configuração do nó, o que fará com que a integração do nó, mas não a atribuição de uma configuração de nó

### <a name="failure-linux-temp-noexec"></a>Cenário: aplicando uma configuração no Linux, uma falha ocorre com um erro geral

#### <a name="issue"></a>Problema

Ao aplicar uma configuração no Linux, ocorre uma falha que contém o erro:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Causa

Os clientes identificaram que, se o local de `/tmp` for definido como `noexec`, a versão atual do DSC não conseguirá aplicar as configurações.

#### <a name="resolution"></a>Resolução

* Remova a opção `noexec` do local `/tmp`.

### <a name="compilation-node-name-overlap"></a>Cenário: nomes de configuração de nó que se sobrepõem podem resultar em uma versão incorreta

#### <a name="issue"></a>Problema

Se um único script de configuração for usado para gerar várias configurações de nó e algumas das configurações de nó tiverem um nome que seja um subconjunto de outros, um problema no serviço de compilação poderá resultar na atribuição da configuração errada.  Isso ocorre apenas ao usar um único script para gerar configurações com dados de configuração por nó e somente quando a sobreposição de nome ocorre no início da cadeia de caracteres.

Por exemplo, se um único script de configuração for usado para gerar configurações com base nos dados do nó passados como uma tabela de hash usando cmdlets e os dados do nó incluírem um servidor chamado "Server" e "1server".

#### <a name="cause"></a>Causa

Problema conhecido com o serviço de compilação.

#### <a name="resolution"></a>Resolução

A melhor solução alternativa seria compilar localmente ou em um pipeline de CI/CD e carregar os arquivos MOF diretamente no serviço.  Se a compilação no serviço for um requisito, a melhor solução alternativa seria dividir os trabalhos de compilação para que não haja sobreposição em nomes.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você poderá arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

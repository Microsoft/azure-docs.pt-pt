---
title: Resolução de problemas com o Azure Automation Desired State Configuration (DSC)
description: Este artigo fornece informações sobre como resolver problemas de Desired State Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 53fef426c927c690a3b697055f467f6cd35c532c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477529"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Resolver problemas de Desired State Configuration (DSC)

Este artigo fornece informações sobre como resolver problemas com o Desired State Configuration (DSC).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Erros comuns ao trabalhar com o Desired State Configuration (DSC)

### <a name="unsupported-characters"></a>Cenário: Uma configuração com carateres especiais não é possível eliminar a partir do portal

#### <a name="issue"></a>Problema

Ao tentar eliminar uma configuração de DSC a partir do portal, verá o seguinte erro:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Causa

Este erro é um problema temporário que está previsto que seja resolvido.

#### <a name="resolution"></a>Resolução

* Utilize o Cmdlet de Az "Remove-AzAutomationDscConfiguration" para eliminar a configuração.
* A documentação para este cmdlet não foi atualizada ainda.  Até lá, consulte a documentação para o módulo AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Cenário: Falha ao registar o agente Dsc

#### <a name="issue"></a>Problema

Ao tentar executar `Set-DscLocalConfigurationManager` ou outro cmdlet de DSC receber o erro:

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

Normalmente, este erro é causado por uma firewall, a máquina a ser protegido por um servidor proxy ou outros erros de rede.

#### <a name="resolution"></a>Resolução

Verifique se que seu computador tem acesso para os pontos de extremidade apropriados para DSC de automatização do Azure e tente novamente. Para obter uma lista de portas e endereços necessários, consulte [planeamento de rede](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>Cenário: Nó está no Estado com falhas com um erro "Não encontrado"

#### <a name="issue"></a>Problema

O nó tem um relatório com **falhada** estado e que contém o erro:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Causa

Esse erro ocorre normalmente quando o nó for atribuído um nome de configuração (por exemplo, ABC) em vez de um nome de configuração de nó (por exemplo, ABC. Servidor Web).

#### <a name="resolution"></a>Resolução

* Certifique-se de que está a atribuir o nó com o "nome de configuração de nó" e não o "nome da configuração".
* Pode atribuir uma configuração de nó a um nó através do portal do Azure ou com um cmdlet do PowerShell.

  * Para atribuir uma configuração de nó a um nó através do portal do Azure, abra a **nós de DSC** página, em seguida, selecione um nó e clique em **atribuir configuração do nó** botão.  
  * Para atribuir uma configuração de nó a um nó com o cmdlet do PowerShell, utilize **Set-AzureRmAutomationDscNode** cmdlet

### <a name="no-mof-files"></a>Cenário: Não existem configurações de nó (ficheiros MOF) foram produzidas quando uma configuração é compilada

#### <a name="issue"></a>Problema

Suspende a tarefa de compilação de DSC com o erro:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Causa

Quando a seguinte expressão a **nó** palavra-chave na configuração do DSC for avaliada como `$null`, em seguida, sem configurações de nó são produzidas.

#### <a name="resolution"></a>Resolução

Qualquer uma das seguintes soluções resolver o problema:

* Certifique-se de que a expressão seguinte para o **nó** palavra-chave na definição de configuração não está a avaliar para $null.
* Se estiver passando ConfigurationData ao compilar a configuração, certifique-se de que está a transmitir os valores esperados, que requer que a configuração do [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Cenário: O relatório de nó DSC fica preso no estado "em curso"

#### <a name="issue"></a>Problema

As saídas de agente DSC:

```error
No instance found with given property values
```

#### <a name="cause"></a>Causa

Atualizar sua versão do WMF e ter danificado WMI.

#### <a name="resolution"></a>Resolução

Para corrigir o problema, siga as instruções no [conhecido limitações e problemas de DSC](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) artigo.

### <a name="issue-using-credential"></a>Cenário: Não é possível utilizar uma credencial numa configuração de DSC

#### <a name="issue"></a>Problema

A tarefa de compilação de DSC foi suspenso com o erro:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Causa

Já utilizou uma credencial numa configuração, mas não forneceu adequada **ConfigurationData** definir **PSDscAllowPlainTextPassword** como true para cada configuração de nó.

#### <a name="resolution"></a>Resolução

* Certifique-se passar o elemento adequado **ConfigurationData** definir **PSDscAllowPlainTextPassword** como true para cada configuração de nó que é mencionada na configuração. Para obter mais informações, consulte [ativos no DSC de automatização do Azure](../automation-dsc-compile.md#assets).

### <a name="failure-processing-extension"></a>Cenário: Inclusão a partir da extensão dsc, erro "Falha de processamento da extensão"

#### <a name="issue"></a>Problema

Quando ocorre a integração com a extensão de DSC, uma falha que contém o erro:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Causa

Este erro ocorre normalmente quando o nó é atribuído um nome de configuração de nó que não existe no serviço.

#### <a name="resolution"></a>Resolução

* Certifique-se de que está a atribuir o nó com um nome de configuração do nó que corresponde exatamente ao nome no serviço.
* Pode optar por não incluir o nome de configuração do nó, o que resulta na inclusão de nó, mas não atribuir uma configuração do nó

### <a name="failure-linux-temp-noexec"></a>Cenário: Aplicar uma configuração no Linux, ocorre uma falha com um erro geral

#### <a name="issue"></a>Problema

Ao aplicar uma configuração no Linux, ocorre uma falha que contém o erro:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Causa

Identificar os clientes que se a localização de /tmp estiver definida como noexec, a versão atual do DSC não aplicar as configurações.

#### <a name="resolution"></a>Resolução

* Remova a opção de noexec a partir da localização de /tmp.

## <a name="next-steps"></a>Passos Seguintes

Se não vir o seu problema ou não é possível resolver o problema, visite um dos seguintes canais de suporte mais:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

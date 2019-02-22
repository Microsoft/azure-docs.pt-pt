---
title: Resolução de problemas com o Azure Automation Desired State Configuration (DSC)
description: Este artigo fornece informações sobre como resolver problemas de Desired State Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a34dea7e1eb53531db55dc62df8fbad8541f7a35
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586805"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Resolver problemas de Desired State Configuration (DSC)

Este artigo fornece informações sobre como resolver problemas com o Desired State Configuration (DSC).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Erros comuns ao trabalhar com o Desired State Configuration (DSC)

### <a name="unsupported-characters"></a>Cenário: Uma configuração com carateres especiais não é possível eliminar a partir do portal

#### <a name="issue"></a>Problema

Ao tentar eliminar uma configuração de DSC a partir do portal, verá o seguinte erro:

```
An error occured while deleteing the DSC configuration '<name>'.  Error-details: The arguement configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Causa

Este é um problema temporário que está previsto que seja resolvido.

#### <a name="resolution"></a>Resolução

* Utilize o Cmdlet de Az "Remove-AzAutomationDscConfiguration" para eliminar a configuração.
* A documentação para este cmdlet ainda não foi atualizada.  Até lá, consulte a documentação para o módulo AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](https://docs.microsoft.com/en-us/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration?view=azurermps-6.13.0)

### <a name="failed-not-found"></a>Cenário: Nó está no Estado com falhas com um erro "Não encontrado"

#### <a name="issue"></a>Problema

O nó tem um relatório com **falhada** estado e que contém o erro:

```
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

```
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

```
No instance found with given property values
```

#### <a name="cause"></a>Causa

Atualizar sua versão do WMF e ter danificado WMI.

#### <a name="resolution"></a>Resolução

Para corrigir a problema siga as instruções a [conhecido limitações e problemas de DSC](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) artigo.

### <a name="issue-using-credential"></a>Cenário: Não é possível utilizar uma credencial numa configuração de DSC

#### <a name="issue"></a>Problema

A tarefa de compilação de DSC foi suspenso com o erro:

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Causa

Utilizou uma credencial numa configuração, mas não forneceu adequada **ConfigurationData** definir **PSDscAllowPlainTextPassword** como true para cada configuração de nó.

#### <a name="resolution"></a>Resolução

* Certifique-se passar o elemento adequado **ConfigurationData** definir **PSDscAllowPlainTextPassword** como true para cada configuração de nó mencionada na configuração. Para obter mais informações, consulte [ativos no DSC de automatização do Azure](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Passos Seguintes

Se não VI o seu problema ou não é possível resolver o problema, visite um dos seguintes canais de suporte mais:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

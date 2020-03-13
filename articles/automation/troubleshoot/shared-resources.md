---
title: Erros de resolução de problemas com os recursos partilhados da Azure Automation
description: Saiba como resolver problemas e resolver problemas com a Azure Automation com recursos partilhados que suportam livros de execução.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278327"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Erros de resolução de problemas com recursos partilhados

Este artigo discute soluções para resolver problemas que pode encontrar ao utilizar os recursos partilhados na Automação Azure.

## <a name="modules"></a>Módulos

### <a name="module-stuck-importing"></a>Cenário: Um Módulo está preso importando

#### <a name="issue"></a>Problema

Um módulo fica preso no estado **importador** quando importa ou atualiza os seus módulos na automatização Do Azure.

#### <a name="cause"></a>Causa

Importar módulos PowerShell é um processo complexo em várias etapas. Este processo introduz a possibilidade de um módulo não importar corretamente. Se este problema ocorrer, o módulo que está a importar pode ficar preso num estado transitório. Para saber mais sobre este processo, consulte [importar um módulo PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Resolução

Para resolver este problema, tem de remover o módulo que está preso no estado **de Importação** utilizando o cmdlet [Remove-AzureRmAutomationModule.](/powershell/module/azurerm.automation/remove-azurermautomationmodule) Em seguida, pode voltar a tentar importar o módulo.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Cenário: Os módulos AzureRM estão presos a importar depois de tentarem atualizá-los

#### <a name="issue"></a>Problema

Um banner com a seguinte mensagem permanece na sua conta depois de tentar atualizar os seus módulos AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Causa

Existe um problema conhecido com a atualização dos módulos AzureRM numa Conta de Automação que está num grupo de recursos com um nome numérico que começa com 0.

#### <a name="resolution"></a>Resolução

Para atualizar os seus módulos Azure na sua Conta de Automação, deve estar num grupo de recursos que tem um nome alfanumérico. Neste momento, grupos de recursos com nomes numéricos a partir de 0 não conseguem atualizar os módulos AzureRM.

### <a name="module-fails-to-import"></a>Cenário: Módulo não importa ou cmdlets não podem ser executados após importação

#### <a name="issue"></a>Problema

Um módulo não importa ou importa com sucesso, mas não são extraídos cmdlets.

#### <a name="cause"></a>Causa

Algumas razões comuns para que um módulo não importe com sucesso para a Automação Azure são:

* A estrutura não corresponde à estrutura em que a Automação precisa de estar.
* O módulo depende de outro módulo que não tenha sido implantado na sua conta de Automação.
* O módulo está a perder as suas dependências na pasta.
* O `New-AzureRmAutomationModule` cmdlet está a ser usado para carregar o módulo, e você não deu o caminho de armazenamento completo ou não carregou o módulo usando um URL acessível ao público.

#### <a name="resolution"></a>Resolução

Qualquer uma das seguintes soluções resolve o problema:

* Certifique-se de que o módulo segue o seguinte formato: ModuleName.Zip **->** ModuleName ou Verno Número **de versão->** (ModuleName.psm1, ModuleName.psd1)
* Abra o ficheiro .psd1 e veja se o módulo tem alguma dependência. Se isso acontecer, faça o upload destes módulos para a conta Automation.
* Certifique-se de que estão presentes na pasta do módulo.

### <a name="all-modules-suspended"></a>Cenário: Update-AzureModule.ps1 suspende ao atualizar módulos

#### <a name="issue"></a>Problema

Ao utilizar o manual [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) para atualizar os módulos Azure, o processo de atualização do módulo é suspenso.

#### <a name="cause"></a>Causa

A definição predefinida para determinar quantos módulos são atualizados simultaneamente é de 10 quando se utiliza o `Update-AzureModule.ps1` script. O processo de atualização é propenso a erros quando demasiados módulos estão a ser atualizados ao mesmo tempo.

#### <a name="resolution"></a>Resolução

Não é comum que todos os módulos AzureRM sejam necessários na mesma conta de Automação. Recomenda-se apenas importar os módulos AzureRM de que necessita.

> [!NOTE]
> Evite importar o módulo **AzureRM.** Importar os módulos **AzureRM** faz com que todos os módulos **De\*AzureRM** sejam importados, isto não é recomensurado.

Se o processo de atualização suspender, terá de adicionar o parâmetro `SimultaneousModuleImportJobCount` ao `Update-AzureModules.ps1` script e fornecer um valor inferior ao padrão que é 10. É aconselhável que implemente esta lógica, comece com um valor de 3 ou 5. `SimultaneousModuleImportJobCount` é um parâmetro do livro de execução do sistema `Update-AutomationAzureModulesForAccount` que é usado para atualizar os módulos Azure. Esta mudança faz com que o processo dure mais tempo, mas tem mais hipóteses de completar. O exemplo que se segue mostra o parâmetro e onde colocá-lo no livro de corridas:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Executar Como contas

### <a name="unable-create-update"></a>Cenário: Não é capaz de criar ou atualizar uma conta Run As

#### <a name="issue"></a>Problema

Quando tenta criar ou atualizar uma conta 'Executar As', recebe um erro semelhante à seguinte mensagem de erro:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Não tem as permissões necessárias para criar ou atualizar a conta Executar Como ou o recurso está bloqueado a nível de grupo de recursos.

#### <a name="resolution"></a>Resolução

Para criar ou atualizar uma conta Run As, deve ter permissões adequadas aos vários recursos utilizados pela conta Run As. Para saber mais sobre as permissões necessárias para criar ou atualizar uma conta Run As, consulte [executar Como permissões de conta](../manage-runas-account.md#permissions).

Se o problema for por causa de um cadeado, verifique se o cadeado está bem para removê-lo. Em seguida, navegue para o recurso que está bloqueado, clique na fechadura e escolha **Eliminar** para remover o bloqueio.

### <a name="iphelper"></a>Cenário: Recebe o erro "Incapaz de encontrar um ponto de entrada chamado 'GetPerAdapterInfo' em DLL 'iplpapi.dll'" ao executar um livro de execução.

#### <a name="issue"></a>Problema

Ao executar um livro de execução, recebe a seguinte exceção:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Causa

Este erro é provavelmente causado por uma conta de [execução incorretamente](../manage-runas-account.md)configurada .

#### <a name="resolution"></a>Resolução

Certifique-se de que a sua [Conta 'Executar As Conta'](../manage-runas-account.md) está corretamente configurada. Uma vez configurado corretamente, certifique-se de que tem o código adequado no seu livro de execução para autenticar com o Azure. O exemplo seguinte mostra um fragmento de código para autenticar o Azure num livro de execução usando uma Conta Run As.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

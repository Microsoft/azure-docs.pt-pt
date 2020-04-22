---
title: Troubleshoot recursos partilhados na Automação Azure
description: Saiba como resolver problemas e resolver problemas com os recursos partilhados da Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733579"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Troubleshoot recursos partilhados na Automação Azure

Este artigo discute soluções para questões que poderá encontrar ao utilizar [recursos partilhados](../automation-intro.md#shared-resources) na Azure Automation.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="modules"></a>Módulos

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Cenário: Um módulo fica preso durante a importação

#### <a name="issue"></a>Problema

Um módulo fica preso no estado de Importação quando está a importar ou a atualizar os seus módulos de Automação Azure.

#### <a name="cause"></a>Causa

Uma vez que importar módulos PowerShell é um processo complexo em várias etapas, um módulo pode não importar corretamente e pode ser preso num estado transitório. Para saber mais sobre o processo de importação, consulte [importar um módulo PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Resolução

Para resolver este problema, tem de remover o módulo que está preso no estado de Importação utilizando o cmdlet [Remove-AzAutomationModule.](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) Em seguida, pode voltar a tentar importar o módulo.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Cenário: Os módulos AzureRM ficam presos durante a importação após uma tentativa de atualização

#### <a name="issue"></a>Problema

Um banner com a seguinte mensagem permanece na sua conta depois de tentar atualizar os seus módulos AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Causa

Existe um problema conhecido com a atualização dos módulos AzureRM numa conta de Automação que está num grupo de recursos com um nome numérico a partir de 0.

#### <a name="resolution"></a>Resolução

Para atualizar os seus módulos AzureRM na sua conta Automation, a conta deve estar num grupo de recursos com um nome alfanumérico. Neste momento, grupos de recursos com nomes numéricos a partir de 0 não conseguem atualizar os módulos AzureRM.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Cenário: Módulo não importa ou cmdlets não podem ser executados após importação

#### <a name="issue"></a>Problema

Um módulo não importa ou importa com sucesso, mas não são extraídos cmdlets.

#### <a name="cause"></a>Causa

Algumas razões comuns para que um módulo não importe com sucesso para a Automação Azure são:

* A estrutura não corresponde à estrutura de que a Automação precisa.
* O módulo depende de outro módulo que não tenha sido implantado na sua conta de Automação.
* O módulo está a perder as suas dependências na pasta.
* O [cmdlet New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) está a ser usado para carregar o módulo, e você não deu o caminho de armazenamento completo ou não carregou o módulo usando um URL acessível ao público.

#### <a name="resolution"></a>Resolução

Utilize qualquer uma destas soluções para corrigir o problema.

* Certifique-se de que o módulo segue o formato: ModuleName.zip -> ModuleName ou Número de Versão -> (ModuleName.psm1, ModuleName.psd1).
* Abra o ficheiro **.psd1** e veja se o módulo tem alguma dependência. Se isso acontecer, faça o upload destes módulos para a conta Automation.
* Certifique-se de que estão **presentes** na pasta do módulo.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Cenário: Update-AzureModule.ps1 suspende ao atualizar módulos

#### <a name="issue"></a>Problema

Ao utilizar o manual [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) para atualizar os seus módulos Azure, o processo de atualização do módulo está suspenso.

#### <a name="cause"></a>Causa

A definição predefinida para determinar quantos módulos são atualizados simultaneamente é de 10 quando utilizar **o Update-AzureModule.ps1**. O processo de atualização é propenso a erros quando demasiados módulos estão a ser atualizados ao mesmo tempo.

#### <a name="resolution"></a>Resolução

Não é comum que todos os módulos AzureRM ou Az sejam necessários na mesma conta de Automação. Recomenda-se apenas importar os módulos específicos de que precisa.

> [!NOTE]
> Evite importar a `Az.Automation` `AzureRM.Automation` totalidade ou módulo, que importa todos os módulos contidos.

Se o processo de atualização suspender, adicione o `SimultaneousModuleImportJobCount` parâmetro ao script **Update-AzureModules.ps1** e forneça um valor inferior ao padrão de 10. Se implementar esta lógica, é aconselhável começar com um valor de 3 ou 5. `SimultaneousModuleImportJobCount`é um parâmetro do livro de execução do sistema **Update-AutomationAzureModulesForAccount** que é utilizado para atualizar os módulos Azure. Se fizer este ajuste, o processo de atualização dura mais tempo, mas tem mais hipóteses de completar. O exemplo que se segue mostra o parâmetro e onde colocá-lo no livro de corridas:

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

## <a name="run-as-accounts"></a>Contas Run As

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Cenário: Não é capaz de criar ou atualizar uma conta Run As

#### <a name="issue"></a>Problema

Quando tenta criar ou atualizar uma conta 'Executar As', recebe um erro semelhante à seguinte mensagem de erro:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Não tem as permissões necessárias para criar ou atualizar a conta Executar Como ou o recurso está bloqueado a nível de grupo de recursos.

#### <a name="resolution"></a>Resolução

Para criar ou atualizar uma conta Run As, deve ter [permissões](../manage-runas-account.md#permissions) adequadas aos vários recursos utilizados pela conta Run As. 

Se o problema for devido a um bloqueio, verifique se o bloqueio pode ser removido. Em seguida, navegue para o recurso que está bloqueado no portal Azure, clique na fechadura à direita e clique em **Delete**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Cenário: Recebe o erro "Incapaz de encontrar um ponto de entrada chamado 'GetPerAdapterInfo' em DLL 'iplpapi.dll'" ao executar um livro de execução

#### <a name="issue"></a>Problema

Ao executar um livro de execução, recebe a seguinte exceção:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Causa

Este erro é provavelmente causado por uma conta de [execução incorretamente](../manage-runas-account.md)configurada .

#### <a name="resolution"></a>Resolução

Certifique-se de que a sua conta Run As está corretamente configurada. Em seguida, verifique se tem o código adequado no seu livro de execução para autenticar com o Azure. O exemplo seguinte mostra um fragmento de código para autenticar o Azure num livro de execução usando uma Conta Run As.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema acima ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport)a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

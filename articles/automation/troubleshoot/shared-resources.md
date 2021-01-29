---
title: Resolução de problemas Azure Automation problemas de recursos partilhados
description: Este artigo diz como resolver problemas e resolver problemas com recursos partilhados da Azure Automation.
services: automation
ms.subservice: ''
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 1a822166ae4c2bf793e0fa50e93018f499fcc27a
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053625"
---
# <a name="troubleshoot-shared-resource-issues"></a>Problemas de recursos partilhados na resolução de problemas

Este artigo discute questões que podem surgir quando se está a usar [recursos partilhados](../automation-intro.md#shared-resources) na Azure Automation.

## <a name="modules"></a>Módulos

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Cenário: Um módulo está preso durante a importação

#### <a name="issue"></a>Problema

Um módulo fica preso no estado *de importação* quando está a importar ou atualizar os seus módulos Azure Automation.

#### <a name="cause"></a>Causa

Como importar módulos PowerShell é um processo complexo e multipasso, um módulo pode não importar corretamente, e pode ser preso em um estado transitório. Para saber mais sobre o processo de importação, consulte [importar um módulo PowerShell.](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)

#### <a name="resolution"></a>Resolução

Para resolver este problema, tem de remover o módulo que está preso utilizando o [cmdlet Remove-AzAutomationModule.](/powershell/module/Az.Automation/Remove-AzAutomationModule) Em seguida, pode voltar a tentar importar o módulo.

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

Existe um problema conhecido com a atualização dos módulos AzureRM numa conta Automation. Especificamente, o problema ocorre se os módulos estiverem em um grupo de recursos com um nome numérico começando com 0.

#### <a name="resolution"></a>Resolução

Para atualizar os seus módulos AzureRM na sua conta Automation, a conta deve estar num grupo de recursos com um nome alfanumérico. Grupos de recursos com nomes numéricos a partir de 0 não conseguem atualizar os módulos AzureRM neste momento.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Cenário: Módulo não importa ou cmdlets não podem ser executados após importação

#### <a name="issue"></a>Problema

Um módulo não importa, ou importa com sucesso, mas não são extraídos cmdlets.

#### <a name="cause"></a>Causa

Algumas razões comuns para que um módulo pode não importar com sucesso para a Azure Automation são:

* A estrutura não corresponde à estrutura de que a Automação precisa.
* O módulo depende de outro módulo que não tenha sido implantado na sua conta de Automação.
* O módulo está a perder as suas dependências na pasta.
* O [cmdlet New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) está a ser utilizado para carregar o módulo, e não forneceu o caminho de armazenamento completo ou não carregou o módulo utilizando um URL acessível ao público.

#### <a name="resolution"></a>Resolução

Utilize qualquer uma destas soluções para corrigir o problema:

* Certifique-se de que o módulo segue o formato: ModuleName.zip -> MóduloName ou Número de Versão -> (ModuleName.psm1, ModuleName.psd1).
* Abra o ficheiro **.psd1** e veja se o módulo tem alguma dependência. Se o fizer, faça o upload destes módulos para a conta Automation.
* Certifique-se de que os ficheiros **.dll** referenciados estão presentes na pasta do módulo.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Cenário: Update-AzureModule.ps1 suspende ao atualizar módulos

#### <a name="issue"></a>Problema

Quando estiver a utilizar o [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook para atualizar os seus módulos Azure, o processo de atualização do módulo está suspenso.

#### <a name="cause"></a>Causa

Para este livro de execução, a definição predefinida para determinar quantos módulos são atualizados simultaneamente é 10. O processo de atualização é propenso a erros quando muitos módulos estão a ser atualizados ao mesmo tempo.

#### <a name="resolution"></a>Resolução

Não é comum que todos os módulos AzureRM ou Az sejam necessários na mesma conta De Automação. Só deve importar os módulos específicos de que necessita.

> [!NOTE]
> Evite importar a totalidade `Az.Automation` ou `AzureRM.Automation` módulo, que importa todos os módulos contidos.

Se o processo de atualização suspender, adicione o `SimultaneousModuleImportJobCount` parâmetro aoUpdate-AzureModules.ps1script e forneça um valor inferior ao padrão de  10. Se implementar esta lógica, tente começar com um valor de 3 ou 5. `SimultaneousModuleImportJobCount` é um parâmetro do runbook do sistema **Update-AutomationAzureModulesForAccount** que é usado para atualizar módulos Azure. Se fizer este ajuste, o processo de atualização é mais longo, mas tem mais hipóteses de completar. O exemplo a seguir mostra o parâmetro e onde colocá-lo no livro de recortes:

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Cenário: Não é possível criar ou atualizar uma conta Run As

#### <a name="issue"></a>Problema

Quando tenta criar ou atualizar uma conta Run As, recebe um erro semelhante ao seguinte:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Não tem as permissões necessárias para criar ou atualizar a conta Run As, ou o recurso está bloqueado a um nível de grupo de recursos.

#### <a name="resolution"></a>Resolução

Para criar ou atualizar uma conta Run As, tem de ter permissões adequadas para os vários recursos [utilizados](../automation-security-overview.md#permissions) pela conta Run As.

Se o problema for devido a uma fechadura, verifique se a fechadura pode ser removida. Em seguida, aceda ao recurso que está bloqueado no portal Azure, clique com o botão direito no bloqueio e selecione **Delete**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Cenário: Recebe o erro "Não é possível encontrar um ponto de entrada denominado 'GetPerAdapterInfo' em 'iplpapi.dll' da DLL" ao executar um livro de bordo

#### <a name="issue"></a>Problema

Ao executar um livro de bordo, recebe a seguinte exceção:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Causa

Este erro é provavelmente causado por uma [conta Run As](../automation-security-overview.md)incorretamente configurada .

#### <a name="resolution"></a>Resolução

Certifique-se de que a sua conta Run As está corretamente configurada. Em seguida, verifique se tem o código adequado no seu livro de aplicação para autenticar com o Azure. O exemplo a seguir mostra um corte de código para autenticar a Azure num livro de contas utilizando uma conta Run As.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Passos seguintes

Se este artigo não resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) . Esta é a conta oficial da Microsoft Azure para ligar a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Apoio**.
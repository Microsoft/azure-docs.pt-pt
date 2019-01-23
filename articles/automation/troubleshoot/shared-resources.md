---
title: Resolver problemas de erros com recursos de automatização do Azure partilhados
description: Aprenda a solucionar problemas com os recursos de automatização do Azure partilhados
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 911f592c43865ea8bdfe85c1ad1071c7112ae9b6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475446"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Resolver problemas de erros com recursos partilhados

Este artigo aborda soluções para resolver problemas que pode encontrar ao utilizar os recursos partilhados na automatização do Azure.

## <a name="modules"></a>Módulos

### <a name="module-stuck-importing"></a>Cenário: Um módulo está preso a importar

#### <a name="issue"></a>Problema

Um módulo fica preso no **importando** estado quando importa ou atualizar seus módulos na automatização do Azure.

#### <a name="cause"></a>Causa

Importar os módulos do PowerShell é um processo complexo de vários passo. Este processo introduz a possibilidade de um módulo não importar corretamente. Se este problema ocorrer, o módulo que está a importar pode estar bloqueado num estado transitório. Para saber mais sobre este processo, veja [importar um módulo do PowerShell]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Resolução

Para resolver este problema, tem de remover o módulo que fica preso no **importando** Estado, utilizando o [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) cmdlet. Pode, em seguida, repita a importação do módulo.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="module-fails-to-import"></a>Cenário: Ocorre uma falha do módulo importar ou cmdlets não pode ser executados depois de importar

#### <a name="issue"></a>Problema

Um módulo não consegue importar ou importa com êxito, mas não existem cmdlets são extraídos.

#### <a name="cause"></a>Causa

Algumas razões comuns que um módulo não pode importar com êxito para a automatização do Azure são:

* A estrutura não corresponde a estrutura que precisa de automatização que seja.
* O módulo depende de outro módulo que não tenha sido implantado para sua conta de automatização.
* O módulo está em falta as respetivas dependências na pasta.
* O `New-AzureRmAutomationModule` cmdlet está a ser utilizado para carregar o módulo, e ainda não tendo em conta o caminho de armazenamento completo ou não tiver carregado o módulo utilizando um URL acessível publicamente.

#### <a name="resolution"></a>Resolução

Qualquer uma das seguintes soluções resolver o problema:

* Certifique-se de que o módulo segue o formato seguinte: ModuleName.Zip **->** ModuleName ou um número de versão **->** (ModuleName.psm1, ModuleName.psd1)
* Abra o ficheiro. psd1 e ver se o módulo tiver dependências. Se assim for, carregar esses módulos para a conta de automatização.
* Certifique-se de que quaisquer DLLs referenciado estão presentes na pasta do módulo.

### <a name="all-modules-suspended"></a>Cenário: Suspende a AzureModule.ps1 de atualização ao atualizar módulos

#### <a name="issue"></a>Problema

Ao utilizar o [AzureModule.ps1 atualização](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook para atualizar os módulos do Azure, a atualização de módulo o processo de atualização é suspenso.

#### <a name="cause"></a>Causa

A configuração padrão para determinar quantos módulos for atualizados simultaneamente é 10 ao utilizar o `Update-AzureModule.ps1` script. O processo de atualização é propenso a erros quando demasiados módulos estão a ser atualizados ao mesmo tempo.

#### <a name="resolution"></a>Resolução

Não é comum que todos os módulos AzureRM são necessários na mesma conta de automatização. É recomendado para importar apenas os módulos AzureRM que precisa.

> [!NOTE]
> Evitar a importar os **AzureRM** módulo. Importar os **AzureRM** módulos faz com que tudo **AzureRM.\***  módulos a serem importados, não se trata de recommened.

Se o processo de atualização suspende, terá de adicionar o `SimultaneousModuleImportJobCount` parâmetro para o `Update-AzureModules.ps1` do script e fornecer um valor inferior a predefinição é 10. Recomenda-se se implementar essa lógica, começar com um valor de 3 ou 5. `SimultaneousModuleImportJobCount` é um parâmetro do `Update-AutomationAzureModulesForAccount` runbook de sistema que é utilizada para atualizar módulos do Azure. Esta alteração torna o processo de execução já, mas tem uma chance maior de conclusão. O exemplo seguinte mostra o parâmetro e onde colocá-lo no runbook:

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

## <a name="run-as-accounts"></a>Contas Run as

### <a name="unable-create-update"></a>Cenário: Não for possível criar ou atualizar uma conta Run As

#### <a name="issue"></a>Problema

Ao tentar criar ou atualizar uma conta Run As, receberá um erro semelhante a seguinte mensagem de erro:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Não tem as permissões que necessita criar ou atualizar a conta Run As ou o recurso está bloqueado num nível de grupo de recursos.

#### <a name="resolution"></a>Resolução

Para criar ou atualizar uma conta Run As, tem de ter permissões adequadas para os vários recursos utilizados pela conta Run As. Para saber mais sobre as permissões necessárias para criar ou atualizar uma conta Run As, consulte [permissões de conta Run As](../manage-runas-account.md#permissions).

Se o problema é devido a um bloqueio, certifique-se de que o bloqueio está ok para removê-lo. Em seguida, navegue para o recurso que está bloqueado, o bloqueio com o botão direito e escolher **eliminar** ao remover o bloqueio.

## <a name="next-steps"></a>Passos Seguintes

Se não vir o seu problema ou não é possível resolver o problema, visite um dos seguintes canais de suporte mais:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
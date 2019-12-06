---
title: Solucionar erros com recursos compartilhados da automação do Azure
description: Saiba como solucionar problemas com recursos compartilhados da automação do Azure
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 9313b042433489307a2bd2822a96d1e0e127362b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849297"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Solucionar erros com recursos compartilhados

Este artigo aborda soluções para resolver problemas que você pode encontrar ao usar os recursos compartilhados na automação do Azure.

## <a name="modules"></a>Módulos

### <a name="module-stuck-importing"></a>Cenário: um módulo está preso importando

#### <a name="issue"></a>Problema

Um módulo está preso no estado de **importação** quando você importa ou atualiza seus módulos na automação do Azure.

#### <a name="cause"></a>Causa

A importação de módulos do PowerShell é um processo complexo de várias etapas. Esse processo introduz a possibilidade de um módulo não ser importado corretamente. Se esse problema ocorrer, o módulo que você está importando poderá ficar preso em um estado transitório. Para saber mais sobre esse processo, consulte [importando um módulo do PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Resolução

Para resolver esse problema, você deve remover o módulo que está preso no estado de **importação** usando o cmdlet [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) . Em seguida, você pode tentar importar o módulo novamente.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Cenário: os módulos AzureRM estão presos na importação depois de tentar atualizá-los

#### <a name="issue"></a>Problema

Uma faixa com a seguinte mensagem permanece em sua conta depois de tentar atualizar os módulos do AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Causa

Há um problema conhecido com a atualização dos módulos AzureRM em uma conta de automação que está em um grupo de recursos com um nome numérico que começa com 0.

#### <a name="resolution"></a>Resolução

Para atualizar seus módulos do Azure em sua conta de automação, ele deve estar em um grupo de recursos que tenha um nome alfanumérico. Os grupos de recursos com nomes numéricos que começam com 0 não podem atualizar os módulos AzureRM no momento.

### <a name="module-fails-to-import"></a>Cenário: falha na importação do módulo ou os cmdlets não podem ser executados após a importação

#### <a name="issue"></a>Problema

Um módulo não é importado ou importado com êxito, mas nenhum cmdlet é extraído.

#### <a name="cause"></a>Causa

Alguns motivos comuns pelos quais um módulo pode não ser importado com êxito para a automação do Azure são:

* A estrutura não corresponde à estrutura na qual a automação precisa estar.
* O módulo depende de outro módulo que não foi implantado em sua conta de automação.
* O módulo não tem suas dependências na pasta.
* O cmdlet `New-AzureRmAutomationModule` está sendo usado para carregar o módulo e você não recebeu o caminho de armazenamento completo ou não carregou o módulo usando uma URL publicamente acessível.

#### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Verifique se o módulo segue o seguinte formato: ModuleName. zip **->** móduloname ou número de versão **->** (ModuleName. psm1, ModuleName. psd1)
* Abra o arquivo. psd1 e veja se o módulo tem alguma dependência. Se tiver, carregue esses módulos na conta de automação.
* Certifique-se de que quaisquer. DLLs referenciadas estejam presentes na pasta do módulo.

### <a name="all-modules-suspended"></a>Cenário: Update-AzureModule. ps1 suspende ao atualizar módulos

#### <a name="issue"></a>Problema

Ao usar o runbook [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) para atualizar seus módulos do Azure, o módulo atualizar o processo de atualização será suspenso.

#### <a name="cause"></a>Causa

A configuração padrão para determinar quantos módulos são atualizados simultaneamente é 10 ao usar o script de `Update-AzureModule.ps1`. O processo de atualização é propenso a erros quando muitos módulos estão sendo atualizados ao mesmo tempo.

#### <a name="resolution"></a>Resolução

Não é comum que todos os módulos AzureRM sejam necessários na mesma conta de automação. É recomendável importar apenas os módulos AzureRM necessários.

> [!NOTE]
> Evite importar o módulo **AzureRM** . A importação dos módulos **AzureRM** faz com que todos os módulos **AzureRM.\*** sejam importados, isso não é recomendados.

Se o processo de atualização suspender, você precisará adicionar o parâmetro `SimultaneousModuleImportJobCount` ao script `Update-AzureModules.ps1` e fornecer um valor menor do que o padrão que é 10. É recomendável se você implementar essa lógica, para começar com um valor de 3 ou 5. `SimultaneousModuleImportJobCount` é um parâmetro do runbook do sistema `Update-AutomationAzureModulesForAccount` usado para atualizar os módulos do Azure. Essa alteração faz com que o processo seja executado por mais tempo, mas tem uma chance melhor de concluir. O exemplo a seguir mostra o parâmetro e onde colocá-lo no runbook:

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

### <a name="unable-create-update"></a>Cenário: não é possível criar ou atualizar uma conta Executar como

#### <a name="issue"></a>Problema

Ao tentar criar ou atualizar uma conta Executar como, você receberá um erro semelhante à seguinte mensagem de erro:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Você não tem as permissões necessárias para criar ou atualizar a conta Executar como ou o recurso está bloqueado em um nível de grupo de recursos.

#### <a name="resolution"></a>Resolução

Para criar ou atualizar uma conta Executar como, você deve ter as permissões apropriadas para os vários recursos usados pela conta Executar como. Para saber mais sobre as permissões necessárias para criar ou atualizar uma conta Executar como, consulte [permissões da conta Executar como](../manage-runas-account.md#permissions).

Se o problema for devido a um bloqueio, verifique se o bloqueio está OK para removê-lo. Em seguida, navegue até o recurso que está bloqueado, clique com o botão direito do mouse no bloqueio e escolha **excluir** para remover o bloqueio.

### <a name="iphelper"></a>Cenário: você recebe o erro "não foi possível encontrar um ponto de entrada chamado ' GetPerAdapterInfo ' na DLL ' iplpapi. dll '" ao executar um runbook.

#### <a name="issue"></a>Problema

Ao executar um runbook, você receberá a seguinte exceção:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Causa

Esse erro é provavelmente causado por uma [conta Executar como](../manage-runas-account.md)configurada incorretamente.

#### <a name="resolution"></a>Resolução

Verifique se a [conta Executar como](../manage-runas-account.md) está configurada corretamente. Depois de configurado corretamente, verifique se você tem o código adequado em seu runbook para autenticar com o Azure. O exemplo a seguir mostra um trecho de código para autenticar no Azure em um runbook usando uma conta Executar como.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você poderá arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

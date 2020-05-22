---
title: Atualizar módulos PowerShell Azure na Automação Azure
description: Este artigo diz como atualizar módulos comuns do PowerShell fornecidos por padrão na Automação Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 6b3832662d2d03c7218da20d414a43a99a9bac8e
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744201"
---
# <a name="update-azure-powershell-modules"></a>Atualizar módulos do Azure PowerShell

Os módulos PowerShell mais comuns são fornecidos por padrão em cada conta de Automação. Ver [módulos Predefinidos](shared-resources/modules.md#default-modules). À medida que a equipa Azure atualiza regularmente os módulos Azure, podem ocorrer alterações com os cmdlets incluídos. Estas alterações, por exemplo, renomear um parâmetro ou deprecizar um cmdlet inteiramente, podem afetar negativamente os seus livros de execução. 

> [!NOTE]
> Não é possível eliminar módulos globais, que são módulos que a Automation fornece fora da caixa.

## <a name="set-up-an-automation-account"></a>Criar uma conta de Automação

Para evitar afetar os seus livros de execução e os processos que automatizam, certifique-se de testar e validar à medida que faz atualizações. Se não tiver uma conta automatizada dedicada destinada a este fim, considere criar uma para que possa testar muitos cenários diferentes durante o desenvolvimento dos seus livros de execução. Este teste deve incluir alterações iterativas, tais como a atualização dos módulos PowerShell.

Certifique-se de que a sua conta Deautomação tem uma [conta Azure Run Tal como](manage-runas-account.md) a credencial de conta criada.

Se desenvolver os seus scripts localmente, recomenda-se que tenha as mesmas versões de módulos localmente que tem na sua conta De automação ao testar para garantir que recebe os mesmos resultados. Após a validação dos resultados e aplicar todas as alterações necessárias, pode mover as alterações para a produção.

> [!NOTE]
> Uma nova conta de Automação pode não conter os módulos mais recentes.

## <a name="obtain-a-runbook-to-use-for-updates"></a>Obtenha um livro de execução para utilizar para atualizações

Para atualizar os módulos Azure na sua conta Automation, tem de utilizar o livro de execução [Update-AutomationAzureModulesForAccount,](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) que está disponível como fonte aberta. Para começar a utilizar este livro de execução para atualizar os seus módulos Azure, descarregue-o a partir do repositório GitHub. Em seguida, pode importá-lo para a sua conta de Automação ou executá-la como um script. Para aprender a importar um livro de execução na sua conta de Automação, consulte importar um livro de [execução](manage-runbooks.md#import-a-runbook).

O livro de execução **Update-AutomationAzureModulesForAccount** suporta a atualização dos módulos Azure, AzureRM e Az por padrão. Reveja o livro de execução de [módulos Update Azure README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) para obter mais informações sobre a atualização de módulos Az.Automation com este livro de execução. Existem fatores importantes adicionais que deve ter em conta ao utilizar os módulos Az na sua conta Automation. Para saber mais, consulte [Gerir módulos em Automação Azure.](shared-resources/modules.md)

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Utilize o código de execução da atualização como um script regular powerShell

Pode utilizar o código do livro de executantes como um script powerShell regular em vez de um livro de execução. Para isso, inscreva-se no Azure usando primeiro o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) e, em seguida, passe `-Login $false` para o script.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Use o livro de atualização sobre nuvens soberanas

Para utilizar este livro de corridas em nuvens soberanas, use o `AzEnvironment` parâmetro para passar o ambiente correto para o livro de corridas. Os valores aceitáveis são AzureCloud (nuvem pública azure), AzureChinaCloud, AzureGermanCloud e AzureUSGovernment. Estes valores podem ser recuperados utilizando `Get-AzEnvironment | select Name` . Se não passar um valor a este cmdlet, o livro de execução não passa para o AzureCloud.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Utilize o livro de atualizações para atualizar uma versão específica do módulo

Se pretender utilizar uma versão específica do módulo Azure PowerShell em vez do mais recente módulo disponível na PowerShell Gallery, passe estas versões para o parâmetro opcional `ModuleVersionOverrides` do livro de execução **Update-AutomationAzureModulesForAccount.** Por exemplo, consulte o livro de execução [Update-AutomationAzureModulesForAccount.ps1.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) Os módulos Azure PowerShell que não são mencionados no `ModuleVersionOverrides` parâmetro são atualizados com as versões mais recentes do módulo na PowerShell Gallery. Se não passar nada para o `ModuleVersionOverrides` parâmetro, todos os módulos são atualizados com as versões mais recentes do módulo na PowerShell Gallery. Este comportamento é o mesmo para o botão **Módulos Update Azure** no portal Azure.

## <a name="next-steps"></a>Passos seguintes

* [Utilizar módulos na Automatização do Azure](shared-resources/modules.md)
* [Atualização do livro de módulos Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)

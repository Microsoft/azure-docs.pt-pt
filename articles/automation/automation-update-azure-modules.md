---
title: Atualizar módulos Azure PowerShell na Azure Automation
description: Este artigo diz como atualizar os módulos Azure PowerShell comuns fornecidos por padrão na Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: f05c3247ecdd77fe4cf02db70c7ba4b2c534f959
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055353"
---
# <a name="update-azure-powershell-modules"></a>Atualizar módulos do Azure PowerShell

Os módulos PowerShell mais comuns são fornecidos por padrão em cada conta Automation. Ver [módulos predefinidos](shared-resources/modules.md#default-modules). À medida que a equipa do Azure atualiza regularmente os módulos Azure, podem ocorrer alterações com os cmdlets incluídos. Estas alterações, por exemplo, renomeando um parâmetro ou depreciando totalmente um cmdlet, podem afetar negativamente os seus livros de execução. 

> [!NOTE]
> Não é possível eliminar módulos globais, que são módulos que a Automação fornece fora da caixa.

## <a name="set-up-an-automation-account"></a>Criar uma conta de Automação

Para evitar impactos nos seus livros de execução e nos processos que automatizam, certifique-se de testar e validar à medida que faz atualizações. Se não tiver uma conta de Automação dedicada destinada a este fim, considere criar uma para que possa testar vários cenários diferentes durante o desenvolvimento dos seus runbooks. Este teste deve incluir alterações iterativas, tais como a atualização dos módulos PowerShell.

Certifique-se de que a sua conta Automation tem uma [conta Azure Run Como](automation-security-overview.md#run-as-accounts) foi criada.

Se desenvolver os seus scripts localmente, recomenda-se ter as mesmas versões de módulo localmente que tem na sua conta de Automação ao testar para garantir que recebe os mesmos resultados. Depois de validados os resultados e de ter aplicado quaisquer alterações necessárias, pode mover as alterações para a produção.

> [!NOTE]
> Uma nova conta Automation pode não conter os módulos mais recentes.

## <a name="obtain-a-runbook-to-use-for-updates"></a>Obtenha um livro de execução para usar para atualizações

Para atualizar os módulos Azure na sua conta Automation, tem de utilizar o manual [de actualização-AutomationAzureModulesForAccount,](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) que está disponível como fonte aberta. Para começar a utilizar este runbook para atualizar os seus módulos Azure, descarregue-o a partir do repositório GitHub. Em seguida, pode importá-lo na sua conta de Automação ou executá-lo como um script. Para aprender a importar um livro na sua conta de Automação, consulte [importar um livro de contas](manage-runbooks.md#import-a-runbook).

O **livro de bordo-AutomationAzureModulesForAccount** suporta a atualização dos módulos AzureRM e Az por padrão. Reveja o runbook de [módulos Azure Update README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) para obter mais informações sobre a atualização dos módulos Az.Automation com este runbook. Existem fatores importantes adicionais que deve ter em conta ao utilizar os módulos Az na sua conta Demôm automação. Para saber mais, consulte [Gerir os módulos na Azure Automation.](shared-resources/modules.md)

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Use o código do runbook de atualização como um script regular do PowerShell

Pode utilizar o código do livro de execução como um script PowerShell regular em vez de um livro de execução. Para isso, inscreva-se no Azure usando primeiro o [cmdlet Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e, em seguida, passe `-Login $false` para o script.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Use o livro de atualização em nuvens soberanas

Para utilizar este livro de recortes em nuvens soberanas, utilize o `AzEnvironment` parâmetro para passar o ambiente correto para o livro de recortes. Os valores aceitáveis são AzureCloud (nuvem pública Azure), AzureChinaCloud, AzureGermanCloud e AzureUSGovernment. Estes valores podem ser recuperados utilizando `Get-AzEnvironment | select Name` . Se não passar um valor a este cmdlet, o livro de execuções fica em incumprimento para o AzureCloud.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Utilize o runbook de atualização para atualizar uma versão específica do módulo

Se pretender utilizar uma versão específica do módulo Azure PowerShell em vez do módulo mais recente disponível na PowerShell Gallery, passe estas versões para o parâmetro opcional `ModuleVersionOverrides` do runbook **Update-AutomationAzureModulesForAccount.** Por exemplo, consulte o  [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) livro. Os módulos Azure PowerShell que não são mencionados no `ModuleVersionOverrides` parâmetro são atualizados com as versões mais recentes do módulo na PowerShell Gallery. Se não passar nada ao `ModuleVersionOverrides` parâmetro, todos os módulos serão atualizados com as versões mais recentes do módulo na PowerShell Gallery. Este comportamento é o mesmo para o botão **'Azure Modules'** de atualização no portal Azure.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a utilização de módulos, consulte [Gerir os módulos na Azure Automation.](shared-resources/modules.md)
* Para obter informações sobre o runbook de atualização, consulte o runbook de [módulos Update Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).

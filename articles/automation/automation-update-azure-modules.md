---
title: Atualizar módulos Azure na Automação Azure
description: Este artigo descreve como pode agora atualizar módulos comuns do PowerShell do Azure fornecidos por padrão na Automação Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3d7eaae452f307b350c111452b819576cf7f17e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420474"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar os módulos do Azure PowerShell na Automatização do Azure

Para atualizar os módulos Azure na sua Conta de Automação é necessário utilizar o livro de execução de [módulos Update Azure,](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)que está disponível como fonte aberta. Para começar a utilizar o livro de execução **Update-AutomationAzureModulesForAccount** para atualizar os seus módulos Azure, descarregue-o a partir do [repositório de releções de módulos Update Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) no GitHub. Em seguida, pode importá-lo para a sua Conta de Automação ou executá-la como um script. Para aprender a importar um livro na sua Conta de Automação, consulte importar um livro de [execução](manage-runbooks.md#import-a-runbook).

Os módulos Mais comuns do PowerShell azureRM são fornecidos por padrão em cada conta de Automação. A equipa azure atualiza regularmente os módulos Azure, pelo que para manter-se atualizado, pretende utilizar o livro de execução [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) para atualizar os módulos nas suas contas De automação.

Como os módulos são atualizados regularmente pelo grupo do produto, podem ocorrer alterações com os cmdlets incluídos. Esta ação pode ter um impacto negativo nos seus livros de execução dependendo do tipo de mudança, como renomear um parâmetro ou depreciar totalmente um cmdlet.

Para evitar afetar os seus livros de execução e os processos que automatizam, testam e validam antes de prosseguir. Se não tiver uma conta automatizada dedicada destinada a este fim, considere criar uma para que possa testar muitos cenários diferentes durante o desenvolvimento dos seus livros de execução. Este teste deve incluir alterações iterativas, tais como a atualização dos módulos PowerShell.

Se desenvolver os seus scripts localmente, recomenda-se que tenha as mesmas versões de módulos localmente que tem na sua Conta de Automação ao testar para garantir que receberá os mesmos resultados. Após a validação dos resultados e aplicar todas as alterações necessárias, pode mover as alterações para a produção.

> [!NOTE]
> Uma nova conta de Automação pode não conter os módulos mais recentes.

> [!NOTE]
> Não será capaz de eliminar módulos globais - módulos que a Automation fornece fora da caixa.

## <a name="considerations"></a>Considerações

Seguem-se algumas considerações a ter em conta ao utilizar este processo para atualizar os módulos Azure:

* Este livro de execução suporta a atualização dos módulos **Azure** e **AzureRm** por padrão. Este livro de execução também suporta a atualização dos módulos **Az.** Reveja o livro de execução de [módulos Update Azure README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) para obter mais informações sobre a atualização `Az` de módulos com este livro de execução. Existem fatores importantes adicionais que precisa `Az` de ter em conta ao utilizar os módulos na sua Conta de Automação, para saber mais, ver [Utilizando módulos Az na sua Conta de Automação](az-modules.md).

* Antes de iniciar este livro de execução, certifique-se de que a sua conta Deautomatem uma conta [Azure Run Tal como](manage-runas-account.md) a credencial de conta criada.

* Pode utilizar este código como um script powerShell regular em vez de um livro de execução: basta `-Login $false` iniciar sessão no Azure usando primeiro o comando [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) e, em seguida, passar para o script.

* Para utilizar este livro de corridas `AzureRmEnvironment` nas nuvens soberanas, use o parâmetro para passar o ambiente correto para o livro de corridas.  Os valores aceitáveis são **AzureCloud,** **AzureChinaCloud,** **AzureGermanCloud**e **AzureUSGovernment.** Estes valores podem ser `Get-AzureRmEnvironment | select Name`recuperados da utilização . Se não passar um valor a este parâmetro, o livro de corridas será padrão para a nuvem pública **AzureCloud**

* Se pretender utilizar uma versão específica do módulo Azure PowerShell em vez das mais recentes `ModuleVersionOverrides` disponíveis na PowerShell Gallery, passe estas versões para o parâmetro opcional do livro de execução **Update-AutomationAzureModulesForAccount.** Por exemplo, consulte o livro de execução [Update-AutomationAzureModulesForAccount.ps1.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Os módulos Azure PowerShell que `ModuleVersionOverrides` não são mencionados no parâmetro são atualizados com as versões mais recentes do módulo na PowerShell Gallery. Se não passar `ModuleVersionOverrides` nada para o parâmetro, todos os módulos são atualizados com as versões mais recentes do módulo na PowerShell Gallery. Este comportamento é o mesmo que o botão **Módulos Update Azure.**

## <a name="next-steps"></a>Passos seguintes

Visite o livro de execução de [módulos Update Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) de código aberto para saber mais sobre o mesmo.

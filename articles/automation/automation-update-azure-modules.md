---
title: Atualizar módulos Azure na Automação Azure
description: Este artigo descreve como pode agora atualizar módulos comuns do PowerShell do Azure fornecidos por padrão na Automação Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: ba926ccbb069c8620259514e0a64c56957529a0f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617471"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar os módulos do Azure PowerShell na Automatização do Azure

Para atualizar os módulos Azure na sua conta Automation é necessário utilizar o livro de execução de [módulos Update Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), que está disponível como fonte aberta. Para começar a utilizar o livro de execução **Update-AutomationAzureModulesForAccount** para atualizar os seus módulos Azure, descarregue-o a partir do [repositório de releções de módulos Update Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) no GitHub. Em seguida, pode importá-lo para a sua conta de Automação ou executá-la como um script. Para aprender a importar um livro de execução na sua conta de Automação, consulte importar um livro de [execução](manage-runbooks.md#importing-a-runbook).

Os módulos PowerShell mais comuns são fornecidos por padrão em cada conta de Automação. A equipa Azure atualiza regularmente os módulos Azure. Portanto, para manter os módulos nas suas contas de Automação atualizados, deve utilizar o livro de execução [Update-AutomationAzureModulesForAccount.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)

Como os módulos são atualizados regularmente pelo grupo do produto, podem ocorrer alterações com os cmdlets incluídos. Estas alterações, por exemplo, renomear um parâmetro ou deprecizar um cmdlet inteiramente, podem afetar negativamente os seus livros de execução.

Para evitar afetar os seus livros de execução e os processos que automatizam, testam e validam antes de prosseguir. Se não tiver uma conta automatizada dedicada destinada a este fim, considere criar uma para que possa testar muitos cenários diferentes durante o desenvolvimento dos seus livros de execução. Este teste deve incluir alterações iterativas, tais como a atualização dos módulos PowerShell.

Se desenvolver os seus scripts localmente, recomenda-se que tenha as mesmas versões de módulos localmente que tem na sua conta De automação ao testar para garantir que receberá os mesmos resultados. Após a validação dos resultados e aplicar todas as alterações necessárias, pode mover as alterações para a produção.

> [!NOTE]
> Uma nova conta de Automação pode não conter os módulos mais recentes.

> [!NOTE]
> Não será possível eliminar módulos globais, que são módulos que a Automation fornece fora da caixa.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Considerações

Seguem-se algumas considerações a ter em conta ao utilizar este artigo para atualizar os módulos Azure:

* O livro de execução descrito neste artigo suporta a atualização dos módulos Azure, AzureRM e Az por padrão. Reveja o livro de execução de [módulos Update Azure README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) para obter mais informações sobre a atualização de módulos Az.Automation com este livro de execução. Existem fatores importantes adicionais que deve ter em conta ao utilizar os módulos Az na sua conta Automation. Para saber mais, consulte [Az utilizando módulos Az na sua conta Automation](az-modules.md).

* Antes de iniciar este livro de execução, certifique-se de que a sua conta Deautomatem uma conta [Azure Run Tal como](manage-runas-account.md) a credencial de conta criada.

* Pode utilizar este código como um script powerShell regular em vez de um livro de execução: basta `-Login $false` iniciar sessão no Azure usando primeiro o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) e, em seguida, passar para o script.

* Para utilizar este livro de corridas `AzEnvironment` nas nuvens soberanas, use o parâmetro para passar o ambiente correto para o livro de corridas.  Os valores aceitáveis são AzureCloud (nuvem pública azure), AzureChinaCloud, AzureGermanCloud e AzureUSGovernment. Estes valores podem `Get-AzEnvironment | select Name`ser recuperados utilizando . Se não passar um valor a este cmdlet, o livro de execução não passa para o AzureCloud.

* Se pretender utilizar uma versão específica do módulo Azure PowerShell em vez do mais recente `ModuleVersionOverrides` módulo disponível na PowerShell Gallery, passe estas versões para o parâmetro opcional do livro de execução **Update-AutomationAzureModulesForAccount.** Por exemplo, consulte o livro de execução [Update-AutomationAzureModulesForAccount.ps1.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Os módulos Azure PowerShell que `ModuleVersionOverrides` não são mencionados no parâmetro são atualizados com as versões mais recentes do módulo na PowerShell Gallery. Se não passar `ModuleVersionOverrides` nada para o parâmetro, todos os módulos são atualizados com as versões mais recentes do módulo na PowerShell Gallery. Este comportamento é o mesmo que o botão **Módulos Update Azure.**

## <a name="next-steps"></a>Passos seguintes

Visite o livro de execução de [módulos Update Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) de código aberto para saber mais sobre o mesmo.

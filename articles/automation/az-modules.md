---
title: Utilizar módulos Az na Automatização do Azure
description: Este artigo fornece informações sobre o uso de módulos AZ na automação do Azure
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: dfbf54c19aef00cbda886a4531797cda7ef3a191
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986109"
---
# <a name="az-module-support-in-azure-automation"></a>Suporte ao módulo AZ na automação do Azure

A automação do Azure dá suporte à capacidade de usar o [módulo AZ do Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) em seus runbooks. O módulo AZ não é importado automaticamente em nenhuma conta de automação nova ou existente. Este artigo discute como usar módulos AZ com a automação do Azure.

## <a name="considerations"></a>Considerações

Há muitas coisas a serem levadas em consideração ao usar o módulo AZ na automação do Azure. Runbooks e módulos podem ser usados por soluções de nível superior em sua conta de automação. Editar runbooks ou atualizar módulos pode potencialmente causar problemas com seus runbooks. Você deve testar todos os runbooks e soluções com cuidado em uma conta de automação separada antes de importar os novos módulos de `Az`. Quaisquer modificações nos módulos podem afetar negativamente a solução [Iniciar/Parar.](automation-solution-vm-management.md) Não recomendamos alterar módulos e livros de execução em Contas de Automação que contenham quaisquer soluções. Esse comportamento não é específico para os módulos AZ. Esse comportamento deve ser levado em consideração ao introduzir qualquer alteração em sua conta de automação.

Importar um módulo `Az` na sua conta de automação não importa automaticamente o módulo na sessão do PowerShell que os runbooks usam. Os módulos são importados para a sessão do PowerShell nas seguintes situações:

* Quando um cmdlet de um módulo é invocado de um runbook
* Quando um runbook importa explicitamente com o cmdlet `Import-Module`
* Quando outro módulo, dependendo do módulo, é importado para uma sessão do PowerShell

> [!IMPORTANT]
> É importante verificar se os runbooks em uma conta de automação importam somente `Az` ou `AzureRM` módulos nas sessões do PowerShell usadas por runbooks e não ambos. Se `Az` for importada antes `AzureRM` num livro de execução, o livro de execução estará completo, mas um [erro que refira o método get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) mostrará nos fluxos de trabalho e os cmdlets podem não ter sido executados corretamente. Se você importar `AzureRM` e `Az` o runbook ainda será concluído, mas você verá um erro nos fluxos de trabalho declarando que ambos `Az` e `AzureRM` não podem ser importados na mesma sessão ou usados no mesmo runbook.

## <a name="migrating-to-az-modules"></a>Migrando para módulos AZ

É recomendável testar a migração para usar módulos AZ em vez de módulos AzureRM em uma conta de automação de teste. Depois que a conta de automação tiver sido criada, você poderá usar as seguintes etapas para garantir que a migração fique tranqüila:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Parar e cancelar o agendamento de todos os runbooks que usam módulos AzureRM

Para garantir que você não execute runbooks existentes que usam cmdlets `AzureRM`, você deve parar e cancelar o agendamento de todos os runbooks que usam módulos `AzureRM`. Você pode ver quais agendamentos existem e quais agendas devem ser removidas executando o seguinte exemplo:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

É importante examinar cada agenda separadamente para garantir que você possa reagendá-la no futuro para seus runbooks, se necessário.

### <a name="import-the-az-modules"></a>Importar os módulos AZ

Importe apenas os módulos AZ necessários para seus runbooks. Não importe o módulo ROLLUP `Az`, pois ele inclui todos os módulos `Az.*` a serem importados. Essa diretriz é a mesma para todos os módulos.

O módulo [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma dependência para os outros módulos de `Az.*`. Por esse motivo, esse módulo precisa ser importado para sua conta de automação antes da importação de outros módulos.

Na sua conta de automação, selecione **módulos** em **recursos compartilhados**. Clique em **procurar na Galeria** para abrir a página procurar na **Galeria** .  Na barra de pesquisa, insira o nome do módulo (como `Az.Accounts`). Na página módulo do PowerShell, clique em **importar** para importar o módulo para sua conta de automação.

![Importar módulos da conta de automação](media/az-modules/import-module.png)

Esse processo de importação também pode ser feito por meio do [Galeria do PowerShell](https://www.powershellgallery.com) pesquisando o módulo. Depois de encontrar o módulo, selecione-o e, na guia **automação do Azure** , clique em **implantar na automação do Azure**.

![Importar módulos diretamente da Galeria](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testar seus runbooks

Depois que os módulos de `Az` são importados em sua conta de automação, agora você pode começar a editar seus runbooks para usar o módulo AZ em vez disso. A maioria dos cmdlets tem o mesmo nome, exceto `AzureRM` foi alterada para `Az`. Para obter uma lista de módulos que não seguem esse processo, consulte [lista de exceções](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Uma maneira de testar seus runbooks antes de modificar o runbook para usar os novos cmdlets é usando `Enable-AzureRMAlias -Scope Process` no início de um runbook. Ao adicioná-lo ao seu runbook, o runbook pode ser executado sem alterações.

## <a name="after-migration-details"></a>Após os detalhes da migração

Depois que a migração for concluída, não inicie runbooks usando `AzureRM` módulos na conta mais. Também é recomendável não importar ou atualizar os módulos `AzureRM` nesta conta. A partir deste momento, considere essa conta migrada para `Az`e opere somente com os módulos `Az`. Quando uma nova conta de automação for criada, os módulos existentes do `AzureRM` ainda serão instalados e os runbooks do tutorial ainda serão criados com `AzureRM` cmdlets. Esses runbooks não devem ser executados.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar módulos AZ, confira [introdução ao módulo AZ](/powershell/azure/get-started-azureps?view=azps-1.1.0).

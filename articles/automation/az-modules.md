---
title: Utilizar módulos Az na Automatização do Azure
description: Este artigo fornece informações utilizando módulos Az na Automação Azure
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: dfbf54c19aef00cbda886a4531797cda7ef3a191
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986109"
---
# <a name="az-module-support-in-azure-automation"></a>Suporte do módulo Az na Automatização do Azure

A automatização Azure suporta a capacidade de utilizar o [módulo Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) nos seus livros de execução. O módulo Az não é importado automaticamente em quaisquer contas de automação novas ou existentes. Este artigo discute como usar módulos Az com a Automação Azure.

## <a name="considerations"></a>Considerações

Há muitas coisas a ter em conta ao usar o módulo Az na Automação Azure. Os livros e módulos podem ser utilizados por soluções de nível superior na sua Conta de Automação. Editar livros de execução ou atualizar módulos pode potencialmente causar problemas com os seus livros de execução. Deve testar cuidadosamente todos os livros e soluções numa Conta `Az` de Automação separada antes de importar os novos módulos. Quaisquer modificações nos módulos podem afetar negativamente a solução [Iniciar/Parar.](automation-solution-vm-management.md) Não recomendamos alterar módulos e livros de execução em Contas de Automação que contenham quaisquer soluções. Este comportamento não é específico dos módulos Az. Este comportamento deve ser tomado em consideração ao introduzir quaisquer alterações na sua Conta de Automação.

Importar um `Az` módulo na sua Conta de Automação não importa automaticamente o módulo na sessão PowerShell que os livros de execução usam. Os módulos são importados para a sessão PowerShell nas seguintes situações:

* Quando um cmdlet de um módulo é invocado a partir de um livro de corridas
* Quando um livro de rume `Import-Module` o importa explicitamente com o cmdlet
* Quando outro módulo dependendo do módulo é importado para uma sessão powerShell

> [!IMPORTANT]
> É importante certificar-se de que os livros `Az` de `AzureRM` execução numa Conta de Automação apenas importam ou módulos nas sessões PowerShell utilizadas por livros de execução e não ambos. Se `Az` for importado antes `AzureRM` num livro de execução, o livro de execução estará completo, mas um erro que [refira o método get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) mostrará nos fluxos de trabalho e os cmdlets podem não ter sido executados corretamente. Se importar `AzureRM` e, em seguida, `Az` o seu livro de execução ainda estará `Az` `AzureRM` completo, mas verá um erro nos fluxos de trabalho afirmando que ambos e não podem ser importados na mesma sessão ou usados no mesmo livro de execução.

## <a name="migrating-to-az-modules"></a>Migrar para módulos Az

Recomenda-se que teste a migração para a utilização de módulos Az em vez de módulos AzureRM numa conta de automatização de teste. Uma vez criada a Conta de Automação, pode utilizar os seguintes passos para garantir que a sua migração decorra sem problemas:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Parar e desmarcar todos os runbook que utilizam módulos AzureRM

Para garantir que não executa os livros `AzureRM` de execução existentes que utilizem cmdlets, deve parar e desmarcar todos os livros que utilizam `AzureRM` módulos. Pode ver quais os horários existentes e quais os horários que devem ser removidos executando o seguinte exemplo:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

É importante rever cada horário separadamente para garantir que pode reagendar no futuro para os seus livros de execução, se necessário.

### <a name="import-the-az-modules"></a>Importar os módulos Az

Apenas importe os módulos Az que são necessários para os seus livros de execução. Não importe o `Az` módulo de rollup, `Az.*` pois inclui todos os módulos a importar. Esta orientação é a mesma para todos os módulos.

O módulo [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma `Az.*` dependência para os outros módulos. Por esta razão, este módulo precisa de ser importado para a sua Conta de Automação antes de importar quaisquer outros módulos.

Na sua Conta de Automação, selecione **Módulos** em **Recursos Partilhados.** Clique na **Galeria Browse** para abrir a página **da Galeria De Navegação.**  Na barra de pesquisa, introduza `Az.Accounts`o nome do módulo (por exemplo). Na página do Módulo PowerShell, clique **em Importar** para importar o módulo para a sua Conta de Automação.

![Módulos de importação da Conta de Automação](media/az-modules/import-module.png)

Este processo de importação também pode ser feito através da [PowerShell Gallery,](https://www.powershellgallery.com) procurando o módulo. Assim que encontrar o módulo, selecione-o e sob o **separador De automação Azure,** clique em **Implementar para automação Azure**.

![Importar módulos diretamente da galeria](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Teste os seus livros de execução

Uma `Az` vez que os módulos são importados na sua Conta de Automação, pode agora começar a editar os seus livros de execução para utilizar o módulo Az. A maioria dos cmdlets tem o `AzureRM` mesmo nome, `Az`exceto que foi alterado para . Para obter uma lista de módulos que não seguem este processo, consulte [a lista de exceções](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Uma maneira de testar os seus livros de execução antes de modificar `Enable-AzureRMAlias -Scope Process` o seu livro de execução para usar os novos cmdlets é usando no início de um livro de execução. Ao adicionar isto ao seu livro de execução, o seu livro de execução pode ser executado sem alterações.

## <a name="after-migration-details"></a>Após detalhes da migração

Depois de concluída a migração, não `AzureRM` comece a fazer runbooks utilizando módulos na conta. Também é recomendado que não importe ou atualize `AzureRM` módulos nesta conta. A partir deste momento, considere esta `Az`conta migrada para, e opere apenas com `Az` módulos. Quando for criada uma nova `AzureRM` Conta de Automação, os módulos existentes continuarão `AzureRM` a ser instalados e os cadernos de ensaios tutoriais continuarão a ser da autoria de cmdlets. Estes livros não devem ser executados.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a utilização de módulos Az, consulte [Getting started with Az module](/powershell/azure/get-started-azureps?view=azps-1.1.0).

---
title: Suporte do módulo Az na Automatização do Azure
description: Este artigo fornece informações sobre a utilização de módulos Az na Automação Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 776834937d81a3ba84e3c1db56496a7d951d7982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548347"
---
# <a name="az-module-support-in-azure-automation"></a>Suporte do módulo Az na Automatização do Azure

A Azure Automation suporta a utilização do [módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) nos seus livros de execução. O módulo Az não é importado automaticamente em quaisquer contas de Automação novas ou existentes. 

## <a name="considerations"></a>Considerações

Há muitas coisas a ter em conta ao usar o módulo Az na Automação Azure. Os livros e módulos podem ser utilizados por soluções de nível superior na sua conta Automation. Editar livros de execução ou atualizar módulos pode potencialmente causar problemas com os seus livros de execução. Deve testar cuidadosamente todos os livros e soluções numa conta `Az` de Automação separada antes de importar os novos módulos. Quaisquer modificações nos módulos podem afetar negativamente a solução [Iniciar/Parar.](automation-solution-vm-management.md) Não recomendamos alterar módulos e livros de execução em contas de Automação que contenham quaisquer soluções. Este comportamento não é específico dos módulos Az. Este comportamento deve ser tomado em consideração ao introduzir quaisquer alterações na sua conta Deautomação.

Importar um `Az` módulo na sua conta Automation não importa automaticamente o módulo na sessão PowerShell que os livros de execução usam. Os módulos são importados para a sessão PowerShell nas seguintes situações:

* Quando um cmdlet de um módulo é invocado a partir de um livro de corridas
* Quando um livro de rume `Import-Module` o importa explicitamente com o cmdlet
* Quando outro módulo dependendo do módulo é importado para uma sessão powerShell

> [!IMPORTANT]
> É importante certificar-se de que os livros `Az` de `AzureRM` execução numa conta automation apenas importam ou módulos nas sessões PowerShell utilizadas por livros de execução e não ambos. Se `Az` for importado antes `AzureRM` num livro de execução, o livro de execução completa, mas um erro que refira o [Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet aparece nos fluxos de trabalho e os cmdlets podem não ser executados corretamente. Se importar `AzureRM` e, em seguida, `Az`o seu livro de execução ainda estiver `Az` `AzureRM` completo, mas recebe um erro nos fluxos de trabalho afirmando que ambos e não podem ser importados na mesma sessão ou usados no mesmo livro de execução.

## <a name="migrating-to-az-modules"></a>Migrar para módulos Az

Recomenda-se que teste a migração para módulos Az numa conta de automação de teste. Uma vez criada a conta Automation, pode utilizar as instruções nesta secção para trabalhar com os módulos.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-cmdlets"></a>Parar e desmarcar todos os livros de execução que usam cmdlets AzureRM

Para garantir que não executa os livros `AzureRM` de execução existentes que utilizem cmdlets, deve parar e desmarcar todos os livros que utilizam `AzureRM` módulos. Pode ver quais os horários existentes e quais os horários que devem ser removidos através do código de execução semelhante a este exemplo.

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

É importante rever cada horário separadamente para garantir que pode reagendar no futuro para os seus livros de execução, se necessário.

### <a name="import-the-az-modules"></a>Importar os módulos Az

Apenas importe os módulos Az que são necessários para os seus livros de execução. Não importe o `Az` módulo de rollup, pois inclui todos os `Az.*` módulos. Esta orientação é a mesma para todos os módulos.

O módulo [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma `Az.*` dependência para os outros módulos. Por esta razão, este módulo precisa de ser importado para a sua conta de Automação antes de importar quaisquer outros módulos.

Na sua conta de Automação, selecione **Módulos** em **Recursos Partilhados**. Clique na **Galeria Browse** para abrir a página **da Galeria De Navegação.**  Na barra de pesquisa, introduza `Az.Accounts`o nome do módulo (por exemplo). Na página do Módulo PowerShell, clique **em Importar** para importar o módulo para a sua conta Deautomação.

![Módulos de importação da conta Automation](media/az-modules/import-module.png)

Este processo de importação também pode ser feito através da [PowerShell Gallery,](https://www.powershellgallery.com) procurando o módulo para importação. Assim que encontrar o módulo, selecione-o e sob o **separador De automação Azure,** clique em **Implementar para automação Azure**.

![Importar módulos diretamente da galeria](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Testar os seus livros de execução

Assim `Az` que os módulos forem importados para a sua conta Automation, pode começar a editar os seus livros de execução para utilizar os módulos Az. A maioria dos cmdlets tem os `AzureRM` mesmos nomes, exceto que foi alterado para `Az`. Para obter uma lista de módulos que não seguem esta convenção de nomeação, consulte [a lista de exceções](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Uma forma de testar a modificação de um livro de `Enable-AzureRMAlias -Scope Process` execução para utilizar os novos cmdlets é utilizando no início do livro de execução. Ao adicionar este comando ao seu livro de execução, o script pode ser executado sem alterações.

## <a name="after-migration-details"></a>Detalhes pós-migração

Depois de concluída a migração, não tente `AzureRM` começar a utilizar os livros utilizando módulos na conta Automation. Também é recomendado não importar `AzureRM` ou atualizar módulos na conta. Considere a conta `Az`migrada para, e opere apenas com `Az` módulos. 

Quando uma nova conta Automation é `AzureRM` criada, os módulos existentes ainda estão instalados. Ainda pode atualizar os livros `AzureRM` de execução tutoriais com cmdlets. Não devias publicar estes livros.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a utilização de módulos Az, consulte [Getting started with Az module](/powershell/azure/get-started-azureps?view=azps-1.1.0).

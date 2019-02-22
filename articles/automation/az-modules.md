---
title: Utilização de módulos de Az na automatização do Azure
description: Este artigo fornece informações de utilização de módulos de Az na automatização do Azure
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c3f46e40dfaf0d1ba2ab393b593cdd479c48c45d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585067"
---
# <a name="az-module-support-in-azure-automation"></a>Suporte de módulo de AZ na automatização do Azure

A automatização do Azure suporta a capacidade de utilizar o [módulo do Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) nos runbooks. O módulo de Az não é importado automaticamente as contas de automatização novo ou existente. Este artigo descreve como utilizar módulos Az automatização do Azure.

## <a name="considerations"></a>Considerações

Há muitas coisas para levar em consideração ao utilizar o módulo de Az na automatização do Azure. Runbooks e módulos podem ser utilizados por soluções de nível superior na sua conta de automatização. Editar runbooks ou atualizar os módulos pode potencialmente causar problemas com os runbooks. Deve testar todos os runbooks e soluções com cuidado numa conta de automatização separada antes de importar a nova `Az` módulos. Quaisquer modificações aos módulos podem afetar negativamente as soluções de nível mais alto, como gestão de atualizações e iniciar/parar VMs durante as horas de inatividade. É recomendado para não alterar módulos e runbooks de contas de automatização que contêm quaisquer soluções. Este comportamento não é específico para os módulos de Az. Esse comportamento deverão ser levado em consideração ao introduzir quaisquer alterações à sua conta de automatização.

Importar um `Az` módulo na conta de automatização automaticamente não importe o módulo na sessão do PowerShell que utilizam os runbooks. Módulos são importados para a sessão do PowerShell nas seguintes situações:

* Quando um cmdlet a partir de um módulo é invocado a partir de um runbook
* Quando um runbook importa-lo explicitamente com o `Import-Module` cmdlet
* Quando outro módulo consoante o módulo é importado para uma sessão do PowerShell

> [!IMPORTANT]
> É importante certificar-se de que os runbooks numa conta de automatização que quer importar apenas `Az` ou `AzureRM` módulos em sessões do PowerShell utilizadas por runbooks e não ambos. Se `Az` são importados antes `AzureRM` num runbook, o runbook será concluída, mas um [erro referenciar o método get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) mostrará os fluxos de trabalho e cmdlets não tenha sido corretamente executado. Se importar `AzureRM` e, em seguida `Az` ainda runbook completo, mas verá um erro dos fluxos de trabalho, informando que ambos `Az` e `AzureRM` não pode ser importado na mesma sessão ou utilizado no mesmo runbook.

## <a name="migrating-to-az-modules"></a>Migrar para módulos de Az

Recomenda-se que teste a migração para a utilização de módulos de Az em vez de módulos AzureRM num teste de conta de automatização. Quando tiver sido criada essa conta de automatização, pode utilizar os seguintes passos para garantir que a migração transcorre normalmente:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Parar e unschedule todos os runbook que utiliza os módulos AzureRM

Para se certificar de que não execute todos os runbooks existentes que utilizam `AzureRM` cmdlets, deve parar e unschedule todos os runbooks que utilizam `AzureRM` módulos. Pode ver as agendas de existir e agendas de que tem de ser removidas ao executar o exemplo seguinte:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

É importante rever cada agenda em separado para garantir que pode reagendá-lo no futuro para os runbooks, se necessário.

### <a name="import-the-az-modules"></a>Importe os módulos de Az

Importe apenas os módulos de Az que são necessários para os runbooks. Não importar o rollup `Az` módulo, como ele inclui todos os `Az.*` módulos a serem importados. Esta orientação é o mesmo para todos os módulos.

O [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) módulo é uma dependência para o outro `Az.*` módulos. Por esse motivo, este módulo tem de ser importado para sua conta de automatização, antes de importar quaisquer outros módulos.

A partir da sua conta de automatização, selecione **módulos** sob **recursos partilhados**. Clique em **Galeria de procura** para abrir o **Galeria de procura** página.  Na barra de pesquisa, introduza o nome do módulo (como `Az.Accounts`). Na página do módulo do PowerShell, clique em **importar** para importar o módulo para a sua conta de automatização.

![Importar módulos da conta de automatização](media/az-modules/import-module.png)

Este processo de importação, também pode ser feito através da [galeria do PowerShell](https://www.powershellgallery.com) ao procurar o módulo. Depois de localizar o módulo, selecioná-lo e, no **automatização do Azure** separador, clique em **implementar na automatização do Azure**.

![Importar os módulos diretamente a partir da Galeria](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testar os runbooks

Uma vez o `Az` os módulos são importados na conta de automatização, agora pode começar a editar os runbooks para utilizar o módulo de Az em vez disso. A maioria dos cmdlets têm o mesmo nome, exceto para `AzureRM` foi alterado para `Az`. Para obter uma lista de módulos que não siga este processo, consulte [lista de exceções](/powershell/azure/migrate-from-azurerm-to-az?view=azps-1.1.0#change-module-imports-and-cmdlet-names).

Uma forma de testar os runbooks antes de modificar o runbook para utilizar os cmdlets novos é utilizando `Enable-AzureRMAlias -Scope Process` no início de um runbook. Ao adicionar isso ao runbook, runbook pode ser executados sem alterações.

## <a name="after-migration-details"></a>Depois de detalhes da migração

Depois de concluída a migração, não começar a utilizar runbooks `AzureRM` módulos na conta mais. Também é recomendável não importar nem atualizar `AzureRM` módulos nesta conta. A partir deste momento, considere esta conta migrada para `Az`e operar com `Az` apenas módulos. Quando é criada uma nova conta de automatização existente `AzureRM` ainda serão instalados módulos e runbooks do tutorial irá ainda ser criados com `AzureRM` cmdlets. Estes runbooks não deve ser executada.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como utilizar módulos Az, veja [introdução ao módulo de Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
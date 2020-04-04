---
title: Suporte do módulo Az na Automatização do Azure
description: Este artigo fornece informações sobre a utilização de módulos Az na Automação Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: a8d6d25a2ba7f0040b13982f14f3d6081ac32f15
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637999"
---
# <a name="az-module-support-in-azure-automation"></a>Suporte do módulo Az na Automatização do Azure

A Azure Automation suporta a utilização do [módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) nos seus livros de execução. O módulo Rollup Az não é importado automaticamente em quaisquer contas de Automação novas ou existentes. 

## <a name="considerations"></a>Considerações

Há várias coisas a ter em conta ao utilizar os módulos Az na Automação Azure:

* Soluções de nível superior na sua conta Automation podem usar livros e módulos. Por isso, editar livros de execução ou atualizar módulos pode potencialmente causar problemas com as suas soluções. Deve testar cuidadosamente todos os livros e soluções numa conta de Automação separada antes de importar novos módulos Az. 

* Quaisquer modificações nos módulos podem afetar negativamente a solução [Iniciar/Parar.](automation-solution-vm-management.md) 

* Importar um módulo Az na sua conta Automation não importa automaticamente o módulo na sessão PowerShell que os livros de execução usam. Os módulos são importados para a sessão PowerShell nas seguintes situações:

    * Quando um livro de corridas invoca um cmdlet de um módulo
    * Quando um livro de resta importa `Import-Module` o módulo explicitamente com o cmdlet
    * Quando um livro importa outro módulo dependendo do módulo

> [!NOTE]
> Não recomendamos alterar módulos e livros de execução em contas de Automação que contenham quaisquer soluções. Esta disposição não é específica dos módulos Az. Deve ser tomado em consideração ao introduzir quaisquer alterações na sua conta Automation.

> [!IMPORTANT]
> Certifique-se de que os livros de execução numa conta automation importam módulos Az ou [módulos AzureRM,](https://www.powershellgallery.com/packages/AzureRM/6.13.1) mas não ambos, numa sessão powerShell. Se um livro de recortes importar módulos Az antes dos módulos AzureRM, o livro de execução completa. No entanto, um erro que refira o [Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet aparece nos fluxos de trabalho e os cmdlets podem não ser executados corretamente. Se o livro de recortes importar módulos AzureRM antes dos módulos Az, o livro de execução também completa. Neste caso, porém, recebe um erro nos fluxos de trabalho afirmando que tanto a Az como a AzureRM não podem ser importadas na mesma sessão ou usadas no mesmo livro de corridas.

## <a name="migrating-to-az-modules"></a>Migrar para módulos Az

Recomendamos que teste uma migração para módulos Az numa conta de automação de teste. Assim que criar a conta, pode utilizar as instruções nesta secção para trabalhar com os módulos.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Parar e desmarcar todos os livros que usam módulos AzureRM

Para garantir que não executa quaisquer livros de execução existentes que utilizem módulos AzureRM, pare e desmarque todos os livros de execução afetados. Pode ver quais os horários existentes e quais os horários a remover executando código semelhante a este exemplo:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

É importante rever cada horário separadamente para garantir que pode reagendar no futuro para os seus livros, se necessário.

### <a name="import-the-az-modules"></a>Importar os módulos Az

>[!NOTE]
>Os seus livros de execução importam apenas os módulos Az necessários. Não importe o módulo Az rollup, pois inclui todos os módulos Az. Esta orientação é a mesma para todos os módulos.

O módulo [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma dependência para os outros módulos Az. Por esta razão, os seus livros de execução devem importar este módulo para a sua conta Desmase antes de importar quaisquer outros módulos.

Para importar os módulos no portal Azure:

1. Na sua conta de Automação, selecione **Módulos** em **Recursos Partilhados**. 
2. Clique na **Galeria Browse** para abrir a página da Galeria De Navegação.  
3. Na barra de pesquisa, introduza o `Az.Accounts`nome do módulo, por exemplo, . 
4. Na página do Módulo PowerShell, clique **em Importar** para importar o módulo para a sua conta Deautomação.

![Módulos de importação da conta Automation](media/az-modules/import-module.png)

Este processo de importação também pode ser feito através da [PowerShell Gallery,](https://www.powershellgallery.com) procurando o módulo para importação. Assim que encontrar o módulo, selecione-o, escolha o separador **De automação Azure** e clique em **Implementar para automação Azure**.

![Importar módulos diretamente da galeria](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Testar os seus livros de execução

Depois de importar os módulos Az para a conta Automation, pode começar a editar os seus livros de execução para utilizar os módulos. A maioria dos cmdlets tem os mesmos nomes que para o módulo AzureRM, exceto que o prefixo AzureRM (ou AzureRm) foi alterado para Az. Para obter uma lista de módulos que não seguem esta convenção de nomeação, consulte [a lista de exceções](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Uma forma de testar a modificação de um livro de `Enable-AzureRmAlias -Scope Process` execução para utilizar os novos cmdlets é utilizando no início do livro de execução. Ao adicionar este comando ao seu livro de execução, o script pode ser executado sem alterações.

## <a name="after-migration-details"></a>Detalhes pós-migração

Depois de concluída a migração, não tente iniciar os livros de execução utilizando módulos AzureRM na conta Automation. Também é recomendado não importar ou atualizar módulos AzureRM na conta. Considere a conta migrada para Az, e opere apenas com módulos Az. 

Quando cria uma nova conta Automation, os módulos AzureRM existentes ainda estão instalados. Ainda pode atualizar os livros de execução tutoriais com cmdlets AzureRM. No entanto, não deve executar estes livros de execução.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a utilização de módulos Az, consulte [Getting started with Az module](/powershell/azure/get-started-azureps?view=azps-1.1.0).

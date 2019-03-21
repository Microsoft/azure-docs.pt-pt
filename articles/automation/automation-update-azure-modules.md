---
title: Atualizar módulos do Azure na automatização do Azure
description: Este artigo descreve como agora pode atualizar os módulos Azure PowerShell comuns fornecidos por predefinição na automatização do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 03174e6336589f8aa49a7fc7197da1301ff54400
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009777"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar módulos do Azure PowerShell na automatização do Azure

Para atualizar os módulos do Azure na sua conta de automatização é recomendado agora, utilize o [runbook de módulos do Azure atualizar](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), que é o código-fonte aberto agora. Além disso, pode continuar a utilizar o **atualizar módulos do Azure** botão no portal para atualizar os módulos do Azure. Para saber mais sobre como utilizar o runbook de código-fonte aberto, veja [módulos do Azure de atualização com código-fonte aberto runbook](#open-source).

Os módulos Azure PowerShell mais comuns são fornecidos por predefinição em cada conta de automatização. A equipa do Azure atualiza regularmente o os módulos do Azure. Na sua conta de automatização, é apresentada uma forma de atualizar os módulos na conta quando existem novas versões do portal.

Uma vez que os módulos são atualizados regularmente pelo grupo de produtos, as alterações podem ocorrer com os cmdlets incluídos. Esta ação pode afetar negativamente os runbooks dependendo do tipo de alteração, tal como mudar o nome de um parâmetro ou descontinuar um cmdlet inteiramente.

Para evitar ter impacto nos seus runbooks e os processos que automatizar, testar e validar antes de continuar. Se não tiver uma conta de automatização dedicada a finalidade para esta finalidade, considere a criação de um para que pode testar vários cenários diferentes durante o desenvolvimento dos seus runbooks. Esse teste deve incluir alterações iterativas, como atualizar os módulos do PowerShell. 

Se desenvolver seus scripts localmente, é recomendado ter as mesmas versões do módulo localmente que tem na sua conta de automatização quando um teste para garantir que receberá os mesmos resultados. Depois dos resultados são validados e aplicou as alterações necessárias, pode mover as alterações para produção.

> [!NOTE]
> Uma nova conta de automatização não pode conter os módulos mais recentes.

## <a name="open-source"></a>Atualizar módulos do Azure com o código-fonte aberto runbook

Para começar a utilizar o **AutomationAzureModulesForAccount de atualização** runbook para atualizar os módulos do Azure, transfira-o a partir do [repositório de runbook de módulos do Azure atualizar](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) no GitHub. Em seguida, pode importá-lo para a sua conta de automatização ou executá-lo como um script. As instruções sobre como fazer isso podem ser encontradas na [repositório de runbook de módulos do Azure de atualização](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).

### <a name="considerations"></a>Considerações

Seguem-se algumas considerações para levar em conta ao utilizar este processo para atualizar os módulos do Azure:

* Se importar este runbook com o nome original `Update-AutomationAzureModulesForAccount`, ele irá substituir o runbook interno com este nome. Como resultado, o runbook importado será executado quando o **módulos do Azure de atualização** botão é emitidos via push ou quando este runbook seja invocado diretamente por meio da API do Azure Resource Manager para esta conta de automatização.

* Este runbook suporta apenas a atualizar o **Azure** e **AzureRm** módulos atualmente. [Os módulos do Azure PowerShell Az](/powershell/azure/new-azureps-module-az) são suportados nas contas de automatização, mas não é possível atualizar este runbook.

* Evite a partir de contas de automatização que contêm módulos Az este runbook.

* Antes de iniciar este runbook, certifique-se de que a sua conta de automatização tem um [credencial da conta Run As do Azure](manage-runas-account.md) criado.

* Pode usar esse código como um script do PowerShell regular em vez de um runbook: basta iniciar sessão no Azure com o [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) comando pela primeira vez, em seguida, passar `-Login $false` ao script.

* Para utilizar este runbook em Clouds soberanas, utilize o `AzureRmEnvironment` parâmetro para passar o ambiente correto para o runbook.  Os valores aceitáveis são **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, e **AzureUSGovernment**. Estes valores podem ser recuperados do usando `Get-AzureRmEnvironment | select Name`. Se não passar um valor para este parâmetro, o runbook será predefinido para a cloud pública do Azure **AzureCloud**

* Se pretender utilizar uma versão do módulo Azure PowerShell específica em vez de mais recente disponível na galeria do PowerShell, passar essas versões para o opcional `ModuleVersionOverrides` parâmetro do **Update-AutomationAzureModulesForAccount**runbook. Para obter exemplos, consulte a [AutomationAzureModulesForAccount.ps1 atualização](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook. Os módulos do Azure PowerShell que não são mencionados no `ModuleVersionOverrides` parâmetro são atualizados com as versões mais recentes do módulo na galeria do PowerShell. Se passar nada para o `ModuleVersionOverrides` parâmetro, todos os módulos são atualizados com as versões mais recentes do módulo na galeria do PowerShell. Este comportamento é igual a **módulos do Azure de atualização** botão.

## <a name="update-azure-modules-in-the-azure-portal"></a>Atualizar módulos do Azure no portal do Azure

1. Na página de módulos de sua conta de automatização, há uma opção chamada **módulos do Azure de atualização**. Ele está sempre ativado.<br><br> ![Atualizar a opção de módulos do Azure na página de módulos](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

   > [!NOTE]
   > Antes de atualizar seus módulos do Azure, que é recomendado que Atualize-os num teste de conta de automatização para garantir que seus scripts existentes funcionam conforme esperado antes de atualizar seus módulos do Azure.
   >
   > O **módulos do Azure de atualização** botão só está disponível na cloud pública. não está disponível na [regiões de soberania](https://azure.microsoft.com/global-infrastructure/). Utilize o **AutomationAzureModulesForAccount atualização** runbook para atualizar os módulos do Azure. Pode baixá-lo do [repositório de runbook de módulos do Azure de atualização](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update). Para saber mais sobre como utilizar o runbook de código-fonte aberto, veja [módulos do Azure de atualização com código-fonte aberto runbook](#open-source).

2. Clique em **módulos do Azure de atualização**, é mostrada uma notificação de confirmação que pede-lhe se pretende continuar.<br><br> ![Atualizar notificação de módulos do Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Clique em **Sim** e iniciar o processo de atualização do módulo. O processo de atualização demora cerca de 15 a 20 minutos para atualizar os seguintes módulos:

   * Azure
   * Azure.Storage
   * AzureRm.Automation
   * AzureRm.Compute
   * AzureRm.Profile
   * AzureRm.Resources
   * AzureRm.Sql
   * AzureRm.Storage

     Se os módulos já estão atualizados, em seguida, o processo for concluído dentro de alguns segundos. Quando tiver concluído o processo de atualização, receberá uma notificação.<br><br> ![Atualizar o estado de atualização de módulos do Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

     Os módulos de AzureRm do .NET core (azurerm. *. Núcleos) não são suportados na automatização do Azure e não pode ser importado.

> [!NOTE]
> A automatização do Azure utiliza os módulos mais recentes na sua conta de automatização quando é executada uma nova tarefa agendada.  

Se utilizar cmdlets destes módulos do PowerShell do Azure nos runbooks, quer para executar este processo de atualização de todos os meses, ou então, para se certificar de que tem os módulos mais recentes. A automatização do Azure utiliza o `AzureRunAsConnection` ligação para se autenticar ao atualizar os módulos. Se o principal de serviço expirou ou já não existe no nível da subscrição, a atualização de módulo irá falhar.

## <a name="known-issues"></a>Problemas conhecidos

Existe um problema conhecido com a atualizar os módulos AzureRM numa conta de automatização que esteja num grupo de recursos com um nome numérico que começa com 0. Para atualizar seus módulos do Azure na sua conta de automatização, tem de ser num grupo de recursos que tem um nome de alfanumérico. Grupos de recursos com nomes numérico, começando com 0 são não é possível atualizar os módulos AzureRM neste momento.

## <a name="next-steps"></a>Passos Seguintes

Visite o código-fonte aberto [runbook de módulos do Azure de atualização](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) para saber mais sobre ele.

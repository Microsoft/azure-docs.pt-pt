---
title: Atualizar módulos do Azure na automatização do Azure
description: Este artigo descreve como agora pode atualizar os módulos Azure PowerShell comuns fornecidos por predefinição na automatização do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd7c268008afbd87e855516d5834676423272646
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67146721"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar módulos do Azure PowerShell na automatização do Azure

Para atualizar os módulos do Azure na conta de automatização, tem de utilizar o [runbook de módulos do Azure atualizar](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), que está disponível como código-fonte aberto. Para começar a utilizar o **AutomationAzureModulesForAccount de atualização** runbook para atualizar os módulos do Azure, transfira-o a partir do [repositório de runbook de módulos do Azure atualizar](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) no GitHub. Em seguida, pode importá-lo para a sua conta de automatização ou executá-lo como um script. Para saber como importar um runbook na conta de automatização, veja [importar um runbook](manage-runbooks.md#import-a-runbook).

Os módulos AzureRM PowerShell mais comuns são fornecidos por predefinição em cada conta de automatização. A equipa do Azure atualiza os módulos do Azure regularmente, que, por conseguinte, para manter atualizados irá querer utilizar o [AutomationAzureModulesForAccount atualização](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook para atualizar os módulos nas suas contas de automatização.

Uma vez que os módulos são atualizados regularmente pelo grupo de produtos, as alterações podem ocorrer com os cmdlets incluídos. Esta ação pode afetar negativamente os runbooks dependendo do tipo de alteração, tal como mudar o nome de um parâmetro ou descontinuar um cmdlet inteiramente.

Para evitar ter impacto nos seus runbooks e os processos que automatizar, testar e validar antes de continuar. Se não tiver uma conta de automatização dedicada a finalidade para esta finalidade, considere a criação de um para que pode testar vários cenários diferentes durante o desenvolvimento dos seus runbooks. Esse teste deve incluir alterações iterativas, como atualizar os módulos do PowerShell.

Se desenvolver seus scripts localmente, é recomendado ter as mesmas versões do módulo localmente que tem na sua conta de automatização quando um teste para garantir que receberá os mesmos resultados. Depois dos resultados são validados e aplicou as alterações necessárias, pode mover as alterações para produção.

> [!NOTE]
> Uma nova conta de automatização não pode conter os módulos mais recentes.

## <a name="considerations"></a>Considerações

Seguem-se algumas considerações para levar em conta ao utilizar este processo para atualizar os módulos do Azure:

* Este runbook oferece suporte a atualizar o **Azure** e **AzureRm** módulos por predefinição. Este runbook oferece suporte a atualizar o **Az** módulos também. Reveja os [runbook de módulos do Azure de atualização Leia-me](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) para obter mais informações sobre como atualizar `Az` módulos com este runbook. Existem adicionais fatores importantes que precisa levar em conta ao utilizar o `Az` módulos na sua conta de automatização, para obter mais informações, consulte [módulos Using Az na conta de automatização](az-modules.md).

* Antes de iniciar este runbook, certifique-se de que a sua conta de automatização tem um [credencial da conta Run As do Azure](manage-runas-account.md) criado.

* Pode usar esse código como um script do PowerShell regular em vez de um runbook: apenas de iniciar sessão no Azure com o [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) comando pela primeira vez, em seguida, passar `-Login $false` ao script.

* Para utilizar este runbook em Clouds soberanas, utilize o `AzureRmEnvironment` parâmetro para passar o ambiente correto para o runbook.  Os valores aceitáveis são **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, e **AzureUSGovernment**. Estes valores podem ser recuperados do usando `Get-AzureRmEnvironment | select Name`. Se não passar um valor para este parâmetro, o runbook será predefinido para a cloud pública do Azure **AzureCloud**

* Se pretender utilizar uma versão do módulo Azure PowerShell específica em vez de mais recente disponível na galeria do PowerShell, passar essas versões para o opcional `ModuleVersionOverrides` parâmetro do **Update-AutomationAzureModulesForAccount**runbook. Para obter exemplos, consulte a [AutomationAzureModulesForAccount.ps1 atualização](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook. Os módulos do Azure PowerShell que não são mencionados no `ModuleVersionOverrides` parâmetro são atualizados com as versões mais recentes do módulo na galeria do PowerShell. Se passar nada para o `ModuleVersionOverrides` parâmetro, todos os módulos são atualizados com as versões mais recentes do módulo na galeria do PowerShell. Este comportamento é igual a **módulos do Azure de atualização** botão.

## <a name="known-issues"></a>Problemas conhecidos

Existe um problema conhecido com a atualizar os módulos AzureRM numa conta de automatização que esteja num grupo de recursos com um nome numérico que começa com 0. Para atualizar seus módulos do Azure na sua conta de automatização, tem de ser num grupo de recursos que tem um nome de alfanumérico. Grupos de recursos com nomes numérico, começando com 0 são não é possível atualizar os módulos AzureRM neste momento.

## <a name="next-steps"></a>Passos Seguintes

Visite o código-fonte aberto [runbook de módulos do Azure de atualização](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) para saber mais sobre ele.

---
title: Atualizar módulos do Azure na automação do Azure
description: Este artigo descreve como agora você pode atualizar módulos Azure PowerShell comuns fornecidos por padrão na automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a71eb00ce4d4ace6ccc17e050946b39debed929c
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034890"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar os módulos Azure PowerShell na automação do Azure

Para atualizar os módulos do Azure em sua conta de automação, você precisa usar o [runbook atualizar módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), que está disponível como código-fonte aberto. Para começar a usar o runbook **Update-AutomationAzureModulesForAccount** para atualizar seus módulos do Azure, baixe-o no [repositório de runbook atualizar módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) no github. Em seguida, você pode importá-lo para sua conta de automação ou executá-lo como um script. Para saber como importar um runbook em sua conta de automação, consulte [importar um runbook](manage-runbooks.md#import-a-runbook).

Os módulos do PowerShell AzureRM mais comuns são fornecidos por padrão em cada conta de automação. A equipe do Azure atualiza os módulos do Azure regularmente, portanto, para manter-se atualizado, você desejará usar o runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) para atualizar os módulos em suas contas de automação.

Como os módulos são atualizados regularmente pelo grupo de produtos, as alterações podem ocorrer com os cmdlets incluídos. Essa ação pode afetar negativamente seus runbooks dependendo do tipo de alteração, como renomear um parâmetro ou substituir totalmente um cmdlet.

Para evitar afetar seus runbooks e os processos que eles automatizam, testam e validam antes de continuar. Se você não tiver uma conta de automação dedicada destinada a essa finalidade, considere a criação de uma para que você possa testar vários cenários diferentes durante o desenvolvimento de seus runbooks. Esse teste deve incluir alterações iterativas, como a atualização dos módulos do PowerShell.

Se você desenvolver seus scripts localmente, é recomendável ter as mesmas versões de módulo localmente que você tem em sua conta de automação ao testar para garantir que você receberá os mesmos resultados. Depois que os resultados são validados e você aplicou as alterações necessárias, você pode mover as alterações para produção.

> [!NOTE]
> Uma nova conta de automação pode não conter os módulos mais recentes.

> [!NOTE]
> Você não poderá excluir módulos globais-módulos que a automação fornece pronta para uso.

## <a name="considerations"></a>Considerações

Veja a seguir algumas considerações a serem levadas em conta ao usar esse processo para atualizar seus módulos do Azure:

* Esse runbook dá suporte à atualização de módulos **do Azure e do** **AzureRm** por padrão. Esse runbook também dá suporte à atualização dos módulos **AZ** . Examine o [Leiame do runbook atualizar módulos do Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) para obter `Az` mais informações sobre a atualização de módulos com este runbook. Há fatores importantes adicionais que você precisa levar em conta ao usar os módulos em `Az` sua conta de automação para saber mais, consulte usando os [módulos AZ em sua conta de automação](az-modules.md).

* Antes de iniciar este runbook, verifique se sua conta de automação tem uma credencial de [conta Executar como do Azure](manage-runas-account.md) criada.

* Você pode usar esse código como um script do PowerShell regular em vez de um runbook: basta entrar no Azure usando o comando [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) primeiro e, `-Login $false` em seguida, passar para o script.

* Para usar esse runbook nas nuvens soberanas, use o `AzureRmEnvironment` parâmetro para passar o ambiente correto para o runbook.  Os valores aceitáveis são **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**e **AzureUSGovernment**. Esses valores podem ser recuperados do `Get-AzureRmEnvironment | select Name`usando. Se você não passar um valor para esse parâmetro, o runbook usará como padrão a nuvem pública do Azure **AzureCloud**

* Se você quiser usar uma versão específica do módulo Azure PowerShell em vez da mais recente disponível no Galeria do PowerShell, passe essas versões para o parâmetro `ModuleVersionOverrides` opcional do runbook **Update-AutomationAzureModulesForAccount** . Para obter exemplos, consulte [o runbook Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) . ps1. Azure PowerShell módulos que não são mencionados no `ModuleVersionOverrides` parâmetro são atualizados com as versões mais recentes do módulo no Galeria do PowerShell. Se você não passar nada para `ModuleVersionOverrides` o parâmetro, todos os módulos serão atualizados com as versões mais recentes do módulo no Galeria do PowerShell. Esse comportamento é o mesmo que o botão **Atualizar módulos do Azure** .

## <a name="known-issues"></a>Problemas conhecidos

Há um problema conhecido com a atualização dos módulos AzureRM em uma conta de automação que está em um grupo de recursos com um nome numérico que começa com 0. Para atualizar seus módulos do Azure em sua conta de automação, ele deve estar em um grupo de recursos que tenha um nome alfanumérico. Os grupos de recursos com nomes numéricos que começam com 0 não podem atualizar os módulos AzureRM no momento.

## <a name="next-steps"></a>Passos Seguintes

Visite o [runbook atualizar software livre módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) para saber mais sobre ele.

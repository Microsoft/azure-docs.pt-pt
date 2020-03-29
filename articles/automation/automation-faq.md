---
title: FaQ de Automação Azure Microsoft Docs
description: Respostas a perguntas frequentes sobre a Automação Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925816"
---
# <a name="azure-automation-frequently-asked-questions"></a>A Automação Azure frequentemente fez perguntas

Esta FAQ da Microsoft é uma lista de perguntas comumente feitas sobre a Automação Azure. Se tiver alguma dúvida adicional sobre as suas capacidades, vá ao fórum de discussão e publique as suas perguntas. Quando uma pergunta é frequentemente feita, adicionamo-la a este artigo para que possa ser encontrado de forma rápida e fácil.

## <a name="update-management-solution"></a>Solução de Gestão de Atualizações

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Posso evitar atualizações inesperadas ao nível do OS?

Em algumas variantes do Linux, como red hat enterprise linux, as atualizações de nível OS podem ocorrer através de pacotes. Isto pode levar a atualizações de gestão onde o número da versão OS muda. Uma vez que a Atualização utiliza os mesmos métodos para atualizar pacotes que um administrador usaria localmente na máquina Linux, este comportamento é intencional.

Para evitar atualizar a versão OS através de implementações de Atualização, utilize a função **Exclusão.**

Em Red Hat Enterprise Linux, o nome do pacote a excluir é redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Por que não são aplicadas atualizações críticas/de segurança?

Quando implementa atualizações para uma máquina Linux, pode selecionar classificações de atualização. Esta opção filtra as atualizações que são aplicadas à máquina que cumprem os critérios especificados. Este filtro é aplicado localmente na máquina quando a atualização é implementada.

Como a Update Management realiza o enriquecimento de atualização na nuvem, algumas atualizações podem ser sinalizadas na Gestão de Atualização como tendo um impacto de segurança, mesmo que a máquina local não tenha essa informação. Como resultado, se aplicar atualizações críticas a uma máquina Linux, pode haver atualizações que não estão marcadas como tendo um impacto de segurança nessa máquina e, portanto, as atualizações não são aplicadas. No entanto, a Atualização de Gestão pode ainda reportar que a máquina não está em conformidade porque tem informações adicionais sobre a atualização relevante.

Implementar atualizações por classificação de atualização não funciona em versões RTM do CentOS. Para implementar corretamente atualizações para o CentOS, selecione todas as classificações para se certificar de que as atualizações são aplicadas. Para o SUSE, selecionar *apenas* **outras atualizações,** uma vez que a classificação pode fazer com que algumas atualizações de segurança também sejam instaladas se forem necessárias atualizações de segurança relacionadas com o zypper (gestor de pacotes) ou as suas dependências primeiro. Este comportamento é uma limitação do zigoto. Em alguns casos, poderá ser necessário reexecutar a implementação da atualização. Para verificar, verifique o registo da atualização.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Posso implementar atualizações entre os inquilinos do Azure?

Se tiver máquinas noutro inquilino azure a reportar à Atualização de Gestão que precisa de corrigir, terá de utilizar a seguinte seleção para as marcar. Pode utilizar o [cmdlet New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) com o `-ForUpdate` interruptor para criar uma programação e utilizar o [cmdlet New-AzureRmAutomationSoftwareUpdateE](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passar as máquinas do outro inquilino para o `-NonAzureComputer` parâmetro. O exemplo que se segue mostra como fazê-lo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Passos seguintes

Se a sua pergunta não for respondida aqui, pode consultar o seguinte fórum para perguntas e respostas adicionais.

- [Automatização do Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Para obter feedback geral sobre a solução De Gestão de Atualizações, visite o fórum de [feedback](https://feedback.azure.com/forums/905242-update-management).
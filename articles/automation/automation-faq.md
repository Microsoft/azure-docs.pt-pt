---
title: Azure Automation FAQ / Microsoft Docs
description: Este artigo dá respostas a perguntas frequentes sobre a Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 76c8d09ef2ef0130ddac856a1f37f8b68d977494
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186236"
---
# <a name="azure-automation-frequently-asked-questions"></a>AZure Automation frequentemente fez perguntas

Este Microsoft FAQ é uma lista de perguntas comumente feitas sobre a Azure Automation. Se tiver alguma dúvida adicional sobre as suas capacidades, vá ao fórum de discussão e publique as suas perguntas. Quando uma pergunta é frequentemente feita, adicionamo-la a este artigo para que possa ser encontrada de forma rápida e fácil.

## <a name="update-management"></a>Gestão de Atualizações

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Posso evitar atualizações inesperadas ao nível do SISTEMA?

Em algumas variantes do Linux, como Red Hat Enterprise Linux, as atualizações ao nível do SO podem ocorrer através de pacotes. Isto pode levar a execuções de Gestão de Atualização em que o número de versão OS muda. Como a Update Management utiliza os mesmos métodos para atualizar pacotes que um administrador utiliza localmente numa máquina Linux, este comportamento é intencional.

Para evitar atualizar a versão OS através de implementações de Gestão de Atualização, utilize a **função Exclusão.**

Na Red Hat Enterprise Linux, o nome do pacote a excluir é `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Por que não são aplicadas atualizações críticas/segurança?

Quando implementar atualizações para uma máquina Linux, pode selecionar classificações de atualização. Esta opção filtra as atualizações que satisfazem os critérios especificados. Este filtro é aplicado localmente na máquina quando a atualização é implementada.

Como a Update Management realiza o enriquecimento de atualização na nuvem, pode sinalizar algumas atualizações na Gestão de Atualização como tendo um impacto de segurança, mesmo que a máquina local não tenha essa informação. Se aplicar atualizações críticas a uma máquina Linux, pode haver atualizações que não estão marcadas como tendo um impacto de segurança nessa máquina e, portanto, não são aplicadas. No entanto, a Update Management pode ainda reportar que a máquina não é conforme porque tem informações adicionais sobre a atualização relevante.

Implementar atualizações por classificação de atualização não funciona nas versões RTM do CentOS. Para implementar corretamente atualizações para o CentOS, selecione todas as classificações para garantir que as atualizações são aplicadas. Para a SUSE, selecionar **APENAS Outras atualizações,** uma vez que a classificação pode fazer com que sejam instaladas algumas atualizações de segurança adicionais relacionadas com o zypper (gestor de pacotes) ou as suas dependências em primeiro lugar. Este comportamento é uma limitação de zypper. Em alguns casos, poderá ser necessário refazer a implementação da atualização e, em seguida, verificar a implementação através do registo de atualização.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Posso implementar atualizações em inquilinos do Azure?

Se você tem máquinas que precisam de remendar em outro inquilino Azure reportando à Update Management, você deve usar uma solução seguinte para o agendar. Pode utilizar o cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) com o `ForUpdateConfiguration` parâmetro especificado para criar um horário. Você pode usar o [Cmdlet New-AzAutomationSoftwareConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passar as máquinas do outro inquilino para o `NonAzureComputer` parâmetro. O exemplo que se segue mostra como fazê-lo.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Passos seguintes

Se a sua pergunta não for respondida aqui, pode consultar as seguintes fontes para perguntas e respostas adicionais.

- [Automatização do Azure](/answers/topics/azure-automation.html)
- [Fórum de comentários](https://feedback.azure.com/forums/905242-update-management)

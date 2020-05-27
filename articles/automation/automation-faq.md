---
title: FaQ de Automação Azure Microsoft Docs
description: Este artigo dá respostas a perguntas frequentes sobre a Automação Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 1cd20e28d1b36167154059adf728a9cfdf8102bc
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836622"
---
# <a name="azure-automation-frequently-asked-questions"></a>A Automação Azure frequentemente fez perguntas

Esta FAQ da Microsoft é uma lista de perguntas comumente feitas sobre a Automação Azure. Se tiver alguma dúvida adicional sobre as suas capacidades, vá ao fórum de discussão e publique as suas perguntas. Quando uma pergunta é frequentemente feita, adicionamo-la a este artigo para que possa ser encontrado de forma rápida e fácil.

## <a name="update-management"></a>Gestão de Atualizações

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Posso evitar atualizações inesperadas ao nível do OS?

Em algumas variantes do Linux, como red hat enterprise linux, as atualizações de nível OS podem ocorrer através de pacotes. Isto pode levar a atualizações de gestão em que o número da versão OS muda. Uma vez que a Atualização utiliza os mesmos métodos para atualizar pacotes que um administrador usa localmente numa máquina Linux, este comportamento é intencional.

Para evitar atualizar a versão OS através de implementações de Atualização, utilize a função **Exclusão.**

Em Red Hat Enterprise Linux, o nome do pacote a excluir é `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Por que não são aplicadas atualizações críticas/de segurança?

Quando implementa atualizações para uma máquina Linux, pode selecionar classificações de atualização. Esta opção filtra as atualizações que satisfazem os critérios especificados. Este filtro é aplicado localmente na máquina quando a atualização é implementada.

Como a Atualização de Gestão executa o enriquecimento de atualização na nuvem, pode sinalizar algumas atualizações na Gestão de Atualizações como tendo um impacto de segurança, mesmo que a máquina local não tenha essa informação. Se aplicar atualizações críticas a uma máquina Linux, pode haver atualizações que não estão marcadas como tendo um impacto de segurança nessa máquina e, portanto, não são aplicadas. No entanto, a Atualização de Gestão pode ainda denunciar essa máquina como incompatível porque tem informações adicionais sobre a atualização relevante.

Implementar atualizações por classificação de atualização não funciona em versões RTM do CentOS. Para implementar corretamente atualizações para o CentOS, selecione todas as classificações para se certificar de que as atualizações são aplicadas. Para o SUSE, selecionar apenas **outras atualizações,** uma vez que a classificação pode fazer com que algumas atualizações de segurança adicionais sejam instaladas se forem necessárias atualizações de segurança relacionadas com o zypper (gestor de pacotes) ou as suas dependências primeiro. Este comportamento é uma limitação do zigoto. Em alguns casos, poderá ser necessário reexecutar a implementação da atualização e, em seguida, verificar a implementação através do registo de atualização.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Posso implementar atualizações entre os inquilinos do Azure?

Se tiver máquinas que necessitem de remendar outro inquilino Azure reportando à Atualização, deve utilizar uma seguinte suposições para as agendar. Pode utilizar o cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) com o `ForUpdateConfiguration` parâmetro especificado para criar um horário. Pode utilizar o [cmdlet New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passar as máquinas do outro inquilino para o `NonAzureComputer` parâmetro. O exemplo que se segue mostra como fazê-lo.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Passos seguintes

Se a sua pergunta não for respondida aqui, pode consultar as seguintes fontes para perguntas e respostas adicionais.

- [Automatização do Azure](https://docs.microsoft.com/answers/topics/azure-automation.html)
- [Fórum de comentários](https://feedback.azure.com/forums/905242-update-management)

---
title: Azure Automation FAQ | Microsoft Docs
description: Este artigo dá respostas a perguntas frequentes sobre a Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 2b40cc3d4cea4476ffde8bee8cec694975eb5083
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97724277"
---
# <a name="azure-automation-frequently-asked-questions"></a>AZure Automation frequentemente fez perguntas

Este Microsoft FAQ é uma lista de perguntas comumente feitas sobre a Azure Automation. Se tiver mais alguma dúvida sobre as suas capacidades, vá ao fórum de discussão e publique as suas perguntas. Quando uma pergunta é frequentemente feita, adicionamos a este artigo para que seja encontrada de forma rápida e fácil.

## <a name="update-management"></a>Gestão de Atualizações

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Posso evitar atualizações inesperadas ao nível do SISTEMA?

Em algumas variantes do Linux, como Red Hat Enterprise Linux, as atualizações ao nível do SO podem ocorrer através de pacotes. Isto pode levar a execuções de Gestão de Atualização em que o número de versão OS muda. Como a Update Management utiliza os mesmos métodos para atualizar pacotes que um administrador utiliza localmente numa máquina Linux, este comportamento é intencional.

Para evitar atualizar a versão OS através de implementações de Gestão de Atualização, utilize a **função Exclusão.**

Na Red Hat Enterprise Linux, o nome do pacote a excluir é `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Por que não são aplicadas atualizações críticas/segurança?

Quando implementar atualizações para uma máquina Linux, pode selecionar classificações de atualização. Esta opção filtra as atualizações que satisfazem os critérios especificados. Este filtro é aplicado localmente na máquina quando a atualização é implementada.

Como a Update Management realiza o enriquecimento de atualização na nuvem, pode sinalizar algumas atualizações na Gestão de Atualização como tendo um impacto de segurança, mesmo que a máquina local não tenha essa informação. Se aplicar atualizações críticas a uma máquina Linux, pode haver atualizações que não estão marcadas como tendo um impacto de segurança nessa máquina e, portanto, não são aplicadas. No entanto, a Update Management pode ainda reportar que a máquina não é conforme porque tem informações adicionais sobre a atualização relevante.

Implementar atualizações por classificação de atualização não funciona nas versões RTM do CentOS. Para implementar corretamente atualizações para o CentOS, selecione todas as classificações para garantir que as atualizações são aplicadas. Para a SUSE, selecionar **APENAS Outras atualizações,** uma vez que a classificação pode instalar outras atualizações de segurança se estiverem relacionadas com o zypper (gestor de pacotes) ou as suas dependências forem necessárias primeiro. Este comportamento é uma limitação de zypper. Em alguns casos, poderá ser necessário refazer a implementação da atualização e, em seguida, verificar a implementação através do registo de atualização.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Posso implementar atualizações em inquilinos do Azure?

Se você tem máquinas que precisam de remendar em outro inquilino Azure reportando à Update Management, você deve usar uma solução seguinte para o agendar. Pode utilizar o cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) com o `ForUpdateConfiguration` parâmetro especificado para criar um horário. Você pode usar o [Cmdlet New-AzAutomationSoftwareConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) e passar as máquinas do outro inquilino para o `NonAzureComputer` parâmetro. O exemplo que se segue mostra como fazê-lo.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Automação de processos - Livros de python

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Qual versão Python 3 é suportada na Azure Automation?

Para trabalhos em nuvem, Python 3.8 é apoiado. Scripts e pacotes de qualquer versão 3.x podem funcionar se o código for compatível em diferentes versões.

Para trabalhos híbridos em Trabalhadores de Runbook Híbridos do Windows, pode optar por instalar qualquer versão 3.x que pretenda utilizar. Para trabalhos híbridos em Trabalhadores de Runbook Híbridos Linux, dependemos da versão Python 3 instalada na máquina para executar A DSC OMSConfig e o Linux Hybrid Worker. Recomendamos a instalação da versão 3.6; no entanto, diferentes versões também devem funcionar se não houver alterações nas assinaturas de métodos ou contratos entre versões de Python 3.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Os livros python 2 e Python 3 podem funcionar na mesma conta de automação?

Sim, não há nenhuma limitação para usar os runbooks Python 2 e Python 3 na mesma conta de automação.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Qual é o plano para migrar os livros e pacotes python 2 existentes para Python 3?

A Azure Automation não planeia migrar os livros e pacotes Python 2 para a Python 3. Terá que realizar esta migração sozinho. Os novos livros e pacotes Python 2 existentes e novos continuarão a funcionar.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Quais são os pacotes suportados por padrão no ambiente Python 3?

O pacote Azure 4.0.0 é instalado por padrão no ambiente de automação Python 3. Pode importar manualmente uma versão mais alta do pacote Azure para anular a versão padrão.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>E se eu publicar um livro python 3 que faz referência a um pacote Python 2 ou vice-versa?

Python 2 e Python 3 têm diferentes ambientes de execução. Enquanto um livro de bordo python 2 está em execução, apenas os pacotes Python 2 podem ser importados e similares para Python 3.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Como posso diferenciar entre os livros e pacotes Python 2 e Python 3?

Python 3 é uma nova definição de runbook, que distingue entre os livros python 2 e Python 3. Da mesma forma, é introduzido outro pacote para pacotes Python 3.

## <a name="next-steps"></a>Passos seguintes

Se a sua pergunta não for respondida aqui, pode consultar as seguintes fontes para mais perguntas e respostas.

- [Automatização do Azure](/answers/topics/azure-automation.html)
- [Fórum de comentários](https://feedback.azure.com/forums/905242-update-management)

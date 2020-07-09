---
title: Remediar servidores de configuração do Estado de Azure Automation não conformes
description: Este artigo diz como reaplicar configurações a pedido para servidores onde o estado de configuração se deslocou.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: ff785bf3ace7c65f83fe8e505f0544edd24776d8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83836860"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Remediar servidores de configuração do Estado de Azure Automation não conformes

Quando os servidores são registados na Configuração do Estado da Automação Azure, o modo de configuração está definido para `ApplyOnly` `ApplyandMonitor` , ou `ApplyAndAutoCorrect` . Se o modo não estiver definido para `ApplyAndAutoCorrect` , os servidores que derivam de um estado conforme por qualquer motivo permanecem incompatíveis até que sejam corrigidos manualmente.

O Azure compute oferece uma funcionalidade chamada Run Command que permite aos clientes executar scripts dentro de máquinas virtuais.
Este documento fornece scripts de exemplo para esta funcionalidade ao corrigir manualmente a deriva de configuração.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Deriva correta de máquinas virtuais do Windows usando PowerShell

Pode corrigir a deriva das máquinas virtuais do Windows utilizando a `Run` função de comando. Consulte [os scripts 'Executar PowerShell' no seu Windows VM com o comando Executar](/azure/virtual-machines/windows/run-command).

Para forçar um nó de configuração do Estado da Automatização Azure a descarregar a configuração mais recente e aplicá-la, utilize o cmdlet [Update-DscConfiguration.](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Deriva correta de máquinas virtuais Linux

Para máquinas virtuais Linux, não tem a opção de usar o `Run` comando. Só é possível corrigir a deriva para estas máquinas repetindo o processo de registo. 

Para os nós Azure, pode corrigir deriva do portal Azure ou usar cmdlets de módulo Az. Os detalhes sobre este processo são documentados em [Enable a VM usando o portal Azure](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).

Para nós híbridos, pode corrigir deriva usando os scripts Python. Consulte [as operações DSC a partir do computador Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Próximos passos

- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Para ver um exemplo da utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte [Configurar uma implementação contínua com chocolateria](automation-dsc-cd-chocolatey.md).
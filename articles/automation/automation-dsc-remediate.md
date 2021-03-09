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
ms.openlocfilehash: 98164c20af2a5d6ae7ccac43331a2876c23ccfb7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500177"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Remediar servidores de configuração do Estado de Azure Automation não conformes

Quando os servidores são registados na Configuração do Estado da Automação Azure, o modo de configuração está definido para `ApplyOnly` `ApplyandMonitor` , ou `ApplyAndAutoCorrect` . Se o modo não estiver definido para `ApplyAndAutoCorrect` , os servidores que derivam de um estado conforme por qualquer motivo permanecem incompatíveis até que sejam corrigidos manualmente.

O Azure compute oferece uma funcionalidade chamada Run Command que permite aos clientes executar scripts dentro de máquinas virtuais.
Este documento fornece scripts de exemplo para esta funcionalidade ao corrigir manualmente a deriva de configuração.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Deriva correta de máquinas virtuais do Windows usando PowerShell

Pode corrigir a deriva das máquinas virtuais do Windows utilizando a `Run` função de comando. Consulte [os scripts 'Executar PowerShell' no seu Windows VM com o comando Executar](../virtual-machines/windows/run-command.md).

Para forçar um nó de configuração do Estado da Automatização Azure a descarregar a configuração mais recente e aplicá-la, utilize o cmdlet [Update-DscConfiguration.](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Deriva correta de máquinas virtuais Linux

Para máquinas virtuais Linux, não tem a opção de usar o `Run` comando. Só é possível corrigir a deriva para estas máquinas repetindo o processo de registo. 

Para os nós Azure, pode corrigir deriva do portal Azure ou usar cmdlets de módulo Az. Os detalhes sobre este processo são documentados em [Enable a VM usando o portal Azure](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).

Para nós híbridos, pode corrigir deriva usando os scripts Python. Consulte [as operações DSC a partir do computador Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Passos seguintes

- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation/#automation).
- Para ver um exemplo da utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte [Configurar uma implementação contínua com chocolateria](automation-dsc-cd-chocolatey.md).

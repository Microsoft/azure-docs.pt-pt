---
title: Remediar servidores de configuração do Estado da Automatização Azure não conformes
description: Este artigo diz como reaplicar configurações a pedido para servidores onde o estado de configuração se deslocou.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: 29322ae244491e58b783745b42323455fa2752b2
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83712966"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Remediar servidores de configuração do Estado da Automatização Azure não conformes

Quando os servidores estão registados com a Configuração do Estado da Automatização Azure, o modo de configuração está definido para `ApplyOnly` , `ApplyandMonitor` ou `ApplyAndAutoCorrect` . Se o modo não estiver definido para `ApplyAndAutoCorrect` servidores que se afastem de um estado conforme por qualquer motivo permanecem incompatíveis até que sejam corrigidos manualmente.

A Azure compute oferece uma funcionalidade chamada Run Command que permite aos clientes executar scripts dentro de máquinas virtuais.
Este documento fornece scripts de exemplo para esta funcionalidade ao corrigir manualmente a deriva de configuração.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Deriva correta de máquinas virtuais windows usando PowerShell

Pode corrigir a deriva das máquinas virtuais do Windows utilizando a `Run` função de comando. Consulte [os scripts Run PowerShell no seu Windows VM com comando Executar](/azure/virtual-machines/windows/run-command).

Para forçar um nó de Configuração do Estado de Automação Azure para descarregar a configuração mais recente e aplicá-la, utilize o cmdlet [Update-DscConfiguration.](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Deriva correta das máquinas virtuais linux

Para máquinas virtuais Linux, não tem a opção de usar o `Run` comando. Só é possível corrigir a deriva destas máquinas repetindo o processo de registo. 

Para nós Azure, pode corrigir a deriva do portal Azure ou utilizando cmdlets de módulo Az. Os detalhes sobre este processo estão documentados no [Enable a VM utilizando o portal Azure](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).

Para nós híbridos, pode corrigir a deriva usando os scripts Python. Consulte [as operações dSC de realização a partir do computador Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Próximos passos

- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte a implantação contínua utilizando a Configuração do Estado da [Automação Azure e](automation-dsc-cd-chocolatey.md)o Chocolatey .
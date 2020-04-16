---
title: Remediar servidores de configuração do Estado da Automatização Azure não conformes
description: Como reaplicar configurações a pedido para servidores onde o estado de configuração tem flutuado
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: cc5884e1f70bdccee4e7a113e6e3ee2d6604b50a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406075"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Remediar servidores DSC não conformes

Quando os servidores estão registados com a Configuração `ApplyandMonitor`do `ApplyAndAutoCorrect`Estado da Automatização Azure, o modo de configuração está definido para `ApplyOnly`, ou . Se o modo não `ApplyAndAutoCorrect`estiver definido para servidores que se afastem de um estado conforme por qualquer motivo permanecem incompatíveis até que sejam corrigidos manualmente.

A Azure compute oferece uma funcionalidade chamada Run Command que permite aos clientes executar scripts dentro de máquinas virtuais.
Este documento fornece scripts de exemplo para esta funcionalidade ao corrigir manualmente a deriva de configuração.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Deriva correta de máquinas virtuais windows usando PowerShell

Para obter instruções passo a passo utilizando a função 'Executar Comando' em máquinas virtuais do Windows, consulte a página de documentação [Executar scripts PowerShell no seu Windows VM com Comando de Execução](/azure/virtual-machines/windows/run-command).

Para forçar um nó de Configuração do Estado de Automação Azure para descarregar a configuração mais recente e aplicá-la, utilize o cmdlet [Update-DscConfiguration.](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Deriva correta das máquinas virtuais linux

Funcionalidade semelhante não está disponível para servidores Linux.
A única opção é repetir o processo de registo.

Para nós Azure, pode corrigir a deriva do portal Azure ou utilizando cmdlets de módulo Az. Os detalhes sobre este processo estão documentados em máquinas de [embarque para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md#onboard-vms-by-using-the-azure-portal).
Para nós híbridos, pode corrigir a deriva usando os scripts Python incluídos.
Consulte [powerShell DSC para linux repo](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Passos seguintes

- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte a implantação contínua utilizando a Configuração do Estado de [Automação Azure e o Chocolatey](automation-dsc-cd-chocolatey.md).
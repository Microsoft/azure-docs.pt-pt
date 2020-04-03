---
title: Servidores de configuração do Estado de Automação Azure não conformes remediar
description: Como reaplicar configurações a pedido para servidores onde o estado de configuração tem flutuado
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: f4ca76f4be9d00e185f8774fc33296d1af1aeece
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585506"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Remediar servidores DSC não compatíveis

Quando os servidores estão registados com a Configuração do Estado da Automação Azure, o 'Modo de Configuração' está definido para Aplicar Apenas, ApplyandMonitor ou ApplyAndAutoCorrect.
Se o modo não estiver definido para O AutoCorrect, os servidores que se afastarem de um estado conforme por qualquer motivo permanecerão incompatíveis até que sejam corrigidos manualmente.

A Azure compute oferece uma funcionalidade chamada Run Command que permite aos clientes executar scripts dentro de máquinas virtuais.
Este documento fornece scripts de exemplo para esta funcionalidade ao corrigir manualmente a deriva de configuração.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Deriva correta de máquinas virtuais windows usando PowerShell

Para instruções passo a passo utilizando a função 'Executar Comando' em máquinas virtuais do Windows, consulte a página de documentação [Executar scripts PowerShell no seu Windows VM com Comando de Execução](/azure/virtual-machines/windows/run-command).

Para forçar um nó de Configuração do Estado de Automação `Update-DscConfiguration` Azure para descarregar a configuração mais recente e aplicá-lo, use o cmdlet.
Para mais detalhes, consulte a documentação cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Deriva correta das máquinas virtuais linux

Funcionalidade semelhante não está disponível para servidores Linux.
A única opção é repetir o processo de registo.
Para os nós Azure, a correção de deriva pode ser feita a partir do portal ou utilizando cmdlets Az Automation.
Os detalhes sobre este processo estão documentados na página [Máquinas de embarque para gestão pela Configuração do Estado da Automação Azure](/azure/automation/automation-dsc-onboarding#onboard-a-vm-using-azure-portal).
Para nós híbridos, a correção de deriva pode ser feita usando os scripts Python incluídos.
Consulte a documentação no [PowerShell DSC para o repo linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Passos seguintes

- Para referência de cmdlet PowerShell, consulte [Os cmdlets](/powershell/module/azurerm.automation/#automation) de configuração do Estado da Automatização Do Azure
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num pipeline de implantação contínua, consulte a implantação contínua utilizando a configuração do Estado da [Automação Azure e](automation-dsc-cd-chocolatey.md) o Chocolatey

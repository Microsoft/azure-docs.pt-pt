---
title: Corrigir servidores de configuração de estado de automação do Azure não compatível
description: Como reaplicar as configurações sob demanda aos servidores em que o estado da configuração foi descompasso
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614501"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Remediar servidores DSC não compatíveis

Quando os servidores são registrados com a configuração de estado de automação do Azure, o ' modo de configuração ' é definido como ApplyOnly, ApplyandMonitor ou ApplyAndAutoCorrect.
Se o modo não estiver definido como AutoCorreção, os servidores que desaparecerem de um estado compatível por qualquer motivo permanecerão não compatíveis até serem corrigidos manualmente.

A computação do Azure oferece um recurso chamado executar comando que permite aos clientes executar scripts dentro de máquinas virtuais.
Este documento fornece scripts de exemplo para esse recurso ao corrigir manualmente a descompasso de configuração.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Corrigir descompasso das máquinas virtuais do Windows usando o PowerShell

Para obter instruções passo a passo usando o recurso executar comando em máquinas virtuais do Windows, consulte a página de documentação [executar scripts do PowerShell em sua VM do Windows com o comando executar](/azure/virtual-machines/windows/run-command).

Para forçar um nó de configuração de estado da automação do Azure a baixar a configuração mais recente e `Update-DscConfiguration` aplicá-la, use o cmdlet.
Para obter detalhes, consulte a documentação de cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Corrigir descompasso das máquinas virtuais do Linux

A funcionalidade semelhante não está disponível atualmente para servidores Linux.
A única opção é repetir o processo de registro.
Para nós do Azure, a correção de descompasso pode ser feita no portal ou usando cmdlets de automação AZ.
Os detalhes sobre esse processo estão documentados na página [integração de máquinas para gerenciamento pela configuração de estado da automação do Azure](/azure/automation/automation-dsc-onboarding#azure-portal).
Para nós híbridos, a correção de descompasso pode ser feita usando os scripts Python incluídos.
Consulte a documentação no [repositório do DSC do PowerShell para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Passos Seguintes

- Para referência de cmdlet do PowerShell, consulte cmdlets de [configuração do estado de automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)

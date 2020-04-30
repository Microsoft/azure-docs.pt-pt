---
title: Executar scripts em um Azure Linux VM
description: Este tópico descreve como executar scripts dentro de uma máquina virtual
services: automation
ms.service: virtual-machines-linux
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: article
ms.openlocfilehash: 9dbfb3e76fe1c0e80dd8020b79f4edd6d32a24a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758596"
---
# <a name="run-scripts-in-your-linux-vm"></a>Execute scripts no seu VM Linux

Para automatizar tarefas ou problemas de resolução de problemas, poderá ser necessário executar comandos num VM. O seguinte artigo dá uma breve visão geral das funcionalidades que estão disponíveis para executar scripts e comandos dentro dos seus VMs.

## <a name="custom-script-extension"></a>Extensão de Script Personalizado

A [extensão do script personalizado](../extensions/custom-script-linux.md) é usada principalmente para configuração de implementação de posts e instalação de software.

* Descarregue e execute scripts em máquinas virtuais Azure.
* Pode ser executado usando modelos de Gestor de Recursos Azure, Azure CLI, REST API, PowerShell ou portal Azure.
* Os ficheiros script podem ser descarregados a partir do armazenamento Do Azure ou do GitHub, ou fornecidos a partir do seu PC quando executados a partir do portal Azure.
* Executar o script PowerShell em máquinas Windows e script Bash em máquinas Linux.
* Útil para a configuração de pós-implementação, instalação de software e outras tarefas de configuração ou gestão.

## <a name="run-command"></a>Executar comando

A função [Executar Command](run-command.md) permite a gestão virtual de máquinas e aplicações e resolução de problemas utilizando scripts, e está disponível mesmo quando a máquina não está acessível, por exemplo, se a firewall do hóspede não tiver a porta RDP ou SSH aberta.

* Execute scripts em máquinas virtuais Azure.
* Pode ser executado usando [portal Azure](run-command.md), [REST API,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)ou [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand)
* Executar rapidamente um script e visualizar a saída e repetir conforme necessário no portal Azure.
* O script pode ser dactilografado diretamente ou pode executar um dos scripts incorporados.
* Executar o script PowerShell em máquinas Windows e script Bash em máquinas Linux.
* Útil para a gestão de máquinas virtuais e aplicações e para executar scripts em máquinas virtuais que são inacessíveis.

## <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida

O [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) fornece uma máquina geral, aplicação e gestão ambiental com scripts personalizados do utilizador armazenados numa conta de Automação.

* Execute scripts em máquinas Azure e não-Azure.
* Pode ser executado usando o portal Azure, Azure CLI, REST API, PowerShell, webhook.
* Scripts armazenados e geridos numa Conta de Automação.
* Executar PowerShell, PowerShell Workflow, Python ou livros gráficos
* Sem limite de tempo no tempo de execução do guião.
* Vários scripts podem ser executados simultaneamente.
* A saída completa do script é devolvida e armazenada.
* Histórico de emprego disponível por 90 dias.
* Os scripts podem ser executados como Sistema Local ou com credenciais fornecidas pelo utilizador.
* Requer [instalação manual](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Consola de série

A [consola Serial](serial-console.md) fornece acesso direto a um VM, semelhante a ter um teclado ligado ao VM.

* Executar comandos em máquinas virtuais Azure.
* Pode ser executado utilizando uma consola baseada em texto para a máquina no portal Azure.
* Inicie sessão na máquina com uma conta de utilizador local.
* Útil quando o acesso à máquina virtual é necessário independentemente da rede da máquina ou do estado do sistema operativo.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as diferentes funcionalidades que estão disponíveis para executar scripts e comandos dentro dos seus VMs.

* [Extensão de Script Personalizado](../extensions/custom-script-linux.md)
* [Executar o Comando](run-command.md)
* [Função de Trabalho de Runbook Híbrida](../../automation/automation-hybrid-runbook-worker.md)
* [Consola em série](serial-console.md)
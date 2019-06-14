---
title: Executar scripts numa VM do Windows Azure
description: Este tópico descreve como executar scripts numa máquina virtual do Windows
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ec34ff874eae9bbdd53470f135bcf0c182d5daed
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60251064"
---
# <a name="run-scripts-in-your-windows-vm"></a>Executar scripts na sua VM do Windows

Para automatizar tarefas ou resolução de problemas, terá de executar comandos numa VM. O seguinte artigo fornece uma breve descrição geral das funcionalidades que estão disponíveis para executar scripts e comandos dentro de suas VMs.

## <a name="custom-script-extension"></a>Extensão de Script Personalizado

O [extensão de Script personalizado](../extensions/custom-script-windows.md) é usado principalmente para a instalação de software e configuração de implementação de post.

* Transfira e execute scripts em máquinas virtuais do Azure.
* Pode ser executado com modelos Azure Resource Manager, da CLI do Azure, REST API, PowerShell ou o portal do Azure.
* Arquivos de script podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos do seu PC quando executar a partir do portal do Azure.
* Executar script do PowerShell em máquinas do Windows e em máquinas do Linux do script de Bash.
* Útil para configuração pós-implementação, instalação de software e outros configuração ou tarefas de gestão.

## <a name="run-command"></a>Execute o comando

O [executar comando](run-command.md) funcionalidade permite que a máquina virtual e a gestão de aplicações e a resolução de problemas com scripts e está disponível, mesmo quando o computador não estiver acessível, por exemplo, se a firewall de convidado não tiver a porta RDP ou SSH Abra.

* Execute scripts em máquinas virtuais do Azure.
* Pode ser executado usando [portal do Azure](run-command.md), [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [da CLI do Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), ou [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand)
* Executar um script e vista de saída e repita conforme necessário no portal do Azure rapidamente.
* Script pode ser digitado diretamente ou pode executar um dos scripts incorporados.
* Executar script do PowerShell em máquinas do Windows e em máquinas do Linux do script de Bash.
* Útil para a máquina virtual e o gerenciamento de aplicativos e para a execução de scripts em máquinas virtuais que são inacessíveis.

## <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida

O [Runbook Worker híbrido](../../automation/automation-hybrid-runbook-worker.md) Fornece gerenciamento geral de máquina, o aplicativo e o ambiente com scripts personalizados do usuário armazenados numa conta de automatização.

* Execute scripts no Azure e máquinas não Azure.
* Podem ser executados através do portal do Azure, CLI do Azure, REST API, PowerShell, o webhook.
* Scripts de armazenados e gerenciados numa conta de automatização.
* Executar runbooks do PowerShell, fluxo de trabalho do PowerShell, Python ou gráfico
* Sem limite de tempo no tempo de execução do script.
* Vários scripts podem ser executadas em simultâneo.
* Saída do script completo é devolvida e armazenada.
* Histórico de tarefas disponível durante 90 dias.
* Os scripts podem ser executados como sistema Local ou com as credenciais fornecido pelo usuário.
* Requer [instalação manual](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Consola de série

O [consola de série](serial-console.md) fornece acesso direto a uma VM, semelhante a ter um teclado ligado à VM.

* Comandos de execução em máquinas virtuais do Azure.
* Pode ser executado com um console baseado em texto para a máquina no portal do Azure.
* Início de sessão para a máquina com uma conta de utilizador local.
* Útil quando for necessário acesso à máquina virtual, independentemente do Estado de rede ou sistema operativo da máquina.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as diferentes funcionalidades que estão disponíveis para executar scripts e comandos dentro de suas VMs.

* [Extensão de Script Personalizado](../extensions/custom-script-windows.md)
* [Execute o comando](run-command.md)
* [A função de trabalho de Runbook híbrida](../../automation/automation-hybrid-runbook-worker.md)
* [Consola de série](serial-console.md)
